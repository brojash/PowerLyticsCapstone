# PowerLytics

PowerLytics es una plataforma web para la gestión integral de entrenamiento de powerlifting. Conecta a coaches y atletas en un mismo espacio para planificar entrenamientos, registrar rendimiento, preparar competencias y administrar suscripciones.

El proyecto demuestra experiencia en desarrollo full-stack con Next.js, React, TypeScript, Supabase, Row Level Security, integración de pagos y diseño de interfaces con componentes reutilizables.

## Funcionalidades principales

### Para coaches

- Dashboard con métricas de atletas, bloques, ingresos y competencias.
- Gestión de atletas e invitaciones.
- Creación de bloques de entrenamiento.
- Creación de días y asignación de ejercicios.
- Configuración de series, repeticiones, peso objetivo, RPE y porcentajes.
- Seguimiento del rendimiento de cada atleta.
- Planificación de competencias y game plans.
- Gestión de facturación y notas.
- Suscripciones con diferentes límites de atletas.

### Para atletas

- Dashboard personal de entrenamiento.
- Visualización de bloques asignados por el coach.
- Consulta de días y ejercicios programados.
- Registro de series y resultados reales.
- Historial de rendimiento, récords personales y peso corporal.
- Calendario de entrenamiento.
- Acceso a información del coach y del plan asignado.

## Stack tecnológico

### Frontend

- **Next.js 16** con App Router.
- **React 19** y TypeScript.
- **Tailwind CSS 4** para estilos responsivos.
- **shadcn/ui** y **Radix UI** para componentes accesibles.
- **Lucide React** para iconografía.
- **Recharts** para gráficas de rendimiento.
- **React Hook Form** y **Zod** para formularios y validación.
- **SWR** para fetching, caché y sincronización de datos.
- **next-themes** para soporte de tema claro/oscuro.
- **date-fns** y React Day Picker para fechas y calendarios.

### Backend y datos

- **Supabase** como backend-as-a-service.
- **Supabase Auth** para registro, inicio de sesión y sesiones.
- **PostgreSQL** como base de datos relacional.
- **Supabase SSR** para integrar autenticación con Server Components y cookies.
- **Row Level Security (RLS)** para proteger los datos por usuario, rol, coach y atleta.
- **Route Handlers de Next.js** para endpoints de backend.
- **Server Actions** para operaciones específicas de actualización.
- Triggers y funciones **PL/pgSQL** para crear automáticamente perfiles y registros de coach o atleta.

### Integraciones

- **Mercado Pago** para crear preferencias de pago, gestionar suscripciones y recibir webhooks.
- **Stripe** está incluido como dependencia para posibles flujos de pago adicionales.

### Herramientas de desarrollo

- pnpm como gestor de paquetes.
- TypeScript para tipado estático.
- PostCSS y Tailwind para el pipeline de estilos.
- Draw.io para documentación de arquitectura, componentes, clases y modelo de datos.

## Arquitectura

El proyecto utiliza una arquitectura basada en Next.js App Router:


app/
├── auth/                  # Callback y flujo de autenticación
├── api/                   # Route Handlers y webhooks
├── blocks/                # Flujo de entrenamiento del atleta
├── coach/                 # Flujos y paneles del coach
├── calendar/              # Calendario de entrenamiento
├── stats/                 # Estadísticas y rendimiento
├── actions/               # Server Actions
├── layout.tsx             # Layout raíz, metadata y proveedores
└── page.tsx               # Página inicial

components/
├── ui/                    # Componentes reutilizables de shadcn/Radix
├── coach-*.tsx            # Gestión y visualizaciones del coach
├── athlete-*.tsx          # Funcionalidades del atleta
└── performance-*.tsx     # Analítica y rendimiento

lib/
├── supabase/              # Clientes browser/server, middleware y acciones
├── swr-config.tsx         # Configuración global de SWR
└── utils.ts               # Utilidades compartidas

scripts/
└── *.sql                  # Esquema, funciones, políticas RLS y migraciones

diagrams/
└── *.drawio               # Documentación técnica del sistema


## Modelo de datos

El esquema relacional incluye, entre otras, las siguientes entidades:

- `profiles`: identidad, email, nombre y rol.
- `coaches`: información profesional y cantidad de atletas.
- `athletes`: datos del atleta y relación con su coach.
- `exercises`: biblioteca de ejercicios.
- `training_blocks`: bloques de planificación.
- `training_days`: días dentro de cada bloque.
- `day_exercises`: ejercicios asignados a un día.
- `exercise_sets`: series, repeticiones, peso y objetivos.
- `exercise_logs`: registro real de ejecución.
- `prs`: récords personales.
- `body_weight`: historial de peso corporal.
- `competitions`: planificación de competencias.
- `billing`: movimientos y cobros.
- `coach_subscriptions`: planes y estado de suscripción.
- `coach_invitations`: invitaciones entre coaches y atletas.
- `trac`: seguimiento complementario del entrenamiento.

Las migraciones SQL se encuentran numeradas dentro de `scripts/` para facilitar la ejecución ordenada del esquema y sus cambios posteriores.

## Seguridad

- Autenticación gestionada por Supabase Auth.
- Sesiones sincronizadas mediante cookies seguras y middleware.
- Políticas RLS para limitar lecturas y escrituras según el usuario autenticado.
- Separación de permisos entre roles `coach` y `athlete`.
- Validación de planes antes de crear preferencias de pago.
- Credenciales sensibles mantenidas en variables de entorno.
- Webhook dedicado para procesar notificaciones de Mercado Pago.
- Consultas filtradas por el usuario, coach o atleta correspondiente.

## Flujo de suscripciones

1. El coach selecciona un plan desde el panel.
2. El frontend envía el identificador del plan al endpoint `/api/create-subscription`.
3. El servidor valida el plan y crea una preferencia en Mercado Pago.
4. El usuario es redirigido al checkout de Mercado Pago.
5. Mercado Pago redirige a las páginas de éxito, error o estado pendiente.
6. Mercado Pago notifica cambios mediante `/api/mercadopago-webhook`.
7. La suscripción se actualiza en Supabase.

Los planes disponibles son Inicial, Básico, Profesional, Avanzado e Ilimitado, cada uno con un límite distinto de atletas.

## Requisitos

- Node.js 18 o superior.
- pnpm 12 o compatible.
- Proyecto de Supabase.
- Cuenta de Mercado Pago con credenciales de prueba o producción.

## Instalación local

bash
pnpm install
pnpm dev


La aplicación estará disponible en `http://localhost:3000`.

También se puede utilizar npm, pero se recomienda pnpm porque el proyecto mantiene `pnpm-lock.yaml`:

bash
npm install
npm run dev


## Variables de entorno

Crea un archivo `.env.local` en la raíz del proyecto:

env
# Supabase
NEXT_PUBLIC_SUPABASE_URL=tu_supabase_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=tu_supabase_anon_key
SUPABASE_SERVICE_ROLE_KEY=tu_supabase_service_role_key

# Mercado Pago
MERCADOPAGO_ACCESS_TOKEN=tu_mercadopago_access_token

# Aplicación
NEXT_PUBLIC_SITE_URL=http://localhost:3000


## Base de datos

Ejecuta los scripts SQL numerados en orden desde el SQL Editor de Supabase. El primer script crea perfiles, coaches, atletas, triggers de alta y políticas iniciales. Los scripts siguientes agregan las tablas de planificación, métricas, facturación, invitaciones, suscripciones y actualizaciones de seguridad.

Para una configuración más detallada, consulta [`LOCAL_SETUP.md`](./LOCAL_SETUP.md).

## Scripts disponibles

bash
pnpm dev      # Servidor de desarrollo
pnpm build    # Compilación de producción
pnpm start    # Ejecutar la compilación de producción
pnpm lint     # Ejecutar linting


## Documentación técnica

La carpeta `diagrams/` contiene documentación visual del sistema:

- Modelo Entidad-Relación.
- Modelo Relacional.
- Modelo Físico.
- Diagrama de Componentes.
- Diagrama de Clases.
- Arquitectura de Software.

## Competencias demostradas

Este proyecto evidencia experiencia en:

- Diseño y desarrollo de aplicaciones full-stack.
- Arquitectura de aplicaciones con Next.js App Router.
- React moderno y componentes reutilizables.
- TypeScript y tipado de datos.
- Diseño de bases de datos relacionales.
- Autenticación y autorización basada en roles.
- Seguridad con Row Level Security.
- Desarrollo de APIs y webhooks.
- Integración de plataformas de pago.
- Gestión de estado remoto con SWR.
- Formularios complejos y validación.
- Visualización de métricas y analítica.
- Diseño responsive y accesible.
- Documentación técnica mediante diagramas UML y de arquitectura.
- Organización de migraciones y evolución de esquemas SQL.

## Posibles mejoras futuras

- Agregar pruebas automatizadas unitarias y end-to-end.
- Centralizar validaciones de entrada con esquemas Zod en todos los endpoints.
- Incorporar notificaciones por email.
- Añadir auditoría de cambios y trazabilidad de sesiones.
- Integrar almacenamiento de imágenes en Supabase Storage.
- Añadir observabilidad avanzada y monitoreo de errores.
- Automatizar las migraciones SQL mediante un pipeline CI/CD.

## Licencia
Este proyecto es privado y de uso demostrativo, salvo que se indique lo contrario.
