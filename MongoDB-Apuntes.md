# MongoDB Consola - Apuntes de Estudio

Este repositorio contiene apuntes detallados y la resolución de ejercicios del manual de MongoDB Consola, organizados como material de estudio universitario.

## Introducción
MongoDB es una base de datos NoSQL orientada a documentos, lo que significa que en lugar de tablas y filas, utiliza colecciones y documentos (similares a objetos JSON).

---

## Capítulo 1: Elementos Esenciales
En MongoDB, la jerarquía es:
1. **Base de Datos**: Contenedor físico para colecciones.
2. **Colección**: Grupo de documentos (equivalente a una tabla).
3. **Documento**: Registro básico de datos (equivalente a una fila).

**Comandos útiles:**
- `use base1`: Crea o cambia a la base de datos `base1`.
- `db`: Muestra la base de datos actual.
- `show dbs`: Lista las bases de datos.

---

## Capítulo 2: Inserción de Documentos
Para agregar datos usamos:
- `insertOne({...})`: Inserta un solo documento.
- `insertMany([{...}, {...}])`: Inserta un arreglo de documentos.

**Ejemplo:**
```javascript
db.libros.insertOne({
  codigo: 1,
  nombre: 'El aleph',
  autor: 'Borges',
  editoriales: ['Planeta', 'Siglo XXI']
})
```

---

## Capítulo 3: El Campo `_id`
Todo documento requiere una clave primaria única llamada `_id`.
- Si no se especifica, MongoDB genera un `ObjectId` automáticamente.
- Si se especifica manualmente, no puede repetirse.

---

## Soluciones: Problemas Propuestos (1.1)
```javascript
// 1. Insertar 2 documentos con _id no repetidos
db.clientes.insertOne({_id: 1, nombre: 'Lopez Marcos', domicilio: 'Colon 111'})
db.clientes.insertOne({_id: 2, nombre: 'Perez Ana', domicilio: 'San Martin 222'})

// 2. Intentar insertar otro con id repetido (dará error)
db.clientes.insertOne({_id: 1, nombre: 'Otro'})

// 3. Mostrar todos
db.clientes.find()
```

---

## Capítulo 4: Borrado de Datos
- `db.coleccion.deleteMany({})`: Borra todos los documentos.
- `db.coleccion.drop()`: Borra la colección completa.
- `db.dropDatabase()`: Borra la base de datos activa.

---

## Soluciones: Problemas Propuestos (1.2)
```javascript
// 1. Crear BD blog
use blog

// 2. Insertar en posts
db.posts.insertOne({titulo: 'Intro', contenido: 'Hola mundo'})

// 3. Mostrar bases
show dbs

// 4. Eliminar colección
db.posts.drop()

// 5. Eliminar BD y mostrar
db.dropDatabase()
show dbs
```

---

## Capítulo 5: Recuperación y Operadores Relacionales
El método `find()` permite filtrar datos.
**Operadores:**
- `$eq`: Igual
- `$lt` / `$lte`: Menor / Menor o igual
- `$gt` / `$gte`: Mayor / Mayor o igual
- `$ne`: Distinto
- `$in`: En un conjunto de valores

---

## Soluciones: Problemas Propuestos (1.3)
*(Basado en la colección 'articulos')*
```javascript
// 2. Imprimir todos
db.articulos.find()

// 3. Que NO son impresoras
db.articulos.find({ rubro: { $ne: 'impresora' } })

// 4. Rubro mouse
db.articulos.find({ rubro: 'mouse' })

// 5. Precio >= 5000
db.articulos.find({ precio: { $gte: 5000 } })

// 6. Impresoras con precio >= 3500
db.articulos.find({ rubro: 'impresora', precio: { $gte: 3500 } })

// 7. Stock entre 0 y 4
db.articulos.find({ stock: { $gte: 0, $lte: 4 } })
```

---

## Capítulo 6: Borrado Selectivo
- `deleteOne({condicion})`: Borra el primer documento que coincida.
- `deleteMany({condicion})`: Borra todos los que coincidan.

---

## Soluciones: Problemas Propuestos (1.4)
```javascript
// 3. Borrar rubro impresoras
db.articulos.deleteMany({ rubro: 'impresora' })

// 4. Borrar _id >= 5
db.articulos.deleteMany({ _id: { $gte: 5 } })
```

---

## Capítulo 7: Modificación de Documentos
- `$set`: Modifica o crea un campo.
- `$unset`: Elimina un campo.
- `$push`: Agrega un elemento a un arreglo.
- `$pull`: Quita un elemento de un arreglo.

---

## Soluciones: Problemas Propuestos (1.5)
```javascript
// 3. Modificar precio del mouse
db.articulos.updateOne({ nombre: 'LOGITECH M90' }, { $set: { precio: 350 } })

// 4. Stock 0 para _id 6
db.articulos.updateOne({ _id: 6 }, { $set: { stock: 0 } })

// 5. Agregar proveedores
db.articulos.updateOne({ _id: 6 }, { $set: { proveedores: ['Martinez', 'Gutierrez'] } })

// 6. Eliminar campo proveedores
db.articulos.updateOne({ _id: 6 }, { $unset: { proveedores: "" } })
```

---

## Capítulo 8: Modificación Múltiple
`updateMany({condicion}, {operacion})` aplica cambios masivos.

---

## Soluciones: Problemas Propuestos (1.6)
```javascript
// 3. Stock 0 para monitores
db.articulos.updateMany({ rubro: 'monitor' }, { $set: { stock: 0 } })

// 4. Agregar 'pedir: true' si stock es 0
db.articulos.updateMany({ stock: 0 }, { $set: { pedir: true } })

// 5. Eliminar 'pedir' de todos
db.articulos.updateMany({}, { $unset: { pedir: "" } })
```

---

## Capítulo 9: CRUD y Operadores Lógicos
- `$and`: Ambas condiciones deben ser ciertas.
- `$or`: Al menos una debe ser cierta.
- `$not`: Niega el resultado de la condición.

---

## Soluciones: Problemas Propuestos (1.7)
*(Basado en 'medicamentos')*
```javascript
// 3. Roche y precio < 5
db.medicamentos.find({ laboratorio: 'Roche', precio: { $lt: 5 } })

// 4. Roche o precio < 5
db.medicamentos.find({ $or: [{ laboratorio: 'Roche' }, { precio: { $lt: 5 } }] })

// 5. Laboratorio NO sea Bayer
db.medicamentos.find({ laboratorio: { $ne: 'Bayer' } })

// 9. Borrar si es Bayer o precio < 3
db.medicamentos.deleteMany({ $or: [{ laboratorio: 'Bayer' }, { precio: { $lt: 3 } }] })
```

---

## Capítulo 10: Cursores y Proyecciones
- `sort({campo: 1})`: Ordena (1 asc, -1 desc).
- `limit(n)`: Limita resultados.
- `skip(n)`: Salta resultados.
- **Proyección**: Segundo parámetro de `find()`. `{campo: 1}` muestra, `{campo: 0}` oculta.

---

## Soluciones: Problemas Propuestos (1.8)
```javascript
// 2. Mostrar solo _id y nombre
db.articulos.find({}, { nombre: 1 })

// 3. Impresoras, mostrar nombre y precio
db.articulos.find({ rubro: 'impresora' }, { nombre: 1, precio: 1 })

// 5. Monitores, nombre y precio, ordenado de menor a mayor precio
db.articulos.find({ rubro: 'monitor' }, { nombre: 1, precio: 1 }).sort({ precio: 1 })
```

---

## Capítulo 11: Documentos Embebidos
MongoDB permite guardar un documento dentro de otro. Para consultar se usa la notación de punto `"campo.subcampo"`.

---

## Soluciones: Problemas Propuestos (1.9)
```javascript
// 3. Autores argentinos
db.libros.find({ "autor.nacionalidad": "Argentina" })

// 4. Libros de Borges
db.libros.find({ "autor.nombre": "Borges" })

// 5. Españoles que cuestan 50 o más
db.libros.find({ "autor.nacionalidad": "Española", precio: { $gte: 50 } })
```
