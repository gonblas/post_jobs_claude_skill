# Portales de Trabajo a Scrapeear

Este archivo define los portales de empleo que Claude va a revisar al buscar nuevas ofertas.
**Editalo libremente** — agregá o eliminá portales según tus necesidades.

---

## Portales activos

### GetOnBoard
- **URL base:** https://www.getonbrd.com/jobs
- **Filtros sugeridos:** `/jobs?tag=programacion` o `/jobs?category=tech`
- **Notas:** Muy bueno para roles tech en Latinoamérica. Las ofertas incluyen stack y modalidad.

### Bumeran Argentina
- **URL base:** https://www.bumeran.com.ar/empleos.html
- **Filtros sugeridos:** `?busqueda=desarrollador` o `?busqueda=programador`
- **Notas:** Portales generales, mayor volumen. Filtrar por categoría "Sistemas y Tecnología".

### Computrabajo Argentina
- **URL base:** https://www.computrabajo.com.ar/ofertas-de-trabajo/
- **Filtros sugeridos:** `/ofertas-de-trabajo/trabajo-de-programador`
- **Notas:** Similar a Bumeran. Bueno para roles semi-sr y sr en empresas locales.

### LinkedIn Jobs
- **URL base:** https://www.linkedin.com/jobs/search/
- **Filtros sugeridos:** `?keywords=developer&location=Argentina&f_WT=2` (remoto)
- **Notas:** ⚠️ LinkedIn bloquea scrapers frecuentemente. Puede requerir reintento o uso manual.

---

## Cómo agregar un nuevo portal

Copiá este bloque y completalo:

```
### Nombre del Portal
- **URL base:** https://...
- **Filtros sugeridos:** (parámetros de búsqueda opcionales)
- **Notas:** (comportamiento especial, autenticación requerida, etc.)
```

---

## Portales desactivados temporalmente

_(Mover acá portales que querés pausar sin borrar)_

---

## Notas generales de scraping

- Si un portal requiere JavaScript para renderizar (ej: React SPA), `web_fetch` puede no obtener todas las ofertas. En ese caso, el usuario puede pegar el HTML manualmente.
- Respetar los `robots.txt` de cada sitio.
- Si hay rate limiting, esperar y reintentar con las ofertas que fallaron.