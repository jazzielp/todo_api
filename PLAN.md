# Plan de Desarrollo API (Express + TypeScript + Prisma)

## Objetivo

Crear una API con:

- Registro de usuario
- Autenticación (login)
- Verificación por email
- Logout
- Persistencia en BD (Prisma)
- Recurso relacionado: lista de pendientes ("todos") asociada a usuario
- Testing con enfoque TDD (Jest + Supertest)

---

## FASE 1 — Setup del proyecto

- [ ] Inicializar proyecto: `npm init -y`
- [ ] Instalar dependencias base:
  - [ ] `express`
  - [ ] `dotenv`
  - [ ] `cors`
- [ ] Instalar dependencias de TypeScript:
  - [ ] `typescript`
  - [ ] `ts-node`
  - [ ] `ts-node-dev` o `nodemon`
  - [ ] `@types/node`
  - [ ] `@types/express`
- [ ] Crear `tsconfig.json`
- [ ] Crear estructura de carpetas:
  - [ ] `src/index.ts` (entrypoint)
  - [ ] `src/app.ts` (configuración de Express)
  - [ ] `src/routes/`
  - [ ] `src/controllers/`
  - [ ] `src/services/`
  - [ ] `src/middlewares/`
  - [ ] `src/schemas/`
  - [ ] `src/config/`
- [ ] Configurar scripts en `package.json`:
  - [ ] `"dev": "ts-node-dev src/index.ts"`
  - [ ] `"build": "tsc"`
  - [ ] `"start": "node dist/index.js"`

---

## FASE 2 — Base de datos con Prisma

- [ ] Instalar Prisma y cliente:
  - [ ] `npm install prisma @prisma/client`
  - [ ] `npx prisma init`
- [ ] Configurar `DATABASE_URL` en `.env`
- [ ] Definir modelo `User` en `prisma/schema.prisma`:
  - [ ] `id`, `name`, `email`, `passwordHash`, `isVerified`, `createdAt`
- [ ] Definir modelo `Todo`:
  - [ ] `id`, `title`, `completed`, `userId`, `createdAt`
  - [ ] Relación many-to-one con `User`
- [ ] Ejecutar migraciones:
  - [ ] `npx prisma migrate dev`
- [ ] Crear helper `src/prisma/client.ts` para instanciar PrismaClient

---

## FASE 3 — Configuración de Testing (TDD)

- [ ] Instalar Jest + Supertest:
  - [ ] `npm install --save-dev jest ts-jest @types/jest supertest @types/supertest`
- [ ] Inicializar Jest:
  - [ ] `npx ts-jest config:init`
- [ ] Configurar `jest.config.ts`:
  - [ ] Test env = node
  - [ ] Usar `ts-jest`
- [ ] Crear BD de test (por ejemplo SQLite) y `DATABASE_URL_TEST`
- [ ] Crear script para correr tests con entorno de test:
  - [ ] `"test": "cross-env NODE_ENV=test jest --runInBand"`
- [ ] Crear test simple `/health`:
  - [ ] Test falla inicialmente (TDD)
  - [ ] Implementar endpoint `/health`
  - [ ] Hacer que el test pase

---

## FASE 4 — Autenticación (Registro y Verificación Email)

### Registro (`POST /auth/register`)

- [ ] **(TEST PRIMERO)** Casos:
  - [ ] Registro exitoso
  - [ ] Email ya registrado
  - [ ] Datos inválidos
- [ ] Implementar `AuthService.register`:
  - [ ] Validar datos
  - [ ] Hashear contraseña con bcrypt
  - [ ] Crear usuario `isVerified = false`
  - [ ] Generar token de verificación
  - [ ] Guardar token (en tabla o generarlo JWT de un solo uso)
  - [ ] Enviar email de verificación (simulado o real con Nodemailer)
- [ ] Implementar controlador y ruta
- [ ] Hacer pasar los tests

### Verificación de email (`GET /auth/verify-email?token=...`)

- [ ] **(TEST PRIMERO)** Casos:
  - [ ] Token válido → `isVerified = true`
  - [ ] Token inválido o expirado
- [ ] Implementar lógica de verificación
- [ ] Marcar usuario como verificado
- [ ] Hacer pasar los tests

---

## FASE 5 — Login y Logout

### Login (`POST /auth/login`)

- [ ] **(TEST PRIMERO)** Casos:
  - [ ] Login exitoso (usuario verificado)
  - [ ] Contraseña incorrecta
  - [ ] Usuario no existe
  - [ ] Usuario no verificado
- [ ] Implementar generación de JWT
- [ ] Responder con token y datos básicos del usuario
- [ ] Hacer pasar los tests

### Logout (`POST /auth/logout`)

- [ ] **(TEST PRIMERO)** Casos:
  - [ ] Logout exitoso con token válido
  - [ ] Token inválido
- [ ] Elegir estrategia:
  - [ ] Blacklist en memoria (para demo)
  - [ ] O lista de tokens inválidos en BD
- [ ] Implementar endpoint
- [ ] Hacer pasar los tests

---

## FASE 6 — Middleware de autenticación

- [ ] **(TEST PRIMERO)** Casos:
  - [ ] Petición con token válido asigna `req.user`
  - [ ] Token inválido → 401
  - [ ] Sin token → 401
- [ ] Implementar `authMiddleware` basado en JWT
- [ ] Reutilizar en rutas `/todos`

---

## FASE 7 — CRUD de Todos (Recurso protegido)

- [ ] **(TESTS PRIMERO)**
  - `GET /todos`
    - [ ] Devuelve solo todos del usuario autenticado

  - `POST /todos`
    - [ ] Crea un todo para el usuario autenticado

  - `PUT /todos/:id`
    - [ ] Actualiza solo si el todo pertenece al usuario
    - [ ] 404 si no existe o no pertenece al usuario

  - `DELETE /todos/:id`
    - [ ] Elimina solo si pertenece al usuario
    - [ ] 404 en caso contrario

- [ ] Implementar `TodoService` + `TodoController`
- [ ] Proteger rutas con `authMiddleware`
- [ ] Hacer pasar los tests

---

## FASE 8 — Validaciones y Errores

- [ ] Instalar Zod `npm install zod`
- [ ] Crear schemas:
  - [ ] `auth.schema.ts` para register/login
  - [ ] `todo.schema.ts` para creación/edición
- [ ] Middleware de validación
- [ ] Middleware global de errores con formato estándar
- [ ] Agregar tests de validación (400 en caso de datos inválidos)

---

## FASE 9 — Documentación y Limpieza Final

- [ ] `README.md` con:
  - [ ] Requisitos
  - [ ] Cómo correr en dev
  - [ ] Cómo correr tests
  - [ ] Lista de endpoints
- [ ] Comprobar cobertura de tests (`jest --coverage`)
- [ ] Revisión final con agente supervisor

---
