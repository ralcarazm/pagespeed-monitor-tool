# Inicio rápido

Guía mínima para ejecutar **pagespeed-monitor-tool**.

---

## 1. Descargar el proyecto

Descarga o copia la carpeta del proyecto en tu ordenador.

La carpeta debe contener, como mínimo:

```text
pagespeed_audit.py
config.toml
requirements.txt
```

Abre una terminal dentro de esa carpeta.

---

## 2. Crear el entorno virtual

### Linux / macOS

```bash
python3 -m venv .venv
```

### Windows PowerShell

```powershell
py -m venv .venv
```

### Windows Símbolo del sistema

```cmd
py -m venv .venv
```

---

## 3. Activar el entorno virtual

### Linux / macOS

```bash
source .venv/bin/activate
```

### Windows PowerShell

```powershell
.\.venv\Scripts\Activate.ps1
```

### Windows Símbolo del sistema

```cmd
.venv\Scripts\activate.bat
```

---

## 4. Instalar dependencias

Con el entorno virtual activado:

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

---

## 5. Actualizar `config.toml`

Abre el fichero:

```text
config.toml
```

Añade tu clave de API:

```toml
api_key = "TU_API_KEY_AQUI"
```

Revisa los ficheros de salida:

```toml
output = "results/pagespeed_results.csv"
xlsx_output = "results/pagespeed_results.xlsx"
```

Indica las estrategias:

```toml
strategies = ["mobile", "desktop"]
```

Añade las URLs que quieres analizar:

```toml
urls = [
  "https://www.example1.com",
  "https://www.example2.com",
  "https://www.example3.com",
  "..."
]
```

---

## 6. Ejecutar el análisis

Si `config.toml` está en la misma carpeta que el script:

```bash
python pagespeed_audit.py
```

También puedes indicar explícitamente el fichero de configuración:

```bash
python pagespeed_audit.py --config config.toml
```

---

## 7. Revisar resultados

Los resultados se guardarán en:

```text
results/pagespeed_results.csv
results/pagespeed_results.xlsx
```

Los logs se guardarán en:

```text
logs/
```

Para trabajar en Excel, se recomienda abrir preferentemente:

```text
results/pagespeed_results.xlsx
```
