# Neo4J-Recomender-System

La idea es crear una BD, con la intención de crear un sistema de recomendación, basado en la información que tenemos disponible
Se nos entregan 4 archivos, "cliente.csv, producto.csv, tipo_producto.csv, venta.csv"

Para realizar este proyecto se está haciendo uso de la consola de Neo4j-Aura, en la versión gratuita, crearse una cuenta es bastante sencillo
Ingresa a la página de [Neo4J](https://neo4j.com/cloud/platform/aura-graph-database/?ref=nav-get-started-cta), te puedes registrarte con tu correo Gmail.

Lo primero es crear una Instancia de Aura DB.
Se generarán automaticamente tus credenciales, solo aparecerán una vez, por lo que recomiendo descargar en ese momento y asegurarse muy bien en dónde se almacenará.

## Carga de los datos

### Cargar Productos
````
LOAD CSV WITH HEADERS FROM "https://raw.githubusercontent.com/Ivan-Cepeda/Neo4J-Recomender-System/main/producto.csv" AS row
CREATE (n:Producto)
SET n = row,
n.IdProducto = toInteger(row.IdProducto),
n.Producto = toString(row.Producto), 
n.IdTipoProducto = toInteger(row.IdTipoProducto);
````
### Cargar Tipo de Productos
````
LOAD CSV WITH HEADERS FROM "https://raw.githubusercontent.com/Ivan-Cepeda/Neo4J-Recomender-System/main/tipo_producto.csv" AS row
CREATE (n:TipoProducto)
SET n = row,
n.IdTipoProducto = toInteger(row.IdTipoProducto),
n.TipoProducto = toString(row.TipoProducto);
````
### Cargar Cliente
````
LOAD CSV WITH HEADERS FROM "https://raw.githubusercontent.com/Ivan-Cepeda/Neo4J-Recomender-System/main/cliente.csv" AS row
CREATE (n:Cliente)
SET n = row,
n.IdCliente = toInteger(row.IdCliente),
n.Nombre_y_Apellido = toString(row.Nombre_y_Apellido),
n.Domicilio = toString(row.Domicilio);
````
## Cargar Venta
````
LOAD CSV WITH HEADERS FROM "https://raw.githubusercontent.com/Ivan-Cepeda/Neo4J-Recomender-System/main/venta.csv" AS row
CREATE (n:Venta)
SET n = row,
n.IdVenta = toInteger(row.IdVenta),
n.IdCliente = toInteger(row.IdCliente),
n.IdProducto = toInteger(row.IdProducto),
n.Precio = toFloat(row.Precio),
n.Cantidad = toInteger(row.Cantidad);
````
## Creación de Relaciones
### Crear las Relaciones entre tipo de producto y prducto
````
MATCH (t:TipoProducto),(p:Producto)
WHERE t.IdTipoProducto = p.IdTipoProducto
CREATE (t)-[:AGRUPA_A]->(p);
````
### Crear la relación entre Producto y Venta
````
MATCH (p:Producto),(v:Venta)
WHERE p.IdProducto = v.IdProducto
CREATE (p)-[:VENDIDO_EN]->(v);
````
### Crear la relación entre cliente y Venta
````
MATCH (c:Cliente),(v:Venta)
WHERE c.IdCliente = v.IdCliente
CREATE (c)-[:CLIENTE_EN]->(v);
````
