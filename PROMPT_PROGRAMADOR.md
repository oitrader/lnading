# Brief de desarrollo — TNT Muscle (MVP)

## 1. Contexto

TNT Muscle es un estudio privado de entrenamiento de fuerza en Lindora, Costa Rica. Se necesita construir el MVP de su plataforma digital:

1. **App móvil para socios** (iOS y Android).
2. **Panel web de administración** para el dueño, el coach y recepción.
3. **Landing page** de captación. Ya existe como HTML estático en `landing/tnt-muscle.html`; hay que publicarla y conectar sus botones.

El producto gira alrededor del **Sistema TNT 6·4·50**:

- **6 movimientos principales:** Back Squat, Bench Press, Deadlift, Push Press, Pull-Ups y Dips. También se usan ejercicios complementarios con barras, mancuernas y peso corporal.
- **4 semanas por ciclo.** Cada semana tiene una función: construcción técnica, volumen y control, intensidad, y consolidación y recuperación.
- **50 minutos por sesión.** Una sesión se divide en calentamiento y movilidad, series progresivas, movimiento principal, ejercicios complementarios y registro de resultados.

El coach publica la programación **cada domingo** y queda disponible de lunes a sábado.

**Referencias del repositorio:**
- `MVP_TNT_MUSCLE.md`: esquema funcional, modelo de datos y cronograma.
- `landing/tnt-muscle.html`: landing con los textos, planes y preguntas frecuentes vigentes.
- Mockup de pantallas de la app, que se entrega por separado: 10 pantallas en tema oscuro con acento naranja.

Si este brief contradice al esquema del MVP, **manda este brief**.

---

## 2. Stack requerido

| Capa | Tecnología |
|---|---|
| App móvil | React Native con Expo (TypeScript) y Expo Router |
| Backend | Supabase: PostgreSQL, Auth, Storage, Edge Functions y Row Level Security |
| Panel de administración | Next.js (App Router) + Supabase, desplegado en Vercel o Netlify |
| Notificaciones | Expo Push Notifications |
| Video | Cloudflare Stream o Mux (clips de técnica de 30 a 60 segundos) |
| Pagos | Pasarela que opere en Costa Rica (Tilopay, ONVO o similar), más una opción de SINPE Móvil con confirmación manual |
| Analítica | PostHog |
| Calidad | ESLint, Prettier, TypeScript estricto y pruebas unitarias de la lógica de negocio (reservas, límites y membresías) |

Si propone otra tecnología, justifíquela por escrito antes de empezar.

---

## 3. Reglas de negocio (obligatorias)

### 3.1 Planes y precios (USD)

| Plan | Mensual | Anual (pague 10, entrene 12) | Límite de reservas |
|---|---|---|---|
| TNT Base | 59 | 590 | Hasta 2 sesiones por semana |
| TNT Performance (recomendado) | 79 | 790 | Hasta 3 sesiones por semana |
| TNT Unlimited | 99 | 990 | Sin límite semanal, máximo 1 sesión por día |

- La **semana** va de lunes a domingo, en la zona horaria America/Costa_Rica.
- **Cuota anual de US$40** para los planes mensuales. Se cobra al activar la membresía y luego cada 12 meses. Los planes anuales ya la incluyen.
- Los precios, los límites y la cuota deben poder **configurarse desde el panel**, sin cambiar código.

### 3.2 Membresía fundadores

- Solo existen **40 cupos**. El contador debe ser atómico y, al llegar a 40, la oferta se cierra automáticamente.
- Beneficios que el sistema debe aplicar:
  - La cuota anual no se cobra durante el primer año.
  - El precio queda congelado 12 meses.
  - El socio tiene prioridad al elegir horarios. Implementación sugerida: los fundadores pueden reservar con más anticipación que el resto; el número de días es configurable.
- **Reserva de fundador con US$25.** Ese monto se acredita completo a la primera mensualidad o al plan anual.
- El texto legal debe decir que la categoría termina al completarse las 40 membresías.

### 3.3 Reservas y capacidad

- **Capacidad máxima de 5 personas por bloque de una hora**, las 24 horas, los 7 días de la semana.
- Las **sesiones guiadas** son bloques marcados con un coach asignado:
  - Lunes a viernes: 2 en la mañana y 2 en la tarde.
  - Sábado: 1 en la mañana.
  - Las horas exactas se configuran en el panel.
- Validaciones en el servidor, dentro de una transacción o con un bloqueo, para que no haya sobreventa:
  1. La membresía está activa.
  2. Queda cupo en el bloque.
  3. No se superó el límite semanal o diario del plan.
  4. El socio no tiene otra reserva a la misma hora.
  5. Si el bloque **no** es guiado, el socio completó la inducción.
- Parámetros configurables: límite de cancelación sin penalización (sugerido: 4 horas antes), anticipación máxima para reservar y política de no presentarse.
- Recordatorio push 2 horas antes de cada reserva.
- En el panel, el coach marca asistencia o no presentación.

### 3.4 Flujo de alta (debe coincidir con la landing)

1. **Crear cuenta** con correo y código OTP, o con Apple o Google.
2. **Elegir plan**, mensual o anual.
3. **Pagar** la primera mensualidad más la cuota anual, o el plan anual, o la reserva de fundador de US$25.
4. **Reservar la inducción**, que es una sesión guiada obligatoria.
5. **Reservar sesiones.** Antes de completar la inducción, el socio solo puede reservar bloques guiados.
6. **Activar el acceso biométrico.** En el MVP la activa el administrador de forma manual, y la app solo muestra el estado: *Pendiente*, *Activo* o *Suspendido*.

Además, antes del primer entrenamiento el socio debe aceptar un **descargo de responsabilidad de salud** y los **términos**.

### 3.5 Estados de la membresía

`pendiente_pago` → `activa` → `vencida` o `suspendida` o `cancelada`.

- Si la membresía está vencida, el socio no puede reservar y la app muestra dos opciones: renovar o escribir por WhatsApp.
- Todo cambio de estado queda registrado en una bitácora de auditoría.

### 3.6 Pagos

- **Pago con tarjeta** mediante la pasarela, con cobro recurrente mensual si la pasarela lo permite. Si no, se envía un enlace de pago con recordatorio.
- **Pago por SINPE Móvil:** el socio sube el comprobante y el administrador lo aprueba en el panel.
- Los webhooks de la pasarela se procesan en una Edge Function **idempotente**.
- Las membresías son un servicio que se consume fuera de la app, por lo que no aplica la compra dentro de la app de Apple o Google. Hay que verificar las normas vigentes de ambas tiendas antes de enviar la app a revisión.
- **Nunca** se almacenan datos de tarjeta.

### 3.7 Entrenamiento y progreso

- **Carga de la programación.** El coach carga la programación semanal en el panel: sesiones A y B, bloques, ejercicios, series, repeticiones, duración y un % de 1RM opcional. Puede duplicar la de una semana anterior.
- **Sesión guiada en la app.** Muestra cada bloque; en el movimiento principal indica la serie actual, las repeticiones y la carga sugerida (editable). Incluye temporizador de descanso y tiempo total.
- **Registro.** Cada serie guarda repeticiones, carga en kg y un RPE opcional.
- **1RM estimado** con la fórmula de Epley: `carga × (1 + reps / 30)`. Se usa la mejor serie del día.
  - En Pull-Ups y Dips se registra el **lastre adicional**, no el peso corporal.
- **Carga sugerida** = % de 1RM × 1RM estimado, redondeado a 2,5 kg. Si el socio no tiene 1RM registrado, se muestra "Registrar carga" en lugar de una sugerencia.
- **Pantalla de progreso:**
  - evolución del 1RM por movimiento;
  - sesiones totales;
  - consistencia, es decir, sesiones realizadas entre sesiones permitidas por su plan;
  - porcentaje de mejora contra el inicio del ciclo.
- **Marcas:** mejor 1RM por movimiento, con su tendencia.

---

## 4. Pantallas de la app

Sigue el mockup, con estas correcciones:

1. **Barra de navegación fija con 5 pestañas:** Inicio, Programa, Reservas, Progreso y Perfil. El Sistema 6·4·50 va dentro de Programa.
2. **Inicio:** saludo, entrenamiento del día, botón "Iniciar sesión" y accesos rápidos.
3. **Programa:** semana actual, detalle de cada sesión y catálogo de los 6 movimientos con su video.
4. **Sesión en curso:** series, carga, temporizador y registro.
5. **Reservas:** calendario, bloques con los cupos disponibles (por ejemplo, "3 de 5"), confirmación y cancelación.
6. **Progreso y Marcas.**
7. **Perfil:** datos del socio, plan y vencimiento, estado del acceso biométrico, próxima reserva, pagos y configuración.
8. **Onboarding:** registro, elección de plan, pago, descargo de salud y reserva de la inducción.

**Errores del mockup que no se deben replicar:**
- las fechas de 2024;
- que la próxima reserva del perfil no coincida con la reserva hecha;
- la carga de 100 kg en un 5 × 3 cuando la marca del socio es de 180 kg;
- que la pantalla "Mi semana" mezcle un tema claro con uno oscuro.

**Identidad visual:**

| Elemento | Valor |
|---|---|
| Fondo | `#0B0B0C` |
| Superficies | `#141416` |
| Bordes | `#2A2A2F` |
| Texto | `#F2EEE8` |
| Texto secundario | `#A39D95` |
| Acento | `#FF5A1F` |
| Tipografía | Big Shoulders Display para títulos y Barlow o Barlow Condensed para el texto. Son las mismas de la landing. |

La app debe verse **igual que el mockup aprobado**: tema oscuro, acento naranja, tarjetas, íconos y distribución de cada pantalla. Solo se corrigen los errores señalados arriba. Además, debe mantener la **misma identidad visual que la landing**, para que el socio sienta que la web y la app son un solo producto.

Todos los textos van en español y tratan al usuario de **usted**.

---

## 5. Panel de administración (web)

- **Socios:** alta, edición y búsqueda; plan, estado, fundador (sí o no), inducción completada y estado del acceso biométrico.
- **Membresías y pagos:** aprobar comprobantes de SINPE, historial, próximos vencimientos y membresías vencidas.
- **Horarios:** generar bloques por hora, marcar los bloques guiados, asignar coach y bloquear fechas (feriados o mantenimiento).
- **Reservas del día:** lista por bloque, marcar asistencia y ocupación.
- **Programación:** editor semanal que se publica el domingo, más el catálogo de ejercicios y videos.
- **Configuración:** precios, límites por plan, cuota anual, cupos de fundador y políticas de reserva y cancelación.
- **Métricas:**
  - socios activos;
  - porcentaje de ocupación por franja horaria;
  - tasa de no presentación;
  - renovaciones y bajas del mes;
  - ingreso mensual recurrente;
  - cupos de fundador vendidos.
- **Roles:** `admin`, `coach` y `socio`, controlados con RLS en Supabase.

---

## 6. Landing page

**El diseño aprobado es el de `landing/tnt-muscle.html` y debe quedar exactamente así:** mismos textos, orden de secciones, colores, tipografías, planes y preguntas frecuentes. No se rediseña. Tampoco se agregan imágenes sin aprobación del dueño.

Tareas:

- Publicar `landing/tnt-muscle.html` en el dominio del estudio. Hay que agregarle `<!doctype html>`, el `<head>` completo, metadatos Open Graph y el favicon.
- Conectar los enlaces pendientes. Al inicio del archivo hay un objeto `LINKS` donde se configuran:
  - `app`: tiendas de apps o registro web;
  - `whatsapp`: enlace `https://wa.me/506XXXXXXXX` con un mensaje prellenado;
  - el enlace exacto del estudio en Google Maps.
- El botón "Quiero ser miembro fundador" debe llevar al pago de la reserva de US$25.
- Instalar el Meta Pixel y Google Analytics 4 o PostHog, con eventos de clic en cada botón y de inicio de pago.

---

## 7. Seguridad, privacidad y aspectos legales

- Cumplir la **Ley 8968 de Costa Rica** (protección de datos personales): política de privacidad y consentimiento explícito.
- **No almacenar datos biométricos.** El equipo de acceso los administra por separado.
- Todas las reglas de reserva y pago se validan en el servidor, nunca solo en la app.
- Las claves secretas van en variables de entorno y nunca en el repositorio.
- La app debe permitir que el socio **elimine su cuenta**, porque Apple y Google lo exigen.

---

## 8. Entregables y cronograma (referencia: 10 a 12 semanas)

| Semanas | Entregable |
|---|---|
| 1–2 | Diseño final en Figma, modelo de datos, proyecto de Supabase y repositorio configurado |
| 3–4 | Autenticación, perfil, planes, membresías, pagos y panel (socios y horarios) |
| 5–6 | Programación semanal, catálogo de ejercicios y pantallas de Inicio y Programa |
| 7–8 | Sesión en curso, registro de series, 1RM y carga sugerida |
| 9 | Reservas con todas sus validaciones, recordatorios y asistencia |
| 10 | Progreso, marcas y métricas del panel |
| 11 | Beta cerrada con entre 15 y 30 socios (TestFlight y prueba interna de Play) |
| 12 | Correcciones y publicación en App Store y Google Play |

**Criterios de aceptación mínimos:**

- [ ] No es posible sobrevender un bloque. Debe haber una prueba con reservas simultáneas.
- [ ] Los límites de 2, 3 y 1 por día se respetan en todos los casos. Debe haber pruebas unitarias.
- [ ] El contador de fundadores se detiene exactamente en 40.
- [ ] Un pago aprobado activa la membresía; un pago vencido la bloquea.
- [ ] Sin la inducción completada, el socio no puede reservar bloques no guiados.
- [ ] La app funciona en iOS 16 o superior y en Android 10 o superior.
- [ ] El código fuente, las cuentas de las tiendas, Supabase y el dominio quedan **a nombre de TNT Muscle**, con un README de instalación y despliegue.

---

## 9. Lo que necesito que me envíe antes de empezar

1. Cotización por fases con entregables, y forma de pago contra entrega (20 / 30 / 30 / 20).
2. Portafolio con al menos 2 apps publicadas en ambas tiendas.
3. Dudas o supuestos sobre este brief.
4. Costos mensuales estimados de la infraestructura.
5. Condiciones de garantía (60 a 90 días) y tarifa de mantenimiento.
