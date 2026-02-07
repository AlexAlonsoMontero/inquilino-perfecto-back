# Plan de implementacion

## Fase 1: Base de dominio
- Modelos y value objects
- Reglas de negocio criticas
- Tests de dominio

## Fase 2: Auth y usuarios
- Registro, activacion, login, reactivacion
- JWT + refresh tokens
- Perfil y baja logica

## Fase 3: Inmuebles y anuncios
- CRUD de inmuebles
- Fotos y portada
- Publicacion de anuncios

## Fase 4: Reservas
- Solicitud, aceptacion, rechazo, cancelacion
- Validacion de solapamientos

## Fase 5: Resenas
- Resenas a inmuebles e inquilinos
- Borrado logico

## Fase 6: Admin
- CRUD global
- Consultas avanzadas

## Fase 7: Emails reales
- Integracion proveedor externo
- Reintentos y logs

## Pruebas prioritarias
- Solapamiento de reservas
- Transiciones de estado
- Resenas solo tras alquiler finalizado
- Soft delete y visibilidad
- Permisos por rol
