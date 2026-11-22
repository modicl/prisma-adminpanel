# prisma-adminpanel

API del panel de administración de P.R.I.S.M.A. Gestiona profesores, tickets de soporte, recursos, anuncios, sesiones de administración, notificaciones y estadísticas por colegio. Toda acción relevante queda registrada en `admin_audit_logs`.

## Qué hace

| Módulo | Función |
|---|---|
| `professors` | CRUD de profesores; al crear uno puede pedir a `ms-users` que cree el usuario. |
| `tickets` | Tickets de soporte y sus respuestas; notifica al cerrar. |
| `resources` | Materiales de recurso. |
| `announcements` | Anuncios, con un endpoint público de activos. |
| `sessions` | Sesiones de administración activas, bloqueadas e históricas. |
| `notifications` | Notificaciones por usuario. |
| `audit-logs` | Registro de auditoría, enriquecido con datos de `ms-users`. |
| `colegio-stats` | Estadísticas por colegio, con datos locales y estadísticas de jobs de `ms-docs`. |

## Autenticación

No tiene identidad propia: no valida tokens ni emite ninguno. `AdminAuthGuard` exige un Bearer token y llama a `ms-users` (`GET /auth/me`) para comprobar que el rol sea administrador (`USERS_SERVICE_URL`). Tampoco crea usuarios: se lo pide a `ms-users`.

Los endpoints de notificaciones no tienen guard y se identifican por `?userId=`; conviene revisarlo antes de exponerlos más allá de la red privada.

## Stack

NestJS 10, Prisma 5 (esquema por defecto), Swagger en `/docs`, prefijo global `/api`. Puerto 3004.

## Configuración

| Variable | Descripción |
|---|---|
| `PORT`, `CORS_ORIGIN` | Servidor. |
| `DATABASE_URL` | PostgreSQL (`prisma_adminpanel`, base propia: `prisma db push` borraría las tablas de perfil si compartieran base). |
| `USERS_SERVICE_URL` | URL de `ms-users` incluyendo `/api`. |
| `DOCS_SERVICE_URL` | URL de `ms-docs`, sin `/api`. |

## Ejecución y pruebas

```bash
npm install
npx prisma generate
npx prisma db push
npm run dev
npm test
```

## Despliegue

Imagen en ECR (`api/adminpanel`), contenedor de la tarea ECS `prisma-app`.
