# Guía: Configurar el Bot de Discord

## Configuración del Bot

El skill usa un **único Bot Token** para todas las operaciones:

| Operación | Endpoint | Propósito |
|-----------|----------|-----------|
| Leer threads | `GET /guilds/{id}/threads/active` | Obtener threads activos/archivados para detectar duplicados |
| Publicar threads | `POST /channels/{id}/threads` | Crear nuevos posts en el Forum Channel |
| Editar mensajes | `PATCH /channels/{id}/messages/{msg_id}` | Actualizar ofertas existentes |

---

## Paso a Paso: Crear el Bot

### 1. Ir al Discord Developer Portal
Abrí [https://discord.com/developers/applications](https://discord.com/developers/applications) e iniciá sesión con tu cuenta de Discord.

### 2. Crear una nueva Application
- Clic en **"New Application"**
- Ponerle un nombre (ej: `JobsFeedBot`)
- Aceptar los términos → **"Create"**

### 3. Crear el Bot
- En el menú izquierdo, clic en **"Bot"**
- Clic en **"Add Bot"** → confirmar con **"Yes, do it!"**

### 4. Copiar el Token
- En la sección **"Token"**, clic en **"Reset Token"** y luego **"Copy"**
- **⚠️ Guardá este token de inmediato** — solo se muestra una vez
- Este es tu `DISCORD_BOT_TOKEN`

### 5. Configurar Permisos del Bot
En la sección **"Bot"**, bajá hasta **"Privileged Gateway Intents"** y activá:
- ✅ **Message Content Intent** (para leer contenido de mensajes)

### 6. Invitar el Bot al Servidor
- En el menú izquierdo, ir a **"OAuth2" → "URL Generator"**
- En **"Scopes"** seleccionar: `bot`
- En **"Bot Permissions"** seleccionar:
  - ✅ Read Messages/View Channels
  - ✅ Send Messages
  - ✅ Read Message History
  - ✅ Manage Messages
  - ✅ Create Public Threads
- Copiar la URL generada y abrirla en el navegador
- Seleccionar tu servidor → **"Authorize"**

### 7. Dar acceso al bot al Forum Channel
- En Discord, ir al Forum Channel donde se publican las ofertas
- Click derecho en el canal → **"Editar Canal"** → **"Permisos"**
- Agregar el bot y darle permisos:
  - ✅ Ver Canal
  - ✅ Leer Historial de Mensajes
  - ✅ Enviar Mensajes
  - ✅ Crear Threads Públicos
  - ✅ Gestionar Mensajes

---

## Obtener el Channel ID

1. En Discord, ir a **Ajustes de Usuario → Avanzado** → activar **Modo Desarrollador**
2. Click derecho en el **Forum Channel** → **"Copiar ID"**
3. Este es tu `DISCORD_CHANNEL_ID`

---

## Archivo .env de ejemplo

Crear un archivo `.env` en el directorio de trabajo:

```env
DISCORD_BOT_TOKEN=tu_token_aqui
DISCORD_CHANNEL_ID=123456789012345678
```

**Nota:** `DISCORD_WEBHOOK_URL` ya no es necesaria.

---

## Verificar que todo funciona

Podés pedirle a Claude que haga una prueba de conectividad:
> "Probá la conexión con Discord y decime cuántos threads hay en el foro"

Claude hará un GET a la API y reportará si las credenciales son válidas.