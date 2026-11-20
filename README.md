# 📡 Conecta+ — Predicción de Churn a 90 Días y Estrategia de Retención

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/TU_USUARIO/TU_REPO/blob/main/solucion.ipynb)

> 💡 **Nota:** Reemplaza `TU_USUARIO/TU_REPO` (y la rama `main` si aplica) con la URL real de tu repositorio en GitHub.

---

## 🎯 Contexto del Negocio

**Conecta+** es una empresa colombiana de servicios por suscripción con cuatro planes (**Básico**, **Estándar**, **Premium** y **Familiar**), clientes personales y Pymes, y operación en app móvil y web. Frente al aumento de la cancelación en el último año y el costo operativo de cada contacto de retención, este proyecto responde dos preguntas clave con corte al **31 de diciembre de 2025**:

1. **¿A quién debemos contactar en los próximos 90 días?**  
   Estimación de la probabilidad de cancelación (`churn_90d`) para los clientes activos al `2025-12-31` y decisión concreta (`contactar` = `0`/`1`) según valor esperado frente al costo de contacto.

2. **¿Por qué se están yendo?**  
   Diagnóstico de los impulsores de cancelación y hallazgos relevantes del negocio identificados en los datos (uso, fricción técnica/errores, soporte/NLP, facturación, suscripciones y gestión de retención).

---

## 📁 Cómo Está Organizado el Repositorio

> 🔒 **Política de datos:** Este repositorio **no incluye archivos de datos crudos** (`conecta.db`, `tickets.jsonl`, `eventos.jsonl.gz`, etc.). Solo contiene los entregables del proyecto:

| Entregable | Descripción |
| :--- | :--- |
| **`README.md`** | Guía general del proyecto, botón de ejecución en Google Colab, estructura del repositorio e instrucciones de ejecución. |
| **`solucion.ipynb`** | *Notebook* principal con preparación de variables sin *data leakage* (respetando `fecha_corte` en entrenamiento y el `2025-12-31` en puntuación), seguimiento de experimentos en MLflow, evaluación de modelos, cálculo de predicciones y decisiones de contacto, análisis de causas de *churn* y lineamientos de operación en producción. |
| **`requirements.txt`** | Librerías adicionales a las preinstaladas en Google Colab (como `mlflow`, `lightgbm`, `xgboost`, `streamlit`, `pycloudflared`, `joblib` y `sweetviz`). |
| **`predicciones.csv`** | Salida generada para los clientes activos al `2025-12-31` (`clientes_a_puntuar`), con las columnas `cliente_id`, `probabilidad` (estimada de *churn* a 90 días) y `contactar` (`0`/`1` según los supuestos de negocio). |
| **`MEMO.md`** | Resumen ejecutivo de una página dirigido a gerencia (en lenguaje no técnico) con hallazgos clave, decisión de contacto y recomendaciones. |
| **`BITACORA_IA.md`** | Registro del uso de inteligencia artificial durante el desarrollo y el tiempo invertido en cada etapa. |

---

## 🚀 Cómo Ejecutarlo

El flujo está diseñado para ejecutarse en **Google Colab** conectando los datos desde tu carpeta en **Google Drive**:

### 1️⃣ Abrir el *Notebook* en Colab
Haz clic en el botón **Open in Colab** en la parte superior de este archivo para cargar `solucion.ipynb`.

### 2️⃣ Montar Google Drive y Ubicar los Datos de Entrada
En la celda inicial de configuración, monta tu unidad de Drive y apunta a la carpeta donde almacenaste los archivos de origen (por ejemplo, `conecta.db`, `tickets.jsonl` y `eventos.jsonl.gz` o las tablas extraídas):

```python
import os
from google.colab import drive

if not os.path.exists("/content/drive/MyDrive"):
  drive.mount("/content/drive")

%cd "/content/drive/MyDrive/TU_CARPETA_DE_DATOS"
!ls -lh
```

### 3️⃣ Instalar Librerías Adicionales
Instala las dependencias listadas en `requirements.txt` desde el repositorio (o desde una copia local en tu carpeta de Drive):

```bash
!pip install -r https://raw.githubusercontent.com/TU_USUARIO/TU_REPO/main/requirements.txt
```
*(Si colocaste `requirements.txt` dentro de tu carpeta en Google Drive, puedes ejecutar `!pip install -r requirements.txt`).*

### 4️⃣ Ejecutar `solucion.ipynb` en Orden
Corre las celdas del *notebook* de principio a fin para:

1. **Preparar variables sin *data leakage*:** Construir las tablas de entrenamiento y puntuación respetando estrictamente la ventana temporal (`fecha <= fecha_corte` en entrenamiento y corte al `2025-12-31` en puntuación).
2. **Registrar y comparar en MLflow:** Ejecutar las corridas de modelado, registrar parámetros, métricas y artefactos, consultar `mlflow.search_runs()` y verificar el modelo seleccionado.
3. **Generar `predicciones.csv`:** Calcular las probabilidades para los clientes activos al `2025-12-31`, aplicar la regla de decisión (`contactar`) según los supuestos de negocio y validar el archivo de salida.
4. **Revisar causas y operación:** Analizar los factores de cancelación y los lineamientos para llevar el modelo a producción.
