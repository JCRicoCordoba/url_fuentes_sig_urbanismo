# Fuentes de Datos · Diagnóstico Urbanístico

**Cuadro de mandos HTML autocontenido con 367 fuentes temáticas y 140 endpoints OGC listos para QGIS, y trabajos de planeamiento urbanístico.**

![Versión](https://img.shields.io/badge/versión-v04-orange)
![Licencia](https://img.shields.io/badge/licencia-uso%20interno-lightgrey)
![Tecnología](https://img.shields.io/badge/tecnología-HTML%20%2B%20JS%20vanilla-blue)
![QGIS](https://img.shields.io/badge/QGIS-3.x-green)
![Ámbito](https://img.shields.io/badge/ámbito-Andalucía%20%2F%20España-yellow)

---

## La idea: un punto de acceso único al laberinto de fuentes de planeamiento

Elaborar el diagnóstico de un Plan exige consultar decenas de portales, servicios y visores: el IGN para cartografía base, el INE para demografía, la REDIAM para medio ambiente, el SIGUA para planeamiento vigente, el Catastro para el parque edificatorio, la DGT para tráfico, Copernicus para cobertura del suelo… y así hasta superar las 500 fuentes por expediente.

El problema concreto: abrir cada portal desde cero en cada proyecto nuevo consume tiempo y dispersa el conocimiento acumulado. Un técnico junior no sabe qué tabla del Padrón sirve para secciones censales (`T68535`) ni la URL exacta del centro de descargas de la REDIAM. Un técnico senior la recuerda, pero no la documenta.

`FUENTES_LINKS.html` resuelve esto con un único fichero HTML que:

1. Indexa todas las fuentes del expediente en un catálogo navegable con 21 secciones temáticas.
2. Permite copiar endpoints OGC (WMS/WFS/XYZ/VT) con un clic para pegarlos directamente en QGIS.
3. Se actualiza sin tocar código: el formulario integrado añade fuentes nuevas y el botón "Guardar cambios" regenera el propio fichero HTML con los datos actualizados.

---

## Características

- **Dos catálogos en uno** — pestaña *Temáticas* (fuentes web por temática) y pestaña *OGC · QGIS* (endpoints de servicios cartográficos listos para conectar).
- **Búsqueda instantánea** — filtra nombre, descripción, organismo y URL al teclear; ignora tildes; atajo de teclado `/`.
- **Filtros por tipo de recurso** — chips que aíslan `WMS`, `WFS`, `XYZ`, `VT`, `WEB`, `RUTA`, `REF` con un solo clic.
- **Índice lateral jerárquico** — 21 secciones desplegables; el ítem activo se resalta al hacer scroll.
- **Botón Copiar URL** — copia el endpoint al portapapeles con retroalimentación visual; imprescindible para OGC.
- **Formulario de alta sin código** — valida duplicados por URL normalizada antes de insertar; soporta nuevas secciones y subsecciones sobre la marcha.
- **Guardado autoregenerativo** — "Guardar cambios" reescribe el propio `.html` con los datos nuevos embebidos; compatible con File System Access API (Chrome/Edge) y descarga de copia en otros navegadores.
- **Tema claro/oscuro** — paleta QGIS Style Guide v1.0; preferencia `prefers-reduced-motion` respetada.
- **Sin dependencias externas en runtime** — funciona offline salvo las fuentes tipográficas de Google Fonts.

---

## Instalación

No requiere instalación ni servidor. El fichero es autocontenido.

1. Descarga `FUENTES_LINKS.html` (o el nombre de versión correspondiente, p.ej. `260612_FUENTES_LINKS_v04.html`).
2. Ábrelo en navegador recomendado para la función de guardado nativo con File System Access API.
3. Listo.

**Requisitos previos:**

| Requisito | Versión mínima | Nota |
|-----------|---------------|------|
| Navegador | Chrome 86+ / Edge 86+ | Para guardado nativo; Firefox funciona con descarga de copia |
| QGIS | 3.x | Para conectar los endpoints OGC catalogados |
| Conexión a Internet | — | Solo para cargar tipografías y acceder a las fuentes enlazadas |

---

## Uso

### Flujo principal

| Paso | Qué hace |
|------|----------|
| **1 — Navegar** | Selecciona la pestaña *Temáticas* u *OGC · QGIS* según el tipo de recurso que necesitas. |
| **2 — Buscar** | Pulsa `/` o haz clic en la barra de búsqueda y escribe: organismo, temática, URL o palabra clave. |
| **3 — Filtrar** | Activa un chip de tipo (`WMS`, `WFS`, `WEB`…) para acotar los resultados. |
| **4 — Copiar** | En servicios OGC, pulsa **Copiar** junto a la URL y pégala en QGIS (*Capa → Añadir capa → WMS/WMTS o WFS → Nueva conexión*). Para teselas XYZ/VT, usa el panel *Navegador*. |
| **5 — Añadir** | Pulsa **＋ Añadir fuente**, rellena el formulario (el sistema avisa si la URL ya existe) y confirma. |
| **6 — Guardar** | Pulsa **💾 Guardar cambios** para fijar las altas en el fichero; el navegador lo sobrescribe (Chrome/Edge) o descarga una copia actualizada. |

### Tipos de marcas en las filas

| Marca | Significado |
|-------|-------------|
| `NUEVA` | Incorporada en la última revisión del catálogo |
| `REC` | Recuperada del fichero fuente original |
| `SIG` | Recurso cartográfico directo (capa o servicio) |
| ⚠ **esfuerzo alto** | Clasificada como de alta carga de gestión en la Matriz de Datos del Plan |

### Generador de municipios

La pestaña *Ayuda* incluye un selector de los 77 municipios de la provincia de Córdoba que construye automáticamente la URL de búsqueda en el **Catálogo Artístico y Monumental** de la Diputación de Córdoba y permite abrirla o copiarla directamente.

---

## Arquitectura

El fichero es un documento HTML único sin build step ni dependencias de servidor.

```
FUENTES_LINKS.html
│
├── <style>          Hoja de estilos embebida (paleta QGIS, tema claro/oscuro,
│                    layout responsive de 2 columnas, componentes: tabs, chips,
│                    tabla de fuentes, modal, toast, índice lateral)
│
├── <body>
│   ├── .hero        Cabecera: título, contador de fuentes, botón de tema
│   ├── .toolbar     Barra pegajosa: tabs, búsqueda, filtros por tipo, botones
│   ├── .indice      Índice lateral jerárquico (generado por JS, sticky)
│   ├── #main        Área principal de resultados (paneles por sección)
│   ├── .ayuda       Pestaña de ayuda y generador de municipios
│   └── .modal       Formulario de alta de fuentes (dialog overlay)
│
└── <script>         Lógica JS vanilla (sin frameworks)
    ├── TEMATICAS[]  Array JSON: 367 fuentes web por temática del Planeamiento
    ├── OGC[]        Array JSON: 140 endpoints de servicios cartográficos
    ├── MUNIS[]      Array: 77 municipios de Córdoba con parámetro fv
    ├── render()     Genera el DOM de paneles y filas a partir de los arrays
    ├── renderFiltros() Construye los chips de tipo dinámicamente
    ├── observa()    IntersectionObserver para resaltar sección activa en índice
    └── Guardar      Serializa document.documentElement y escribe el fichero
                     actualizado vía File System Access API o descarga Blob
```

### Estructura de un registro de datos

```json
{
  "cat":  "tema",
  "sec":  "3. Estadística. Demografía y Socioeconómico",
  "sub":  "3.1 Fuentes estatales — INE",
  "n":    "Padrón: secciones censales (T68535)",
  "tipo": "WEB",
  "url":  "https://www.ine.es/jaxiT3/Tabla.htm?t=68535",
  "d":    "Tabla del Padrón con población desagregada por sección censal.",
  "m":    ["sig"]
}
```

Campos `cat`: `"tema"` o `"ogc"` · `tipo`: `WEB`, `RUTA`, `REF` / `WMS`, `WFS`, `XYZ`, `VT` · `m`: marcas opcionales.

---

## Secciones temáticas del catálogo

Las 21 secciones del catálogo siguen el índice de un diagnóstico de Planeamiento:

```
 0. Servicios API-REST / Open Data
 1. Marco Normativo y Planeamiento
 2. Datos Abiertos y Portales Generales
 3. Estadística. Demografía y Socioeconómico
 4. Ordenación Territorial y Encuadre
 5. Parque Edificatorio y Suelo Urbano
 6. Dotaciones Locales y Estándares
 7. Infraestructuras Urbanas Básicas
 8. Movilidad Urbana
 9. Calidad Ambiental y Confort Térmico
10. Paisaje Urbano
11. Patrimonio Cultural
12. Vulnerabilidad Urbana y Mercado Residencial
13. Afecciones Sectoriales: Infraestructuras Lineales
14. Afecciones Sectoriales: Aeronáuticas y Energéticas
15. Dominio Público Hidráulico y Riesgos Hídricos
16. Riesgos Naturales
17. Medio Natural. Red Natura, RENPA y Vías Pecuarias
18. Planes Sectoriales con Incidencia Territorial
19. Telecomunicaciones y Sociedad Digital
20. Salud
```

---

## Editar los datos directamente en el fichero

Si prefieres editar sin la interfaz gráfica, los dos bloques de datos están marcados en el `<script>` con los comentarios:

```
/* ▼▼▼ AÑADIR AQUÍ NUEVAS FUENTES TEMÁTICAS */
/* ▼▼▼ AÑADIR AQUÍ NUEVOS SERVICIOS OGC     */
```

Inserta un objeto JSON por línea, con coma al final, antes del comentario marcador. Los ficheros fuente de referencia son:

```
260612_FUENTES_LINKS.md     ← fuente maestra de fuentes temáticas
260612_OGC_Fuentes.md       ← fuente maestra de servicios OGC
```

Anota siempre los cambios en los `.md` además de en el `.html`.

---

## Historia / Versiones

| Versión | Fecha | Cambios principales |
|---------|-------|---------------------|
| v04 | 2026-06-12 | Secciones 19 (Telecomunicaciones) y 20 (Salud); secciones 8 y 12 ampliadas; generador de municipios en Ayuda; guardado con File System Access API |
| v03 | — | Incorporación de servicios OGC como catálogo independiente; filtros por tipo |
| v02 | — | Tema oscuro; índice lateral con IntersectionObserver; formulario de alta |
| v01 | — | Primera versión: catálogo temático estático con búsqueda |

---

## Licencia

Uso interno — [Rico&co] · CITYLAB 360, SCA.

El fichero puede redistribuirse y adaptarse libremente dentro del equipo técnico. Para uso externo, contactar con el autor.

---

## Autor / Contacto

**José Carlos Rico Córdoba**  
[Rico&co] · [CITYLAB 360, S.C.A.](https://citylab360.es)

Fuentes compiladas para los trabajos de diagnóstico y planeamiento en el ámbito de la provincia de Córdoba (Andalucía, España).
