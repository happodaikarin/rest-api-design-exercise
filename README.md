# Library Catalogue API Design

## Introduction
Este documento describe el diseño de una API RESTful para un sistema de Catálogo de Biblioteca. El sistema gestiona libros, autores, categorías y préstamos en una biblioteca.

## Descripción de Entidades

1. **Libros (Books)**
   - Información básica sobre libros (título, fecha de publicación, ISBN, descripción)
   - Relaciones con autores y categorías

2. **Autores (Authors)**
   - Información sobre los autores (nombre, biografía, fecha de nacimiento)
   - Relación con sus obras (libros)

3. **Categorías (Categories)**
   - Categorías jerárquicas para organizar libros (ficción, no ficción, ciencia, historia, etc.)

4. **Usuarios (Users)**
   - Usuarios de la biblioteca que pueden tomar prestados libros
   - Información de autenticación y autorización

5. **Ejemplares (BookCopies)**
   - Copias físicas de libros que pueden ser prestadas
   - Información sobre estado (disponible, prestado, en reparación)

6. **Préstamos (Loans)**
   - Registros de préstamos (quién tomó qué y cuándo, fechas de devolución)

## Requerimientos

### Requerimientos Funcionales
1. La API debe permitir crear, leer, actualizar y eliminar todas las entidades
2. Los usuarios deben poder buscar libros por varios criterios (título, autor, categoría)
3. Los usuarios deben poder ver la disponibilidad de los libros
4. El sistema debe realizar seguimiento de préstamos y devoluciones
5. La API debe soportar filtrado de libros por categoría, autor, fecha de publicación

### Requerimientos No Funcionales
1. **Seguridad**: La API debe implementar autenticación y autorización
2. **Usabilidad**: La API debe estar bien documentada y seguir patrones consistentes
3. **Paginación**: Todos los endpoints de colecciones deben soportar paginación
4. **HATEOAS**: La API debe seguir principios HATEOAS para descubrimiento
5. **Caché**: Los recursos frecuentemente accedidos deben ser cacheados

## Diseño de la API

### URL Base
```
https://api.librarycatalogue.com/v1
```

### Autenticación
- La API utiliza JWT (JSON Web Tokens)
- Los tokens deben incluirse en el encabezado `Authorization` como `Bearer {token}`

### Códigos de Estado
- **200 OK**: Solicitud exitosa
- **201 Created**: Recurso creado exitosamente
- **204 No Content**: Solicitud exitosa sin cuerpo de respuesta
- **400 Bad Request**: Formato de solicitud o parámetros inválidos
- **401 Unauthorized**: Fallo de autenticación
- **403 Forbidden**: Autenticado pero no autorizado
- **404 Not Found**: Recurso no encontrado
- **409 Conflict**: La solicitud entra en conflicto con el estado actual
- **422 Unprocessable Entity**: Errores de validación
- **500 Internal Server Error**: Error del servidor

### Paginación
Todos los endpoints de colecciones soportan paginación con los siguientes parámetros:
- `page`: Número de página (default: 1)
- `perPage`: Elementos por página (default: 20, max: 100)

### Caché
La API implementa mecanismos de caché HTTP:
- Cabeceras `ETag` para validación de caché
- Cabeceras `Cache-Control` para comportamiento de caché

## Endpoints de la API

### Libros (Books)

#### Listar Libros
```
GET /books
```
- **Descripción**: Obtener una lista paginada de libros, con filtrado opcional
- **Autenticación**: Opcional
- **Parámetros**: title, authorId, categoryId, page, perPage
- **Caché**: Sí (Cache-Control: max-age=3600)

#### Obtener Libro
```
GET /books/{id}
```
- **Descripción**: Obtener información detallada sobre un libro específico
- **Autenticación**: Opcional
- **Caché**: Sí (Cache-Control: max-age=3600)

#### Crear Libro
```
POST /books
```
- **Descripción**: Crear un nuevo libro
- **Autenticación**: Requerida (Bibliotecario, Admin)
- **Caché**: No

#### Actualizar Libro
```
PUT /books/{id}
```
- **Descripción**: Actualizar un libro existente
- **Autenticación**: Requerida (Bibliotecario, Admin)
- **Caché**: No

#### Eliminar Libro
```
DELETE /books/{id}
```
- **Descripción**: Eliminar un libro
- **Autenticación**: Requerida (Admin)
- **Caché**: No

### Autores (Authors)

#### Listar Autores
```
GET /authors
```
- **Descripción**: Obtener una lista paginada de autores
- **Autenticación**: Opcional
- **Parámetros**: name, page, perPage
- **Caché**: Sí

#### Obtener Autor
```
GET /authors/{id}
```
- **Descripción**: Obtener información detallada sobre un autor específico
- **Autenticación**: Opcional
- **Caché**: Sí

#### Crear Autor
```
POST /authors
```
- **Descripción**: Crear un nuevo autor
- **Autenticación**: Requerida (Bibliotecario, Admin)
- **Caché**: No

#### Actualizar Autor
```
PUT /authors/{id}
```
- **Descripción**: Actualizar un autor existente
- **Autenticación**: Requerida (Bibliotecario, Admin)
- **Caché**: No

#### Eliminar Autor
```
DELETE /authors/{id}
```
- **Descripción**: Eliminar un autor
- **Autenticación**: Requerida (Admin)
- **Caché**: No

### Categorías (Categories)

#### Listar Categorías
```
GET /categories
```
- **Descripción**: Obtener una lista paginada de categorías
- **Autenticación**: Opcional
- **Caché**: Sí

#### Obtener Categoría
```
GET /categories/{id}
```
- **Descripción**: Obtener información detallada sobre una categoría específica
- **Autenticación**: Opcional
- **Caché**: Sí

#### Crear Categoría
```
POST /categories
```
- **Descripción**: Crear una nueva categoría
- **Autenticación**: Requerida (Bibliotecario, Admin)
- **Caché**: No

#### Actualizar Categoría
```
PUT /categories/{id}
```
- **Descripción**: Actualizar una categoría existente
- **Autenticación**: Requerida (Bibliotecario, Admin)
- **Caché**: No

#### Eliminar Categoría
```
DELETE /categories/{id}
```
- **Descripción**: Eliminar una categoría
- **Autenticación**: Requerida (Admin)
- **Caché**: No

### Préstamos (Loans)

#### Listar Préstamos
```
GET /loans
```
- **Descripción**: Obtener una lista paginada de préstamos
- **Autenticación**: Requerida (Bibliotecario, Admin)
- **Caché**: No (datos dinámicos)

#### Obtener Préstamos de Usuario
```
GET /users/{userId}/loans
```
- **Descripción**: Obtener una lista paginada de préstamos para un usuario específico
- **Autenticación**: Requerida (Usuario propio, Bibliotecario, Admin)
- **Caché**: No (datos dinámicos)

#### Crear Préstamo
```
POST /loans
```
- **Descripción**: Crear un nuevo préstamo
- **Autenticación**: Requerida (Bibliotecario, Admin)
- **Caché**: No

#### Devolver Préstamo
```
PUT /loans/{id}/return
```
- **Descripción**: Marcar un préstamo como devuelto
- **Autenticación**: Requerida (Bibliotecario, Admin)
- **Caché**: No

## Implementación HATEOAS

La API implementa HATEOAS para hacer la API auto-descubrible:

Ejemplo:
```json
{
  "data": {
    "id": 123,
    "title": "Don Quijote de la Mancha",
    "isbn": "9780743273565"
  },
  "links": {
    "self": { "href": "/books/123" },
    "authors": { "href": "/books/123/authors" },
    "copies": { "href": "/books/123/copies" }
  }
}
```
