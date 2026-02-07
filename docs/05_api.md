# API

## Convenciones
- Formato JSON.
- Autenticacion: JWT con refresh token.
- Errores estandar: code, message, details.
- Soft delete: endpoints de borrado hacen delete logico salvo inmuebles por admin.
- Paginacion: query params page, size (por defecto page=1, size=20).
- Fechas en ISO-8601 (YYYY-MM-DD).

### Cabeceras
- Authorization: Bearer <token>
- X-Request-Id opcional para trazabilidad

### Errores estandar
```json
{
  "code": "VALIDATION_ERROR",
  "message": "Invalid request",
  "details": [
    { "field": "email", "error": "invalid_format" }
  ]
}
```

### Codigos de error
- AUTH_INVALID_CREDENTIALS
- AUTH_USER_DEACTIVATED
- AUTH_USER_PENDING_ACTIVATION
- AUTH_TOKEN_EXPIRED
- VALIDATION_ERROR
- NOT_FOUND
- FORBIDDEN
- CONFLICT

## Auth

### POST /auth/register
Request
```json
{
  "username": "maria",
  "email": "maria@mail.com",
  "password": "secret123",
  "role": "TENANT",
  "profile": {
    "name": "Maria",
    "last_name": "Lopez",
    "phone": "+34600000000"
  },
  "avatar": "https://cdn/app/avatar.png"
}
```
Validaciones
- username: 3-30 caracteres, unico
- email: formato valido y unico
- password: 8-64, al menos 1 numero
- role: TENANT, LANDLORD, BOTH
- avatar: opcional; si no se envia, se asigna uno por defecto

Response 201
```json
{
  "id": "user-1",
  "status": "PENDING_ACTIVATION"
}
```

### POST /auth/activate
Request
```json
{ "token": "activation-token" }
```
Response 200
```json
{ "status": "ACTIVE" }
```

### POST /auth/login
Request
```json
{ "identifier": "maria@mail.com", "password": "secret123" }
```
Response 200
```json
{
  "access_token": "jwt",
  "refresh_token": "refresh",
  "expires_in": 3600
}
```

### POST /auth/refresh
Request
```json
{ "refresh_token": "refresh-token" }
```
Response 200
```json
{ "access_token": "jwt", "expires_in": 3600 }
```

### POST /auth/reactivate
Request
```json
{ "token": "reactivation-token" }
```
Response 200
```json
{ "status": "ACTIVE" }
```

## Users

### GET /users/me
### PUT /users/me
Request
```json
{
  "username": "maria",
  "email": "maria@mail.com",
  "password": "newsecret123",
  "avatar": "https://cdn/app/new.png"
}
```
Validaciones
- email y username mantienen unicidad
- password opcional con mismas reglas de registro

### DELETE /users/me
- Soft delete

### GET /admin/users
- Filtros: status, role, include_deleted

### PUT /admin/users/{id}
- Admin puede modificar cualquier usuario

## Properties

### POST /properties
Request
```json
{
  "title": "Piso centro",
  "description": "Luminoso",
  "address": {
    "province": "Madrid",
    "city": "Madrid",
    "region": "Madrid",
    "street": "Calle Mayor 1",
    "zip": "28001"
  },
  "features": {
    "sqm": 80,
    "rooms": 2,
    "bathrooms": 1
  }
}
```
Validaciones
- title: 3-120
- description: 10-2000
- features.sqm > 0

### PUT /properties/{id}
### DELETE /admin/properties/{id}
- Admin: hard delete

### POST /properties/{id}/photos
Request (multipart o url)
Validaciones
- formatos: jpg, png
- tamano max: 10MB
- minimo 1 foto para publicar anuncio
- al menos una foto debe ser portada

### PUT /properties/{id}/photos/reorder
Request
```json
{ "ordered_ids": ["p1", "p2", "p3"] }
```

### DELETE /properties/{id}/photos/{photoId}
- Soft delete

## Listings

### POST /listings
Request
```json
{
  "property_id": "prop-1",
  "type": "MENSUAL_Y_INDEFINIDO",
  "price": 900,
  "inherit_photos": true
}
```
Validaciones
- price > 0
- type: MENSUAL, INDEFINIDO, MENSUAL_Y_INDEFINIDO
- si inherit_photos=false, requiere al menos 1 foto propia

### PUT /listings/{id}
### DELETE /listings/{id}
- Soft delete

### POST /listings/{id}/photos
- Solo si inherit_photos=false
Validaciones
- formatos: jpg, png
- tamano max: 10MB

### PUT /listings/{id}/photos/reorder
### DELETE /listings/{id}/photos/{photoId}

### GET /listings/search
- Filtros: provincia, ciudad, comunidad, precio, fechas
- Paginacion: page, size

## Bookings

### POST /bookings
Request
```json
{
  "listing_id": "list-1",
  "date_from": "2026-03-01",
  "date_to": "2026-06-01"
}
```
Validaciones
- date_from < date_to
- minimo 30 dias
- no solape con reservas ACEPTADA o CONFIRMADA
- solo inquilino, inquilino-casero o admin

### POST /bookings/{id}/accept
### POST /bookings/{id}/reject
### POST /bookings/{id}/cancel

### GET /bookings/history
- Filtros: tenant_id, property_id, status

## Reviews

### POST /reviews
Request
```json
{
  "target_type": "INMUEBLE",
  "target_id": "prop-1",
  "rating": 5,
  "comment": "Excelente"
}
```
Validaciones
- rating: 1-5
- comment: 10-1000
- reserva finalizada obligatoria
- el autor debe haber participado en la reserva

### PUT /reviews/{id}
### DELETE /reviews/{id}
- Soft delete (autor o admin)

### GET /reviews/search
- Filtros: target_type, target_id, author_id

## Admin reports

### GET /admin/reports/bookings
- Filtros: fecha, provincia, ciudad, coste, comunidad, inquilino, casero

### GET /admin/reports/listings
- Filtros: fecha, provincia, coste, comunidad, casero

### GET /admin/reports/reviews
- Filtros: total, inquilino, casero, inmueble
