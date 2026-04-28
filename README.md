# Discord Jobs Skill

Skill para Claude Code que scrapea portales de empleo y publica ofertas automáticamente en un canal de Discord (Forum Channel).

## Características

- Scrapeo automático de portales de empleo (GetOnBoard, Bumeran, Computrabajo, LinkedIn, etc.)
- Publicación automática en Discord Forum Channel
- Detección de duplicados por link y título
- Filtrado por ubicación (Argentina y Remoto)
- Formato consistente y profesional para las ofertas
- Actualización de ofertas existentes

## Instalación

### Paso 1: Clonar el repositorio

```bash
# Opción A: Clonar en la carpeta de skills de Claude Code
git clone https://github.com/TU_USUARIO/discord-jobs-skill.git ~/.claude/skills/discord-jobs

# Opción B: Clonar en cualquier lugar y copiar manualmente
git clone https://github.com/TU_USUARIO/discord-jobs-skill.git
cp -r discord-jobs-skill ~/.claude/skills/discord-jobs
```

### Paso 2: Configurar el Bot de Discord

Seguí la guía completa en [`references/setup-discord-bot.md`](references/setup-discord-bot.md)

**Resumen rápido:**

1. Crear un bot en [Discord Developer Portal](https://discord.com/developers/applications)
2. Copiar el **Bot Token**
3. Invitar el bot a tu servidor con permisos para:
   - Ver canales
   - Leer historial de mensajes
   - Enviar mensajes
   - Crear threads públicos
   - Gestionar mensajes
4. Obtener el **Channel ID** de tu Forum Channel (Modo Desarrollador → Click derecho → Copiar ID)

### Paso 3: Crear el archivo `.env`

Copiá el archivo de ejemplo y completá con tus credenciales:

```bash
cp .env.example .env
```

Editá `.env` con tus valores:

```env
DISCORD_BOT_TOKEN=MTQ5NTkwNDMwMjAwMTE2NDMwMA.GjAEU0.tu_token_aqui
DISCORD_CHANNEL_ID=123456787654321098
```

### Paso 4: Verificar la instalación

```bash
ls ~/.claude/skills/discord-jobs/
# Deberías ver: skill.md, references/, .env
```

## Uso

Una vez instalado, el skill se activa automáticamente cuando mencionás:

- "Publicar trabajos en Discord"
- "Scrapear portales de empleo"
- "Hay nuevas ofertas para publicar"
- "Revisar el feed de Discord"
- Portales como "LinkedIn", "GetOnBoard", "Bumeran" junto con "Discord"

### Comando manual

```
/discord-jobs
```

O simplemente pedí:
> "Publicá las ofertas de trabajo en Discord"

## Configuración de portales

El archivo [`references/portales.md`](references/portales.md) contiene la lista de portales a scrapear. Podés editarlo libremente para agregar o quitar portales según tus necesidades.

### Portales incluidos por defecto

| Portal | URL | Notas |
|--------|-----|-------|
| GetOnBoard | https://www.getonbrd.com/jobs | Tech en Latinoamérica |
| Bumeran Argentina | https://www.bumeran.com.ar | Volumen alto |
| Computrabajo | https://www.computrabajo.com.ar | Roles semi-sr y sr |
| LinkedIn Jobs | https://www.linkedin.com/jobs | Puede requerir reintentos |

## Formato de publicación

### Título del thread
```
{Posición} | {Seniority} | {Modalidad}
```
Ejemplo: `Senior Full-Stack Engineer | Senior | Remoto`

### Cuerpo del mensaje
```
**Posición:** {valor}
**Empresa:** {valor}
**Estado:** {Activo | Expirado}
**Fecha de publicación:** {DD/MM/YYYY}
**Fecha de expiración:** {DD/MM/YYYY}
**Fuente:** {Portal} — {URL}
**Industria / Rubro:** {valor}
**Modalidad:** {Remoto | Presencial | Híbrido}
**Seniority:** {Jr | Semi Sr | Sr | Lead}
**Stack Tecnológico:** {tecnologías}
**Link:** {URL}
**Contacto:** {email/LinkedIn}
**Ubicación:** {ciudad, país}
**Información Extra:** {descripción}
```

## Criterios de filtrado

Solo se publican ofertas que cumplen:
- ✅ Argentina (cualquier modalidad: remoto, presencial, híbrido)
- ✅ Remoto (full remoto desde cualquier país)
- ❌ Híbrido/Presencial en otro país → se descartan

## Troubleshooting

| Error | Causa | Solución |
|-------|-------|----------|
| 401 Unauthorized | Token inválido | Regenerar token en Discord Developer Portal |
| 403 Forbidden | Sin permisos | Verificar permisos del bot en el canal |
| 404 Not Found | Channel ID incorrecto | Verificar que el ID sea del Forum Channel |
| 429 Too Many Requests | Rate limit | Esperar y reintentar |

## Estructura del repositorio

```
discord-jobs-skill/
├── README.md                 # Este archivo
├── .env.example              # Template de variables de entorno
├── skill.md                  # Definición del skill
└── references/
    ├── setup-discord-bot.md  # Guía de configuración del bot
    └── portales.md           # Lista de portales a scrapear
```

## Seguridad

- **Nunca compartas tu `.env`** — contiene credenciales sensibles
- El archivo `.env` está en `.gitignore` por defecto
- Regenerá el token si lo exponés accidentalmente

## Contribuciones

Sentite libre de:
- Agregar nuevos portales a `references/portales.md`
- Mejorar el formato de publicación
- Reportar bugs o sugerencias

## Licencia

MIT
