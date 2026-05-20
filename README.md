# tbrm.restaurante.evaluacion
Sistema de administración de restaurante desarrollado con arquitectura de microservicios en Spring Boot.

## Descripción del proyecto

Este proyecto corresponde a un sistema de administración de restaurante desarrollado con arquitectura de microservicios utilizando Java y Spring Boot.

La aplicación permite gestionar distintos procesos internos de un restaurante, como la administración de comensales, mesas, reservas, meseros, menú, pedidos, cocina, inventario, notificaciones y autenticación.

Cada microservicio posee una responsabilidad específica y trabaja con su propia base de datos. La comunicación entre microservicios se realiza mediante APIs REST y, en los servicios que lo requieren, mediante OpenFeign.

---

## Integrantes

- Tomás Benjamín Rojas Montiel

**Número de equipo:** Equipo N° 3
**Nombre de la aplicación/contexto:** Sistema de Administración de Restaurante

---

## Arquitectura general

El sistema está compuesto por 10 microservicios independientes:

1. `auth-service`
2. `diner-service`
3. `table-service`
4. `reservation-service`
5. `waiter-service`
6. `menu-service`
7. `order-service`
8. `kitchen-service`
9. `inventory-service`
10. `notification-service`

Cada microservicio tiene su propio proyecto Spring Boot, su propio `pom.xml`, su propia configuración en `application.properties` y su propia base de datos.

---

## Repositorios de microservicios

| Microservicio | Repositorio |
|---|---|
| diner-service | https://github.com/tomasrojasm-duoc/tbrm.restaurante.diner.git |
| menu-service | https://github.com/tomasrojasm-duoc/tbrm.restaurante.menu.git |
| reservation-service | https://github.com/tomasrojasm-duoc/tbrm.restaurante.reservation.git |
| notification-service | https://github.com/tomasrojasm-duoc/tbrm.restaurante.notification.git |
| inventory-service | https://github.com/tomasrojasm-duoc/tbrm.restaurante.inventory.git |
| kitchen-service | https://github.com/tomasrojasm-duoc/tbrm.restaurante.kitchen.git |
| order-service | https://github.com/tomasrojasm-duoc/tbrm.restaurante.order.git |
| auth-service | https://github.com/tomasrojasm-duoc/tbrm.restaurante.auth.git |
| waiter-service | https://github.com/tomasrojasm-duoc/tbrm.restaurante.waiter.git |
| table-service | https://github.com/tomasrojasm-duoc/tbrm.restaurante.table.git |

---

## Funcionalidad de cada microservicio

### auth-service

Microservicio encargado de la autenticación del sistema.

Actualmente permite iniciar sesión con usuarios definidos en memoria y genera un token JWT cuando las credenciales son correctas. Este token permite dejar preparada la arquitectura para proteger endpoints mediante roles como `ADMIN`, `WAITER` y `DINER`.

Funcionalidades principales:

- Login de usuarios.
- Validación de credenciales.
- Generación de token JWT.
- Uso de Spring Security.
- Uso de BCrypt para contraseñas.
- Manejo de roles en memoria.

---

### diner-service

Microservicio encargado de administrar los comensales del restaurante.

Guarda información básica de los clientes, como RUN, nombre, apellido, teléfono, dirección, correo y fecha de nacimiento.

Funcionalidades principales:

- Crear comensales.
- Listar comensales.
- Buscar comensal por ID.
- Buscar comensal por RUN.
- Actualizar comensal.
- Eliminar comensal.

---

### table-service

Microservicio encargado de administrar las mesas del restaurante.

Cada mesa tiene un número, capacidad y estado. Esto permite controlar qué mesas existen y cuáles se encuentran disponibles, reservadas, ocupadas o deshabilitadas.

Funcionalidades principales:

- Crear mesas.
- Listar mesas.
- Buscar mesa por ID.
- Buscar mesa por número.
- Buscar mesas por estado.
- Buscar mesas por capacidad.
- Actualizar mesas.
- Eliminar mesas.

---

### reservation-service

Microservicio encargado de gestionar las reservas.

Una reserva se asocia a un comensal y a una mesa mediante sus IDs. Este microservicio utiliza referencias externas como `dinerId` y `tableId`, respetando la separación de bases de datos propia de una arquitectura de microservicios.

Funcionalidades principales:

- Crear reservas.
- Listar reservas.
- Buscar reserva por ID.
- Buscar reservas por comensal.
- Buscar reservas por mesa.
- Actualizar reservas.
- Eliminar reservas.

---

### waiter-service

Microservicio encargado de administrar los meseros del restaurante.

Guarda datos como RUN, nombre, apellido, teléfono, correo, turno y estado del mesero.

Funcionalidades principales:

- Crear meseros.
- Listar meseros.
- Buscar mesero por ID.
- Buscar mesero por RUN.
- Buscar meseros por turno.
- Buscar meseros por estado.
- Actualizar meseros.
- Eliminar meseros.

---

### menu-service

Microservicio encargado de administrar los productos del menú.

Cada producto tiene nombre, descripción, categoría, precio y estado. Este microservicio es importante porque `order-service` consulta los precios reales desde aquí para calcular subtotales y total del pedido.

Funcionalidades principales:

- Crear productos del menú.
- Listar productos.
- Buscar producto por ID.
- Buscar producto por nombre.
- Buscar productos por categoría.
- Buscar productos por estado.
- Buscar productos por precio máximo.
- Actualizar productos.
- Eliminar productos.

---

### order-service

Microservicio encargado de gestionar los pedidos del restaurante.

Este servicio es uno de los más importantes, porque coordina información de otros microservicios. Para crear un pedido, recibe un `dinerId`, `tableId`, `waiterId` y una lista de productos del menú con sus cantidades.

Antes de guardar el pedido, valida mediante OpenFeign que existan:

- El comensal en `diner-service`.
- La mesa en `table-service`.
- El mesero en `waiter-service`.
- Los productos en `menu-service`.

Luego calcula automáticamente:

- Precio unitario.
- Subtotal por producto.
- Total del pedido.
- Estado inicial del pedido.

Funcionalidades principales:

- Crear pedidos.
- Listar pedidos.
- Buscar pedido por ID.
- Buscar pedidos por comensal.
- Buscar pedidos por mesa.
- Buscar pedidos por mesero.
- Buscar pedidos por estado.
- Buscar pedidos por fecha.
- Cambiar estado del pedido.
- Eliminar pedidos.

---

### kitchen-service

Microservicio encargado de gestionar los tickets o comandas de cocina.

Este microservicio no crea pedidos directamente. Recibe un `orderId` ya existente desde `order-service` y crea un ticket de cocina asociado a ese pedido.

Permite manejar estados como:

- `PENDING`
- `IN_PREPARATION`
- `READY`
- `CANCELLED`

Funcionalidades principales:

- Crear ticket de cocina.
- Listar tickets.
- Buscar ticket por ID.
- Buscar ticket por orden.
- Buscar tickets por estado.
- Actualizar ticket.
- Cambiar estado del ticket.
- Eliminar ticket.

---

### inventory-service

Microservicio encargado de administrar el inventario del restaurante.

Permite registrar ingredientes o insumos, indicando stock actual, stock mínimo, unidad de medida y estado.

El estado se calcula según el stock:

- `AVAILABLE`
- `LOW_STOCK`
- `OUT_OF_STOCK`

Funcionalidades principales:

- Crear inventario.
- Listar inventario.
- Buscar inventario por ID.
- Buscar inventario por nombre.
- Buscar inventario por estado.
- Buscar inventario por unidad.
- Buscar productos con bajo stock.
- Actualizar stock.
- Actualizar inventario.
- Eliminar inventario.

---

### notification-service

Microservicio encargado de administrar notificaciones internas del sistema.

Permite registrar avisos relacionados con eventos del restaurante, como pedidos listos, reservas creadas o stock bajo.

Funcionalidades principales:

- Crear notificaciones.
- Listar notificaciones.
- Buscar notificación por ID.
- Buscar notificaciones por destinatario.
- Buscar notificaciones por tipo.
- Buscar notificaciones por estado.
- Cambiar estado de notificación.
- Eliminar notificaciones.

---

## Flujo lógico del sistema

El sistema funciona de la siguiente manera:

Un comensal se registra en `diner-service`. Luego puede realizar una reserva en `reservation-service`, asociándose a una mesa registrada en `table-service`.

Los meseros se administran en `waiter-service`, mientras que los productos disponibles para consumir se administran en `menu-service`.

Cuando se crea un pedido en `order-service`, este valida que existan el comensal, la mesa, el mesero y los productos del menú. Luego calcula el total del pedido y guarda la orden junto a sus detalles.

Después, `kitchen-service` toma una orden ya existente y genera una comanda o ticket de cocina, permitiendo controlar si está pendiente, en preparación o lista.

El inventario se administra desde `inventory-service`, controlando el stock de ingredientes y detectando productos con bajo stock.

Finalmente, `notification-service` permite registrar notificaciones internas, como avisos de pedido listo, reserva creada o bajo inventario.

`auth-service` permite autenticar usuarios y generar tokens JWT, dejando preparada la seguridad del sistema para controlar accesos mediante roles.

---

## Tecnologías utilizadas

- Java 25
- Spring Boot 4.0.6
- Spring Web / WebMVC
- Spring Data JPA
- MySQL
- Flyway Migration
- Lombok
- Jakarta Validation
- OpenFeign
- Spring Security
- JWT / JJWT
- BCrypt
- Laragon
- HeidiSQL
- Postman
- GitHub
- IntelliJ IDEA

---

## Bases de datos

Cada microservicio utiliza su propia base de datos:

| Microservicio | Base de datos |
|---|---|
| diner-service | `diner_db` |
| reservation-service | `reservation_db` |
| table-service | `table_db` |
| waiter-service | `waiter_db` |
| menu-service | `menu_db` |
| order-service | `order_db` |
| kitchen-service | `kitchen_db` |
| inventory-service | `inventory_db` |
| notification-service | `notification_db` |

`auth-service` actualmente utiliza usuarios en memoria, por lo que no requiere base de datos en la versión actual.

---

## Puertos utilizados

| Microservicio | Puerto |
|---|---|
| diner-service | 8081 |
| reservation-service | 8082 |
| table-service | 8083 |
| auth-service | 8084 |
| waiter-service | 8085 |
| menu-service | 8086 |
| order-service | 8087 |
| kitchen-service | 8088 |
| inventory-service | 8089 |
| notification-service | 8090 |

---

## Pasos para ejecutar el proyecto

### 1. Clonar los repositorios

Clonar cada repositorio desde GitHub:

```bash
git clone https://github.com/tomasrojasm-duoc/tbrm.restaurante.diner.git
git clone https://github.com/tomasrojasm-duoc/tbrm.restaurante.menu.git
git clone https://github.com/tomasrojasm-duoc/tbrm.restaurante.reservation.git
git clone https://github.com/tomasrojasm-duoc/tbrm.restaurante.notification.git
git clone https://github.com/tomasrojasm-duoc/tbrm.restaurante.inventory.git
git clone https://github.com/tomasrojasm-duoc/tbrm.restaurante.kitchen.git
git clone https://github.com/tomasrojasm-duoc/tbrm.restaurante.order.git
git clone https://github.com/tomasrojasm-duoc/tbrm.restaurante.auth.git
git clone https://github.com/tomasrojasm-duoc/tbrm.restaurante.waiter.git
git clone https://github.com/tomasrojasm-duoc/tbrm.restaurante.table.git

## Instrucciones para ejecutar el proyecto

### 2. Iniciar Laragon

Antes de ejecutar los microservicios, se debe abrir Laragon e iniciar el servicio de MySQL.

El proyecto fue desarrollado usando MySQL mediante Laragon, por lo que las bases de datos deben estar disponibles localmente en el puerto correspondiente.

---

### 3. Crear las bases de datos

Desde HeidiSQL, consola MySQL o el gestor de base de datos utilizado, se deben crear las bases de datos necesarias para cada microservicio:

```sql
CREATE DATABASE diner_db;
CREATE DATABASE reservation_db;
CREATE DATABASE table_db;
CREATE DATABASE waiter_db;
CREATE DATABASE menu_db;
CREATE DATABASE order_db;
CREATE DATABASE kitchen_db;
CREATE DATABASE inventory_db;
CREATE DATABASE notification_db;

### 4. Abrir cada microservicio en IntelliJ IDEA

Cada repositorio corresponde a un proyecto Spring Boot independiente. Se debe abrir cada microservicio en IntelliJ IDEA y verificar que Maven cargue correctamente las dependencias del archivo `pom.xml`.

También se debe revisar el archivo:

```txt
src/main/resources/application.properties

### 5. Verificar migraciones Flyway

Cada microservicio con base de datos posee una carpeta de migraciones en:

```txt
src/main/resources/db/migration

### 6. Ejecutar los microservicios

Se recomienda ejecutar los microservicios en el siguiente orden:

```txt
diner-service         → puerto 8081
table-service         → puerto 8083
waiter-service        → puerto 8085
menu-service          → puerto 8086
order-service         → puerto 8087
kitchen-service       → puerto 8088
inventory-service     → puerto 8089
notification-service  → puerto 8090
reservation-service   → puerto 8082
auth-service          → puerto 8084

### 7. Probar con Postman

Para verificar el funcionamiento del sistema, se recomienda probar primero los microservicios independientes:

```txt
diner-service
table-service
waiter-service
menu-service
inventory-service
notification-service

Y los que son más dependientes de otros microservicios

reservation-service
order-service
kitchen-service

Finalmente, probar auth-service.

#### Ejemplos de pruebas en Postman

A continuación se muestran ejemplos básicos para verificar el funcionamiento de algunos microservicios del sistema.

---


El microservicio `auth-service` permite iniciar sesión y generar un token JWT.

**Endpoint:**

```txt
POST http://localhost:8084/api/v1/auth/login

{
  "username": "admin",
  "password": "admin"
}

{
  "token": "eyJ..."
}

Prueba para listar comensales con diner-service

El microservicio diner-service permite consultar los comensales registrados.

**Endpoint:**

```txt
GET http://localhost:8081/api/v1/diners

[
  {
    "id": 1,
    "run": "12345678-9",
    "name": "Carlos",
    "lastName": "Muñoz",
    "phone": "+56912345678",
    "address": "Av. Siempre Viva 123",
    "email": "carlos.munoz@email.com",
    "birthday": "1998-05-20"
  }
]


### 8. Consideraciones importantes

Cada microservicio posee su propia base de datos y no accede directamente a tablas de otros microservicios.

Cuando un microservicio necesita validar información externa, utiliza comunicación mediante APIs REST. Por ejemplo, order-service valida la existencia de comensales, mesas, meseros y productos del menú consultando a sus respectivos microservicios.

Los IDs como dinerId, tableId, waiterId, menuItemId y orderId corresponden a registros previamente creados en otros microservicios.
