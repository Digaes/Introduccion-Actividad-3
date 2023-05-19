

---

## 1) ¿Cuál es el producto más vendido en una ubicación geográfica específica?
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
Este analisis realiza una consulta a tres tablas:
**"Compras"**, **"Datos** **clientes"** y **"Productos"**. 
Se realiza una subconsulta llamada **"Producto_ciudad"** la cual cuenta la cantidad de compras por producto para aquellos clientes nacidos en Barranquilla, ordenando los resultados de manera descendente. 

Posteriormente se unen los resultados de **"Producto_ciudad"** con la tabla **"Productos"** usando el codigo de producto como clave de union. Por ultimo, se ordenan los resultados de manera descendente. 
Como resultado obtenemos una lista de productos con la cantidad de compras realizadas por los clientes nacidos en Barranquilla. 

---
## 2) ¿Cuántos clientes han comprado productos de un rango de precios específico?
```sql
WITH cantidad AS(
SELECT count (*) as cantidad, com.cliente
FROM `Is_2023_project.Compras` com
INNER JOIN `Is_2023_project.Productos` pro ON pro.Codigo = com.producto
WHERE pro.Precio > 500 and pro.Precio < 5000
GROUP BY com.cliente
ORDER BY com.cliente)
SELECT count(*) AS Resultado
from cantidad 
```
En este analisis se realizo una consulta a dos tablas: **"Compras"** y **"Productos"**. Tenemos la subconsulta **"Cantidad"** la cual, cuenta la cantidad de compras realizadas por cada cliente que cumple con la condicion que el precio del producto sea mayor a 500 y menor a 5.000, los resultados se agrupan y ordenan por cliente.

En la consulta principal, se cuentan la cantidad de registros en la subconsulta **"Cantidad"**, la cual proporciona el numero total de clientes que han realizado compras dentro del rango seleccionado.

Como resultado se obtuvo la cantidad de clientes que cumplen con los criterios de precio establecidos. 

---
## 3) ¿Cuáles son los clientes más frecuentes en realizar compras y cuánto han gastado en total?
```sql
WITH cantidad_clientes AS(
SELECT count (*) as cantidad, sum(pro.Precio) as valor, com.cliente as client
FROM `Is_2023_project.Compras` com
INNER JOIN `Is_2023_project.Productos` pro ON pro.Codigo = com.producto
GROUP BY com.cliente
ORDER BY cantidad DESC LIMIT 10)

SELECT cli.Nombre_1, cli.Nombre_2, cli.Apellidos, cant.cantidad, cant.valor
FROM cantidad_clientes cant
INNER JOIN `Is_2023_project.Datos_clientes` cli ON cli.Numero = cant.client
```
En este analisis se realizo una consulta a dos tablas: **"Compras"** y "**Productos"**. 

En primer lugar tenemos la subconsulta **"Cantidad_clientes"** el cual, cuenta la cantidad de compras realizadas por cada clientes y suma el valor total de esas compras. Se agrupan los resultados por cliente y se ordenan de manera descendente ademas de limitar los resultados a los 10 clientes por compras. 

En la consulta principal, se realiza una union entre los resultados de **"Cantidad_clientes"** y la tabla **"Datos_Clientes"** usando el nuemero de clientes como clave de union. 

Como resultado final se obtuvo una lista de los 10 clientes con mas compras, mostrando sus nombres, apellidos, la cantidad de compras realizadas y el valor total de esas compras. 

---
## 4) ¿Cuál es el producto más vendido en cada ubicación geográfica?
```sql
WITH cant_dep as (
SELECT COUNT(*) AS cantidad, cli.Departamento as dp, com.producto AS producto
FROM `Is_2023_project.Compras` com
INNER JOIN `Is_2023_project.Datos_clientes` cli ON com.cliente = cli.Numero
GROUP BY cli.Departamento, com.producto
ORDER BY cli.Departamento, cantidad desc), 

cant_dep_max as (
SELECT cd.dp as depa, MAX(cd.cantidad) AS maxi
FROM cant_dep cd
GROUP BY cd.dp),

pro_dep_max as (
SELECT cd.dp,cd.producto,cd.cantidad
FROM cant_dep_max cdm
INNER JOIN cant_dep cd ON (cd.dp = cdm.depa AND cd.cantidad = cdm.maxi))

SELECT pdm.dp, pro.Producto, pdm.cantidad
FROM pro_dep_max pdm
INNER JOIN `Is_2023_project.Productos` pro ON pro.Codigo = pdm.producto
```
En este analisis, se realiza una consulta a dos tablas: **"Compras"** y **"Datos_clientes"**. La subconsulta **"Cant_dep"** cuenta la cantidad de compras realizadas por cada departamento y por producto. 

En segundo lugar tenemos la sub consulta **"Cant_dep_max"** la cual, encuentra el maximo valor de cantidad para cada departamento. 
La subconsulta **"Pro_dep_max"** se une con **"Cant_dep"** y **"Cant_dep_max"** para obtenr el producto correspondiente al maximo valor de cantidad por departamento.

Por ultimo, en la consulta principal, se realiza una union entre **"Pro_dep_max"** y la tabla **"Productos"** utilizando el codigo producto como clave de union. Lo cual nos da como resultado, el nombre del producto correspondiente. 

Como resultado final obtenemos una lista de los departamentos, los productos con la mayor cantidad de compras por departamento y la contidad correspondiente. 

---
## 5) ¿Cuáles son los clientes que han comprado todos los productos disponibles?
```sql
WITH cli_pro_uni AS (
SELECT DISTINCT com.cliente as cli, com.producto as pro 
FROM `Is_2023_project.Compras` as com
order by com.cliente, com.producto ),

cli_can as (
SELECT cpu.cli as client, count(*) as can_pro
FROM cli_pro_uni cpu
GROUP BY cpu.cli )

SELECT cli.Nombre_1, cli.Nombre_2, cli.Apellidos, cc.can_pro as cantidad
FROM cli_can cc
INNER JOIN `Is_2023_project.Datos_clientes` as cli ON cli.Numero = cc.client
ORDER BY cc.can_pro DESC
LIMIT 10
```
En este analisis, se realiza una consulta a la tabla **"Compras"**.

En primer lugar se crea la subconsulta, **"Cli_pro_uni"** la cual selecciona de forma distintiva cada combinacion de clientes y productos presentes en la tabla **"Compras"**, los cuales son ordenados por cliente y productos.

La subconsulta "**CLi_can"** cuenta la cantidad de productos distintos comprados por cada cliente ultilizando los resultados de "**Cli_pro_uni"**. Los cuales se agrupan por cliente y se cuentan el numero de productos distintos. 

En la consulta principal, se realizo una union entre **"Cli_can"** y la tabla **"Datos_clientes"** la cual utiliza el numero de clientes como clave de union. Esto nos permite obtener informacion adicional sobre los clientes, como sus nombre y apellidos. 

Como resultado final, obtenemos una lista de los 10 clientes con la mayor cantidad de productos distintos comprados, mostrando sus nombres, apellidos y la cantidad de productos distintos. 

**Como podemos observar, no existe un cliente que haya comprado todos los productos disponibles, la cantidad maxima de productos comprados son 10.** 

----
## Dashboard
[Lookerstudio.com](https://lookerstudio.google.com/u/0/reporting/d378f783-3b16-40aa-b43d-3630a37a1f41/page/dCxQD/edit)