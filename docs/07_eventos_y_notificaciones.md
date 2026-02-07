# Eventos y notificaciones

## Emails obligatorios
- Registro: envio de activacion
- Reactivacion: envio al detectar usuario DEACTIVATED en login
- Reserva: notificacion de aceptacion o rechazo

## Eventos de dominio
- UserRegistered
- UserActivated
- UserReactivated
- BookingRequested
- BookingAccepted
- BookingRejected
- BookingCanceled
- ReviewCreated

## Politica de entrega
- Entrega asincrona mediante adaptador de email
- Reintentos en caso de fallo
- Registro en audit_log
