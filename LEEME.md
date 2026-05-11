# Portal PROTEGE — Sistema Multi-Instrumento

## Estado actual

**5 municipios · 4 instrumentos · 10 reportes generados · 66 respondentes**

| Municipio   | Departamento | 1a · ONGs | 1b · Gob. | 2 · Comunidad | 3 · DNA |
|-------------|--------------|-----------|-----------|---------------|---------|
| El Alto     | La Paz       | ✓ n=5     | ✓ n=7     | —             | —       |
| Trinidad    | Beni         | ✓ n=2     | ✓ n=11    | ✓ n=4         | —       |
| Cobija      | Pando        | ✓ n=1     | ✓ n=14    | ✓ n=2         | —       |
| Concepción  | Santa Cruz   | —         | —         | —             | ✓ n=14  |
| Boyuibe     | Santa Cruz   | —         | —         | —             | ✓ n=6   |

## Estructura de archivos

```
/mnt/user-data/outputs/
├── index.html                 ← Selector principal (5 municipios)
├── el_alto.html               ← Selector de instrumentos por municipio
├── trinidad.html
├── cobija.html
├── concepcion.html
├── boyuibe.html
├── el_alto_1a.html            ← Reportes <slug>_<id_instrumento>.html
├── el_alto_1b.html
├── trinidad_1a.html
├── trinidad_1b.html
├── trinidad_2.html
├── cobija_1a.html
├── cobija_1b.html
├── cobija_2.html
├── concepcion_3.html
└── boyuibe_3.html
```

## Identidad visual

- **Header institucional** PROTEGE × UNICEF en todas las páginas
- **5 banderas oficiales** dibujadas en SVG:
  - **El Alto**: verde olivo + triángulo rojo (Ord. Mun. 016/89)
  - **Trinidad**: verde + franja roja con cruz dorada (Beni)
  - **Cobija**: 3 franjas — azul cielo + blanco + verde amazónico
  - **Concepción**: verde selva + franja amarilla con cruz misional jesuita (Chiquitanía)
  - **Boyuibe**: verde + amarillo + azul petróleo con 3 estrellas guaraníes (Cordillera)
- **Paletas distintivas** por municipio
- **5 iconos por instrumento** con colores propios (1a coral, 1b cyan, 1 morado, 2 rosa, 3 verde teal)
- **Footer institucional** con UNICEF Bolivia

## Pipeline de generación

```bash
cd /home/claude
python3 build_data_1b.py    # Excel 1b → JSON × 3 municipios
python3 build_data_1a.py    # Excel 1a → JSON × 3 municipios
python3 build_data_2.py     # Excel 2  → JSON × 2 municipios
python3 build_data_3.py     # Excel 3  → JSON × 2 municipios
python3 render_unified.py   # JSONs → HTML reports (auto-discovery)
python3 build_portal.py     # Build index + selectores
```

El renderer y portal builder **autodescubren** los archivos `data_*.json` disponibles, así que no hay que mantener listas manuales.

## Análisis temático

**0% de respuestas en "Otros"** sobre 539 respuestas abiertas categorizadas. Cada instrumento tiene su propio diccionario `THEMES` con keywords específicos:

- **1a/1b** comparten 13 categorías temáticas (resultados, desafíos, fortalezas, etc.)
- **2** tiene 7 categorías propias (selección de líderes, espacios seguros, etc.)
- **3** (DNA) tiene 11 categorías específicas (tipologías de casos, dificultades operativas, etc.)

La función `theme_classify` en `pipeline_common.py` normaliza acentos y ñ automáticamente, así que los keywords se pueden escribir sin tilde.

## Cómo agregar un nuevo municipio

1. **Bandera SVG** en `/home/claude/svg_assets.py` → diccionario `FLAGS`
2. **Paleta** en `/home/claude/build_data_1b.py` → diccionario `PALETTES`
3. **Entrada** en `/home/claude/portal_config.json` → diccionario `municipios`
4. Volver a correr todo el pipeline. El portal se regenera automáticamente.

## Cómo agregar un nuevo instrumento al sistema

Crear `build_data_<id>.py` siguiendo el patrón de los existentes:
- Importar `pipeline_common` y `PALETTES` de `build_data_1b`
- Definir `INSTRUMENT_META`, `SECTIONS`, `THEMES`
- Función `build_municipio(muni)` que produce el JSON
- Generar `data_<slug>_<id>.json`

Añadir el catálogo del instrumento en `portal_config.json` y volver a correr `render_unified.py` + `build_portal.py`.

## Despliegue

Todos los archivos son estáticos y autocontenidos (Chart.js empotrado, SVGs inline). Para desplegar:
- GitHub Pages (push de la carpeta `outputs/`)
- Netlify (drag-and-drop)
- Cualquier servidor web

`index.html` es el punto de entrada.
