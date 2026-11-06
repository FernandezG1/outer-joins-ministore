# 🛒 MiniStore — Análisis de Inventario y Ventas con Outer JOINs

Repositorio creado para resolver la práctica integradora de consultas SQL utilizando uniones externas (`LEFT JOIN`, `RIGHT JOIN` y simulación de `FULL OUTER JOIN`) en el contexto del negocio minorista **MiniStore**.

---

## 📌 Preguntas de Negocio y Decisiones Metodológicas

### 1. ¿Por qué usaste `LEFT JOIN` para la Consulta 1 y no un `INNER JOIN`? ¿Qué se perdería si usaras `INNER JOIN`?
* **Motivo:** La pregunta de negocio exige detectar *"qué productos del catálogo nunca fueron vendidos"*. 
* **Qué pasaría con `INNER JOIN`:** Este tipo de unión solo devuelve los registros que tienen una **coincidencia exacta** en ambas tablas. Si lo usáramos, los productos sin ventas (como el Hub USB-C o el Parlante Bluetooth) desaparecerían por completo del resultado porque no tienen transacciones registradas. Con el `LEFT JOIN`, preservamos el 100% de la tabla de la izquierda (`productos`), permitiendo que aquellos sin ventas queden visibles con valores nulos en el lado derecho.

---

### 2. ¿Por qué usaste `RIGHT JOIN` para la Consulta 2? ¿Qué tabla está a la izquierda y cuál a la derecha en tu consulta?
* **Motivo:** La consigna busca identificar si existen ventas registradas con productos que **no figuran** en el catálogo (como la venta huérfana con `producto_id = 999`).
* **Estructura:** En nuestra consulta pusimos a `productos` a la izquierda y a `ventas` a la derecha mediante un `RIGHT JOIN`. De este modo, garantizamos que todas las filas de la tabla `ventas` aparezcan sí o sí. Aquella venta cuyo ID no encuentre un producto asociado en el catálogo quedará con valores `NULL` en las columnas del producto.

---

### 3. ¿Qué representan los valores `NULL` en cada resultado?
* **En el `LEFT JOIN` (Consulta 1):** Un valor `NULL` en las columnas de ventas (como `v.venta_id IS NULL`) significa que ese producto específico del catálogo **nunca registró una transacción de venta**.
* **En el `RIGHT JOIN` (Consulta 2):** Un valor `NULL` en las columnas de la tabla productos (como `p.producto_id IS NULL`) representa un **registro huérfano o error de carga**, indicando que se registró una venta de un producto que no existe en el inventario actual.

---

### 4. ¿Cuándo usarías `FULL OUTER JOIN` en un caso real de negocio?
* Se utiliza principalmente en **auditorías de integridad de datos**. Un caso real típico es cruzar bases de datos de dos sistemas diferentes (por ejemplo, el sistema de facturación y el sistema de inventario físico) para detectar simultáneamente:
  1. Productos que figuran en el inventario pero jamás se facturaron (potencial stock obsoleto).
  2. Facturas emitidas de productos que no figuran en el inventario (posibles errores de registro, robos o fallas de stock).
* *Nota técnica:* Como MySQL no soporta `FULL OUTER JOIN` de forma directa, se simuló combinando un `LEFT JOIN` y un `RIGHT JOIN` mediante la cláusula `UNION`.
