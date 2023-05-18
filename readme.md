# jasdhajkhcqj
## hgjgg

gshgwdhgchgghcgha

```sql
WITH Producto_ciudad AS (
SELECT COUNT(*) AS cantidad, com.producto AS cp
FROM `Is_2023_project.Compras` com
INNER JOIN `Is_2023_project.Datos_clientes` cli ON com.cliente = cli.Numero
WHERE cli.Ciudad_de_Nacimiento = 'Barranquilla'
GROUP BY com.producto
ORDER BY cantidad DESC)

SELECT pro.Producto, pc.cantidad
FROM Producto_ciudad pc 
INNER JOIN `Is_2023_project.Productos` pro ON pro.Codigo = pc.cp
ORDER BY pc.cantidad DESC

```