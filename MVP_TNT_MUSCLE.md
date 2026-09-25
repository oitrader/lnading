# TNT Muscle — Esquema del MVP

**App de entrenamiento y membresía · Sistema 6·4·50**
Versión 1.0 · Septiembre 2026

> **Leyenda:** **[H]** Hecho · **[E]** Estimación · **[S]** Supuesto · **[O]** Opinión · **[R]** Recomendación

---

## 0. Conclusión ejecutiva

1. **[R] El MVP debe tener un objetivo concreto: retener socios y llenar horarios, no "tener una app".** Lo que se mide es asistencia, ocupación de clases y renovación de membresías.
2. **[R] Alcance del MVP: 5 módulos.** Autenticación y perfil, programa 6·4·50, sesión guiada con registro de cargas, progreso y marcas (1RM), y reservas con cupos. Además, un **panel web de administración** que el diseño no muestra y sin el cual la app no funciona.
3. **[R] Fuera del MVP:** pagos dentro de la app, integración con el acceso biométrico, chat, rutinas personalizadas por IA y wearables. Todo eso va en la fase 2, cuando haya datos de uso.
4. **[E] Inversión:** entre USD 12.000 y 25.000 con un desarrollador o una agencia latinoamericana, en un plazo de 10 a 12 semanas. Costos recurrentes de USD 60 a 150 al mes.
5. **[R] Stack sugerido:** React Native (Expo) + Supabase (PostgreSQL, autenticación y almacenamiento) + panel web en Next.js. Una sola base de código publica en iOS y Android.

---

## 1. Qué se necesita (inventario)

### 1.1 Decisiones de negocio (antes de programar)

| # | Decisión | Por qué importa |
|---|----------|-----------------|
| 1 | ¿La app es **solo para socios** o también se venderá el programa a personas externas? | Cambia el modelo de ingresos, los pagos y el alcance. **[R]** Empezar solo con socios. |
| 2 | Definición completa del **Sistema 6·4·50** | Cómo se reparten los 6 movimientos entre Fuerza A y Fuerza B, series, repeticiones y **% de 1RM por semana**. Es el núcleo del producto y hoy no está documentado. |
| 3 | Política de **reservas** | Cupos por horario, anticipación máxima, límite para cancelar y penalización por no presentarse. |
| 4 | Tipos de **membresía** | TNT Unlimited y otros planes, sus precios y lo que incluye cada uno (por ejemplo, reservas por semana). |
| 5 | Quién administra | Coach o recepción: quién crea horarios, da de alta socios y carga el programa. |

### 1.2 Contenido

- **Videos cortos (de 30 a 60 s) de los 6 movimientos** y de los ejercicios accesorios, con técnica y errores comunes.
- **Fotografía real** del gimnasio y de los coaches. **[O]** Las imágenes del mockup parecen generadas por IA. Sirven como referencia, pero con fotos reales el producto se ve más creíble.
- Textos: bienvenida, descripción del sistema, términos de uso y política de privacidad.

### 1.3 Cuentas y activos técnicos

| Elemento | Costo **[E]** | Notas |
|----------|---------------|-------|
| Apple Developer Program | USD 99 al año | A nombre de la empresa (requiere número D-U-N-S) |
| Google Play Console | USD 25, pago único | Cuenta de organización |
| Dominio y correo corporativo | USD 15 a 30 al año | Por ejemplo, app.tntmuscle.com |
| Supabase (Pro) | USD 25 al mes | Base de datos, autenticación y almacenamiento |
| Expo EAS (compilación y actualizaciones) | USD 0 a 99 al mes | El plan gratuito alcanza al inicio |
| Hosting de video (Cloudflare Stream o Mux) | USD 5 a 20 al mes | Según los minutos que se reproduzcan |
| Envío de correo (Resend o similar) y notificaciones push (Expo) | USD 0 a 20 al mes | |

### 1.4 Aspectos legales **[R]**

- **Política de privacidad** conforme a la **Ley 8968 de Costa Rica** (protección de datos personales). Tanto Apple como Google la exigen.
- **Descargo de responsabilidad de salud**, que el usuario acepta antes de su primer entrenamiento.
- **Datos biométricos:** la app **no debe almacenarlos**. Solo muestra si el acceso está activo o inactivo. La huella queda en el equipo de acceso.

### 1.5 Equipo

| Rol | Dedicación | Opción |
|-----|------------|--------|
| Product owner (usted o un delegado) | De 3 a 5 h por semana | Valida alcance, contenido y pruebas |
| Diseñador UI/UX | De 2 a 3 semanas | Pasa el mockup a un sistema de diseño en Figma |
| Desarrollador full-stack móvil | De 10 a 12 semanas | React Native + Supabase |
| QA / pruebas con socios reales | Últimas 2 semanas | Entre 15 y 30 socios en beta cerrada |

---

## 2. Alcance del MVP por pantalla (según el diseño)

| Pantalla del mockup | En el MVP | Funcionalidad mínima |
|---|---|---|
| **Splash / Login** | ✅ | Ingreso con correo y OTP o contraseña, y con Apple o Google. El administrador activa la cuenta. |
| **Inicio** | ✅ | Saludo, entrenamiento del día, botón "Iniciar sesión" y accesos rápidos. |
| **Sistema 6·4·50** | ✅ | Pantalla informativa y catálogo de los 6 movimientos con su video. |
| **Detalle de sesión (Fuerza A – Semana 2)** | ✅ | Bloques (calentamiento, progresión, movimiento principal, accesorios y vuelta a la calma) con duración y prescripción. |
| **Sesión en curso (Back Squat)** | ✅ | Serie actual, repeticiones, **carga sugerida editable**, temporizador de descanso y tiempo total. Registra cada serie. |
| **Progreso** | ✅ | Gráfico de 1RM estimado por movimiento, sesiones totales, consistencia y porcentaje de mejora. |
| **Mi semana** | ✅ | Calendario semanal con las sesiones completadas y el avance del ciclo. |
| **Mis marcas** | ✅ | Récord y 1RM estimado de cada movimiento, con su tendencia. |
| **Reservar** | ✅ | Calendario, horarios con cupos disponibles y confirmación. Incluye cancelar. |
| **Mi perfil** | ✅ (parcial) | Datos, estado de la membresía, próxima reserva y configuración. La tarjeta de acceso biométrico **solo muestra el estado**. |
| **Panel de administración web** *(no está en el diseño)* | ✅ **Crítico** | Socios, membresías, horarios y cupos, lista de reservas y asistencia, y edición del programa. |

---

## 3. Lógica funcional clave

### 3.1 Sistema 6·4·50

- **6 movimientos:** Back Squat, Bench Press, Deadlift, Push Press, Pull-Ups y Dips.
- **4 semanas por ciclo**, con progresión semanal.
- **Sesiones de 50 minutos**: calentamiento (8) + progresión (10) + principal (~15) + accesorios (12) + vuelta a la calma (5) = 50.
- **[S]** Fuerza A y Fuerza B se alternan, con tres sesiones por semana (por ejemplo, lunes, miércoles y sábado).
- **Carga sugerida = % de 1RM del socio × el factor de la semana**, redondeada al disco disponible (2,5 kg). **[S]** Ejemplo de una periodización que el coach debe validar:

| Semana | Principal | % 1RM |
|---|---|---|
| 1 | 5 × 5 | 75 % |
| 2 | 5 × 3 | 82 % |
| 3 | 4 × 2 | 88 % |
| 4 | Descarga 3 × 5 | 65 % |

### 3.2 Cálculo de 1RM

- Fórmula de Epley: **1RM = carga × (1 + repeticiones / 30)**. Se usa la mejor serie de cada sesión.
- En Pull-Ups y Dips se registra el **lastre adicional** ("+50 kg"), no el peso corporal.
- Si el socio es nuevo, la **semana 0 es de evaluación**: registra una serie de 5 repeticiones por movimiento y el sistema calcula su 1RM inicial.

### 3.3 Reservas

- Cada horario tiene su **capacidad**. La reserva se confirma dentro de una **transacción** para evitar que dos personas tomen el último cupo.
- Reglas configurables: una reserva por bloque horario, cancelación hasta X horas antes y un máximo de N reservas activas.
- Recordatorio push 2 horas antes. El administrador marca la asistencia desde el panel.

### 3.4 Membresía (sin pagos en el MVP)

- El administrador registra el plan y la fecha de vencimiento. Si la membresía está vencida, la app bloquea las reservas y muestra un botón de WhatsApp para renovar.
- **[R]** Los cobros siguen por el canal actual (SINPE Móvil, tarjeta en recepción). La pasarela (Tilopay, ONVO o BAC) va en la fase 2. **[S]** Hay que verificar la disponibilidad y las comisiones vigentes.

---

## 4. Modelo de datos (resumen)

```
users            (id, nombre, email, teléfono, foto, rol[socio|coach|admin], creado)
memberships      (id, user_id, plan, estado, inicio, vence)
exercises        (id, nombre, tipo[principal|accesorio], video_url, usa_lastre)
programs         (id, nombre "6·4·50", semanas=4)
program_weeks    (id, program_id, semana, factor_intensidad)
workouts         (id, program_id, codigo[A|B], nombre, duracion_min)
workout_blocks   (id, workout_id, orden, tipo, duracion_min)
block_exercises  (id, block_id, exercise_id, series, reps, pct_1rm)
user_cycles      (id, user_id, program_id, fecha_inicio, semana_actual)
session_logs     (id, user_id, workout_id, semana, inicio, fin, estado)
set_logs         (id, session_log_id, exercise_id, serie, reps, carga_kg, rpe)
personal_records (id, user_id, exercise_id, e1rm_kg, fecha)
class_slots      (id, fecha, hora_inicio, capacidad, coach_id)
bookings         (id, slot_id, user_id, estado[activa|cancelada|asistió|no_show])
```

---

## 5. Arquitectura recomendada

| Capa | Tecnología | Justificación |
|---|---|---|
| App móvil | **React Native + Expo** (TypeScript) | Un solo código para iOS y Android, actualizaciones OTA y buen soporte |
| Backend | **Supabase** (PostgreSQL, Auth, Storage, Edge Functions) | Poca infraestructura, seguridad por fila (RLS) y costo bajo |
| Panel admin | **Next.js** + Supabase | Web, sin publicación en tiendas |
| Video | Cloudflare Stream o Mux | Streaming adaptativo |
| Notificaciones | Expo Push | Gratuito en este volumen |
| Analítica | PostHog (plan gratuito) | Embudos, retención y eventos |

**Alternativa evaluada [O]:** plataformas de marca blanca como Trainerize, Glofox o Mindbody. Salen más rápido (2 a 4 semanas), pero cobran una mensualidad por socio o por sede, limitan la marca y no permiten modelar el Sistema 6·4·50 como producto propio. Convienen si el objetivo es solo gestionar reservas. Si el sistema es el activo diferenciador, conviene el desarrollo propio.

---

## 6. Cronograma (12 semanas) **[E]**

| Semanas | Fase | Entregable |
|---|---|---|
| 1–2 | Definición y diseño | Programa 6·4·50 documentado, Figma final, modelo de datos, cuentas creadas |
| 3–4 | Base | Autenticación, perfil, membresía, panel admin (socios y horarios) |
| 5–6 | Programa | Catálogo de ejercicios, programa, detalle de sesión, Inicio |
| 7–8 | Sesión en vivo | Registro de series, temporizador, carga sugerida, cálculo de 1RM |
| 9 | Reservas | Calendario, cupos, cancelación, recordatorios, asistencia en el panel |
| 10 | Progreso | Gráficos, marcas, Mi semana, métricas |
| 11 | Beta cerrada | De 15 a 30 socios por TestFlight y Play Console (prueba interna) |
| 12 | Lanzamiento | Correcciones, publicación en las tiendas y onboarding en el gimnasio |

---

## 7. Presupuesto **[E]**

| Escenario | Modalidad | Inversión inicial | Plazo | Riesgo |
|---|---|---|---|---|
| **Conservador** | No-code (FlutterFlow + Supabase) con un freelancer | USD 5.000 a 10.000 | 8 a 10 semanas | Techo técnico y dependencia de la plataforma |
| **Base** ✅ | Desarrollador senior o agencia pequeña en LATAM | USD 12.000 a 25.000 | 10 a 12 semanas | Medio. Hay que revisar su portafolio. |
| **Optimista (premium)** | Agencia establecida con PM, QA y diseño | USD 35.000 a 60.000 | 12 a 16 semanas | Bajo en ejecución, alto en costo |

- **Costo operativo mensual [E]:** USD 60 a 150, más mantenimiento evolutivo (entre el 15 % y el 20 % del desarrollo inicial al año).
- **[R] El escenario Base es el más conveniente.** La lógica de periodización y el registro de cargas son muy específicos para no-code, y el costo premium no se justifica antes de validar el uso.

---

## 8. Métricas de éxito (primeros 90 días)

| Métrica | Meta **[S]** |
|---|---|
| Socios activos que usan la app cada semana | ≥ 60 % |
| Sesiones registradas por socio activo por semana | ≥ 2 |
| Reservas hechas por la app en lugar de WhatsApp | ≥ 80 % |
| Ocupación promedio de los horarios | +15 % frente a la línea base |
| Tasa de no presentarse | < 10 % |
| Renovación mensual de membresías | +5 pp frente a la línea base |

**[R]** Mida la línea base **antes** del lanzamiento (asistencia, ocupación y renovación). Sin ella no se podrá demostrar el retorno.

---

## 9. Observaciones sobre el diseño actual

1. **Barra de navegación inconsistente:** la pantalla del Sistema muestra la pestaña "Sistema" en lugar de "Reservas". **[R]** Dejar 5 pestañas fijas: Inicio, Programa, Reservas, Progreso y Perfil. El Sistema 6·4·50 queda dentro de Programa.
2. **Fechas contradictorias:** Reservar selecciona el jueves 12 a las 9:00, pero el Perfil muestra como próxima reserva el sábado 13 a las 9:00. Además, las fechas son de 2024.
3. **Cargas incoherentes:** la marca de Back Squat es de 180 kg, pero en la sesión de 5 × 3 la carga actual es de 100 kg (~55 % de 1RM). En la semana 2 de un bloque de fuerza se esperaría entre el 80 % y el 85 % (~145–150 kg). Hay que corregirlo para que el diseño comunique bien la lógica.
4. **Falta el flujo de onboarding:** evaluación inicial de 1RM, aceptación del descargo de salud y selección de días de entrenamiento.
5. **Falta el panel de administración**, que es imprescindible para operar.
6. **Mi semana** combina un encabezado oscuro con un cuerpo claro. Hay que decidir un solo tema; se recomienda el oscuro, que es coherente con la marca.

---

## 10. Plan de acción priorizado

**Urgente**
- [ ] Documentar el Sistema 6·4·50 completo (A/B, series, repeticiones, % por semana)
- [ ] Definir las políticas de reservas y membresías
- [ ] Medir la línea base: asistencia, ocupación y renovación actuales
- [ ] Crear las cuentas de Apple Developer (D-U-N-S) y Google Play a nombre de la empresa

**Importante**
- [ ] Contratar al desarrollador o agencia (pedir 3 cotizaciones sobre este documento)
- [ ] Filmar los videos de los 6 movimientos y los accesorios
- [ ] Corregir las inconsistencias del diseño (sección 9) y cerrar el Figma
- [ ] Redactar la política de privacidad y los términos (Ley 8968)

**Mejora (fase 2)**
- [ ] Pagos y renovación automática dentro de la app
- [ ] Integración con el control de acceso biométrico
- [ ] Rankings y retos entre socios
- [ ] Programas adicionales (hipertrofia, principiantes)

**Opcional (fase 3)**
- [ ] Venta del programa 6·4·50 a usuarios externos (suscripción en línea)
- [ ] Integración con Apple Health, Google Fit y Strava
- [ ] Recomendaciones de carga con IA a partir del historial

---

## 11. Criterio para contratar al desarrollador

- Portafolio con **al menos 2 apps publicadas** en ambas tiendas.
- Cotización **por fases con entregables** y pagos contra entrega (20 / 30 / 30 / 20).
- **Código y cuentas a nombre de TNT Muscle**, con repositorio propio desde el primer día.
- Garantía de 60 a 90 días después del lanzamiento y una tarifa definida para el mantenimiento.
