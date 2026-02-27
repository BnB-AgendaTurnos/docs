# Reglas de Negocio — MVP  
**Sistema:** Agenda Turnos  
**Empresa:** B&B Software  

---

# 1. Objetivo

Este documento define las reglas funcionales, restricciones y permisos por rol correspondientes a la versión MVP (Minimum Viable Product) del sistema Agenda Turnos.

El sistema se encuentra en evolución controlada.  
Las reglas aquí definidas representan el comportamiento esperado del producto, indicando qué funcionalidades se encuentran implementadas actualmente y cuáles están planificadas para próximas iteraciones.

---

# 2. Alcance del MVP

El MVP contempla la gestión de:

- Usuarios  
- Sucursales  
- Turnos  
- Reservas  

Incluye:

- Administración básica de usuarios
- Gestión de sucursales
- Creación y administración de turnos
- Creación y cancelación de reservas
- Control de estados de reserva
- Asociación de turnos a sucursales

El sistema contempla los roles `ADMIN`, `CLIENTE` y `PROFESOR` en su modelo de datos.  
El rol `PROFESOR` está definido como parte de la arquitectura escalable del sistema, pero no forma parte del alcance funcional activo del MVP.

---

# 3. Reglas de Negocio


## 3.1 Usuarios

### Reglas

- El email debe ser único en el sistema.  
  **Estado:** Implementado.

- Todo usuario se crea con estado de cuenta `ACTIVA`.  
  **Estado:** Implementado.

- El rol debe pertenecer a los valores definidos en el sistema (`ADMIN`, `CLIENTE`, `PROFESOR`).  
  **Estado:** Implementado (validado por enumeración del modelo).

### Estados de cuenta

- `ACTIVA`
- `INACTIVA`

### Eliminación

- No se realiza eliminación física.
- La desactivación se realiza cambiando el estado de cuenta a `INACTIVA`.  
  **Estado:** Implementado.

### Restricciones

- Solo a los usuarios con cuenta `ACTIVA` se le podrán generar nuevas reservas.  
  **Estado:** Planificado.


## 3.2 Sucursales

### Reglas

- El horario de cierre debe ser posterior al horario de apertura.  
  **Estado:** Planificado.

### Restricciones

- No pueden existir turnos asociados a sucursales inexistentes.  
  **Estado:** Implementado.

### Eliminación

- No se realiza eliminación física.
- La desactivación de una sucursal se realizará mediante un mecanismo de eliminación lógica a definir.
  **Estado:** Planificado.


## 3.3 Turnos

### Reglas

Un turno debe:

- Estar asociado a una sucursal válida.  
  **Estado:** Implementado.

- Tener una fecha válida.  
  **Estado:** Implementado.

- Tener hora de inicio y hora de fin.  
  **Estado:** Implementado.

- Cumplir que `hora_inicio < hora_fin`.  
  **Estado:** Planificado.

- Tener `cupo_maximo` mayor a 0.  
  **Estado:** Planificado.

- Respetar el rango horario definido por la sucursal.  
  **Estado:** Planificado.

### Restricciones adicionales

- No se permiten turnos con fecha en el pasado.  
  **Estado:** Planificado.

### Eliminación

- No se realizará eliminación física.
- La baja de un turno se gestionará mediante un mecanismo de eliminación lógica a definir.
- Los turnos con reservas asociadas deberán conservar su información histórica.  
  **Estado:** Planificado.


## 3.4 Reservas

### Reglas

Para que una reserva sea válida deben cumplirse las siguientes condiciones:

- El usuario debe existir.  
  **Estado:** Implementado.

- El turno debe existir.  
  **Estado:** Implementado.

- El turno debe tener cupo disponible.  
  **Estado:** Implementado.

- El estado inicial debe ser `RESERVADO`.  
  **Estado:** Implementado.

### Reglas adicionales

- Un usuario solo puede tener una reserva por turno.  
  **Estado:** Planificado (pendiente restricción única).

- No se permiten reservas en turnos ya ocurridos.  
  **Estado:** Planificado.

- Solo usuarios con cuenta `ACTIVA` podrán reservar.  
  **Estado:** Planificado.

### Estados posibles

- `RESERVADO`
- `CANCELADO`
- `ASISTIO`
- `AUSENTE`  

  **Estado:** Implementado.

### Cancelación

- No se elimina físicamente la reserva.
- Se cambia su estado a `CANCELADO`.  
  **Estado:** Implementado.

### Control de cupo

- El sistema impide que la cantidad de reservas en estado `RESERVADO` supere el `cupo_maximo` del turno.  
- Las reservas en estado `CANCELADO`, `ASISTIO` o `AUSENTE` no ocupan cupo.  
  **Estado:** Implementado.

---

# 4. Permisos por Rol

Actualmente el sistema no implementa autenticación ni middleware de autorización.  
Los permisos definidos a continuación representan el comportamiento esperado del sistema en su siguiente fase evolutiva.  
**Estado general:** Planificado (Autorización por rol).

El sistema está diseñado bajo un modelo multi-sucursal.  
Cada usuario con rol `ADMIN` pertenece a una única sucursal y su alcance operativo se limita a dicha sucursal.
La gestión estructural de sucursales está reservada para una futura capa de administración organizacional y no forma parte del MVP.

## 4.1 ADMIN

Acceso administrativo limitado a la sucursal a la que pertenece.

Puede:

- Crear, listar y actualizar usuarios de su sucursal.
- Desactivar usuarios de su sucursal.
- Crear, listar, actualizar y eliminar turnos de su sucursal.
- Visualizar reservas asociadas a turnos de su sucursal.
- Modificar estados de reserva de su sucursal.
- Cancelar reservas dentro de su sucursal.
- Consultar información de su sucursal.

No puede:

- Administrar otras sucursales.
- Modificar información estructural de sucursales.
- Acceder a datos de otras sucursales.


## 4.2 CLIENTE

Puede:

- Consultar turnos disponibles.
- Crear reservas.
- Cancelar sus propias reservas.
- Consultar sus reservas.

No puede:

- Administrar usuarios.
- Administrar turnos.
- Administrar sucursales.
- Modificar reservas de otros usuarios.


## 4.3 PROFESOR

- Definido en el modelo de datos.
- No activo funcionalmente en el MVP.
- Incorporación planificada para gestión de asistencia y visualización de turnos asignados.

---

# 5. Principios del MVP

- Se prioriza la integridad de datos.
- Se evita la pérdida de información histórica.
- Las reglas de negocio se implementan en la capa de servicios.
- El modelo está diseñado para soportar escalabilidad de roles y funcionalidades.
- La evolución del sistema se realiza bajo un enfoque incremental y controlado.

---

# 6. Próximas Iteraciones Técnicas

Las siguientes mejoras están previstas para los próximos avances del proyecto:

- Implementación de autenticación mediante JWT.
- Middleware de autorización por rol.
- Validaciones robustas a nivel de API.
- Validaciones temporales en turnos.

La planificación podrá ampliarse conforme evolucione el producto y sus necesidades operativas.