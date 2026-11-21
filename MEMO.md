# MEMO EJECUTIVO — CONECTA+

**Para:** Direcciones de Producto, Operación y Retención  
**De:** Equipo de Analítica y Ciencia de Datos  
**Fecha de corte de los datos:** 31 de diciembre de 2025  
**Asunto:** Priorización rentable de contactos de retención a 90 días y causas principales de cancelación  

---

## 1. Resumen Ejecutivo

Durante 2025, la tasa de cancelación a 90 días (`churn_90d`) en **Conecta+** mostró un crecimiento sostenido: pasó de **10.72%** en el corte de marzo (154 bajas de 1,436 clientes) a **12.68%** en junio (209 de 1,648) y alcanzó **14.87%** en septiembre (267 de 1,795), acumulando un promedio de **12.91%** (630 bajas en 4,879 evaluaciones).

Con el modelo predictivo —que integra uso de la aplicación, historial de facturación, tickets de soporte y señales de texto de atención— evaluamos a los **1,611 clientes activos** al cierre del 31 de diciembre de 2025 para definir **a quién contactar de forma rentable** y entender **qué está impulsando las salidas**:

* **Decisión de contacto:** Se recomienda contactar a **416 clientes (25.82% del total)** y no llamar al **74.18%** restante, donde el costo de gestión supera el retorno esperado.
* **Impacto financiero proyectado:** La campaña estima un **beneficio neto total de 8,695,294 COP** (un promedio de **20,902 COP** netos por cliente contactado), superando en **+633,650 COP (+7.86%)** y **+14 clientes** priorizados a la versión previa sin señales de texto de soporte.

---

## 2. ¿A Quién Debemos Contactar? (Criterio de Rentabilidad)

Cada gestión de retención tiene un costo operativo de **25,000 COP**, se asume una efectividad del **40%** para retener a un cliente en riesgo y el valor salvado equivale a **12 meses** de su mensualidad vigente:

| Plan Vigente | Mensualidad (COP) | Valor Retenido 12 Meses (COP) |
| :--- | :---: | :---: |
| **Básico** | $23,900 | $286,800 |
| **Estándar** | $34,900 | $418,800 |
| **Premium** | $49,900 | $598,800 |
| **Familiar** | $59,900 | $718,800 |

En lugar de aplicar un umbral rígido igual para toda la base, solo se marca `contactar = 1` cuando el beneficio esperado de retener al cliente supera los **25,000 COP** de costo. De esta forma, los planes de mayor ingreso justifican actuar con probabilidades de fuga más moderadas que un plan básico, maximizando el margen neto de la campaña.

---

## 3. ¿Por Qué Se Están Yendo? (Hallazgos Clave)

1. **Caída de uso y fricción técnica en Android:**
   * La reducción de actividad reciente (`eventos_30d`) y el aumento de errores en los últimos 30 días (`errores_30d`) son motores centrales de cancelación en el modelo.
   * En los registros técnicos, **Android 5.4.0** concentra una tasa de error del **51.10%** (15,820 eventos en 704 clientes), muy por encima de **Android 5.3.3 (2.31%)**, **iOS 5.3.3 (2.10%)** y **Web (1.98%)**.
   * Aunque en el acumulado general **Android 5.3.3** muestra mayor `churn_90d` (**15.93%** en 2,172 evaluaciones) que **Android 5.4.0** (**11.36%** en 484 evaluaciones), al desagregar por fecha de corte se comprueba que en **`2025-09-30`** **Android 5.4.0** alcanza **`19.78%`** (273 evaluaciones) frente a **`19.71%`** en **Android 5.3.3** (685 evaluaciones), tras haber tenido baja presencia en marzo (`0.00%`) y junio (`0.70%`). Adicionalmente, **Android 5.3.3** concentra **`19.75%`** de bajas por fricción voluntaria y **`18.65%`** de `cambio_plan`.

2. **Alertas tempranas en tickets de soporte:**
   * Los reportes asociados a **lentitud**, recurrencia de atención y menciones de **cancelación** anticipan la salida antes de que el cliente formalice su baja.

3. **Facturación e impagos al corte:**
   * Acumular facturas impagas al momento del corte (`impagas_al_corte_count`) incrementa la probabilidad de cancelación. Entre los medios de pago, las tasas de factura impaga oscilan entre **3.84% y 4.27%** (**débito automático** en **4.27%**, **efectivo** en **3.90%**, **tarjeta** en **3.88%** y **PSE** en **3.84%**, con montos promedio cercanos a $37,113–$37,600 COP). Al analizar los datos, se diferenció el saldo impago puntual al corte del histórico acumulado de facturas (`11.58%` de churn en historial con impago vs. `13.76%` sin impago) para evitar lecturas sesgadas.

4. **Intervención reactiva actual y separación de `cambio_plan`:**
   * De los **892 clientes** marcados como contactados por retención en el histórico, **845 (94.73%)** ya figuraban inactivos, con un `churn_90d` del **47.00%** (832 evaluaciones) frente al **5.91%** (4,047 evaluaciones) en no contactados, evidenciando que la gestión actual actúa de forma reactiva o tardía.
   * En los motivos de cierre, **`cambio_plan`** (781 evaluaciones; **10.93%** en la distribución general de suscripciones) registra un `churn_90d` de **`10.50%`**, muy por debajo de los motivos de fricción o baja real: **`facturacion` (`62.50%`)**, **`competencia` (`62.39%`)**, **`precio` (`60.26%`)**, **`calidad_servicio` (`56.25%`)**, **`no_lo_usa` (`56.19%`)**, **`problemas_tecnicos` (`53.39%`)** y **`otro` (`52.50%`)**. Separar `cambio_plan` evita sobreestimar la fuga voluntaria.

---

## 4. Acciones Recomendadas

* **Orientación preventiva al usuario:** Informar y guiar a los clientes —especialmente en **Android 5.4.0** y **Android 5.3.3**— sobre cómo su versión de sistema operativo o aplicación y los reportes de lentitud pueden afectar su experiencia, acompañando esa orientación con la gestión comercial.
* **Despliegue de campaña anticipada:** Ejecutar el contacto preventivo sobre los **416 clientes** priorizados (`contactar = 1`) en `predicciones.csv` antes de que se deteriore su uso o acumulen impagos.
* **Gobierno de métricas y monitoreo:** Reportar por separado `cambio_plan` de las bajas por fricción, mantener controles temporales estrictos para evitar fuga de información en futuros reentrenamientos y monitorear periódicamente la tasa real de retención alcanzada frente al **40%** proyectado.
