# Casos de uso

## Registro de usuario
- Actor: Invitado
- Precondiciones: email y username no existen
- Flujo:
  - Validar datos
  - Crear usuario con status PENDING_ACTIVATION
  - Enviar email de activacion
- Postcondiciones: usuario creado sin acceso hasta activacion

## Activacion de usuario
- Actor: Registrado
- Precondiciones: token valido
- Flujo:
  - Validar token
  - Cambiar status a ACTIVE
- Postcondiciones: usuario activado

## Login
- Actor: Registrado
- Precondiciones: credenciales validas
- Flujo:
  - Login con email o username
  - Si status DEACTIVATED, enviar email reactivacion
  - Si status PENDING_ACTIVATION, reenviar activacion
- Postcondiciones: sesion iniciada si ACTIVE

## Modificar perfil
- Actor: Cualquier usuario logado
- Precondiciones: usuario ACTIVE
- Flujo:
  - Actualizar datos permitidos
- Postcondiciones: perfil actualizado

## Solicitar baja
- Actor: Cualquier usuario logado
- Precondiciones: usuario ACTIVE
- Flujo:
  - Marcar usuario como DEACTIVATED y set deleted_at
- Postcondiciones: usuario no visible

## Crear inmueble
- Actor: Casero o Admin
- Precondiciones: usuario ACTIVE
- Flujo:
  - Crear inmueble
  - Subir al menos una foto
- Postcondiciones: inmueble disponible para anuncio

## Publicar anuncio
- Actor: Casero o Admin
- Precondiciones: inmueble con fotos
- Flujo:
  - Crear anuncio
  - Heredar fotos o subir fotos propias
- Postcondiciones: anuncio visible
- Regla: no se publica si no hay fotos ni portada

## Buscar anuncios
- Actor: Invitado, Inquilino, Casero
- Precondiciones: ninguna
- Flujo:
  - Filtrar por provincia, ciudad, comunidad, precio, fechas
- Postcondiciones: lista de anuncios

## Solicitar reserva
- Actor: Inquilino o Admin
- Precondiciones: usuario ACTIVE
- Flujo:
  - Validar disponibilidad (no solape)
  - Crear reserva en estado SOLICITADA
- Postcondiciones: reserva creada
- Regla: la reserva es mensual y se marca indefinida si supera 12 meses

## Gestionar reserva
- Actor: Casero
- Precondiciones: reserva SOLICITADA
- Flujo:
  - Aceptar o rechazar
  - Notificar inquilino
- Postcondiciones: reserva ACEPTADA o RECHAZADA

## Cancelar reserva
- Actor: Inquilino
- Precondiciones: reserva SOLICITADA o ACEPTADA
- Flujo:
  - Cambiar estado a CANCELADA
- Postcondiciones: reserva cancelada

## Resenar inmueble
- Actor: Inquilino
- Precondiciones: reserva finalizada
- Flujo:
  - Crear resena sobre inmueble
- Postcondiciones: resena visible
- Regla: el autor debe haber participado en la reserva

## Resenar inquilino
- Actor: Casero
- Precondiciones: reserva finalizada
- Flujo:
  - Crear resena sobre inquilino
- Postcondiciones: resena visible
- Regla: el autor debe haber participado en la reserva

## Admin CRUD y consultas
- Actor: Administrador
- Precondiciones: usuario ADMIN
- Flujo:
  - CRUD total de usuarios, inmuebles, anuncios, resenas
  - Consultas por fecha, provincia, ciudad, coste, comunidad, inquilino, casero
- Postcondiciones: datos gestionados
