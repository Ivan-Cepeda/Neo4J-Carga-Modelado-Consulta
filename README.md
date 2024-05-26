# Neo4J-Carga - Modelado - Consulta.

La idea es crear una BD, cargar los datos desde documentos, modelar la base de datos y crear las relaciones, realizar algunas posibles consultas. Para ello se nos entregan 4 archivos, "cliente.csv, producto.csv, tipo_producto.csv, venta.csv".

Para realizar este proyecto se está haciendo uso de la consola de Neo4j-Aura, en la versión gratuita, crearse una cuenta es bastante sencillo
Ingresa a la página de [Neo4J](https://neo4j.com/cloud/platform/aura-graph-database/?ref=nav-get-started-cta), te puedes registrarte con tu correo Gmail.

Lo primero es crear una Instancia de Aura DB.
Se generarán automaticamente tus credenciales, solo aparecerán una vez, por lo que recomiendo descargar en ese momento y asegurarse muy bien en dónde se almacenará.

Para ello, vamos a hacer uso del lenguaje Cypher, este es un lenguaje declarativo con el que trabaja Neo4J, te invito a revisar la [documentación](https://neo4j.com/docs/cypher-manual/current/introduction/) para aprender cómo usarlo, sin embargo aquí dejo algunos querys que puedes empezar a usar.

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
## Creación de relaciones
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
### Crear la relación entre Cliente y Venta
````
MATCH (c:Cliente),(v:Venta)
WHERE c.IdCliente = v.IdCliente
CREATE (c)-[:CLIENTE_EN]->(v);
````
## Consultas
### Cientes relacionados a alguna Venta
````
MATCH (c:Cliente) -- (p:Venta) return c,p LIMIT 10;
````
Ambas consultas pueden llevar a un resultado específico, sin embargo si hay información específica en la relación para filtrar, es una buena idea partir desde aquí.
````
MATCH (c:Cliente) -[:CLIENTE_EN] - (p:Venta) return c,p LIMIT 10;
````
Se puede traer mas de una información en una relación. Para detallar visualmente el grafo, pulsa "*click derecho - show all rellationship*".
````
MATCH (c:Cliente) -[:CLIENTE_EN] - (v:Venta) -- (p:Producto) -- (t:TipoProducto) return c,v,p,t LIMIT 10;
````
Podemos hacer filtrado y consultas un poco mas complejas
````
MATCH (c:Cliente) -[:CLIENTE_EN] - (v:Venta) - [:VENDIDO_EN] - (p:Producto) - [:AGRUPA_A] - (t:TipoProducto) WHERE t.TipoProducto = "Informatica" return c,v,p,t LIMIT 10;
````
Otro ejemplo, esta vez creando una ruta para acceder a los productos agrupados en una categoría específica.
````
MATCH path = (t: TipoProducto {TipoProducto: "Gaming"}) - [:AGRUPA_A] - (p: Producto) RETURN path;
````
## Conclusión
El nivel de alcance en Neo4j es bastante variado, esto ha sido una muestra muy sencilla de como modelar, y comenzar a explorar la base de datos. Para explorar y jugar un poco mas con los distintos casos de uso con los que trabaja Neo4j, te invito a probar los ejemplos ingresando en la plataforma de [Neo4j Sandbox](https://neo4j.com/sandbox/) y haciendo clic en "*Launch the Free Sandbox*". 