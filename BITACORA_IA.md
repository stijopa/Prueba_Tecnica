## Uso de la IA

* **Propósito general:** Se utilizó **Gemini** para acelerar el desarrollo y el análisis del proyecto.
* **Exploración de datos (EDA):** Se propuso utilizar la librería **`sweetviz`** para obtener insumos adicionales durante el análisis exploratorio de datos.
* **Modelado:** Se propuso implementar un modelo de **regresión logística** y un modelo de **XGBoost**.
* **Interpretabilidad y valores atípicos:** Se propuso analizar **valores atípicos** y aplicar **SHAP** para aportar una lectura distinta sobre el modelo de regresión logística.
* **Dashboard interactivo en Streamlit:** Se contó con apoyo integral para diseñar, estructurar y poner en marcha el *dashboard* de riesgo y retención en **Google Colab** (`dash.ipynb` / `app.py` con `dashboard_clientes.csv`, `shap_importancia.csv` y túnel de Cloudflare), una implementación que no se había abordado previamente de esa forma.
* **Decisiones no aceptadas:** No se aceptó aplicar balanceo en la regresión logística, ya que en la operación cotidiana los datos no están balanceados; aun así, se conservan los resultados de esa variante como soporte.
* **Decisiones aceptadas:** Se aceptó la forma propuesta de consolidar las fuentes para construir el *DataFrame* definitivo, así como el enfoque de implementación del *dashboard* interactivo.