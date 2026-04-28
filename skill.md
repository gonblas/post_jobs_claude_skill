---
name: discord-jobs
description: >
  Skill para gestionar un feed de ofertas laborales en un canal Foro de Discord.
  Úsala siempre que el usuario quiera publicar trabajos en Discord, scrapeear portales
  de empleo, actualizar posts existentes, chequear duplicados en el feed, o mantener
  un canal de Discord con ofertas de trabajo. También se activa cuando el usuario
  menciona portales de trabajo (LinkedIn, GetOnBoard, Bumeran, Computrabajo, etc.)
  junto con Discord, o cuando quiere extraer y postear ofertas laborales en cualquier
  formato. Triggerea incluso si el usuario solo dice "agregar trabajos al feed",
  "revisar el foro de Discord", "hay nuevas ofertas para publicar" o frases similares.
---

# Discord Jobs Skill

Skill para scrapeear portales de empleo y publicar/actualizar ofertas en un Forum Channel de Discord, evitando duplicados.

## Configuración requerida

Antes de operar, verificar que exista esta variable. Si falta, dirigir al usuario a `references/setup-discord-bot.md`.

```
DISCORD_BOT_TOKEN=     # Token del Bot (para leer threads y publicar nuevos posts)
DISCORD_CHANNEL_ID=    # ID del Forum Channel donde se publican las ofertas
```

**Dónde guardarlas:** en un archivo `.env` en el directorio de trabajo, o como variables de entorno del sistema.

---

## Arquitectura

El skill usa un **único Bot Token** para todas las operaciones:

| Operación | Endpoint | Credencial |
|-----------|----------|------------|
| Leer threads activos/archivados | `/guilds/{id}/threads/active` | `DISCORD_BOT_TOKEN` |
| Publicar nuevos threads | `/channels/{id}/threads` | `DISCORD_BOT_TOKEN` |
| Editar mensajes existentes | `/channels/{id}/messages/{msg_id}` | `DISCORD_BOT_TOKEN` |

---

## Flujo principal

### 1. Cargar portales de trabajo
Leer el archivo `references/portales.md` que contiene los links y contexto de cada portal configurado.

### 2. Scrapeear ofertas
Para cada portal listado, usar `web_fetch` para obtener el contenido y extraer las ofertas disponibles.

Extraer de cada oferta:
- **Posición / título del puesto** → limpiar para el título del thread (ver "Títulos de Thread" más abajo)
- Empresa
- Estado (Activo / Expirado / Desconocido)
- Fecha de publicación
- Fecha de expiración (si aplica)
- Fuente (nombre del portal + URL)
- Industria / Rubro
- Modalidad (Remoto / Presencial / Híbrido)
- Seniority (Jr / Semi Sr / Sr / Lead / No especificado)
- Stack Tecnológico
- Link directo a la oferta
- Contacto (email, LinkedIn, formulario, etc.)
- Ubicación
- Información Extra (beneficios, descripción breve, etc.)

### 3. Leer posts existentes en Discord
Usar el Bot Token para consultar la API de Discord y obtener todos los threads activos del **guild** (servidor).

```
GET https://discord.com/api/v10/guilds/{GUILD_ID}/threads/active
Authorization: Bot {DISCORD_BOT_TOKEN}
```

Luego filtrar los threads por `parent_id` para obtener solo los del Forum Channel especificado por `DISCORD_CHANNEL_ID`.

**Nota:** Para obtener el `GUILD_ID`, hacer un GET a `/channels/{DISCORD_CHANNEL_ID}` y extraer el campo `guild_id`.

También buscar threads archivados:
```
GET https://discord.com/api/v10/channels/{DISCORD_CHANNEL_ID}/threads/archived/public
Authorization: Bot {DISCORD_BOT_TOKEN}
```

Extraer de cada thread su nombre (título) y los primeros mensajes para identificar el link de la oferta.

### 4. Filtrar por ubicación (Argentina)
Antes de detectar duplicados, filtrar las ofertas por ubicación:

**Criterios de aceptación:**
- ✅ **Argentina** (cualquier modalidad: remoto, presencial, híbrido)
- ✅ **Remoto** (full remoto desde cualquier país)
- ❌ **Híbrido/Presencial en otro país** → descartar (ej: "Santiago, Chile", "Bogotá, Colombia")

**Regla:** Solo publicar si la oferta es para Argentina o es full remoto. Si es híbrido/presencial, debe ser en Argentina.

### 5. Detectar duplicados
Comparar las ofertas scrapeadas contra los posts existentes usando:
- El **link directo** a la oferta (match exacto)
- El **título + empresa** (match fuzzy, ignorar mayúsculas/minúsculas y espacios extra)

Clasificar cada oferta en:
- `NUEVA` → no existe en el feed → **publicar**
- `EXISTENTE` → ya está en el feed → **saltar** (o actualizar si cambió)
- `ACTUALIZAR` → existe pero hay datos nuevos o cambió el estado

### 5. Publicar ofertas nuevas
Para cada oferta `NUEVA`, crear un thread en el Forum Channel usando el **Bot Token**:

```
POST https://discord.com/api/v10/channels/{DISCORD_CHANNEL_ID}/threads
Content-Type: application/json
Authorization: Bot {DISCORD_BOT_TOKEN}

{
  "name": "{Posición}",
  "auto_archive_duration": 10080,
  "message": {
    "content": "<contenido formateado>"
  }
}
```

El `name` debe ser solo el rol, sin seniority (ver sección "Títulos de Thread").

### 7. Actualizar ofertas existentes
Para ofertas `ACTUALIZAR`, editar el mensaje original del thread:
```
PATCH https://discord.com/api/v10/channels/{thread_id}/messages/{message_id}
Authorization: Bot {DISCORD_BOT_TOKEN}
Content-Type: application/json

{ "content": "<contenido actualizado>" }
```

### 8. Reportar resultados
Al finalizar, mostrar un resumen:
```
✅ Publicadas: X nuevas ofertas
🔄 Actualizadas: X ofertas
⏭️ Saltadas (duplicadas): X ofertas
❌ Errores: X (con detalle)
```

---

## Formato de Post

Cada thread debe tener este contenido **exactamente**:

```
**Posición:** {valor o "No especificado"}
**Empresa:** {valor o "No especificado"}
**Estado:** {Activo | Expirado | No especificado}
**Fecha de publicación:** {DD/MM/YYYY o "No especificado"}
**Fecha de expiración:** {DD/MM/YYYY o "No especificado"}
**Fuente:** {Nombre del portal} — {URL}
**Industria / Rubro:** {valor o "No especificado"}
**Modalidad:** {Remoto | Presencial | Híbrido | No especificado}
**Seniority:** {Jr | Semi Sr | Sr | Lead | No especificado}
**Stack Tecnológico:** {tecnologías separadas por comas, o "No especificado"}
**Link:** {URL directa a la oferta}
**Contacto:** {email / LinkedIn / formulario / "No especificado"}
**Ubicación:** {ciudad, país o "Remoto" o "No especificado"}
**Información Extra:** {descripción breve o beneficios destacados, o "—"}
```

> **Nota:** Cada campo va en su propia línea, incluyendo `Modalidad` y `Seniority`.

## Títulos de Thread

El título del thread sigue el formato: `{Posición} | {Seniority} | {Modalidad}`

**Reglas:**
- Solo incluir los campos que están **completos/especificados** (ignorar "No especificado")
- La **Posición** va sin prefijos de seniority (ver ejemplos abajo)
- Separar cada campo con ` | ` (espacio-pipe-espacio)

**Ejemplos:**
| Datos disponibles | Título del thread |
|-------------------|-------------------|
| Posición: Full-Stack Engineer, Seniority: Senior, Modalidad: Remoto | `Full-Stack Engineer | Senior | Remoto` |
| Posición: Backend Developer, Seniority: Jr, Modalidad: Presencial | `Backend Developer | Jr | Presencial` |
| Posición: Product Manager, Seniority: Lead, Modalidad: No especificado | `Product Manager | Lead` |
| Posición: Data Scientist, Seniority: No especificado, Modalidad: Remoto | `Data Scientist | Remoto` |
| Posición: DevOps Engineer, Seniority: No especificado, Modalidad: No especificado | `DevOps Engineer` |

**Prefijos a eliminar de la Posición:**
- Seniority: `Junior`, `Jr`, `Semi-Sr`, `Senior`, `Sr`, `Lead`, `Head of`, `Principal`, `Staff`
- Otros: `About`, `Apply for`, `Vacancy for`, etc.

**Regla:** Mantener solo el rol base. Si el título es ambiguo, usar el más descriptivo sin seniority.

---

## Manejo de errores comunes

| Error | Causa probable | Acción |
|-------|---------------|--------|
| 401 Unauthorized | Bot Token inválido o expirado | Pedir al usuario que regenere el token |
| 403 Forbidden | El bot no tiene permisos en el canal | Ver `references/setup-discord-bot.md` → sección Permisos |
| 404 Not Found | Channel ID incorrecto | Verificar que el ID corresponda al Forum Channel |
| 429 Too Many Requests | Rate limit de Discord | Esperar el tiempo indicado en `retry_after` y reintentar |
| 400 Bad Request | `name` vacío o muy largo (>100 chars) | Truncar el título a 100 caracteres |

---

## Referencias

- `references/setup-discord-bot.md` → Cómo crear el bot, obtener el token y configurar permisos
- `references/portales.md` → Lista de portales de empleo a scrapeear (editable por el usuario)
