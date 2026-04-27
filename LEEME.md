# pagespeed-monitor-tool

![pagespeed-monitor-tool workflow](https://github.com/ralcarazm/pagespeed-monitor-tool/blob/main/pagespeed-monitor-tool.png)

**pagespeed-monitor-tool** es una herramienta de línea de comandos desarrollada en Python para auditar una o más URLs con la API de Google PageSpeed Insights. Recupera datos de laboratorio de Lighthouse, puntuaciones por categoría de PageSpeed y, cuando están disponibles, datos de campo procedentes de Chrome User Experience Report (CrUX).

La herramienta está pensada para análisis por lotes, tareas de monitorización y flujos de trabajo de investigación. Puede exportar resultados a CSV y XLSX, escribir logs de ejecución, gestionar errores temporales de red o de la API mediante reintentos y conservar resultados parciales mientras el proceso está en ejecución.

## Autor

**Rubén Alcaraz Martínez**

## Licencia

Este proyecto se distribuye bajo la licencia **GNU General Public License v3.0**.

---

## Estructura recomendada del proyecto

```text
pagespeed-monitor-tool/
├── pagespeed_audit.py
├── config.toml
├── requirements.txt
├── README.md
├── LEEME.md
├── INICIO_RAPIDO.md
├── LICENSE
├── results/
└── logs/
```

Las carpetas `results/` y `logs/` pueden crearse manualmente o automáticamente por el script cuando sean necesarias.

---

## Funcionalidades principales

- Analizar una o más URLs mediante la API de Google PageSpeed Insights.
- Ejecutar auditorías para `mobile`, `desktop` o ambas estrategias.
- Recuperar puntuaciones de categorías de Lighthouse:
  - Performance / Rendimiento
  - Accessibility / Accesibilidad
  - Best Practices / Buenas prácticas
  - SEO
- Recuperar métricas de laboratorio de Lighthouse:
  - First Contentful Paint
  - Largest Contentful Paint
  - Speed Index
  - Total Blocking Time
  - Cumulative Layout Shift
  - Time to Interactive
- Recuperar métricas de campo de CrUX cuando están disponibles:
  - Largest Contentful Paint
  - Cumulative Layout Shift
  - Interaction to Next Paint
  - First Contentful Paint
  - Time to First Byte
- Exportar resultados a:
  - CSV
  - XLSX
- Generar CSV compatibles con Excel mediante:
  - UTF-8 con BOM
  - separador de punto y coma
  - coma como separador decimal
- Guardar filas de CSV de manera progresiva después de cada análisis URL/estrategia.
- Generar el XLSX al final del proceso.
- Añadir valores de estado controlados para datos no disponibles y errores.
- Generar logs de ejecución en una carpeta `logs/`.
- Reintentar errores temporales de red o de la API.

---

## Requisitos

Recomendado:

- Python 3.10 o superior
- Una clave de API de Google PageSpeed Insights
- Conexión a Internet
- Paquetes de Python indicados en `requirements.txt`

El proyecto utiliza:

```txt
requests>=2.31.0
openpyxl>=3.1.2
tomli>=2.0.1; python_version < "3.11"
```

`tomli` solo es necesario en versiones de Python anteriores a la 3.11. Python 3.11 y versiones posteriores ya incluyen `tomllib` en la biblioteca estándar.

---

## Obtener una clave de API de PageSpeed Insights

Necesitas una clave de API de Google Cloud si quieres realizar peticiones repetidas o automatizadas.

Proceso general:

1. Abrir Google Cloud Console.
2. Crear o seleccionar un proyecto.
3. Activar la API de PageSpeed Insights.
4. Crear una clave de API.
5. Restringir la clave si procede.

Mantén la clave de API en privado y escríbela únicamente en tu fichero local `config.toml`.

---

## Instalación

### 1. Descargar el proyecto

Descarga o copia los ficheros del proyecto en tu ordenador y abre una terminal dentro de la carpeta del proyecto.

La carpeta del proyecto debe contener, como mínimo:

```text
pagespeed_audit.py
config.toml
requirements.txt
```

---

## Preparar el entorno virtual

### Linux

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
pip install -r requirements.txt
```

### macOS

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
pip install -r requirements.txt
```

Si `python3` no está disponible, comprueba tu instalación de Python:

```bash
python --version
python3 --version
```

### Windows PowerShell

```powershell
py -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
```

Si PowerShell bloquea la ejecución de scripts, ejecuta:

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

Después, activa de nuevo el entorno:

```powershell
.\.venv\Scripts\Activate.ps1
```

### Windows Símbolo del sistema

```cmd
py -m venv .venv
.venv\Scripts\activate.bat
python -m pip install --upgrade pip
pip install -r requirements.txt
```

---

## `requirements.txt`

Crea un fichero llamado `requirements.txt` con este contenido:

```txt
requests>=2.31.0
openpyxl>=3.1.2
tomli>=2.0.1; python_version < "3.11"
```

Instálalo después de activar el entorno virtual:

```bash
pip install -r requirements.txt
```

---

## Fichero de configuración

La herramienta se configura mediante un fichero TOML. Por defecto, el script busca:

```text
config.toml
```

El fichero debe estar en la misma carpeta que `pagespeed_audit.py`, salvo que se indique explícitamente otra ruta mediante la opción `--config`.

---

## Ejemplo de `config.toml`

```toml
# ------------------------------------------------------------
# GOOGLE PAGESPEED INSIGHTS API
# ------------------------------------------------------------

# Clave de API de PageSpeed Insights.
api_key = "YOUR_API_KEY_HERE"


# ------------------------------------------------------------
# FICHEROS DE SALIDA
# ------------------------------------------------------------

# Fichero CSV de salida.
output = "results/pagespeed_results.csv"

# Fichero Excel de salida.
xlsx_output = "results/pagespeed_results.xlsx"

# Generar salida CSV.
write_csv = true

# Generar salida XLSX.
write_xlsx = true


# ------------------------------------------------------------
# CONFIGURACIÓN DEL CSV
# ------------------------------------------------------------

# Codificación del CSV.
# "utf-8-sig" es recomendable para abrir el fichero directamente en Excel.
csv_encoding = "utf-8-sig"

# Separador de columnas del CSV.
# El punto y coma es recomendable para configuraciones regionales españolas/catalanas.
csv_delimiter = ";"

# Convertir puntos decimales en comas decimales en el CSV.
csv_decimal_comma = true


# ------------------------------------------------------------
# VALORES DE VISUALIZACIÓN Y VALORES DE CONTROL
# ------------------------------------------------------------

# Exportar valores de visualización de Lighthouse como "5.1 s" o "120 ms".
# Para análisis cuantitativo se recomienda false.
include_display_values = false

# Rellenar campos textuales de control con valores como "sin_error",
# "not_available" o "not_evaluated".
fill_control_values = true

# Eliminar columnas completamente vacías de la salida final.
drop_empty_columns = false


# ------------------------------------------------------------
# LOGS
# ------------------------------------------------------------

# Activar la generación de ficheros de log.
log_enabled = true

# Carpeta donde se guardarán los logs.
log_dir = "logs"

# Nivel de detalle del log.
# Valores habituales: DEBUG, INFO, WARNING, ERROR.
log_level = "INFO"


# ------------------------------------------------------------
# ESTRATEGIAS Y CATEGORÍAS DE LIGHTHOUSE
# ------------------------------------------------------------

# Estrategias de análisis.
# Valores permitidos: "mobile", "desktop".
strategies = ["mobile", "desktop"]

# Categorías de Lighthouse.
# Valores permitidos:
# "performance", "accessibility", "best-practices", "seo".
categories = ["performance", "accessibility", "best-practices", "seo"]


# ------------------------------------------------------------
# IDIOMA Y RITMO DE PETICIONES
# ------------------------------------------------------------

# Idioma de los resultados.
locale = "es"

# Pausa entre peticiones a la API, en segundos.
sleep = 1.0

# Timeout de cada petición, en segundos.
timeout = 120


# ------------------------------------------------------------
# REINTENTOS
# ------------------------------------------------------------

# Número de reintentos tras errores temporales.
# Número total máximo de intentos = 1 intento inicial + retries.
retries = 3

# Factor de espera exponencial entre reintentos.
retry_backoff = 2.0

# Códigos HTTP que activan reintentos.
retry_status_codes = [408, 429, 500, 502, 503, 504]


# ------------------------------------------------------------
# URLS A ANALIZAR
# ------------------------------------------------------------

# Lista de URLs que se analizarán.
urls = [
  "https://www.example1.com",
  "https://www.example2.com",
  "https://www.example3.com"
]
```

---

## Opciones de configuración

| Opción | Tipo | Ejemplo | Descripción |
|---|---:|---|---|
| `api_key` | cadena | `"YOUR_API_KEY_HERE"` | Clave de API de Google PageSpeed Insights. |
| `output` | cadena | `"results/pagespeed_results.csv"` | Ruta del fichero CSV de salida. |
| `xlsx_output` | cadena | `"results/pagespeed_results.xlsx"` | Ruta del fichero Excel XLSX de salida. |
| `write_csv` | booleano | `true` | Activa o desactiva la generación del CSV. |
| `write_xlsx` | booleano | `true` | Activa o desactiva la generación del XLSX. |
| `csv_encoding` | cadena | `"utf-8-sig"` | Codificación utilizada para el CSV. `utf-8-sig` es recomendable para compatibilidad con Excel. |
| `csv_delimiter` | cadena | `";"` | Separador del CSV. El punto y coma es recomendable en configuraciones regionales españolas/catalanas. |
| `csv_decimal_comma` | booleano | `true` | Convierte puntos decimales en comas decimales en los valores numéricos del CSV. |
| `include_display_values` | booleano | `false` | Añade valores legibles de Lighthouse con unidades, como `"5.1 s"` o `"120 ms"`. Para análisis, se recomienda mantenerlo desactivado. |
| `fill_control_values` | booleano | `true` | Añade valores textuales controlados a columnas no numéricas de estado o mensaje cuando faltan datos. |
| `drop_empty_columns` | booleano | `false` | Elimina de la salida final CSV/XLSX las columnas completamente vacías. |
| `log_enabled` | booleano | `true` | Activa la creación de ficheros de log. |
| `log_dir` | cadena | `"logs"` | Carpeta donde se guardan los logs. |
| `log_level` | cadena | `"INFO"` | Nivel de logging. Valores habituales: `DEBUG`, `INFO`, `WARNING`, `ERROR`. |
| `strategies` | lista | `["mobile", "desktop"]` | Estrategias de dispositivo que se analizarán. Cada estrategia crea una petición API por URL. |
| `categories` | lista | `["performance", "accessibility", "best-practices", "seo"]` | Categorías de Lighthouse solicitadas a PageSpeed Insights. |
| `locale` | cadena | `"es"` | Idioma usado para los mensajes de respuesta de la API cuando estén disponibles. |
| `sleep` | número | `1.0` | Pausa entre peticiones a la API, en segundos. |
| `timeout` | entero | `120` | Tiempo máximo de espera para cada petición a la API, en segundos. |
| `retries` | entero | `3` | Número de reintentos tras errores temporales. |
| `retry_backoff` | número | `2.0` | Factor de espera exponencial entre reintentos. |
| `retry_status_codes` | lista | `[408, 429, 500, 502, 503, 504]` | Códigos HTTP considerados temporales y reintentables. |
| `urls` | lista | `["https://example.com/"]` | URLs que se analizarán. Cada URL debe empezar por `http://` o `https://`. |

---

## Cómo ejecutar la herramienta

### Ejecutar con el fichero de configuración por defecto

Si el fichero se llama `config.toml` y está en la misma carpeta que el script:

```bash
python pagespeed_audit.py
```

Esto equivale a:

```bash
python pagespeed_audit.py --config config.toml
```

### Ejecutar con un fichero de configuración específico

```bash
python pagespeed_audit.py --config config.toml
```

o:

```bash
python pagespeed_audit.py -c config.toml
```

Puedes usar diferentes ficheros de configuración para diferentes lotes:

```bash
python pagespeed_audit.py --config config_universities.toml
python pagespeed_audit.py --config config_libraries.toml
python pagespeed_audit.py --config config_repositories.toml
```

---

## ¿Cuántas peticiones API se generan?

El número normal de peticiones es:

```text
número de URLs × número de estrategias
```

Ejemplo:

```toml
urls = [
  "https://example.com/",
  "https://example.org/"
]

strategies = ["mobile", "desktop"]
```

Esto genera:

```text
2 URLs × 2 estrategias = 4 peticiones API
```

Los reintentos pueden incrementar el número total de peticiones. Con:

```toml
retries = 3
```

cada análisis URL/estrategia puede intentarse hasta cuatro veces:

```text
1 intento inicial + 3 reintentos = 4 intentos
```

---

## Tamaño de lote recomendado

Para un funcionamiento estable, se recomienda procesar URLs por lotes.

Rangos sugeridos:

| Escenario | Número recomendado de URLs |
|---|---:|
| Prueba inicial | 10-50 |
| Análisis pequeño controlado | 100-300 |
| Lote habitual | 500-1.000 |
| Proyecto grande | Varios lotes de 1.000 |

El CSV se escribe progresivamente después de cada combinación URL/estrategia. El XLSX se genera al final de la ejecución.

---

## Ficheros de salida

La herramienta puede generar:

```text
results/pagespeed_results.csv
results/pagespeed_results.xlsx
logs/pagespeed_audit_YYYYMMDD_HHMMSS.log
```

### Salida CSV

El CSV está pensado para ser compatible con Excel en configuraciones regionales españolas/catalanas:

- UTF-8 con BOM
- separador de punto y coma
- coma decimal, si `csv_decimal_comma = true`

El CSV se actualiza progresivamente. Esto significa que, si el proceso se interrumpe, las filas ya analizadas deberían conservarse.

Al final del proceso, el CSV final se regenera para aplicar ajustes finales de columnas como `drop_empty_columns`.

### Salida XLSX

El fichero XLSX se genera al final del proceso. Conserva los valores numéricos como números reales, lo que lo convierte en el formato más seguro para análisis en Excel.

La salida XLSX incluye:

- fila de cabecera en negrita;
- primera fila congelada;
- autofiltro;
- ajuste básico de anchura de columnas;
- formatos numéricos para milisegundos, segundos, porcentajes, puntuaciones y valores CLS.

---

## Logs

Cuando `log_enabled = true`, el script crea un fichero de log en la carpeta configurada.

Ejemplo:

```text
logs/pagespeed_audit_20260427_153022.log
```

El log registra:

- inicio de la ejecución;
- fichero de configuración utilizado;
- inicialización del CSV;
- cada URL y estrategia analizada;
- cada intento de llamada a la API;
- errores HTTP temporales;
- reintentos;
- timeouts de red o errores de conexión;
- errores finales de la API;
- falta de datos de campo de CrUX;
- filas añadidas al CSV progresivo;
- generación final de CSV y XLSX;
- interrupciones por parte del usuario;
- excepciones inesperadas con traceback.

Ejemplo de mensajes de log:

```text
2026-04-27 15:30:22 | INFO | Inicio de ejecución.
2026-04-27 15:30:22 | INFO | Fichero de configuración: config.toml
2026-04-27 15:30:23 | INFO | [1/10] Analizando URL=https://www.ub.edu/ | strategy=mobile
2026-04-27 15:30:23 | INFO | Petición API intento 1/4 | URL=https://www.ub.edu/ | strategy=mobile
2026-04-27 15:31:10 | INFO | Análisis correcto | URL=https://www.ub.edu/ | strategy=mobile
2026-04-27 15:31:10 | INFO | Fila añadida al CSV progresivo.
```

---

## Fuentes de datos y tipos de métricas

La herramienta recoge dos tipos principales de datos.

### 1. Datos de laboratorio

Los datos de laboratorio proceden del test de Lighthouse ejecutado por PageSpeed Insights en el momento del análisis.

Estos valores son útiles para pruebas controladas, pero pueden variar entre ejecuciones en función de las condiciones de red, el estado del servidor, la simulación de dispositivo y el entorno de Lighthouse.

Ejemplos:

- `lab_fcp_ms`
- `lab_lcp_ms`
- `lab_speed_index_ms`
- `lab_tbt_ms`
- `lab_cls`
- `lab_tti_ms`

### 2. Datos de campo

Los datos de campo proceden de CrUX, cuando están disponibles. Representan datos agregados de experiencia real de usuarios.

Los datos de campo pueden faltar para algunas URLs u orígenes. Esto suele significar que no hay suficientes datos de usuarios reales para que Google informe métricas fiables.

Los datos de campo aparecen en dos grupos:

- `field_url_*`: datos de la URL específica analizada;
- `field_origin_*`: datos del origen/dominio completo.

---

## Referencia de columnas

El CSV/XLSX final puede incluir muchas columnas. Algunas columnas solo aparecen si el dato correspondiente está disponible o si se activan opciones de configuración específicas.

### Columnas generales de ejecución

| Columna | Definición |
|---|---|
| `checked_at_utc` | Fecha y hora en que el script procesó la fila URL/estrategia, en UTC. |
| `status` | Estado de la fila. Normalmente `ok` o `error`. |
| `requested_input_url` | URL proporcionada originalmente en el fichero de configuración. |
| `strategy` | Estrategia de análisis usada por PageSpeed Insights: `mobile` o `desktop`. |
| `error_type` | Tipo de error si la fila ha fallado. Usa `sin_error` cuando no hay error. |
| `error_message` | Descripción del error si la fila ha fallado. Usa `sin_error` cuando no hay error. |
| `psi_id` | Identificador devuelto por PageSpeed Insights para la página analizada. Normalmente corresponde a la URL auditada. |
| `analysis_utc_timestamp` | Marca temporal devuelta por PageSpeed Insights para el análisis. |
| `final_url` | URL final auditada por Lighthouse después de redirecciones. |
| `lighthouse_version` | Versión de Lighthouse utilizada por PageSpeed Insights. |
| `runtime_error_code` | Código de error de ejecución devuelto por Lighthouse, si existe. Usa `sin_error` cuando no hay error de ejecución. |
| `runtime_error_message` | Mensaje de error de ejecución devuelto por Lighthouse, si existe. Usa `sin_error` cuando no hay error de ejecución. |

---

### Columnas de puntuaciones de categorías Lighthouse

Las puntuaciones se exportan en una escala de 0 a 100.

| Columna | Definición |
|---|---|
| `performance_score` | Puntuación Lighthouse de rendimiento. |
| `accessibility_score` | Puntuación Lighthouse de accesibilidad. |
| `best_practices_score` | Puntuación Lighthouse de buenas prácticas. |
| `seo_score` | Puntuación Lighthouse de SEO. |

Si una categoría no se ha solicitado en `categories`, su puntuación puede aparecer vacía.

---

### Columnas de métricas de laboratorio Lighthouse

Estas columnas se extraen del bloque `lighthouseResult.audits`.

| Columna | Definición |
|---|---|
| `lab_fcp_ms` | First Contentful Paint en milisegundos. |
| `lab_fcp_s` | First Contentful Paint en segundos. |
| `lab_fcp_ms_score` | Puntuación de la auditoría Lighthouse para First Contentful Paint. |
| `lab_lcp_ms` | Largest Contentful Paint en milisegundos. |
| `lab_lcp_s` | Largest Contentful Paint en segundos. |
| `lab_lcp_ms_score` | Puntuación de la auditoría Lighthouse para Largest Contentful Paint. |
| `lab_speed_index_ms` | Speed Index en milisegundos. |
| `lab_speed_index_s` | Speed Index en segundos. |
| `lab_speed_index_ms_score` | Puntuación de la auditoría Lighthouse para Speed Index. |
| `lab_tbt_ms` | Total Blocking Time en milisegundos. |
| `lab_tbt_s` | Total Blocking Time en segundos. |
| `lab_tbt_ms_score` | Puntuación de la auditoría Lighthouse para Total Blocking Time. |
| `lab_cls` | Cumulative Layout Shift medido en la prueba de laboratorio de Lighthouse. |
| `lab_cls_score` | Puntuación de la auditoría Lighthouse para Cumulative Layout Shift. |
| `lab_tti_ms` | Time to Interactive en milisegundos. |
| `lab_tti_s` | Time to Interactive en segundos. |
| `lab_tti_ms_score` | Puntuación de la auditoría Lighthouse para Time to Interactive. |

---

### Columnas opcionales de visualización Lighthouse

Estas columnas solo aparecen cuando:

```toml
include_display_values = true
```

Son valores legibles devueltos por Lighthouse. Pueden contener unidades como `s` o `ms`, por lo que no se recomiendan para análisis cuantitativo.

| Columna | Definición |
|---|---|
| `lab_fcp_ms_display` | Valor legible de First Contentful Paint. |
| `lab_lcp_ms_display` | Valor legible de Largest Contentful Paint. |
| `lab_speed_index_ms_display` | Valor legible de Speed Index. |
| `lab_tbt_ms_display` | Valor legible de Total Blocking Time. |
| `lab_cls_display` | Valor legible de Cumulative Layout Shift. |
| `lab_tti_ms_display` | Valor legible de Time to Interactive. |

---

### Columnas de disponibilidad de datos de campo

Estas columnas indican si se han devuelto datos de campo de CrUX.

| Columna | Definición |
|---|---|
| `field_url_id` | Identificador CrUX para la URL específica, cuando está disponible. |
| `field_url_overall_category` | Categoría global CrUX para la URL. |
| `field_url_data_available` | `true` si hay métricas de campo a nivel de URL; en caso contrario, `false`. |
| `field_url_data_status` | Estado de los datos de campo a nivel de URL, normalmente `available` o `not_available`. |
| `field_url_data_message` | Explicación legible de la disponibilidad de datos de campo a nivel de URL. |
| `field_origin_id` | Identificador CrUX para el origen/dominio, cuando está disponible. |
| `field_origin_overall_category` | Categoría global CrUX para el origen/dominio. |
| `field_origin_data_available` | `true` si hay métricas de campo a nivel de origen; en caso contrario, `false`. |
| `field_origin_data_status` | Estado de los datos de campo a nivel de origen, normalmente `available` o `not_available`. |
| `field_origin_data_message` | Explicación legible de la disponibilidad de datos de campo a nivel de origen. |

Valores controlados posibles:

| Valor | Significado |
|---|---|
| `available` | Los datos están disponibles. |
| `not_available` | PageSpeed Insights o CrUX no ha devuelto los datos. |
| `not_evaluated` | Los datos no se han podido evaluar porque la petición a la API ha fallado. |
| `sin_error` | No se ha producido ningún error. |
| `no_ejecutado` | El análisis no se ha completado correctamente. |

---

## Columnas de métricas de campo a nivel de URL

El prefijo `field_url_` se refiere a datos reales de usuarios de CrUX para la URL exacta analizada, cuando están disponibles.

### LCP a nivel de URL

| Columna | Definición |
|---|---|
| `field_url_lcp_ms` | Valor percentil de Largest Contentful Paint a nivel de URL, en milisegundos. |
| `field_url_lcp_ms_status` | Estado de disponibilidad de LCP a nivel de URL en milisegundos. |
| `field_url_lcp_s` | Valor percentil de Largest Contentful Paint a nivel de URL, en segundos. |
| `field_url_lcp_s_status` | Estado de disponibilidad de LCP a nivel de URL en segundos. |
| `field_url_lcp_ms_category` | Categoría de calidad CrUX para LCP a nivel de URL. |
| `field_url_lcp_ms_good_pct` | Porcentaje de cargas de página clasificadas como buenas para LCP a nivel de URL. |
| `field_url_lcp_ms_needs_improvement_pct` | Porcentaje de cargas de página clasificadas como mejorables para LCP a nivel de URL. |
| `field_url_lcp_ms_poor_pct` | Porcentaje de cargas de página clasificadas como pobres para LCP a nivel de URL. |

### CLS a nivel de URL

| Columna | Definición |
|---|---|
| `field_url_cls` | Valor percentil de Cumulative Layout Shift a nivel de URL. |
| `field_url_cls_status` | Estado de disponibilidad de CLS a nivel de URL. |
| `field_url_cls_category` | Categoría de calidad CrUX para CLS a nivel de URL. |
| `field_url_cls_good_pct` | Porcentaje de cargas de página clasificadas como buenas para CLS a nivel de URL. |
| `field_url_cls_needs_improvement_pct` | Porcentaje de cargas de página clasificadas como mejorables para CLS a nivel de URL. |
| `field_url_cls_poor_pct` | Porcentaje de cargas de página clasificadas como pobres para CLS a nivel de URL. |

### INP a nivel de URL

| Columna | Definición |
|---|---|
| `field_url_inp_ms` | Valor percentil de Interaction to Next Paint a nivel de URL, en milisegundos. |
| `field_url_inp_ms_status` | Estado de disponibilidad de INP a nivel de URL en milisegundos. |
| `field_url_inp_s` | Valor percentil de Interaction to Next Paint a nivel de URL, en segundos. |
| `field_url_inp_s_status` | Estado de disponibilidad de INP a nivel de URL en segundos. |
| `field_url_inp_ms_category` | Categoría de calidad CrUX para INP a nivel de URL. |
| `field_url_inp_ms_good_pct` | Porcentaje de cargas de página clasificadas como buenas para INP a nivel de URL. |
| `field_url_inp_ms_needs_improvement_pct` | Porcentaje de cargas de página clasificadas como mejorables para INP a nivel de URL. |
| `field_url_inp_ms_poor_pct` | Porcentaje de cargas de página clasificadas como pobres para INP a nivel de URL. |

### FCP a nivel de URL

| Columna | Definición |
|---|---|
| `field_url_fcp_ms` | Valor percentil de First Contentful Paint a nivel de URL, en milisegundos. |
| `field_url_fcp_ms_status` | Estado de disponibilidad de FCP a nivel de URL en milisegundos. |
| `field_url_fcp_s` | Valor percentil de First Contentful Paint a nivel de URL, en segundos. |
| `field_url_fcp_s_status` | Estado de disponibilidad de FCP a nivel de URL en segundos. |
| `field_url_fcp_ms_category` | Categoría de calidad CrUX para FCP a nivel de URL. |
| `field_url_fcp_ms_good_pct` | Porcentaje de cargas de página clasificadas como buenas para FCP a nivel de URL. |
| `field_url_fcp_ms_needs_improvement_pct` | Porcentaje de cargas de página clasificadas como mejorables para FCP a nivel de URL. |
| `field_url_fcp_ms_poor_pct` | Porcentaje de cargas de página clasificadas como pobres para FCP a nivel de URL. |

### TTFB a nivel de URL

| Columna | Definición |
|---|---|
| `field_url_ttfb_ms` | Valor percentil de Time to First Byte a nivel de URL, en milisegundos. |
| `field_url_ttfb_ms_status` | Estado de disponibilidad de TTFB a nivel de URL en milisegundos. |
| `field_url_ttfb_s` | Valor percentil de Time to First Byte a nivel de URL, en segundos. |
| `field_url_ttfb_s_status` | Estado de disponibilidad de TTFB a nivel de URL en segundos. |
| `field_url_ttfb_ms_category` | Categoría de calidad CrUX para TTFB a nivel de URL. |
| `field_url_ttfb_ms_good_pct` | Porcentaje de cargas de página clasificadas como buenas para TTFB a nivel de URL. |
| `field_url_ttfb_ms_needs_improvement_pct` | Porcentaje de cargas de página clasificadas como mejorables para TTFB a nivel de URL. |
| `field_url_ttfb_ms_poor_pct` | Porcentaje de cargas de página clasificadas como pobres para TTFB a nivel de URL. |

---

## Columnas de métricas de campo a nivel de origen

El prefijo `field_origin_` se refiere a datos reales de usuarios de CrUX para el origen/dominio completo, cuando están disponibles.

Las columnas a nivel de origen siguen la misma estructura que las columnas a nivel de URL.

### LCP a nivel de origen

| Columna | Definición |
|---|---|
| `field_origin_lcp_ms` | Valor percentil de Largest Contentful Paint a nivel de origen, en milisegundos. |
| `field_origin_lcp_ms_status` | Estado de disponibilidad de LCP a nivel de origen en milisegundos. |
| `field_origin_lcp_s` | Valor percentil de Largest Contentful Paint a nivel de origen, en segundos. |
| `field_origin_lcp_s_status` | Estado de disponibilidad de LCP a nivel de origen en segundos. |
| `field_origin_lcp_ms_category` | Categoría de calidad CrUX para LCP a nivel de origen. |
| `field_origin_lcp_ms_good_pct` | Porcentaje de cargas de página clasificadas como buenas para LCP a nivel de origen. |
| `field_origin_lcp_ms_needs_improvement_pct` | Porcentaje de cargas de página clasificadas como mejorables para LCP a nivel de origen. |
| `field_origin_lcp_ms_poor_pct` | Porcentaje de cargas de página clasificadas como pobres para LCP a nivel de origen. |

### CLS a nivel de origen

| Columna | Definición |
|---|---|
| `field_origin_cls` | Valor percentil de Cumulative Layout Shift a nivel de origen. |
| `field_origin_cls_status` | Estado de disponibilidad de CLS a nivel de origen. |
| `field_origin_cls_category` | Categoría de calidad CrUX para CLS a nivel de origen. |
| `field_origin_cls_good_pct` | Porcentaje de cargas de página clasificadas como buenas para CLS a nivel de origen. |
| `field_origin_cls_needs_improvement_pct` | Porcentaje de cargas de página clasificadas como mejorables para CLS a nivel de origen. |
| `field_origin_cls_poor_pct` | Porcentaje de cargas de página clasificadas como pobres para CLS a nivel de origen. |

### INP a nivel de origen

| Columna | Definición |
|---|---|
| `field_origin_inp_ms` | Valor percentil de Interaction to Next Paint a nivel de origen, en milisegundos. |
| `field_origin_inp_ms_status` | Estado de disponibilidad de INP a nivel de origen en milisegundos. |
| `field_origin_inp_s` | Valor percentil de Interaction to Next Paint a nivel de origen, en segundos. |
| `field_origin_inp_s_status` | Estado de disponibilidad de INP a nivel de origen en segundos. |
| `field_origin_inp_ms_category` | Categoría de calidad CrUX para INP a nivel de origen. |
| `field_origin_inp_ms_good_pct` | Porcentaje de cargas de página clasificadas como buenas para INP a nivel de origen. |
| `field_origin_inp_ms_needs_improvement_pct` | Porcentaje de cargas de página clasificadas como mejorables para INP a nivel de origen. |
| `field_origin_inp_ms_poor_pct` | Porcentaje de cargas de página clasificadas como pobres para INP a nivel de origen. |

### FCP a nivel de origen

| Columna | Definición |
|---|---|
| `field_origin_fcp_ms` | Valor percentil de First Contentful Paint a nivel de origen, en milisegundos. |
| `field_origin_fcp_ms_status` | Estado de disponibilidad de FCP a nivel de origen en milisegundos. |
| `field_origin_fcp_s` | Valor percentil de First Contentful Paint a nivel de origen, en segundos. |
| `field_origin_fcp_s_status` | Estado de disponibilidad de FCP a nivel de origen en segundos. |
| `field_origin_fcp_ms_category` | Categoría de calidad CrUX para FCP a nivel de origen. |
| `field_origin_fcp_ms_good_pct` | Porcentaje de cargas de página clasificadas como buenas para FCP a nivel de origen. |
| `field_origin_fcp_ms_needs_improvement_pct` | Porcentaje de cargas de página clasificadas como mejorables para FCP a nivel de origen. |
| `field_origin_fcp_ms_poor_pct` | Porcentaje de cargas de página clasificadas como pobres para FCP a nivel de origen. |

### TTFB a nivel de origen

| Columna | Definición |
|---|---|
| `field_origin_ttfb_ms` | Valor percentil de Time to First Byte a nivel de origen, en milisegundos. |
| `field_origin_ttfb_ms_status` | Estado de disponibilidad de TTFB a nivel de origen en milisegundos. |
| `field_origin_ttfb_s` | Valor percentil de Time to First Byte a nivel de origen, en segundos. |
| `field_origin_ttfb_s_status` | Estado de disponibilidad de TTFB a nivel de origen en segundos. |
| `field_origin_ttfb_ms_category` | Categoría de calidad CrUX para TTFB a nivel de origen. |
| `field_origin_ttfb_ms_good_pct` | Porcentaje de cargas de página clasificadas como buenas para TTFB a nivel de origen. |
| `field_origin_ttfb_ms_needs_improvement_pct` | Porcentaje de cargas de página clasificadas como mejorables para TTFB a nivel de origen. |
| `field_origin_ttfb_ms_poor_pct` | Porcentaje de cargas de página clasificadas como pobres para TTFB a nivel de origen. |

---

## Por qué algunas celdas pueden estar en blanco

Las celdas numéricas en blanco suelen indicar que la métrica no estaba disponible.

Esto es esperable en varios casos:

- CrUX no tiene suficientes datos de campo para la URL concreta.
- CrUX no tiene suficientes datos de campo para el origen.
- La petición a la API ha fallado y la fila tiene `status = "error"`.
- No se ha solicitado una categoría de Lighthouse.
- PageSpeed Insights no ha devuelto una métrica específica.

La herramienta no rellena las celdas numéricas con textos como `not_available`, porque eso dificultaría el análisis en Excel. En su lugar, añade columnas complementarias de estado y mensaje.

Ejemplo:

```text
field_url_data_available = false
field_url_data_status = not_available
field_url_data_message = Datos de campo no disponibles para la URL analizada.
```

---

## Gestión de errores

Si una URL falla, el script no detiene todo el lote. Crea una fila con:

```text
status = error
```

y guarda los detalles en:

```text
error_type
error_message
runtime_error_code
runtime_error_message
```

En peticiones fallidas a la API, las columnas de datos de campo se marcan con:

```text
field_url_data_status = not_evaluated
field_origin_data_status = not_evaluated
```

El error también se escribe en el log.

---

## Funcionamiento de los reintentos

El script reintenta errores temporales según:

```toml
retries = 3
retry_backoff = 2.0
retry_status_codes = [408, 429, 500, 502, 503, 504]
```

Con esta configuración:

```text
Intento 1: petición inicial
Intento 2: después de 2 segundos
Intento 3: después de 4 segundos
Intento 4: después de 8 segundos
```

Solo se reintentan errores temporales de red o códigos HTTP configurados.

---

## Trabajo con Excel

Para análisis en Excel, el fichero recomendado es:

```text
results/pagespeed_results.xlsx
```

El fichero XLSX conserva correctamente los valores numéricos.

El CSV también está adaptado para Excel, pero, según la configuración regional, Excel puede interpretar algunos valores de forma diferente si se abre directamente. Si ocurre, importa el CSV usando el asistente de importación de datos de Excel y selecciona:

- codificación: UTF-8;
- delimitador: punto y coma;
- separador decimal: coma.

---

## Flujo de trabajo sugerido

1. Crear y activar un entorno virtual.
2. Instalar dependencias con `pip install -r requirements.txt`.
3. Editar `config.toml`.
4. Añadir la clave de API en `api_key`.
5. Añadir las URLs a la lista `urls`.
6. Ejecutar una prueba pequeña con 2-5 URLs.
7. Revisar `results/pagespeed_results.xlsx`.
8. Revisar el fichero de log generado.
9. Ejecutar el lote completo.

Ejemplo:

```bash
python pagespeed_audit.py --config config.toml
```

---

## Resolución de problemas

### `No se ha encontrado el fichero de configuración`

El script no encuentra `config.toml`.

Solución: comprueba que `config.toml` existe en la misma carpeta que `pagespeed_audit.py`, o ejecuta:

```bash
python pagespeed_audit.py --config ruta/a/tu_config.toml
```

### Los caracteres acentuados se ven mal en Excel

Usa:

```toml
csv_encoding = "utf-8-sig"
```

Además, para análisis, es preferible usar el fichero XLSX.

### Los valores decimales se interpretan mal en Excel

Usa:

```toml
csv_delimiter = ";"
csv_decimal_comma = true
```

Además, es preferible usar el fichero XLSX, que almacena los valores numéricos directamente.

### Muchas celdas de datos de campo están en blanco

Normalmente significa que los datos de campo de CrUX no están disponibles para esa URL u origen. Revisa:

```text
field_url_data_available
field_url_data_status
field_url_data_message
field_origin_data_available
field_origin_data_status
field_origin_data_message
```

### La API devuelve HTTP 429

HTTP 429 suele indicar demasiadas peticiones en poco tiempo o un problema de cuota.

Posibles ajustes:

```toml
sleep = 2.0
retries = 3
retry_backoff = 3.0
```

También conviene revisar la configuración de cuotas del proyecto en Google Cloud.

### El XLSX no se genera si el proceso se interrumpe de forma abrupta

El script escribe el CSV progresivamente. El XLSX se genera al final o cuando el script gestiona una interrupción de teclado. Si el proceso se cierra de forma forzada, el CSV progresivo es la salida parcial más segura.

---

## Notas de desarrollo

El script principal es:

```text
pagespeed_audit.py
```

El script está planteado como un flujo simple de línea de comandos:

```bash
python pagespeed_audit.py --config config.toml
```

Para proyectos grandes, posibles mejoras futuras incluyen:

- dividir listas de URLs muy grandes en lotes;
- añadir modo de reanudación;
- añadir ejecución paralela con control estricto de ritmo;
- exportar respuestas JSON para preservación;
- añadir pruebas unitarias.
