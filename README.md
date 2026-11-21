# 📡 Conecta+ — Predicción de Churn a 90 Días y Estrategia de Retención

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/stijopa/Prueba_Tecnica/blob/main/solucion.ipynb)

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
!pip install -r https://raw.githubusercontent.com/stijopa/Prueba_Tecnica/main/requirements.txt
```
*(Si colocaste `requirements.txt` dentro de tu carpeta en Google Drive, puedes ejecutar `!pip install -r requirements.txt`).*

### 4️⃣ Ejecutar `solucion.ipynb` en Orden
Corre las celdas del *notebook* de principio a fin para:

1. **Preparar variables sin *data leakage*:** Construir las tablas de entrenamiento y puntuación respetando estrictamente la ventana temporal (`fecha <= fecha_corte` en entrenamiento y corte al `2025-12-31` en puntuación).
2. **Registrar y comparar en MLflow:** Ejecutar las corridas de modelado, registrar parámetros, métricas y artefactos, consultar `mlflow.search_runs()` y verificar el modelo seleccionado.
3. **Generar `predicciones.csv`:** Calcular las probabilidades para los clientes activos al `2025-12-31`, aplicar la regla de decisión (`contactar`) según los supuestos de negocio y validar el archivo de salida.
4. **Revisar causas y operación:** Analizar los factores de cancelación y los lineamientos para llevar el modelo a producción.

### 5️⃣ Se realizar el punto extra: Dashboard en Streamlit

### 6️⃣ Aprovechamiento de Tickets (`tickets.jsonl`) y Logs de Uso (`eventos.jsonl.gz`)

#### Enfoque elegido
Adoptamos un enfoque **híbrido, ligero y trazable** a nivel de `(cliente_id, fecha_corte)`:
* **Tickets de soporte (`tickets.jsonl`):** Combinación de **reglas de dominio por palabras clave/patrones** (intenciones de cancelación, cobros/facturación, fallos/errores, lentitud), **NLP clásico comprimido** (`TfidfVectorizer` + `TruncatedSVD` con pocas componentes) y **variables estructuradas** (tickets previos al corte, proporción abiertos/sin resolver, recencia y satisfacción homologada).
* **Logs de uso (`eventos.jsonl.gz`):** **Agregaciones tabulares por ventanas temporales y ratios técnicos** (días activos, sesiones/eventos en ventanas recientes vs. históricas, caída de uso y tasa de errores con `codigo_error` por `plataforma` y `version_app`), en lugar de modelar el log como texto libre o secuencia paso a paso.

#### Cómo lo implementamos
1. **Corte temporal estricto (anti-*leakage*):** Cada registro o evento se filtra con `fecha <= fecha_corte` (`2025-12-31` para `clientes_a_puntuar`), evitando información futura o cierres posteriores al corte.
2. **Homologación y limpieza:** Se normalizan esquemas heterogéneos y texto en `tickets.jsonl` antes de extraer señales.
3. **Ajuste dentro de entrenamiento:** Cualquier transformación aprendida del texto (como TF-IDF + SVD) se ajusta **únicamente en los datos de entrenamiento** (o dentro de cada *fold* de validación) y luego transforma validación/puntuación.
4. **Agregación por cliente y corte:** Se unen las métricas de soporte y uso mediante *left join* a la tabla base sin alterar el grano de `(cliente_id, fecha_corte)`.

#### Justificación y límites
* **Por qué esta elección:** Con un tamaño de muestra moderado y variables tabulares de CRM, suscripciones y facturación, evita sobreajuste, mantiene baja latencia/costo operativo y ofrece alta explicabilidad para gerencia (por ejemplo, aislando picos críticos de error en versiones específicas de app o fricción operativa).
* **Límites:** Las reglas y TF-IDF + SVD no captan ironía, sarcasmo o negaciones complejas con la precisión semántica de *embeddings* densos o un *LLM*, y las agregaciones por ventana resumen tendencias sin conservar el orden exacto de cada acción dentro de una sesión. Un *LLM* o *embeddings* aportarían mayor matiz semántico en textos ambiguos, pero con mayor costo computacional, latencia y riesgo de *overfitting* dado el volumen de datos.

### 7️⃣ Llevarlo a producción
* **Ejecución programada por corte:** Automatizar el *pipeline* en cada corte operativo cuando se realicen los cortes de facturación.
* **Trazabilidad y registro:** Registrar cada corrida en MLflow (versión del modelo, parámetros, métricas y fecha de corte de datos).
* **Reentrenamiento extraordinario (por evento):** Disparar un reentrenamiento fuera de calendario si ocurren cambios estructurales relevantes, como lanzamientos o cambios de precios/planes, migraciones o cambios en la app/plataformas, modificaciones en los procesos de cobro/retención o una caída significativa en las métricas de validación.
* **Rentabilidad real negativa:** Si en la operación real, la retención es muy inferior al 40% esperado o el costo operativo real supera lo presupuestado, haciendo que contactar deje de generar beneficio neto positivo.

