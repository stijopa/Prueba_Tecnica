# MEMO EJECUTIVO — CONECTA+

**Para:** Direcciones de Producto, Operación y Retención  
**De:** Equipo de Analítica y Ciencia de Datos  
**Fecha de corte de los datos:** 31 de diciembre de 2025  
**Asunto:** Priorización rentable de contactos de retención a 90 días y causas principales de cancelación  

---

### 1. Resumen ejecutivo
Durante 2025 la tasa de cancelación a 90 días en **Conecta+** mostró un ascenso sostenido (pasando de **10.72%** en el corte de marzo a **12.68%** en junio y **14.87%** en septiembre). Con el modelo predictivo (que integra comportamiento de uso, facturación, soporte y señales de texto de tickets) evaluamos a los **1,611 clientes activos** al cierre del 31 de diciembre de 2025 para definir **a quién contactar de forma rentable** e identificar **qué está impulsando las salidas**.

- **Decisión de contacto:** Se recomienda contactar a **416 clientes (25.82% del total)** y no llamar al **74.18%** restante donde el costo del contacto supera el retorno esperado.
- **Impacto financiero proyectado:** La campaña estima un **beneficio neto total de 8,695,294 COP** (unos **20,902 COP** netos por cliente contactado), superando en **+633,650 COP (+7.86%)** y **+14 clientes** a la versión previa sin señales de texto de soporte.

---

### 2. ¿A quién debemos contactar? (Criterio de Rentabilidad)
Cada llamada o gestión de retención tiene un costo de **25,000 COP**, se asume una efectividad del **40%** para retener a un cliente que iba a cancelar y el valor salvado equivale a **12 meses** de su mensualidad vigente (Básico \$23,900, Estándar \$34,900, Premium \$49,900 o Familiar \$59,900 COP).

En lugar de aplicar una regla rígida igual para todos, solo se marca `contactar = 1` cuando el beneficio esperado de retener al cliente supera los **25,000 COP** de costo. Así, un plan mayor justifica actuar con probabilidades de fuga más moderadas que un plan básico, protegiendo el margen de la campaña.

---

### 3. ¿Por qué se están yendo? (Hallazgos claves)
1. **Caída de uso y fallos técnicos en Android:** Menor actividad reciente y mayor volumen de errores en los últimos 30 días son las señales más fuertes de riesgo. En los registros técnicos, **Android 5.4.0** presenta una tasa de error del **51.10%** (15,820 eventos en 704 clientes), muy por encima de **Android 5.3.3 (2.31%)**, **iOS 5.3.3 (2.10%)** y **Web (1.98%)**. A su vez, **Android 5.3.3** registra la mayor tasa de cancelación a 90 días (**15.93%**), seguida por **Android 5.4.0 (11.36%)**.
2. **Señales tempranas en tickets de soporte:** Los tickets asociados a **lentitud**, temas recurrentes de atención y menciones de **cancelación** aportan alertas valiosas antes de que el cliente se dé de baja.
3. **Facturas impagas:** Acumular facturas sin pagar al corte eleva el riesgo de salida. Entre los medios de pago, las tasas de impago son similares (alrededor de **3.84% a 4.27%**, con **débito automático** en **4.27%** y montos promedio cercanos a \$37,100–\$37,600 COP).
4. **Intervención tardía y movimientos internos (`cambio_plan`):** De los **892 clientes** marcados como contactados por retención en el histórico, **845 (94.73%)** figuran inactivos (con **47.00%** de cancelación a 90 días frente a **5.91%** en no contactados), lo que indica que suele llamarse tarde. Además, entre los motivos registrados de cierre, **`cambio_plan`** es el más frecuente (**368 casos, 10.93%**), por lo que conviene separarlo de las bajas voluntarias reales para no inflar la pérdida de clientes.

---

### 4. Recomendaciones inmediatas
- **Ejecutar la campaña preventiva sobre los 416 clientes priorizados** (`predicciones.csv`) antes de que caigan en inactividad avanzada o mayor mora.
- **Informar y guiar a los clientes sobre el impacto de su versión de sistema operativo y los casos de lentitud reportados en soporte en paralelo a la gestión comercial.**
- **Adelantar el contacto a señales tempranas de riesgo** y **distinguir `cambio_plan` de las cancelaciones definitivas** en los reportes de seguimiento de retención.
