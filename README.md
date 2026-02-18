# 📦 InvenTree - Sistema de Gestión de Inventario

Configuración completa para desplegar **InvenTree** en **Dokploy** usando Docker Compose.

## 📋 ¿Qué es InvenTree?

InvenTree es un sistema de código abierto para gestionar inventario, piezas, órdenes de compra, producción y más. Ideal para negocios, talleres y proyectos.

## 📁 Archivos Incluidos

- `docker-compose.yml` - Servicios: PostgreSQL, Redis, InvenTree Server, Worker y Caddy
- `.env.template` - Plantilla de variables de entorno (copia esto a Dokploy)
- `Caddyfile` - Configuración del servidor web
- `.gitignore` - Protege el archivo `.env` local

## 🚀 Pasos para Instalar en Dokploy

### 1️⃣ Preparar el Repositorio (Ya hecho)

Este repositorio ya está listo. Solo necesitas subirlo a GitHub.

### 2️⃣ Crear el Compose en Dokploy

1. **Inicia sesión en Dokploy** (tu panel de administración)
2. **Selecciona o crea un Proyecto**
3. Haz clic en **"+ Create Service"** o **"Add Service"**
4. Selecciona **"Compose"** (NO "Application")

### 3️⃣ Conectar el Repositorio Git

1. En la configuración del Compose:
   - **Provider**: Selecciona "GitHub"
   - **Repository**: Pega la URL: `https://github.com/CientificoJose/inventree.git`
   - **Branch**: `main` (o `master` si es el caso)
   - **Compose Path**: Deja en blanco o escribe `/` (raíz del repo)

2. Dokploy detectará automáticamente el archivo `docker-compose.yml`

### 4️⃣ Configurar Variables de Entorno

En la sección **"Environment"** de Dokploy, copia y pega esto:

```env
INVENTREE_DEBUG=False
INVENTREE_LOG_LEVEL=WARNING
INVENTREE_DB_ENGINE=postgresql
INVENTREE_DB_NAME=inventree
INVENTREE_DB_HOST=inventree-db
INVENTREE_DB_PORT=5432
INVENTREE_DB_USER=inventree
INVENTREE_DB_PASSWORD=TU_CONTRASEÑA_SEGURA_AQUI
INVENTREE_CACHE_ENABLED=True
INVENTREE_CACHE_HOST=inventree-cache
INVENTREE_CACHE_PORT=6379
INVENTREE_GUNICORN_TIMEOUT=90
INVENTREE_PLUGINS_ENABLED=True
INVENTREE_AUTO_UPDATE=True
INVENTREE_TAG=0.16.0
INVENTREE_WEB_PORT=8000
INVENTREE_SITE_URL=http://tu-dominio.com
INVENTREE_SECRET_KEY=TU_CLAVE_SECRETA_AQUI
```

**⚠️ IMPORTANTE:**
- Cambia `TU_CONTRASEÑA_SEGURA_AQUI` por una contraseña fuerte
- Cambia `TU_CLAVE_SECRETA_AQUI` por una clave aleatoria de 32+ caracteres
- Actualiza `INVENTREE_SITE_URL` con tu dominio real
- Configura las variables de email si quieres notificaciones por correo

### 5️⃣ Configurar Dominio (Opcional)

Si quieres acceder con un dominio personalizado:

1. En Dokploy, ve a la sección **"Domains"**
2. Agrega tu dominio (ej: `inventree.tudominio.com`)
3. Dokploy configurará automáticamente SSL con Let's Encrypt

### 6️⃣ Desplegar

1. Haz clic en **"Deploy"**
2. Espera 2-5 minutos mientras se descargan las imágenes
3. Verifica el estado en los logs

## � Configuración de Email (Opcional)

Si quieres que InvenTree envíe notificaciones por correo electrónico, agrega estas variables:

```env
INVENTREE_EMAIL_BACKEND=django.core.mail.backends.smtp.EmailBackend
INVENTREE_EMAIL_HOST=mail.press-cloud.com
INVENTREE_EMAIL_PORT=465
INVENTREE_EMAIL_USERNAME=erp-inventree@press-cloud.com
INVENTREE_EMAIL_PASSWORD=tu_contraseña_email
INVENTREE_EMAIL_TLS=False
INVENTREE_EMAIL_SSL=True
INVENTREE_EMAIL_SENDER=erp-inventree@press-cloud.com
INVENTREE_EMAIL_PREFIX=[InvenTree]
```

**Notas:**
- Usa `INVENTREE_EMAIL_SSL=True` para puerto 465
- Usa `INVENTREE_EMAIL_TLS=True` para puerto 587
- `EMAIL_SENDER` es el remitente que aparecerá en los correos

## �🔐 Crear Usuario Administrador

Después del primer despliegue, necesitas crear un usuario admin:

**Opción A: Desde Dokploy (Recomendado)**

1. Ve al servicio `inventree-server`
2. Abre la **Terminal/Console**
3. Ejecuta:
```bash
invoke superuser
```

**Opción B: Configurar en .env (Antes del deploy)**

Agrega estas líneas a las variables de entorno:
```env
INVENTREE_ADMIN_USER=admin
INVENTREE_ADMIN_PASSWORD=tu_password_admin
INVENTREE_ADMIN_EMAIL=tu@email.com
```

## 🌐 Acceso a la Aplicación

- **Con dominio**: `https://inventree.tudominio.com`
- **Sin dominio**: `http://IP_SERVIDOR:8000`

## 🔧 Solución de Problemas

### El contenedor no inicia
- Revisa los logs en Dokploy
- Verifica que todas las variables de entorno estén configuradas

### Error de base de datos
- Asegúrate que `INVENTREE_DB_PASSWORD` sea la misma en todos los servicios
- Espera 30 segundos después del deploy para que PostgreSQL inicie

### No puedo acceder
- Verifica que el puerto 8000 esté expuesto
- Si usas dominio, asegúrate que el DNS apunte a tu servidor

### Error INVE-E1: "No frontend included"
Este error ocurre cuando usas el tag `stable` en lugar de una versión específica.

**Solución:**
- Cambia `INVENTREE_TAG=stable` por `INVENTREE_TAG=0.16.0` (o la versión más reciente)
- El tag `stable` NO incluye el frontend compilado
- Solo las versiones con números específicos (0.16.0, 1.0.0, etc.) incluyen el frontend

## 📚 Recursos

- [Documentación InvenTree](https://docs.inventree.org/)
- [GitHub InvenTree](https://github.com/inventree/InvenTree)
- [Dokploy Docs](https://docs.dokploy.com/)
