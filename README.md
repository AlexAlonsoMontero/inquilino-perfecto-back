# Inquilino Perfecto Backend
![Estado](https://img.shields.io/badge/estado-activo-brightgreen)
![Licencia](https://img.shields.io/badge/licencia-MIT-blue)
![Version](https://img.shields.io/badge/version-1.0.0-blueviolet)

> Idioma del README: Espanol

Backend del portal inmobiliario Inquilino Perfecto. Implementa un dominio rico con arquitectura hexagonal, reglas de negocio estrictas y una API REST pensada para un producto real.

## Caracteristicas clave 🚀

- Autenticacion completa con activacion y reactivacion
- Gestion de inmuebles, anuncios, reservas y resenas
- Reglas de negocio para solapes, fotos y soft delete
- Base documental completa en `docs/`

## Tabla de contenidos

- [Vision general](#vision-general)
- [Tech Stack](#tech-stack)
- [Prerequisitos](#prerequisitos)
- [Getting Started](#getting-started)
- [Arquitectura](#arquitectura)
- [Variables de entorno](#variables-de-entorno)
- [Scripts disponibles](#scripts-disponibles)
- [Testing](#testing)
- [Deployment](#deployment)
- [Troubleshooting](#troubleshooting)
- [Documentacion funcional](#documentacion-funcional)
- [Contributing](#contributing)
- [License](#license)

## Vision general

El sistema cubre el ciclo completo de alquiler: registro y activacion de usuarios, publicacion de inmuebles y anuncios con fotos, reservas con validacion de solapes, resenas post-alquiler y un rol admin con consultas avanzadas. Esta documentacion esta orientada a que cualquier persona pueda ejecutar el backend localmente y entender el dominio en profundidad.

## Tech Stack

- **Language**: Java 21
- **Framework**: Spring Boot 3.5
- **Build**: Maven
- **Database**: PostgreSQL
- **ORM**: Spring Data JPA (Hibernate)
- **Auth**: JWT + refresh tokens
- **Mail**: Spring Boot Mail

## Prerequisitos

- Java 21 (temurin recomendado)
- Maven 3.9+ (o usar `./mvnw`)
- PostgreSQL 14+ (o Docker)
- Git

## Getting Started

### 1. Clonar el repositorio

```bash
git clone https://github.com/<owner>/inquilino-perfecto-back.git
cd inquilino-perfecto
```

### 2. Configurar base de datos

Ejemplo con Docker:

```bash
docker run --name inquilino-postgres \
  -e POSTGRES_USER=alex \
  -e POSTGRES_PASSWORD=postgres \
  -e POSTGRES_DB=inquilino_perfecto \
  -p 5433:5432 \
  -d postgres:16
```

Si usas una base local, crea la base `inquilino_perfecto` y actualiza las variables.

### 3. Variables de entorno

Este proyecto usa `application.yml` con defaults. Puedes sobreescribirlos con variables:

```bash
export DB_URL=jdbc:postgresql://localhost:5433/inquilino_perfecto
export DB_USER=alex
export DB_PASSWORD=postgres
export MAIL_HOST=smtp.example.com
export MAIL_PORT=587
export MAIL_USER=usuario
export MAIL_PASSWORD=secreto
export SERVER_PORT=8080
```

### 4. Ejecutar la aplicacion

```bash
./mvnw spring-boot:run
```

La API quedara en `http://localhost:8080`.

### 5. Build local

```bash
./mvnw clean package
```

Genera el jar en `target/`.

## Arquitectura

### Estructura de directorios

```
src/
├── domain/       # Entidades, value objects e invariantes
├── application/  # Casos de uso y puertos
├── adapters/     # Infraestructura (API, persistencia, email)
├── shared/       # Errores y utilidades compartidas
└── main/
    ├── java/com/inquilino/perfecto/ # Bootstrap de Spring
    └── resources/                   # application.yml
docs/            # Documentacion funcional y tecnica
```

### Ciclo de request

1. Request HTTP entra en el controlador (adaptador de entrada)
2. El controlador llama un caso de uso en `application/`
3. El caso de uso orquesta el dominio y consulta puertos
4. Los adaptadores concretos implementan persistencia y servicios
5. La respuesta se serializa a JSON

### Dominios principales

- Usuarios y autenticacion (activacion, reactivacion)
- Inmuebles y anuncios (fotos, herencia, portada)
- Reservas con validacion de solapes
- Resenas post-alquiler
- Admin con CRUD global y consultas

### Persistencia

El modelo logico esta documentado en `docs/06_persistencia.md` e incluye soft delete en todas las entidades principales mediante `deleted_at`.

## Variables de entorno

### Required

| Variable | Description | Example |
| --- | --- | --- |
| `DB_URL` | JDBC URL de PostgreSQL | `jdbc:postgresql://localhost:5433/inquilino_perfecto` |
| `DB_USER` | Usuario de DB | `alex` |
| `DB_PASSWORD` | Password de DB | `postgres` |

### Optional

| Variable | Description | Default |
| --- | --- | --- |
| `MAIL_HOST` | Servidor SMTP | vacio |
| `MAIL_PORT` | Puerto SMTP | vacio |
| `MAIL_USER` | Usuario SMTP | vacio |
| `MAIL_PASSWORD` | Password SMTP | vacio |
| `SERVER_PORT` | Puerto HTTP | `8080` |

## Scripts disponibles

| Command | Description |
| --- | --- |
| `./mvnw spring-boot:run` | Ejecutar en modo desarrollo |
| `./mvnw clean package` | Build del jar |
| `./mvnw test` | Ejecutar tests |
| `./mvnw -DskipTests package` | Build sin tests |

## Testing

```bash
./mvnw test
```

Si mas adelante se incluyen tests de integracion con DB, se recomienda usar un contenedor PostgreSQL dedicado.

## Deployment

Como no hay configuracion de despliegue en el repo, la recomendacion es Docker + PostgreSQL gestionado.

### Docker (recomendado)

```bash
./mvnw clean package
docker build -t inquilino-perfecto-back .
docker run -p 8080:8080 \
  -e DB_URL=jdbc:postgresql://<host>:5432/inquilino_perfecto \
  -e DB_USER=<user> \
  -e DB_PASSWORD=<password> \
  -e MAIL_HOST=<smtp_host> \
  -e MAIL_PORT=587 \
  -e MAIL_USER=<smtp_user> \
  -e MAIL_PASSWORD=<smtp_password> \
  inquilino-perfecto-back
```

### Manual/VPS

```bash
./mvnw clean package
scp target/*.jar user@server:/opt/inquilino/
java -jar /opt/inquilino/*.jar
```

## Troubleshooting

### Error de conexion a PostgreSQL

**Error:** `Connection refused`

1. Verifica que PostgreSQL esta en ejecucion
2. Revisa `DB_URL` y credenciales
3. Confirma que el puerto `5433` esta expuesto si usas Docker

### Error de credenciales SMTP

Si los emails fallan, valida `MAIL_HOST`, `MAIL_PORT`, `MAIL_USER` y `MAIL_PASSWORD` o deja los valores vacios durante desarrollo.

### Hibernate ddl-auto

`ddl-auto: validate` requiere que el esquema exista. Para desarrollo inicial puedes cambiarlo a `update` en `src/main/resources/application.yml`.

## Documentacion funcional

- `docs/README.md`
- `docs/01_contexto.md`
- `docs/02_dominio.md`
- `docs/03_casos_de_uso.md`
- `docs/04_permisos.md`
- `docs/05_api.md`
- `docs/06_persistencia.md`
- `docs/07_eventos_y_notificaciones.md`
- `docs/08_plan_implementacion.md`

## Contributing

Si vas a contribuir, abre una issue y propone un PR con contexto claro. Este proyecto sigue una arquitectura hexagonal estricta, asi que respeta las capas `domain`, `application`, `adapters` y `shared`.

## License

MIT. Si quieres otra licencia, actualiza este apartado.
