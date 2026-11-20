# NTT_Prueba

Conecta+ — Predicción de Churn a 90 Días y Estrategia de Retención

Contexto del negocio

Conecta+ es una empresa colombiana de servicios por suscripción con cuatro planes (Básico, Estándar, Premium y Familiar), clientes personales y Pymes, y operación a través de app móvil y web. Ante el crecimiento de la cancelación de clientes en el último año y el costo asociado a cada gestión de retención, este proyecto responde a dos preguntas clave con fecha de corte operativa al 31 de diciembre de 2025:

¿A quién debemos contactar en los próximos 90 días? Estimación calibrada de la probabilidad de cancelación (churn_90d) para los clientes activos al 2025-12-31 y una regla concreta de decisión (contactar = 0/1) basada en valor esperado frente al costo de contacto.

¿Por qué se están yendo? Diagnóstico de los factores de fuga y hallazgos relevantes del negocio identificados en los datos (uso, fricción técnica/errores, soporte/NLP, facturación, suscripciones y gestión de retención).

Cómo está organizado el repositorio

Por políticas de entrega, este repositorio no incluye los archivos de datos crudos (conecta.db, tickets.jsonl, eventos.jsonl.gz, etc.) y contiene únicamente los siguientes entregables:

README.md: Guía general del proyecto, botón de acceso a Google Colab, estructura del repositorio e instrucciones de ejecución.

solucion.ipynb: Notebook principal que consolida la exploración, la construcción de variables a nivel de (cliente_id, fecha_corte) sin data leakage, el seguimiento de experimentos en MLflow, el entrenamiento y evaluación de modelos, la generación de predicciones, el análisis de causas de churn a 90 días y los lineamientos de operación en producción.

requirements.txt: Dependencias adicionales a las preinstaladas en Google Colab (como mlflow, lightgbm, xgboost, streamlit, pycloudflared, joblib y sweetviz).

predicciones.csv: Archivo de salida generado para los clientes activos al 2025-12-31 (clientes_a_puntuar), con las columnas cliente_id, probabilidad (probabilidad estimada de cancelación en los próximos 90 días) y contactar (0 o 1 según los supuestos de negocio).

MEMO.md: Resumen ejecutivo de una página dirigido a gerencia (en lenguaje no técnico) con hallazgos clave, decisión de contacto y recomendaciones accionables.

BITACORA_IA.md: Registro del uso de inteligencia artificial durante el desarrollo del proyecto y el desglose del tiempo invertido en cada etapa.

Cómo ejecutarlo

El proyecto está diseñado para ejecutarse en Google Colab, conectando los archivos de datos desde una carpeta en tu Google Drive:

1. Abrir el notebook en Colab

Haz clic en el botón Open in Colab ubicado en la parte superior de este archivo para cargar solucion.ipynb.

2. Montar Google Drive y ubicar los datos de entrada

En la celda inicial de configuración, monta tu Google Drive y cambia al directorio donde almacenaste los archivos de origen (por ejemplo, conecta.db, tickets.jsonl y eventos.jsonl.gz o las tablas extraídas):

import os
from google.colab import drive

if not os.path.exists("/content/drive/MyDrive"):
    drive.mount("/content/drive")

%cd "/content/drive/MyDrive/TU_CARPETA_DE_DATOS"
!ls -lh


3. Instalar librerías adicionales

Instala las dependencias listadas en requirements.txt desde el repositorio (o desde una copia local en tu carpeta de Drive):

!pip install -r https://raw.githubusercontent.com/TU_USUARIO/TU_REPO/main/requirements.txt


(Si colocaste requirements.txt dentro de tu carpeta en Google Drive, puedes ejecutar !pip install -r requirements.txt).

4. Ejecutar solucion.ipynb en orden

Ejecuta las celdas del notebook de principio a fin para:

Preparar las tablas y construir los conjuntos de entrenamiento y puntuación respetando estrictamente la ventana temporal (fecha <= fecha_corte en entrenamiento y corte al 2025-12-31 en puntuación) para evitar data leakage.

Ejecutar las corridas de modelado, registrar parámetros, métricas y artefactos en MLflow, consultar la tabla comparativa con mlflow.search_runs() y verificar el modelo seleccionado.

Calcular las probabilidades para los clientes activos al 2025-12-31, aplicar la regla de decisión (contactar) bajo los supuestos de negocio y generar/validar predicciones.csv.

Revisar el análisis de causas de cancelación y los lineamientos para llevar el modelo a producción.
