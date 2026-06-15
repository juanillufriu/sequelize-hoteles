# Sistema de Gestión Hotelera - Node.js, Sequelize y PostgreSQL

![Node.js](https://img.shields.io/badge/Node%20js-339933?style=for-the-badge&logo=nodedotjs&logoColor=white)
![Express.js](https://img.shields.io/badge/Express%20js-000000?style=for-the-badge&logo=express&logoColor=white)
![Sequelize](https://img.shields.io/badge/Sequelize-52B0E7?style=for-the-badge&logo=Sequelize&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?style=for-the-badge&logo=postgresql&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2CA5E0?style=for-the-badge&logo=docker&logoColor=white)
![Docker Compose](https://img.shields.io/badge/Docker%20Compose-2496ED?style=for-the-badge&logo=docker&logoColor=white)

TP Sequelize - Cátedra Bases de Datos (UTN FRVM)

## Integrantes

- Albarracín Trinidad
- Altamirano Mateo
- Anselmi Lara
- Barra Juan Patricio
- Caullo Mateo
- Llufriu Juan Ignacio
- Magallanes Agustin
- Mansilla Santiago

## Descripción

Este proyecto implementa un sistema de gestión para las reservas y facturación de un hotel
utilizando Node.js, Express, Sequelize y PostgreSQL. La aplicación permite administrar:

- Tipos de Documentos y Clientes
- Tipos de Habitaciones y Habitaciones
- Reservas y sus Detalles (Múltiples habitaciones por reserva)
- Facturación

El proyecto se encuentra completamente dockerizado para facilitar su instalación y ejecución.

## Tecnologías Utilizadas

- Node.js
- Express
- Sequelize
- PostgreSQL
- Docker
- Docker Compose

## Instalación

### 1. Clonar repositorio

```bash
git clone <repositorio>
cd Sequelize-BDD2026
```

### 2. Configurar variables de entorno

Crear un archivo `.env` en base a `.env.example`:

```
DB_HOST=db
DB_PORT=5432
DB_NAME=hotel
DB_USER=postgres
DB_PASSWORD=postgres
```

### 3. Ejecución con Docker

Construir y levantar los servicios (PostgreSQL + backend):

```bash
docker compose up --build
```

### 4. Migraciones y datos iniciales

Con los contenedores corriendo, ejecutar dentro del contenedor `backend`:

```bash
docker compose exec backend npx sequelize-cli db:migrate
docker compose exec backend npx sequelize-cli db:seed:all
```

### Ejecución local (sin Docker)

```bash
npm install
npx sequelize-cli db:migrate
npx sequelize-cli db:seed:all
npm run dev
```

## Modelo de Datos y Relaciones

- **TipoDocumento → Cliente** (1:N)
- **TipoHabitacion → Habitacion** (1:N)
- **Cliente → Reserva** (1:N)
- **Reserva → DetalleReserva** (1:N)
- **Habitacion → DetalleReserva** (1:N) *(Resuelve la relación N:M entre Reserva y Habitación)*
- **Reserva → Factura** (1:1)

### Entidades

| Entidad | Atributos |
|---|---|
| TipoDocumento | descripcion |
| Cliente | nombre, apellido, telefono, numeroDocumento, email, direccion, idTipoDoc |
| TipoHabitacion | descripcion, precio |
| Habitacion | numero, capacidad, estado, idTipoHabitacion |
| Reserva | fechaHoraReserva, fechaHoraEntrada, fechaHoraSalida, total, estado, idCliente |
| DetalleReserva | precioHistorico, idReserva, idHabitacion |
| Factura | fechaEmision, subtotal, impuestos, total, estadoPago, idReserva |

## Endpoints de la API

Todos los endpoints están disponibles bajo el prefijo `/api`.

| Recurso | Endpoints |
|---|---|
| Tipos de Documento | `GET/POST /api/tipos-documento`, `GET/PUT/DELETE /api/tipos-documento/:id` |
| Tipos de Habitación | `GET/POST /api/tipos-habitacion`, `GET/PUT/DELETE /api/tipos-habitacion/:id` |
| Clientes | `GET/POST /api/clientes`, `GET/PUT/DELETE /api/clientes/:id` |
| Habitaciones | `GET/POST /api/habitaciones`, `GET/PUT/DELETE /api/habitaciones/:id` |
| Reservas | `GET/POST /api/reservas`, `GET/PUT/DELETE /api/reservas/:id` |
| Detalle de Reservas | `GET/POST /api/detalle-reservas`, `GET/PUT/DELETE /api/detalle-reservas/:id` |
| Facturas | `GET/POST /api/facturas`, `GET/PUT/DELETE /api/facturas/:id` |

### Crear una reserva (múltiples habitaciones)

`POST /api/reservas`

```json
{
  "idCliente": 1,
  "fechaHoraEntrada": "2026-07-01T15:00:00Z",
  "fechaHoraSalida": "2026-07-04T11:00:00Z",
  "habitaciones": [1, 3]
}
```

El total de la reserva se calcula automáticamente en base al precio del `TipoHabitacion`
de cada habitación seleccionada y la cantidad de noches, generando además un
`DetalleReserva` por cada habitación con el precio histórico aplicado.

### Generar factura de una reserva

`POST /api/facturas`

```json
{
  "idReserva": 1,
  "estadoPago": "pendiente"
}
```

Calcula `subtotal` (total de la reserva), `impuestos` (21%) y `total` de la factura.

## Comandos Útiles

- Ejecutar migraciones: `npx sequelize-cli db:migrate`
- Revertir migraciones: `npx sequelize-cli db:migrate:undo`
- Ejecutar seeders: `npx sequelize-cli db:seed:all`
- Eliminar contenedores: `docker compose down`
- Eliminar contenedores y volúmenes: `docker compose down -v`

## Resultados Obtenidos

Se logró implementar una aplicación utilizando Sequelize como ORM para PostgreSQL,
permitiendo gestionar los datos del hotel mediante una estructura relacional correctamente
normalizada (incluyendo la resolución de reservas multi-habitación y facturación). El uso de
Docker permitió crear un entorno reproducible y portable, facilitando el despliegue del
sistema.

## Conclusiones

La utilización de Sequelize simplificó la gestión de entidades, relaciones y migraciones
dentro de PostgreSQL. La combinación de Node.js, Express y Docker permitió desarrollar
una solución moderna, escalable y acorde a las necesidades operativas de un sistema de
reservas de alojamiento.
