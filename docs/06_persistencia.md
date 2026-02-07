# Persistencia

## Modelo logico

### users
- id (pk)
- username (unique)
- email (unique)
- password_hash
- roles
- status
- avatar_url
- activated_at
- deleted_at

### properties
- id (pk)
- owner_id (fk users)
- title
- description
- address_* (province, city, region, street, zip)
- features_* (sqm, rooms, bathrooms, etc.)
- deleted_at

### property_photos
- id (pk)
- property_id (fk)
- url
- caption
- ordering
- is_cover
- status
- deleted_at

### listings
- id (pk)
- property_id (fk)
- type
- price
- status
- inherit_photos
- deleted_at

### listing_photos
- id (pk)
- listing_id (fk)
- url
- caption
- ordering
- is_cover
- status
- deleted_at

### bookings
- id (pk)
- tenant_id (fk users)
- listing_id (fk listings)
- date_from
- date_to
- status
- deleted_at

### reviews
- id (pk)
- author_id (fk users)
- target_type
- target_id
- rating
- comment
- deleted_at

### activation_tokens
- id (pk)
- user_id (fk)
- token
- expires_at
- used_at

### refresh_tokens
- id (pk)
- user_id (fk)
- token
- expires_at
- revoked_at

## Indices
- users(email), users(username)
- bookings(listing_id, date_from, date_to, status)
- listings(property_id)
- property_photos(property_id, ordering)
- listing_photos(listing_id, ordering)

## Borrado logico
- deleted_at en entidades principales
- filtros por defecto excluyen deleted_at != null
- admin puede incluir historico con include_deleted=true

## Borrado fisico
- Solo admin puede borrar inmuebles de la base de datos
