# Sprints 1–3 (historias + criterios de aceptación)

> Cadencia recomendada: 2 semanas por sprint, equipo base 6-8 personas.

## Sprint 1 — Foundation + Eventos + Ticket Types

### Objetivo
Tener autenticación operativa, organizer, creación/publicación de eventos y ticket types.

### Historias comprometidas
- AUTH-001, AUTH-002, AUTH-003, AUTH-004
- ORG-001
- EVT-001, EVT-002, EVT-003
- TKT-001, TKT-003

### Entregables
- API auth + perfiles.
- API organizers/events/ticket-types.
- Vista básica de creación de evento en dashboard.
- Listado público de eventos publicado con filtros básicos.

### Criterios de salida del sprint
- Se puede crear una cuenta productora y publicar un evento con al menos 1 tipo de ticket.
- Un comprador puede descubrir el evento desde marketplace público.

### Riesgos
- Definición incompleta de taxonomías (categorías/ciudades).
- Contenido y validaciones legales (política de devoluciones/condiciones).

---

## Sprint 2 — Checkout + Pagos + Emisión de tickets

### Objetivo
Cerrar compra end-to-end con pasarela principal y generación de tickets QR.

### Historias comprometidas
- TKT-002
- CHK-001, CHK-002, CHK-004
- PAY-001, PAY-002
- CHK-003
- TKT-004

### Entregables
- Quote/orden/confirmación de orden.
- Webhook idempotente de pago.
- Emisión de ticket y envío de email de confirmación.
- Bloqueo de sobreventa por reservas temporales de stock.

### Criterios de salida del sprint
- Flujo compra exitoso (happy path) desde evento hasta ticket emitido.
- Reintento de webhook no genera duplicados.

### Riesgos
- Reglas de expiración de reservas (TTL) no calibradas.
- Manejo de estados intermedios de pago (requires_action, pending).

---

## Sprint 3 — Validación QR online + Dashboard ventas

### Objetivo
Operar día de evento con escaneo online y panel de métricas mínimas.

### Historias comprometidas
- VAL-001, VAL-002, VAL-003, VAL-004
- REP-001, REP-002, REP-003
- UI-001

### Entregables
- Endpoint/flujo de validación QR con anti-reuso.
- Bitácora de escaneos por validador/dispositivo.
- Dashboard productor con ventas, ingresos y stock.
- Export CSV de órdenes/tickets.

### Criterios de salida del sprint
- En entorno staging, un evento puede ejecutar check-in real con validadores.
- Productor ve ventas y stock actualizado en dashboard.

### Riesgos
- Latencia en validación en picos de acceso.
- Inconsistencias por relojes/dispositivos.

---

## Historias listas para Jira/Linear (template)

### Template Story
- **Título**: `[DOMINIO] Como <rol> quiero <objetivo> para <resultado>`
- **Descripción**: contexto y alcance.
- **Criterios de aceptación**:
  1. Dado ... cuando ... entonces ...
  2. ...
- **No incluye**: límites explícitos.
- **Dependencias**: IDs relacionadas.
- **Definición de terminado**: DoD transversal + pruebas.
