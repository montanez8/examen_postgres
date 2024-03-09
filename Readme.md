## Consultas sobre una tabla

1.Devuelve un listado con todos los pedidos que se han realizado. Los pedidos deben estar ordenados por la fecha de realización, mostrando en primer lugar los pedidos más recientes.
```sql
SELECT * FROM pedido ORDER BY fecha DESC;

```
2. Devuelve todos los datos de los dos pedidos de mayor valor.
```sql

SELECT * FROM pedido ORDER BY total DESC LIMIT 2;
```
3. Devuelve un listado con los identificadores de los clientes que han realizado algún pedido. Tenga en cuenta que no debe mostrar identificadores que estén repetidos.
```sql
SELECT DISTINCT id_cliente FROM pedido;

```
4. Devuelve un listado de todos los pedidos que se realizaron durante el año 2017, cuya cantidad total sea superior a 500€.
```sql
SELECT * FROM pedido WHERE fecha BETWEEN '2017-01-01' AND '2017-12-31' AND total > 500;

```
5. Devuelve un listado con el nombre y los apellidos de los comerciales que tienen una comisión entre 0.05 y 0.11.
```sql
SELECT nombre, apellido1, apellido2 FROM comercial WHERE comisión BETWEEN 0.05 AND 0.11;

```
6. Devuelve el valor de la comisión de mayor valor que existe en la tabla `comercial`.
```sql

SELECT MAX(comisión) FROM comercial;
```
7. Devuelve el identificador, nombre y primer apellido de aquellos clientes cuyo segundo apellido **no** es `NULL`. El listado deberá estar ordenado alfabéticamente por apellidos y nombre.
```sql
SELECT id, nombre, apellido1 FROM cliente WHERE apellido2 IS NOT NULL ORDER BY apellido1, nombre;

```
8. Devuelve un listado de los nombres de los clientes que empiezan por `A` y terminan por `n` y también los nombres que empiezan por `P`. El listado deberá estar ordenado alfabéticamente.
```sql
SELECT nombre FROM cliente WHERE nombre LIKE 'A%n' OR nombre LIKE 'P%' ORDER BY nombre;

```
9. Devuelve un listado de los nombres de los clientes que **no** empiezan por `A`. El listado deberá estar ordenado alfabéticamente.
```sql
SELECT nombre FROM cliente WHERE nombre NOT LIKE 'A%' ORDER BY nombre;

```
10. Devuelve un listado con los nombres de los comerciales que terminan por `el` o `o`. Tenga en cuenta que se deberán eliminar los nombres repetidos.
```sql
SELECT DISTINCT nombre FROM comercial WHERE nombre LIKE '%el' OR nombre LIKE '%o';

```

## Consultas multitabla (Composición interna)
1. Devuelve un listado con el identificador, nombre y los apellidos de todos los clientes que han realizado algún pedido. El listado debe estar ordenado alfabéticamente y se deben eliminar los elementos repetidos.
```sql
SELECT DISTINCT cliente.id, cliente.nombre, cliente.apellido1, cliente.apellido2 
FROM cliente 
JOIN pedido ON cliente.id = pedido.id_cliente 
ORDER BY cliente.nombre, cliente.apellido1, cliente.apellido2;
```
2. Devuelve un listado que muestre todos los pedidos que ha realizado cada cliente. El resultado debe mostrar todos los datos de los pedidos y del cliente. El listado debe mostrar los datos de los clientes ordenados alfabéticamente.
```sql
SELECT cliente.*, pedido.* 
FROM cliente 
JOIN pedido ON cliente.id = pedido.id_cliente 
ORDER BY cliente.nombre, cliente.apellido1, cliente.apellido2;
```
3. Devuelve un listado que muestre todos los pedidos en los que ha participado un comercial. El resultado debe mostrar todos los datos de los pedidos y de los comerciales. El listado debe mostrar los datos de los comerciales ordenados alfabéticamente.
```sql
SELECT comercial.*, pedido.* 
FROM comercial 
JOIN pedido ON comercial.id = pedido.id_comercial 
ORDER BY comercial.nombre, comercial.apellido1, comercial.apellido2;
```
4. Devuelve un listado que muestre todos los clientes, con todos los pedidos que han realizado y con los datos de los comerciales asociados a cada pedido.
```sql
SELECT cliente.*, pedido.*, comercial.* 
FROM cliente 
JOIN pedido ON cliente.id = pedido.id_cliente 
JOIN comercial ON pedido.id_comercial = comercial.id;
```
5. Devuelve un listado de todos los clientes que realizaron un pedido durante el año `2017`, cuya cantidad esté entre `300` € y `1000` €.
```sql
SELECT cliente.* 
FROM cliente 
JOIN pedido ON cliente.id = pedido.id_cliente 
WHERE pedido.fecha BETWEEN '2017-01-01' AND '2017-12-31' AND pedido.total BETWEEN 300 AND 1000;
```
6. Devuelve el nombre y los apellidos de todos los comerciales que ha participado en algún pedido realizado por `María Santana Moreno`.
```sql
SELECT DISTINCT comercial.nombre, comercial.apellido1, comercial.apellido2 
FROM comercial 
JOIN pedido ON comercial.id = pedido.id_comercial 
JOIN cliente ON pedido.id_cliente = cliente.id 
WHERE cliente.nombre = 'María' AND cliente.apellido1 = 'Santana' AND cliente.apellido2 = 'Moreno';
```
7. Devuelve el nombre de todos los clientes que han realizado algún pedido con el comercial `Daniel Sáez Vega`.
```sql
SELECT DISTINCT cliente.nombre 
FROM cliente 
JOIN pedido ON cliente.id = pedido.id_cliente 
JOIN comercial ON pedido.id_comercial = comercial.id 
WHERE comercial.nombre = 'Daniel' AND comercial.apellido1 = 'Sáez' AND comercial.apellido2 = 'Vega';
```

## Consultas multitabla (Composición externa)
1. Devuelve un listado con **todos los clientes** junto con los datos de los pedidos que han realizado. Este listado también debe incluir los clientes que no han realizado ningún pedido. El listado debe estar ordenado alfabéticamente por el primer apellido, segundo apellido y nombre de los clientes.
```sql
SELECT cliente.*, pedido.*
FROM cliente
LEFT JOIN pedido ON cliente.id = pedido.id_cliente
ORDER BY cliente.apellido1, cliente.apellido2, cliente.nombre;

```
2. Devuelve un listado con **todos los comerciales** junto con los datos de los pedidos que han realizado. Este listado también debe incluir los comerciales que no han realizado ningún pedido. El listado debe estar ordenado alfabéticamente por el primer apellido, segundo apellido y nombre de los comerciales.
```sql
SELECT comercial.*, pedido.*
FROM comercial
LEFT JOIN pedido ON comercial.id = pedido.id_comercial
ORDER BY comercial.apellido1, comercial.apellido2, comercial.nombre;
```
3. Devuelve un listado que solamente muestre los clientes que no han realizado ningún pedido.
```sql
SELECT cliente.*
FROM cliente
LEFT JOIN pedido ON cliente.id = pedido.id_cliente
WHERE pedido.id IS NULL;
```
4. Devuelve un listado que solamente muestre los comerciales que no han realizado ningún pedido.
```sql
SELECT comercial.*
FROM comercial
LEFT JOIN pedido ON comercial.id = pedido.id_comercial
WHERE pedido.id IS NULL;
```
5. Devuelve un listado con los clientes que no han realizado ningún pedido y de los comerciales que no han participado en ningún pedido. Ordene el listado alfabéticamente por los apellidos y el nombre. En en listado deberá diferenciar de algún modo los clientes y los comerciales.
```sql
(SELECT 'Cliente' AS Tipo, nombre, apellido1, apellido2
FROM cliente
LEFT JOIN pedido ON cliente.id = pedido.id_cliente
WHERE pedido.id IS NULL)
UNION
(SELECT 'Comercial' AS Tipo, nombre, apellido1, apellido2
FROM comercial
LEFT JOIN pedido ON comercial.id = pedido.id_comercial
WHERE pedido.id IS NULL)
ORDER BY apellido1, apellido2, nombre;
```
6. ¿Se podrían realizar las consultas anteriores con `NATURAL LEFT JOIN` o `NATURAL RIGHT JOIN`? Justifique su respuesta.


## Consultas resumen

1. Calcula la cantidad total que suman todos los pedidos que aparecen en la tabla `pedido`.
```sql
SELECT SUM(total) FROM pedido;
```
2. Calcula la cantidad media de todos los pedidos que aparecen en la tabla `pedido`.
```sql
SELECT AVG(total) FROM pedido;
```
3. Calcula el número total de comerciales distintos que aparecen en la tabla `pedido`.
```sql
SELECT COUNT(DISTINCT id_comercial) FROM pedido;
```
4. Calcula el número total de clientes que aparecen en la tabla `cliente`.
```sql
SELECT COUNT(*) FROM cliente;
```
5. Calcula cuál es la mayor cantidad que aparece en la tabla `pedido`.
```sql
SELECT MAX(total) FROM pedido;
```
6. Calcula cuál es la menor cantidad que aparece en la tabla `pedido`.
```sql
SELECT MIN(total) FROM pedido;
```
7. Calcula cuál es el valor máximo de categoría para cada una de las ciudades que aparece en la tabla `cliente`.
```sql
SELECT ciudad, MAX(categoría) FROM cliente GROUP BY ciudad;
```
8. Calcula cuál es el máximo valor de los pedidos realizados durante el mismo día para cada uno de los clientes. Es decir, el mismo cliente puede haber realizado varios pedidos de diferentes cantidades el mismo día. Se pide que se calcule cuál es el pedido de máximo valor para cada uno de los días en los que un cliente ha realizado un pedido. Muestra el identificador del cliente, nombre, apellidos, la fecha y el valor de la cantidad.
```sql
SELECT cliente.id, cliente.nombre, cliente.apellido1, cliente.apellido2, pedido.fecha, MAX(pedido.total)
FROM cliente
JOIN pedido ON cliente.id = pedido.id_cliente
GROUP BY cliente.id, pedido.fecha;
```
9. Calcula cuál es el máximo valor de los pedidos realizados durante el mismo día para cada uno de los clientes, teniendo en cuenta que sólo queremos mostrar aquellos pedidos que superen la cantidad de 2000 €.
```sql
SELECT cliente.id, cliente.nombre, cliente.apellido1, cliente.apellido2, pedido.fecha, MAX(pedido.total)
FROM cliente
JOIN pedido ON cliente.id = pedido.id_cliente
GROUP BY cliente.id, pedido.fecha
HAVING MAX(pedido.total) > 2000;
```
10. Calcula el máximo valor de los pedidos realizados para cada uno de los comerciales durante la fecha `2016-08-17`. Muestra el identificador del comercial, nombre, apellidos y total.
```sql
SELECT comercial.id, comercial.nombre, comercial.apellido1, comercial.apellido2, MAX(pedido.total)
FROM comercial
JOIN pedido ON comercial.id = pedido.id_comercial
WHERE pedido.fecha = '2016-08-17'
GROUP BY comercial.id;
```
11. Devuelve un listado con el identificador de cliente, nombre y apellidos y el número total de pedidos que ha realizado cada uno de clientes. Tenga en cuenta que pueden existir clientes que no han realizado ningún pedido. Estos clientes también deben aparecer en el listado indicando que el número de pedidos realizados es `0`.
```sql
SELECT cliente.id, cliente.nombre, cliente.apellido1, cliente.apellido2, COUNT(pedido.id)
FROM cliente
LEFT JOIN pedido ON cliente.id = pedido.id_cliente
GROUP BY cliente.id;
```
12. Devuelve un listado con el identificador de cliente, nombre y apellidos y el número total de pedidos que ha realizado cada uno de clientes **durante el año 2017**.
```sql
SELECT cliente.id, cliente.nombre, cliente.apellido1, cliente.apellido2, COUNT(pedido.id)
FROM cliente
JOIN pedido ON cliente.id = pedido.id_cliente
WHERE pedido.fecha BETWEEN '2017-01-01' AND '2017-12-31'
GROUP BY cliente.id;
```
13. Devuelve un listado que muestre el identificador de cliente, nombre, primer apellido y el valor de la máxima cantidad del pedido realizado por cada uno de los clientes. El resultado debe mostrar aquellos clientes que no han realizado ningún pedido indicando que la máxima cantidad de sus pedidos realizados es `0`. Puede hacer uso de la función `
```sql
SELECT cliente.id, cliente.nombre, cliente.apellido1, IFNULL(MAX(pedido.total), 0)
FROM cliente
LEFT JOIN pedido ON cliente.id = pedido.id_cliente
GROUP BY cliente.id;
```
14. Devuelve cuál ha sido el pedido de máximo valor que se ha realizado cada año.
```sql
SELECT EXTRACT(YEAR FROM fecha) AS year, MAX(total)
FROM pedido
GROUP BY year;
```
15. Devuelve el número total de pedidos que se han realizado cada año.
```sql
SELECT EXTRACT(YEAR FROM fecha) AS year, COUNT(*)
FROM pedido
GROUP BY year;
```

## Con operadores básicos de comparación 

1. Devuelve un listado con todos los pedidos que ha realizado `Adela Salas Díaz`. (Sin utilizar `INNER JOIN`).
```sql
SELECT *
FROM pedido
WHERE id_cliente = (SELECT id FROM cliente WHERE nombre = 'Adela' AND apellido1 = 'Salas' AND apellido2 = 'Díaz');
```
2. Devuelve el número de pedidos en los que ha participado el comercial `Daniel Sáez Vega`. (Sin utilizar `INNER JOIN`)
```sql
SELECT COUNT(*)
FROM pedido
WHERE id_comercial = (SELECT id FROM comercial WHERE nombre = 'Daniel' AND apellido1 = 'Sáez' AND apellido2 = 'Vega');
```
3. Devuelve los datos del cliente que realizó el pedido más caro en el año `2019`. (Sin utilizar `INNER JOIN`)
```sql
SELECT *
FROM cliente
WHERE id = (SELECT id_cliente
            FROM pedido
            WHERE fecha BETWEEN '2019-01-01' AND '2019-12-31'
            ORDER BY total DESC
            LIMIT 1);
```
4. Devuelve la fecha y la cantidad del pedido de menor valor realizado por el cliente `Pepe Ruiz Santana`.
```sql
SELECT fecha, total
FROM pedido
WHERE id_cliente = (SELECT id FROM cliente WHERE nombre = 'Pepe' AND apellido1 = 'Ruiz' AND apellido2 = 'Santana')
ORDER BY total
LIMIT 1;
```
5. Devuelve un listado con los datos de los clientes y los pedidos, de todos los clientes que han realizado un pedido durante el año `2017` con un valor mayor o igual al valor medio de los pedidos realizados durante ese mismo año.
```sql
SELECT cliente.*, pedido.*
FROM cliente, pedido
WHERE cliente.id = pedido.id_cliente
AND pedido.fecha BETWEEN '2017-01-01' AND '2017-12-31'
AND pedido.total >= (SELECT AVG(total) FROM pedido WHERE fecha BETWEEN '2017-01-01' AND '2017-12-31');
```

## Subconsultas con `ALL` y `ANY`

1. Devuelve el pedido más caro que existe en la tabla `pedido` si hacer uso de `MAX`, `ORDER BY` ni `LIMIT`.
```sql
SELECT *
FROM pedido
WHERE total >= ALL (SELECT total FROM pedido);
```
2. Devuelve un listado de los clientes que no han realizado ningún pedido. (Utilizando `ANY` o `ALL`).
```sql
SELECT *
FROM cliente
WHERE id NOT IN (SELECT id_cliente FROM pedido);
```
3. Devuelve un listado de los comerciales que no han realizado ningún pedido. (Utilizando `ANY` o `ALL`).
```sql
SELECT *
FROM comercial
WHERE id NOT IN (SELECT id_comercial FROM pedido);
```

## Subconsultas con `IN` y `NOT IN`

1. Devuelve un listado de los clientes que no han realizado ningún pedido. (Utilizando `IN` o `NOT IN`).
```sql
SELECT *
FROM cliente
WHERE id NOT IN (SELECT id_cliente FROM pedido);
```
2. Devuelve un listado de los comerciales que no han realizado ningún pedido. (Utilizando `IN` o `NOT IN`).
```sql
SELECT *
FROM comercial
WHERE id NOT IN (SELECT id_comercial FROM pedido);
```

## Subconsultas con `EXISTS` y `NOT EXISTS`

1. Devuelve un listado de los clientes que no han realizado ningún pedido. (Utilizando `EXISTS` o `NOT EXISTS`).
```sql
SELECT *
FROM cliente c
WHERE NOT EXISTS (SELECT 1 FROM pedido p WHERE p.id_cliente = c.id);
```
2. Devuelve un listado de los comerciales que no han realizado ningún pedido. (Utilizando `EXISTS` o `NOT EXISTS`).
```sql
SELECT *
FROM comercial c
WHERE NOT EXISTS (SELECT 1 FROM pedido p WHERE p.id_comercial = c.id);
```