# Portal de Reportes PROTEGE

## Estructura

```
/mnt/user-data/outputs/
├── index.html              ← Selector principal (municipios)
├── el_alto.html            ← Selector de instrumentos (El Alto)
├── trinidad.html           ← Selector de instrumentos (Trinidad)
├── el_alto_1b.html         ← Reporte completo Pilar 1b · El Alto
└── trinidad_1b.html        ← Reporte completo Pilar 1b · Trinidad
```

Convención de nombres: `<slug_municipio>_<id_instrumento>.html`

## Identidad visual

Cada página incluye:
- **Site header** con escudo "PROTEGE" + wordmark "unicef" (azul cyan)
- **Bandera oficial** del municipio en el hero (SVG inline, sin imágenes externas)
- **Footer institucional** con identidad PROTEGE × UNICEF Bolivia

Las banderas están dibujadas en SVG según las descripciones oficiales:
- **El Alto** (Ord. Mun. 016/89): franja verde olivo con triángulo rojo izquierdo en forma de lanza
- **Trinidad**: fondo verde oscuro, franja roja izquierda con cruz dorada con cuernos en la esquina superior

## Reemplazar el wordmark "unicef" con el logo oficial

El wordmark actual es un placeholder tipográfico. Para usar el logo oficial:

1. Edita `/home/claude/svg_assets.py`
2. Reemplaza la variable `UNICEF_WORDMARK` con el SVG oficial
3. Corre `python3 build_portal.py && python3 render_html.py`

Si tienes el logo en PNG, reemplaza por:
```python
UNICEF_WORDMARK = '<img src="logo_unicef.png" style="height:22px" alt="UNICEF">'
```
y coloca `logo_unicef.png` junto a los HTMLs en outputs/.

## Cómo agregar un municipio nuevo

### 1. Generar el reporte

En `/home/claude/render_html.py`, agrega el municipio al diccionario `PALETTES`:
```python
"Cobija": {
    "slug": "cobija", "depto": "Pando",
    "primary": "#2C7873", "secondary": "#6FB98F", "accent": "#A4C2A8",
    "hero_grad": "linear-gradient(135deg,#1A4A47 0%,#2C7873 55%,#3D9E8F 100%)",
}
```
Y agrégalo al loop `for muni in [...]`. Corre:
```bash
cd /home/claude && python3 build_data.py && python3 render_html.py
```

### 2. Añadir bandera (opcional)

En `/home/claude/svg_assets.py`, añade la bandera al diccionario `FLAGS`.
Si no agregas, la card se verá sin imagen.

### 3. Registrar en el portal

Edita `/home/claude/portal_config.json` con la nueva entrada en `municipios`.

### 4. Regenerar
```bash
cd /home/claude && python3 build_portal.py
```

## Agregar un instrumento nuevo (1a, 1, 2, 3) a un municipio existente

1. Generar el HTML del reporte (ej. `el_alto_1a.html`) en `outputs/`
2. En `portal_config.json`, añadir la entrada al municipio:
```json
"instrumentos_disponibles": {
  "1b": { ... },
  "1a": { "file": "el_alto_1a.html", "n_respondentes": 12 }
}
```
3. `python3 build_portal.py`

La card pasará automáticamente de "Próximamente" a "Disponible".

## Editar nombres de instrumentos

En `portal_config.json` → `instrumentos_catalog`, cada uno tiene `nombre`,
`subtitle`, `descripcion`. Edita y corre `build_portal.py`.

## Despliegue

Todos los archivos son estáticos sin dependencias externas (Chart.js empotrado,
SVGs inline). Súbelos como una carpeta a:
- GitHub Pages
- Netlify (drag-and-drop)
- Cualquier servidor web (Apache, Nginx, S3)

El `index.html` sirve como landing page.
