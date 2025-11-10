# GUÍA DE INSTALACIÓN RÁPIDA - Gescon360-auth-mail

## ⚡ En 5 minutos tienes autenticación funcionando

### PASO 1: Crear cuenta en Supabase (GRATIS)

1. Ve a https://supabase.com
2. Haz clic en **"Start your project"**
3. Crea una cuenta con Google o email
4. Crea un nuevo proyecto:
   - **Nombre del proyecto**: Gescon360-auth
   - **Región**: Europa (Paris o Frankfurt)
   - Espera 1-2 minutos a que inicie

### PASO 2: Obtener credenciales

1. En Supabase, ve a **Settings ➜ API**
2. Copia estos valores:
   - **URL**: `https://xxxxx.supabase.co`
   - **ANON KEY**: `eyJ...`

### PASO 3: Crear base de datos

1. En Supabase, ve a **SQL Editor**
2. Copia y pega esto en el editor:

```sql
-- Crear tabla de usuarios
CREATE TABLE IF NOT EXISTS public.users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email TEXT UNIQUE NOT NULL,
  role TEXT DEFAULT 'user' CHECK (role IN ('admin', 'user', 'viewer')),
  status TEXT DEFAULT 'pending' CHECK (status IN ('pending', 'active', 'inactive')),
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Crear tabla de invitaciones
CREATE TABLE IF NOT EXISTS public.invitations (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  code TEXT UNIQUE NOT NULL,
  email TEXT,
  created_by UUID REFERENCES public.users(id) ON DELETE SET NULL,
  used_at TIMESTAMP WITH TIME ZONE,
  expires_at TIMESTAMP WITH TIME ZONE DEFAULT NOW() + INTERVAL '30 days',
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Crear tabla de logs
CREATE TABLE IF NOT EXISTS public.access_logs (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES public.users(id) ON DELETE CASCADE,
  action TEXT NOT NULL,
  ip_address TEXT,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Crear índices para mejor rendimiento
CREATE INDEX idx_users_email ON public.users(email);
CREATE INDEX idx_invitations_code ON public.invitations(code);
CREATE INDEX idx_invitations_expires ON public.invitations(expires_at);
CREATE INDEX idx_access_logs_user ON public.access_logs(user_id);
```

3. Haz clic en **Run**
4. Verifica que se crearon las 3 tablas en **Table Editor**

### PASO 4: Configurar tu proyecto

1. **Clona el repositorio**:
```bash
git clone https://github.com/infogescon360/Gescon360-auth-mail.git
cd Gescon360-auth-mail
```

2. **Instala dependencias**:
```bash
npm install
```

3. **Crea `.env.local`** en la raíz:
```bash
VITE_SUPABASE_URL=https://xxxxx.supabase.co
VITE_SUPABASE_ANON_KEY=eyJ...
VITE_ADMIN_EMAIL_DOMAIN=gescon360.es
VITE_APP_NAME=GESCON 360
```

### PASO 5: Inicia en desarrollo

```bash
npm run dev
```

Abre http://localhost:5173 ✅

## 🔐 Crear tu primer usuario admin

### Opción A: Mediante Supabase (MÁS FÁCIL)

1. En Supabase, ve a **Table Editor ➜ users**
2. Haz clic en **Insert Row**
3. Completa:
   - **email**: tu_email@gescon360.es
   - **role**: admin
   - **status**: active
4. Listo! Ya puedes login en la app

### Opción B: Crear usuario mediante API

En el navegador, abre la consola (F12) y ejecuta:

```javascript
import { createClient } from '@supabase/supabase-js'

const supabase = createClient(
  'https://xxxxx.supabase.co',
  'eyJ...'
)

const { data, error } = await supabase
  .from('users')
  .insert([
    { 
      email: 'admin@gescon360.es',
      role: 'admin',
      status: 'active'
    }
  ])

console.log(data, error)
```

## 🎯 Próximos pasos

1. **Crear invitaciones** desde el panel admin
2. **Invitar usuarios** con @gescon360.es
3. **Gestionar permisos** de cada usuario
4. **Integrar con tu app** (documentación en README.md)

## 🚨 Troubleshooting

### Error: "Invalid SUPABASE URL"
- ✅ Verifica que la URL tenga `.supabase.co`
- ✅ Copia el valor exacto de Settings ➜ API

### Error: "Domain not allowed"
- ✅ Usa email @gescon360.es
- ✅ Verifica `VITE_ADMIN_EMAIL_DOMAIN` en .env.local

### No puedo acceder
- ✅ Verifica que el usuario existe en la tabla users
- ✅ Verifica que status = 'active'
- ✅ Limpia cookies del navegador (CTRL+SHIFT+DEL)

## 📞 Soporte

- Documentación completa: Ve a **README.md**
- Problemas: Abre un Issue en GitHub
- Contacta: Equipo de GESCON 360

---

**¡Ya tienes autenticación funcionando! 🎉**

Próximo: Leer README.md para características avanzadas
