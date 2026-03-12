# Backlog priorizado (P0/P1/P2) — Formato listo para Jira/Linear

## Convenciones
- **ID**: prefijo por dominio (AUTH, EVT, TKT, CHK, PAY, VAL, REP, ADM)
- **Tipo**: Epic / Story / Task
- **Prioridad**: P0 (MVP), P1 (post-MVP corto), P2 (escalado)
- **Estimación**: en puntos (1,2,3,5,8)
- **DoD**: definición de terminado transversal

### DoD transversal
1. Endpoint/documentación actualizada en OpenAPI cuando aplica.
2. Validaciones, manejo de errores y logs de auditoría básicos.
3. Tests mínimos (unit + integración happy path).
4. Métricas instrumentadas (evento analítico o log estructurado).
5. Permisos por rol aplicados.

---

## EPIC P0-01: Identidad y acceso (MVP)

| ID | Tipo | Título | Prioridad | Estimación | Dependencias |
|---|---|---|---|---:|---|
| AUTH-001 | Story | Registro con email + password | P0 | 3 | — |
| AUTH-002 | Story | Login con JWT + refresh token rotativo | P0 | 5 | AUTH-001 |
| AUTH-003 | Story | Middleware RBAC base (admin, productor, comprador, validador) | P0 | 5 | AUTH-002 |
| AUTH-004 | Task | Endpoint `GET /v1/me` y edición perfil básica | P0 | 2 | AUTH-002 |

### Criterios de aceptación (AUTH-002)
- Dado un usuario válido, cuando envía credenciales correctas, entonces recibe `access_token` + `refresh_token`.
- Dado un refresh token revocado/expirado, cuando solicita refresh, entonces responde 401.
- Se audita login exitoso y fallido con IP y user-agent.

---

## EPIC P0-02: Organización y eventos

| ID | Tipo | Título | Prioridad | Estimación | Dependencias |
|---|---|---|---|---:|---|
| ORG-001 | Story | Crear organizer con branding básico | P0 | 3 | AUTH-003 |
| EVT-001 | Story | Crear evento (draft) con datos principales | P0 | 5 | ORG-001 |
| EVT-002 | Story | Editar evento y publicar | P0 | 5 | EVT-001 |
| EVT-003 | Story | Listado público de eventos con filtros básicos | P0 | 5 | EVT-002 |
| EVT-004 | Task | Duplicar evento | P1 | 3 | EVT-001 |

### Criterios de aceptación (EVT-003)
- Permite filtrar por ciudad, fecha y categoría.
- Devuelve únicamente eventos `published` y aprobados para marketplace.
- Respuesta paginada y ordenable por fecha.

---

## EPIC P0-03: Ticketing e inventario

| ID | Tipo | Título | Prioridad | Estimación | Dependencias |
|---|---|---|---|---:|---|
| TKT-001 | Story | Crear tipos de ticket por evento | P0 | 5 | EVT-001 |
| TKT-002 | Story | Gestión de stock e inventario por ticket type | P0 | 5 | TKT-001 |
| TKT-003 | Story | Reglas base: ventana de venta y max por orden | P0 | 3 | TKT-001 |
| TKT-004 | Story | Emisión de ticket con ID único + QR firmado | P0 | 8 | CHK-003 |
| TKT-005 | Task | Soporte cortesías | P1 | 5 | TKT-002 |

### Criterios de aceptación (TKT-004)
- Ticket emitido sólo para órdenes pagadas o de total cero.
- QR contiene payload firmado verificable por backend.
- Estado inicial del ticket: `active`.

---

## EPIC P0-04: Checkout y pagos (1 pasarela)

| ID | Tipo | Título | Prioridad | Estimación | Dependencias |
|---|---|---|---|---:|---|
| CHK-001 | Story | Crear quote de checkout (subtotal, fees, descuentos) | P0 | 5 | TKT-002 |
| CHK-002 | Story | Crear orden `pending` con reserva temporal de stock | P0 | 8 | CHK-001 |
| PAY-001 | Story | Integrar pasarela principal (Stripe o MP) | P0 | 8 | CHK-002 |
| CHK-003 | Story | Confirmación de orden + emisión tickets + email | P0 | 8 | PAY-001 |
| CHK-004 | Task | Cupón básico (porcentaje/fijo) | P0 | 5 | CHK-001 |
| PAY-002 | Task | Webhook idempotente de pagos | P0 | 5 | PAY-001 |

### Criterios de aceptación (PAY-002)
- Reprocesar webhook duplicado no duplica pagos/tickets.
- Errores de firma webhook retornan 401.
- Se registran eventos de conciliación.

---

## EPIC P0-05: Validación QR online

| ID | Tipo | Título | Prioridad | Estimación | Dependencias |
|---|---|---|---|---:|---|
| VAL-001 | Story | Alta de validador por evento | P0 | 3 | AUTH-003, EVT-002 |
| VAL-002 | Story | Endpoint validar QR (online) | P0 | 8 | TKT-004 |
| VAL-003 | Story | Marcar ticket usado e impedir reuso | P0 | 5 | VAL-002 |
| VAL-004 | Task | Bitácora de escaneos por dispositivo/validador | P0 | 3 | VAL-002 |

### Criterios de aceptación (VAL-002)
- Si ticket es válido y activo del evento correcto: `valid`.
- Si ticket ya usado: `already_used`.
- Si firma inválida/no existe/no pertenece al evento: `invalid`.

---

## EPIC P0-06: Dashboard productor y reportes básicos

| ID | Tipo | Título | Prioridad | Estimación | Dependencias |
|---|---|---|---|---:|---|
| REP-001 | Story | Resumen de ventas por evento (hoy, total, tickets) | P0 | 5 | CHK-003 |
| REP-002 | Story | Ventas por tipo de ticket + stock restante | P0 | 5 | TKT-002, CHK-003 |
| REP-003 | Task | Export CSV de órdenes/tickets | P0 | 3 | REP-001 |
| UI-001 | Story | Vista dashboard productor (cards + tabla) | P0 | 8 | REP-001, REP-002 |

---

## P1 (post-MVP corto)
- RRPP/Embajadores base con links únicos y métricas básicas.
- Cortesías completas con historial.
- Wallet usuario (mis tickets + reenvío).
- Duplicar evento, pausa/fin de ventas.
- Cupones avanzados por reglas.

## P2 (escala)
- Validación offline + sincronización de conflictos.
- White-label avanzado (dominios custom, emails custom por tenant).
- Multipasarela completa (Stripe + MP + PayPal).
- Payouts automáticos y conciliación avanzada.
- Antifraude con scoring y reglas dinámicas.
