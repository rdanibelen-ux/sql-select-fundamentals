# UNION y UNION ALL: Consolidando inventario de múltiples sucursales — RetailChain

Este laboratorio contiene el análisis técnico y las estructuras SQL orientadas a consolidar los registros de inventario de las sucursales Norte y Sur.

## 📊 Respuestas al Cuestionario Técnico

### 1. ¿Cuántas filas devuelve cada consulta y por qué son distintas?
* **Consulta 1 (UNION):** Devuelve **11 filas**. Al consolidar el catálogo unificado de productos con `UNION`, el motor compara las columnas seleccionadas (`id_producto`, `nombre_producto`, `categoria`) y elimina los registros duplicados existentes entre ambas sucursales.
  * *Ejemplo real:* Los productos Monitor 4K (103), Teclado Mecánico (104) y SSD Externo (106) se repiten en ambos inventarios base, por lo que se unifican en filas únicas. La Webcam HD se mantiene duplicada debido a que posee identificadores distintos en cada sede (107 y 111).
* **Consulta 2 (UNION ALL):** Devuelve **14 filas**. Al incluir la columna `stock` para auditoría operativa y aplicar `UNION ALL`, el sistema ejecuta una combinación vertical directa sin validación de duplicados. Concatena la totalidad física de los registros (7 de la Sucursal Norte y 7 de la Sucursal Sur).

### 2. ¿Por qué UNION ALL es más eficiente que UNION? ¿Qué operación adicional realiza internamente?
**UNION ALL** es óptimo en rendimiento debido a que no inspecciona el contenido de las filas; simplemente las acopla secuencialmente. 

Por el contrario, **UNION** obliga al motor relacional a ejecutar procesos adicionales en memoria:
1. **Sort (Ordenamiento):** Organiza el set completo de datos de manera temporal.
2. **Distinct Scan (Filtrado de duplicados):** Ejecuta una comparación lineal para remover filas idénticas. En ambientes productivos con grandes volúmenes de datos, este comportamiento degrada severamente el rendimiento del servidor.

### 3. ¿En qué casos de negocio usarías cada uno? (Ejemplos reales)
* **Usaría UNION para:**
  1. **Consolidación de Leads de Marketing:** Unificar bases de datos de suscriptores de un blog y registros de un webinar para lanzar una campaña publicitaria limpia, impidiendo que un usuario reciba el mismo correo dos veces.
  2. **Directorio Unificado de Proveedores:** Cruzar catálogos de proveedores de diferentes filiales corporativas para obtener un listado maestro único de distribución.
* **Usaría UNION ALL para:**
  1. **Auditorías Contables Transaccionales:** Unificar libros diarios de ventas mensuales. Emplear `UNION` provocaría la pérdida de transacciones legítimas si dos clientes diferentes compraran idénticos productos por el mismo monto el mismo día.
  2. **Consolidación de Logs del Servidor:** Centralizar registros de errores e interacciones de aplicaciones móviles y web para computar métricas totales de tráfico.

### 4. ¿Qué pasa si las columnas de ambas consultas no coinciden en número o tipo?
Si las estructuras de los bloques `SELECT` no son idénticas en cantidad y correspondencia de tipos de datos de izquierda a derecha, SQL abortará la ejecución por un error de sintaxis y compatibilidad estructural.
* Si el número de columnas no coincide, se arrojará un error similar a: *"Each UNION query must have the same number of columns"*.
* Si difieren en tipos incompatibles en la misma posición ordinal (ej. mapear un `INT` contra un `VARCHAR`), se generará un fallo de casteo: *"Conversion failed when converting the varchar value to data type int"*.
