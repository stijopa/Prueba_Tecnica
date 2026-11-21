## Uso de la IA

* **Tiempo estimado:** Se utilizó **7 horas** aprox para realizar el reto.
* **Propósito general:** Se utilizó **Gemini** para acelerar el desarrollo y el análisis del proyecto.
* **Exploración de datos (EDA):** Propuse utilizar la librería **`sweetviz`** para obtener insumos adicionales durante el análisis exploratorio de datos.
* **Modelado:** Se propuso implementar un modelo de **regresión logística** y un modelo de **XGBoost**.
* **Interpretabilidad y valores atípicos:** Propuse analizar **valores atípicos** y aplicar **SHAP** para aportar una lectura distinta sobre el modelo de regresión logística.
* **Dashboard interactivo en Streamlit:** Se contó con apoyo integral para diseñar, estructurar y poner en marcha el *dashboard* de riesgo y retención en **Google Colab** (`dash.ipynb` / `app.py` con `dashboard_clientes.csv`, `shap_importancia.csv` y túnel de Cloudflare), una implementación que no se había abordado previamente de esa forma.
* **Decisiones no aceptadas:** No se aceptó aplicar balanceo en la regresión logística, ya que en la operación cotidiana los datos no están balanceados; aun así, se conservan los resultados de esa variante como soporte.
* **Decisiones no aceptadas:** En la propuesta orientada al negocio se sugiere contactar a los clientes que utilizan Android para que actualicen su versión. Esta recomendación se descarta debido a que excede el alcance de la operación y se desvía de la razón social de la empresa
* **Decisiones aceptadas:** Se aceptó la forma propuesta de consolidar las fuentes para construir el *DataFrame* definitivo, así como el enfoque de implementación del *dashboard* interactivo.
* **Decisiones aceptadas:** Exploración de las métricas de evaluación. Aunque XGBoost destaca inicialmente como el mejor candidato, se contrasta su desempeño frente a la regresión lineal, poniendo especial atención en el error cuadrático medio (MSE / RMSE).

