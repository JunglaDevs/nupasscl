# Plataforma SaaS de Ticketing de Nueva Generación

## 1) Visión estratégica del producto

### North Star
Construir la plataforma **B2B2C de ticketing más flexible para productores pequeños y medianos en LatAm**, con dos motores integrados:
1. **Marketplace de descubrimiento** (adquisición orgánica y performance).
2. **Infraestructura white-label** (marca propia + operación profesional para productores).

### Propuesta de valor
- **Para productores:** control de branding, herramientas de venta/operación y crecimiento (RRPP, referidos, analytics, validación, cortesías) desde un solo panel.
- **Para asistentes:** experiencia de compra rápida, segura y móvil, con wallet y comunicación contextual.
- **Para la plataforma:** ingresos híbridos (fees + suscripción + addons), alta retención por lock-in operativo (datos, flujos y automatizaciones).

### Segmentos objetivo
- **SMB eventos**: fiestas, clubes, teatros independientes, promotores regionales.
- **Mid-market**: festivales, venues con programación continua, ligas/amateurs deportivas.
- **Enterprise-light**: marcas que requieren white-label + SLA.

### Modelo de negocio (unit economics)
- Comisión por ticket vendido (% + fijo opcional).
- Fee fijo por evento.
- Suscripción mensual por productor (Starter / Growth / Pro / White-label).
- Add-ons: campañas, soporte premium, validación onsite, diseño de branding, antifraude avanzado.
- Revenue share del módulo RRPP (opcional por plan).

### Diferenciador competitivo (vs Passline/Eventbrite/Ticketmaster)
- Branding real por productor (dominio custom, checkout tematizable, emails white-label).
- Arquitectura modular activable por plan.
- RRPP/Embajadores nativo con atribución y liquidaciones.
- Validación robusta online/offline con controles de fraude.
- UX mobile-first tanto para compradores como para staff operativo.

---

## 2) Mapa completo de módulos

1. **Marketplace público**
   - Home, búsqueda, categorías, ciudades, organizadores destacados.
   - Fichas de evento enriquecidas y SEO-ready.
2. **Checkout & Pagos**
   - Selección ticket, cupón, referido, pago multipasarela, confirmación, wallet.
3. **Dashboard Productor**
   - Gestión full-cycle de eventos, ventas, reportes, colaboradores.
4. **Motor de Tickets & Inventario**
   - Tipologías complejas, fases, reglas de visibilidad, stock, cortesías.
5. **Access Control / QR Validator**
   - App validador, claves temporales, anti-reuso, bitácora, modo offline.
6. **RRPP / Embajadores / Referidos**
   - Links únicos, cookies, atribución, ranking y comisiones.
7. **Cortesías**
   - Emisión gratuita con trazabilidad de stock e invitado.
8. **Módulo Venues (SimplePlaces+)**
   - Espacios, catálogo de servicios, eventos asociados, beneficios descargables.
9. **Wallet/App usuario**
   - Tickets, transferencias, historial, alertas.
10. **Analytics y BI operativo**
   - KPIs en tiempo real y reportes exportables.
11. **Admin Global**
   - Gobierno de la plataforma, riesgos, payouts, soporte y catálogo.
12. **Seguridad, antifraude y auditoría**
   - Roles, logs, firma QR, rate limiting, detecciones.

---

## 3) Arquitectura técnica

### Stack
- **Frontend web:** Next.js (App Router), TypeScript, Tailwind, shadcn/ui.
- **Backend:** NestJS modular (REST principal + GraphQL opcional para dashboards).
- **DB:** PostgreSQL (OLTP).
- **Cache/colas:** Redis + BullMQ.
- **Storage:** S3 (tickets PDF, imágenes, assets de marca).
- **Pagos:** Stripe, Mercado Pago, PayPal (adaptadores por proveedor).
- **Email:** Postmark/Sendgrid/Resend.
- **Infra:** Vercel (FE), AWS ECS/Fargate o Railway/Render (BE), CloudFront/CDN.
- **Observabilidad:** OpenTelemetry + Grafana + Sentry.

### Principios arquitectónicos
- **Modular monolith** al inicio (rápido, mantenible), preparado para extraer microservicios.
- **Arquitectura hexagonal** en backend (domain, application, infra).
- **Event-driven interno**: eventos de dominio (OrderPaid, TicketIssued, ScanPerformed).
- **Idempotencia** en pagos y webhooks.
- **Multi-tenant logical**: producer_id scoping + políticas RBAC/ABAC.

### Módulos backend (Nest)
- Auth & IAM
- Users
- Organizers
- Venues
- Events
- Ticketing
- Checkout
- Payments
- Referrals
- Validators
- Notifications
- Reports
- Admin
- Audit & Risk

### Escalabilidad
- Read replicas para reporting.
- Redis para sesiones y locks de inventario.
- CQRS light para reportes pesados.
- CDN para activos.
- Sharding futuro por región/productor grande.

---

## 4) Flujo del usuario comprador

1. Descubre evento (home/búsqueda/categoría/ciudad).
2. Revisa ficha (info, tickets, condiciones, organizador).
3. Selecciona tipo y cantidad (validación stock en tiempo real).
4. Completa datos de comprador y asistentes (si ticket nominal).
5. Ingresa cupón o link RRPP (atribución visible en resumen).
6. Elige método de pago (Stripe/MP/PayPal/transferencia/manual/free).
7. Confirmación de orden y emisión de tickets.
8. Recepción email + wallet interna + detalle de política de cambios.
9. Pre-evento: recordatorios, cambios de horario/lugar, upsells.
10. Post-evento: NPS, recomendaciones y follow para próximos eventos.

**Objetivo UX:** checkout <= 90 segundos en móvil para comprador recurrente.

---

## 5) Flujo del productor

1. Onboarding: crear organizer, branding básico, datos fiscales/pagos.
2. Crear evento:
   - datos base (nombre, categoría, fecha, venue, portada)
   - publicación (marketplace, privado, oculto)
   - tickets (tipos, fases, stock, precios, reglas)
   - cupones/referrals/RRPP
   - validadores y staff
3. Pre-lanzamiento: revisión legal, prueba compra, activar evento.
4. Operación en vivo: monitoreo ventas, conversiones, incidencias.
5. Día del evento: control de acceso, métricas de escaneo en tiempo real.
6. Cierre: conciliación, reportes, payouts, exportaciones, learnings.

---

## 6) Flujo del validador

1. Invitación del productor (rol validador, evento específico).
2. Login o acceso con key temporal de corta duración.
3. Selección del evento activo.
4. Escaneo QR:
   - válido no usado -> permitir acceso + marcar usado
   - ya usado -> alerta roja
   - inválido/no corresponde evento -> denegar
5. Modo offline:
   - descarga bloom/cached set de tickets válidos
   - firma local de scans
   - sync posterior con resolución de conflictos
6. Dashboard rápido de validador (ingresos por hora, pendientes, incidencias).

---

## 7) Flujo del RRPP / Embajador

1. Productor invita RRPP por email.
2. RRPP acepta y accede a panel propio.
3. Sistema genera links/códigos por evento/campaña.
4. RRPP comparte; sistema atribuye por link y/o cookie.
5. Venta cerrada -> se registra ambassador_sale.
6. RRPP ve métricas: clicks, conversión, tickets, revenue atribuida.
7. Productor liquida comisiones (manual o payout automatizado según país).

---

## 8) Wireframes textuales de cada pantalla

### Marketplace - Home
- Header: logo, buscador, categorías, login.
- Hero: claim + search bar universal.
- Carrusel “Destacados”.
- Bloques: por ciudad / por categoría.
- CTA para organizadores (“Publica tu evento”).
- Footer: FAQ, contacto, legal.

### Listado / Búsqueda
- Sidebar filtros: ciudad, fecha, precio, categoría, formato.
- Grid de cards evento con badge (agotando, nuevo, gratis).
- Ordenar: popularidad, fecha, precio.

### Event Detail
- Hero media + resumen lateral sticky de tickets.
- Tabs: Descripción / Venue / Políticas / Organizador.
- Bloque tickets: tipos, stock visible, beneficios.
- Mapa + eventos relacionados.

### Checkout
- Stepper: Tickets -> Datos -> Pago -> Confirmación.
- Right panel sticky: resumen, cupón, RRPP aplicado.
- Trust signals: métodos de pago, seguridad, políticas claras.

### Wallet Usuario
- Lista tickets activos/pasados.
- CTA transferir, reenviar, descargar.
- Notificaciones y cambios del evento.

### Dashboard Productor (overview)
- KPIs cards (ventas hoy, ingresos, conversión, escaneados).
- Gráfico ventas por hora/día.
- Tabla tickets por tipo + stock.
- Panel RRPP ranking.
- Alertas operativas.

### Dashboard Evento (detalle)
- Estado evento (activo/pausado/finalizado).
- Embudo (visitas -> checkout -> compra).
- Cortesías emitidas y pendientes.
- Validaciones por puerta/dispositivo.

### Panel RRPP
- Link principal y códigos.
- KPIs personales.
- Ventas atribuidas por ticket type.
- Historial de comisiones.

### Panel Validador (mobile-first)
- Botón scan full-screen.
- Resultado scan con color semántico.
- Conteo accesos en tiempo real.
- Log de últimos 20 scans.

### Admin Global
- Vista multi-tenant con filtros.
- Moderación eventos.
- Riesgo/pagos/incidencias.
- Gestión taxonomías (categorías/ciudades/banners).

---

## 9) Estructura del dashboard

### Navegación Productor
- Inicio
- Eventos
  - Todos
  - Borradores
  - Activos
  - Finalizados
- Ventas
- RRPP
- Validadores
- Cortesías
- Cupones
- Reportes
- Configuración (branding, dominio, integraciones, equipo)

### Widgets clave
- Revenue gross/net
- Tickets vendidos vs disponibles
- Conversión checkout
- Source attribution (directo, campaña, RRPP)
- Asistencia (escaneados/emitidos)

### Tablas críticas
- Orders con estado y riesgo
- Tickets con estado ciclo de vida
- Scans con dispositivo/validador
- Ambassador sales y payout status

---

## 10) Modelo relacional de base de datos

> Claves: `id` UUID, `created_at`, `updated_at`, `deleted_at` (soft delete cuando aplica).

### Núcleo IAM
- **roles**(id, key, name)
- **users**(id, email, phone, password_hash, oauth_provider, status, last_login_at)
- **user_roles**(user_id, role_id, organizer_id nullable)
- **sessions**(id, user_id, refresh_token_hash, ip, user_agent, expires_at)

### Organización y equipos
- **organizers**(id, legal_name, display_name, slug, tax_id, billing_email, brand_settings_json)
- **organizer_members**(id, organizer_id, user_id, role_in_org, status)

### Venues
- **venues**(id, organizer_id nullable, name, description, address, city_id, lat, lng, socials_json, services_json)
- **venue_assets**(id, venue_id, type, url, metadata_json)

### Eventos
- **events**(id, organizer_id, venue_id nullable, title, slug, category_id, city_id, starts_at, ends_at, timezone, status, visibility, description, terms, refund_policy)
- **event_images**(id, event_id, url, sort_order)
- **event_publish_settings**(event_id, is_marketplace_visible, is_featured, approval_status)

### Ticketing
- **ticket_types**(id, event_id, name, description, price_amount, currency, sales_start_at, sales_end_at, is_hidden, requires_name, max_per_order)
- **ticket_inventory**(ticket_type_id, total_stock, reserved_stock, sold_stock, courtesy_stock)
- **ticket_batches**(id, ticket_type_id, batch_name, stock, starts_at, ends_at, price_override nullable)
- **tickets**(id, order_item_id, event_id, ticket_type_id, attendee_name, attendee_email, qr_payload, qr_hash, status, used_at)

### Checkout/Orders
- **orders**(id, user_id nullable, organizer_id, event_id, subtotal, fees, discounts, total, currency, status, source_channel, referral_id nullable)
- **order_items**(id, order_id, ticket_type_id, qty, unit_price, discount_amount)
- **payments**(id, order_id, provider, provider_payment_id, amount, status, paid_at, raw_payload_json)
- **refunds**(id, payment_id, amount, reason, status)

### Promos/RRPP
- **coupons**(id, organizer_id, event_id nullable, code, type_percent_or_fixed, value, max_uses, starts_at, ends_at, status)
- **referrals**(id, organizer_id, event_id, ambassador_id nullable, token, cookie_days, landing_url)
- **ambassadors**(id, organizer_id, user_id, display_name, commission_type, commission_value, status)
- **ambassador_event_access**(id, ambassador_id, event_id, status)
- **ambassador_sales**(id, ambassador_id, order_id, event_id, attributed_revenue, commission_amount, status)

### Validación
- **validators**(id, organizer_id, user_id nullable, name, status)
- **validator_event_access**(id, validator_id, event_id, access_start_at, access_end_at, temp_key_hash)
- **scans**(id, ticket_id, event_id, validator_id, device_id, scan_result, scanned_at, offline_sync_batch_id nullable)

### Cortesías y payouts
- **courtesy_tickets**(id, event_id, ticket_type_id, issued_by_user_id, guest_name, guest_email, order_id, note)
- **payouts**(id, organizer_id, period_start, period_end, gross, fees, net, status, paid_at)

### Sistema y auditoría
- **notifications**(id, user_id, channel, template_key, payload_json, status, sent_at)
- **audit_logs**(id, actor_user_id, entity_type, entity_id, action, before_json, after_json, ip, user_agent)
- **support_tickets**(id, requester_user_id, organizer_id nullable, subject, status, priority)

Índices críticos:
- unique: users.email, organizers.slug, events.slug, coupons.code(scope).
- compuestos: tickets(event_id,status), orders(event_id,status,created_at), scans(event_id,scanned_at), ambassador_sales(ambassador_id,event_id).

---

## 11) Endpoints API (REST)

### Auth
- `POST /v1/auth/register`
- `POST /v1/auth/login`
- `POST /v1/auth/refresh`
- `POST /v1/auth/logout`
- `POST /v1/auth/oauth/:provider`

### Users / Profile
- `GET /v1/me`
- `PATCH /v1/me`
- `GET /v1/me/tickets`
- `POST /v1/me/tickets/:id/transfer`

### Organizers
- `POST /v1/organizers`
- `GET /v1/organizers/:id`
- `PATCH /v1/organizers/:id`
- `POST /v1/organizers/:id/members`
- `PATCH /v1/organizers/:id/branding`

### Events
- `POST /v1/events`
- `GET /v1/events/:id`
- `PATCH /v1/events/:id`
- `POST /v1/events/:id/duplicate`
- `POST /v1/events/:id/publish`
- `POST /v1/events/:id/pause-sales`
- `POST /v1/events/:id/end-sales`
- `GET /v1/public/events` (marketplace con filtros)

### Ticketing
- `POST /v1/events/:id/ticket-types`
- `PATCH /v1/ticket-types/:id`
- `POST /v1/ticket-types/:id/batches`
- `GET /v1/events/:id/inventory`

### Checkout/Orders
- `POST /v1/checkout/quote`
- `POST /v1/checkout/orders`
- `POST /v1/checkout/orders/:id/confirm`
- `GET /v1/orders/:id`

### Payments
- `POST /v1/payments/stripe/create-intent`
- `POST /v1/payments/mercadopago/preference`
- `POST /v1/payments/paypal/order`
- `POST /v1/payments/webhooks/:provider`

### Scans / Validators
- `POST /v1/scans/validate`
- `POST /v1/scans/offline-sync`
- `GET /v1/events/:id/scans`
- `POST /v1/events/:id/validators`

### RRPP / Embajadores
- `POST /v1/events/:id/ambassadors/invite`
- `GET /v1/events/:id/ambassadors`
- `POST /v1/events/:id/referrals`
- `GET /v1/ambassadors/me/metrics`

### Coupons
- `POST /v1/coupons`
- `PATCH /v1/coupons/:id`
- `POST /v1/coupons/validate`

### Reports
- `GET /v1/reports/events/:id/overview`
- `GET /v1/reports/events/:id/sales-timeseries`
- `GET /v1/reports/events/:id/ambassadors`
- `GET /v1/reports/events/:id/attendance`
- `GET /v1/reports/export?format=csv`

### Admin
- `GET /v1/admin/events`
- `POST /v1/admin/events/:id/approve`
- `POST /v1/admin/events/:id/reject`
- `GET /v1/admin/organizers`
- `PATCH /v1/admin/users/:id/status`
- `GET /v1/admin/finance/commissions`

---

## 12) Estructura de carpetas del proyecto

```txt
nupasscl/
  apps/
    web/                      # Next.js marketplace + producer dashboard + wallet
      src/
        app/
          (public)/
          (auth)/
          (producer)/
          (admin)/
          (validator)/
        components/
        modules/
        services/
        styles/
    api/                      # NestJS
      src/
        main.ts
        modules/
          auth/
          users/
          organizers/
          events/
          ticketing/
          checkout/
          payments/
          referrals/
          validators/
          reports/
          admin/
        common/
          guards/
          interceptors/
          decorators/
        infra/
          db/
          cache/
          queues/
  packages/
    ui/                       # design system
    config/                   # eslint, tsconfig, tailwind presets
    sdk/                      # cliente TS para API
  infra/
    docker/
    terraform/
    ci/
  docs/
    adr/
    api/
```

---

## 13) Roadmap por fases de desarrollo

### Fase 0 (2-3 semanas) — Foundations
- Discovery + PRD + UX mapping.
- Arquitectura base, diseño sistema visual, setup monorepo, CI/CD.

### Fase 1 (6-8 semanas) — MVP comercial
- Marketplace básico + detalle evento.
- Creación de eventos/tickets.
- Checkout con 1 pasarela (Stripe o MP según mercado).
- Emisión ticket QR + email.
- Validador online básico.
- Dashboard productor core (ventas, stock, export CSV).

### Fase 2 (6 semanas) — Diferenciadores
- RRPP/embajadores + atribución.
- Cortesías.
- Validación offline.
- Cupones avanzados.
- Wallet usuario.

### Fase 3 (6-8 semanas) — Escala y white-label
- Dominio custom + branding ampliado.
- Multipasarela completa.
- Admin global avanzado.
- Payouts y conciliaciones.
- Observabilidad/SLOs/alerting.

### Fase 4 (continuo)
- Machine learning antifraude.
- Recomendador de eventos.
- Dynamic pricing por demanda.
- API pública y marketplace de integraciones.

---

## 14) MVP recomendado

### Qué sí entra
- Auth + roles principales (admin, productor, comprador, validador).
- Organizer + eventos + ticket types + stock.
- Checkout + pago principal + emisión QR.
- Panel productor con KPIs mínimos.
- Escaneo QR online.
- Gestión básica de cupones.

### Qué no entra (fase posterior)
- White-label avanzado (dominio custom full).
- RRPP completo con payout automático.
- Offline sync sofisticado con conflict resolution avanzada.
- Multi-pasarela completa desde día 1.

**Meta MVP:** lanzar 10-20 productores activos y validar CAC/LTV + tasa de recompra.

---

## 15) Versión escalable futura

- Arquitectura multirregión (LatAm + Europa).
- Data warehouse (BigQuery/Redshift) para BI profundo.
- Feature flags por plan/tenant.
- App nativa iOS/Android para wallet + scanner pro.
- Motor de pricing y segmentación automatizada.
- Integraciones externas: Meta Ads CAPI, Google Analytics server-side, CRM (HubSpot), contabilidad.

---

## 16) Features que la hacen mejor que Passline

1. **Branding extremo por productor**
   - Dominios, temas, checkout y comunicación custom por tenant.
2. **RRPP nativo de verdad**
   - Atribución híbrida link+cookie+cupón y ranking accionable.
3. **Operación integral del evento**
   - Validadores con permisos finos + modo offline confiable.
4. **UX de productor más clara**
   - Dashboard orientado a decisiones (embudo + ventas + asistencia).
5. **Cortesías con trazabilidad**
   - Stock, historial e impacto separados de revenue monetario.
6. **Arquitectura API-friendly**
   - SDK, webhooks, módulos activables por plan.
7. **Mobile-first real**
   - Compra, wallet y validación pensadas primero para móvil.
8. **Modelo comercial flexible**
   - Comisión, suscripción, fee por evento y add-ons combinables.

---

## Seguridad y cumplimiento (transversal)

- JWT corto + refresh rotativo.
- RBAC + ABAC por organizer/event.
- CSRF/XSS/SQLi mitigado (helmet, validation pipes, ORM parametrizado).
- Rate limiting por IP/usuario/ruta sensible.
- Firma HMAC de webhooks.
- QR firmado (JWS/JWT) + hash server-side + check anti-replay.
- Logs de auditoría inmutables (WORM policy opcional en storage).
- Detección fraude: velocity checks, fingerprinting device, score de riesgo.
- Cumplimiento: PCI DSS (delegado en pasarela), GDPR/LPDP local.

---

## KPIs de éxito del producto

- GMV mensual.
- Tickets vendidos por productor activo.
- Conversión visita -> compra.
- Tiempo promedio de checkout.
- Tasa de chargebacks/fraude.
- Tasa de asistencia (scans/tickets emitidos).
- NRR de productores.
- % ventas atribuibles a RRPP/referidos.

Este blueprint está diseñado para ejecución real: permite lanzar rápido un MVP comercial y evolucionar a una plataforma enterprise-ready sin rehacer la base técnica.
