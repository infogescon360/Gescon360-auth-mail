# Gescon360-auth-mail

**Sistema de autenticación seguro y control de acceso para GESCON 360 con validación de dominio gescon360.es**

## 🌟 Características

- ✅ **Autenticación segura** con Supabase (gratuita hasta 50K usuarios)
- 🔐 **Validación de dominio** - Solo mails @gescon360.es
- 🚫 **Panel de Admin** - Acceso restringido solo a administradores
- 🃄 **Sistema de invitaciones** - Genera códigos/enlaces para nuevos usuarios
- 🔀 **Gestión de permisos** - Controla quién tiene acceso
- 🔠 **Completamente independiente** - Se integra con tu app cuando estés listo
- 📂 **Almacenamiento seguro** - Base de datos encriptada en Supabase

## 🚀 Inicio rápido

### 1. Clonar el repositorio

```bash
git clone https://github.com/infogescon360/Gescon360-auth-mail.git
cd Gescon360-auth-mail
```

### 2. Crear cuenta en Supabase (GRATIS)

- Ve a [supabase.com](https://supabase.com)
- Haz clic en "Start your project"
- Crea una cuenta con tu email (@gescon360.es preferible)
- Crea un nuevo proyecto (region: Europa)

### 3. Configurar variables de entorno

Crea un archivo `.env.local` en la raíz:

```bash
VITE_SUPABASE_URL=https://xxxxx.supabase.co
VITE_SUPABASE_ANON_KEY=eyxxxx...
VITE_ADMIN_EMAIL_DOMAIN=gescon360.es
```

Encuentras las credenciales en:
- Supabase Dashboard ➜ Settings ➜ API

### 4. Instalar dependencias

```bash
npm install
```

### 5. Crear base de datos

En Supabase SQL Editor, ejecuta el archivo `database/schema.sql`:

```sql
-- Tabla de usuarios
CREATE TABLE public.users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email TEXT UNIQUE NOT NULL,
  role TEXT DEFAULT 'user' CHECK (role IN ('admin', 'user', 'viewer')),
  status TEXT DEFAULT 'pending' CHECK (status IN ('pending', 'active', 'inactive')),
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Tabla de invitaciones
CREATE TABLE public.invitations (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  code TEXT UNIQUE NOT NULL,
  email TEXT,
  created_by UUID REFERENCES public.users(id),
  used_at TIMESTAMP WITH TIME ZONE,
  expires_at TIMESTAMP WITH TIME ZONE DEFAULT NOW() + INTERVAL '30 days',
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Tabla de logs de acceso
CREATE TABLE public.access_logs (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES public.users(id),
  action TEXT,
  ip_address TEXT,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);
```

### 6. Ejecutar en desarrollo

```bash
npm run dev
```

Abre [http://localhost:5173](http://localhost:5173)

## 📚 Estructura del proyecto

```
Gescon360-auth-mail/
├── src/
│  ├── components/
│  │  ├── LoginForm.vue
│  │  ├─│  AdminPanel.vue
│  │  ├─│  UserManagement.vue
│  │  └─│  InvitationGenerator.vue
│  ├── lib/
│  │  ├── supabase.js
│  │  ├── auth.js
│  │  └─│  validation.js
│  ├── views/
│  │  ├─│  Login.vue
│  │  ├─│  Dashboard.vue
│  │  └─│  AdminDashboard.vue
│  ├─│  App.vue
│  └─│  main.js
├── database/
│  └─│  schema.sql
├── public/
├── .env.local
├── vite.config.js
├── package.json
└── README.md
```

## 📐 Flujo de uso

### Como administrador:

1. Accedes con tu email @gescon360.es
2. Panel de admin con opciones:
   - Ver usuarios registrados
   - Cambiar roles (admin, user, viewer)
   - Desactivar usuarios
   - Generar códigos de invitación
   - Ver logs de acceso

### Como usuario invitado:

1. Recibes un código o enlace de invitación
2. Te registras con email @gescon360.es
3. Confirmas tu email
4. Accedes al sistema

## 🔈 Variables de entorno

```bash
# Supabase
VITE_SUPABASE_URL=tu_url_supabase
VITE_SUPABASE_ANON_KEY=tu_anon_key

# Dominio permitido
VITE_ADMIN_EMAIL_DOMAIN=gescon360.es

# Opcional
VITE_APP_NAME=GESCON 360
VITE_LOGO_URL=tu_logo.png
```

## 😒 Funciones principales

### Auth (src/lib/auth.js)

```javascript
// Login
await authService.login(email, password)

// Register (solo con invitación)
await authService.register(email, password, invitationCode)

// Logout
await authService.logout()

// Get current user
authService.getCurrentUser()

// Check if admin
authService.isAdmin(user)
```

### User Management (Admin)

```javascript
// Crear invitación
await userService.createInvitation(email, createdBy)

// Listar usuarios
await userService.getUsers()

// Cambiar rol
await userService.updateUserRole(userId, newRole)

// Desactivar usuario
await userService.deactivateUser(userId)
```

## 🔗 Integración con tu app

Cuando estés listo para integrar con Gescon360-web:

```javascript
// En tu app principal
import { authService } from '@/lib/auth.js'

// Proteger rutas
router.beforeEach(async (to, from) => {
  const user = authService.getCurrentUser()
  if (to.meta.requiresAuth && !user) {
    return '/login'
  }
})
```

## 🖑 Seguridad

- ✅ Contraseñas hasheadas con bcrypt
- ✅ Tokens JWT con expiración
- ✅ Validación de dominio en servidor
- ✅ Rate limiting en login
- ✅ Logs de acceso completos
- ✅ SSL/TLS automático con Supabase

## 📄 Documentación

- [Supabase Docs](https://supabase.com/docs)
- [Vue 3 Guide](https://vuejs.org)
- [JWT Basics](https://jwt.io/introduction)

## 🐝 Problemas comunes

### Error: "Domain not allowed"
```
Asegúrate de usar email @gescon360.es
Verifica VITE_ADMIN_EMAIL_DOMAIN en .env.local
```

### Error: "Invalid invitation code"
```
Comprueba que el código no haya expirado (30 días)
El código solo se puede usar una vez
```

### Error de conexión a Supabase
```
Verifica las credenciales en .env.local
Asegúrate de que Supabase esté activo
```

## 🌟 Soporte

Para preguntas o problemas:
- Abre un Issue en GitHub
- Contacta con el equipo de desarrollo

## 📁 Licencia

MIT - Libre para uso interno en Gescon360

---

**Creado para GESCON 360 - Sistema Integral de Gestión de Expedientes**
