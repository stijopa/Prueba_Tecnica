# MEMO EJECUTIVO — CONECTA+

**Para:** Direcciones de Producto, Operación y Retención  
**De:** Equipo de Analítica y Ciencia de Datos  
**Fecha de corte de los datos:** 31 de diciembre de 2025  
**Asunto:** Priorización rentable de contactos de retención a 90 días y causas principales de cancelación  

---

## 1. Resumen Ejecutivo

Durante 2025 la tasa de cancelación a 90 días (`churn_90d`) en **Conecta+** mostró un ascenso sostenido, pasando de **10.72%** en el corte del 31 de marzo de 2025 (1,436 registros) a **12.68%** en el 30 de junio de 2025 (1,648 registros) y **14.87%** en el 30 de septiembre de 2025 (1,795 registros). A partir del modelo predictivo calibrado, evaluamos a los **1,611 clientes activos** al cierre del 31 de diciembre de 2025 para definir **a quién contactar de forma rentable** e identificar **qué está impulsando las salidas**.

* **Decisión de contacto:** Se prioriza contactar únicamente a los clientes cuyo beneficio esperado supera el costo de gestión, evitando invertir recursos donde el retorno no compensa el esfuerzo.
* **Impacto de negocio:** Enfocar el contacto en los casos rentables permite proteger ingresos recurrentes a 12 meses sin saturar al equipo de retención con cuentas de bajo retorno.

---

## 2. ¿A Quién Debemos Contactar? (Criterio de Rentabilidad)

Cada gestión de retención tiene un costo de **25,000 COP**, se estima una efectividad del **40%** para retener a un cliente que iba a cancelar y el valor salvado corresponde a **12 meses** de su mensualidad vigente (**Básico** $23,900, **Estándar** $34,900, **Premium** $49,900 o **Familiar** $59,900 COP).

En lugar de aplicar un corte fijo igual para todos, la decisión de contactar (`contactar = 1`) se activa solo cuando el beneficio esperado de retener al cliente supera los **25,000 COP** de costo. De este modo, los planes de mayor mensualidad justifican actuar con probabilidades de fuga más moderadas que un plan básico, cuidando el margen de toda la campaña.

---

## 3. ¿Por Qué Se Están Yendo? (Hallazgos Clave de los Datos)

1. **Fuga creciente en 2025 y mayor exposición en Android:** La cancelación a 90 días subió **4.15 puntos porcentuales** entre el primer corte de 2025 (**10.72%**) y el tercero (**14.87%**). Al observar la plataforma principal al corte, **Android** registra la mayor tasa de `churn_90d` (**15.19%** sobre 2,659 filas / 1,181 clientes), por encima de **iOS** (**10.48%** sobre 1,785 filas / 795 clientes) y **Web** (**8.97%** sobre 435 filas / 193 clientes).
2. **Alta tasa de error en Android 5.4.0:** En los registros de eventos, **Android 5.4.0** presenta una tasa de error del **51.10%** (15,820 eventos en 704 clientes), muy superior a **Android 5.3.3** (**2.31%** en 42,448 eventos / 1,232 clientes), **iOS 5.3.3** (**2.10%** en 39,152 eventos / 879 clientes) y **Web** (**1.98%** en 10,392 eventos / 220 clientes).
3. **Facturas impagas y presión de cobro:** Entre los métodos de pago, las tasas de factura impaga son similares y moderadas —**débito automático** **4.27%** (monto promedio $37,355 COP), **efectivo** **3.90%** (~$37,113 COP), **tarjeta** **3.88%** (~$37,268 COP) y **PSE** **3.84%** ($37,600 COP)—. Sin embargo, al observar el estado al corte en el conjunto de entrenamiento, los clientes con facturas impagas (`tiene_impago = 1`, 1,461 filas) muestran un `churn_90d` de **15.74%** frente a **11.70%** en quienes no registran impagos (`0`, 3,418 filas).
4. **Contacto de retención concentrado en casos críticos:** Quienes figuran contactados por retención hasta el corte (`contactado_retencion_hasta_corte = 1`, 74 filas / 63 clientes) presentan un `churn_90d` de **64.86%**, frente a **12.11%** en los no contactados (`0`, 4,805 filas / 2,138 clientes), lo que refuerza la necesidad de actuar de forma preventiva antes de que el deterioro sea irreversible.
5. **Diferenciar `cambio_plan` de las cancelaciones reales:** De **3,368 suscripciones**, **1,611 (47.83%)** no tienen motivo registrado (`sin_cancelacion_o_None`). Entre los motivos visibles, el más frecuente es **`cambio_plan`** con **368 casos (10.93%)**, seguido de **`problemas_tecnicos`** (**280 · 8.31%**), **`calidad_servicio`** (**229 · 6.80%**), **`otro`** (**204 · 6.06%**), **`no_lo_usa`** (**188 · 5.58%**), **`facturacion`** (**181 · 5.37%**), **`competencia`** (**170 · 5.05%**) y **`precio`** (**137 · 4.07%**). En el cruce con `churn_90d`, **`cambio_plan`** muestra apenas **10.50%** de cancelación a 90 días (781 filas), mientras que los motivos asociados a fricción superan el **52%–62.5%** (**facturación** **62.50%**, **competencia** **62.39%**, **precio** **60.26%**, **calidad de servicio** **56.25%**, **no lo usa** **56.19%**, **problemas técnicos** **53.39%** y **otro** **52.50%**).

---

## 4. Recomendaciones Inmediatas

* **Priorizar la gestión comercial rentable:** Concentrar las llamadas de retención en los clientes con beneficio esperado positivo bajo los supuestos de **25,000 COP**, **40%** de efectividad y **12 meses** de valor por plan.
* **Informar y orientar a los usuarios sobre su versión y reportes de lentitud:** Guiar a los clientes —especialmente en **Android 5.4.0** y **Android 5.3.3**— sobre cómo su versión de sistema operativo/aplicación o los reportes de lentitud pueden incidir en su experiencia, acompañando esa orientación con la gestión comercial.
* **Actuar antes del deterioro y separar `cambio_plan`:** Adelantar el contacto preventivo en clientes con señales de fricción o facturas impagas al corte, y reportar **`cambio_plan`** por separado de las bajas definitivas para medir con precisión la pérdida real de clientes.
