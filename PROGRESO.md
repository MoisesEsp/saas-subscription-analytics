# PROGRESO.md

> Log cronológico del proyecto. Se añade una entrada al cerrar cada sub-paso.
> No editar entradas pasadas. Solo agregar al final.

---

## 2026-10-15 — Inicio del proyecto

- Definido stack tecnológico (PostgreSQL, Python, Docker, Superset, DBeaver).
- Decidido enfoque: pipeline end-to-end con datos sintéticos.
- Creados archivos de contexto: `PLAN_PROYECTO.md`, `AGENTS.md`,
  `PROGRESO.md`, `docs/DECISIONES.md`, `docs/COMANDOS_UTILES.md`,
  `CONTRIBUTING.md`, `LICENSE`.

## 2026-10-15 — Fase 0.1 cerrada

- Verificadas versiones: Docker 29.8.2, Compose v5.6.0, Git 2.56.0,
  Python 3.14.7.
- Instalado DBeaver.
- Instalado VS Code con extensiones: Docker, Python, SQLTools, GitLens.
- Decidido usar Superset en Docker desde el inicio.
- Decidido usar `psycopg` v3 en lugar de `psycopg2`.

## 2026-10-15 — Fase 0.2 cerrada

- Creada estructura de carpetas completa.
- Inicializado Git, rama `main`.
- Creado repo vacío en GitHub, remoto HTTPS configurado.
- `.gitkeep` añadidos a carpetas vacías.
- `README.md` renombrado a mayúsculas.

## 2026-10-15 — Fase 0.3 iniciada

- Pendiente: escribir `.env`, `.env.example`, generar `SUPERSET_SECRET_KEY`.

## 2026-10-15 — Fase 0.3 cerrada

- Escrito `docker/.env` con valores reales (postgres, pgAdmin, Superset).
- Escrito `docker/.env.example` con valores ficticios y comentarios.
- Generado `SUPERSET_SECRET_KEY` con `openssl rand -base64 42`.
- Verificada coincidencia de los 15 nombres de variable entre `.env` y `.env.example`.
- Corregido el nombre del archivo plantilla (era `docker/.env,example`).
- Corregido el nombre `docs/DESICIONES.md` → `docs/DECISIONES.md`.
- Detectado que `.gitignore` está vacío: aplazada su solución al paso 0.7
  (ver ADR-008). No ejecutar `git add .` hasta entonces.
- Fechas placeholder `2025-XX-XX` reemplazadas por `2026-10-15` en los archivos guía.

## 2026-10-16 — Fase 0.4 cerrada

- Creado `docker/docker-compose.yml` con servicios `postgres` y `pgadmin`.
- `postgres` usa `postgres:16-alpine`; `pgadmin`, `dpage/pgadmin4:7.7`.
- Credenciales vía `env_file: .env`; puertos parametrizados con `${...}`.
- Volúmenes nombrados `postgres_data` y `pgadmin_data` declarados.
- Red explícita `saas_net` usada por ambos servicios.
- Validado con `docker compose config` (exit 0); servicios detectados:
  `postgres`, `pgadmin`.
- Registrado en `docs/DECISIONES.md` como ADR-009.

## 2026-10-18 — Fase 0.5 cerrada

- Creado `python/Dockerfile` con `python:3.11-slim`.
- Orden de capas optimizado: `requirements.txt` antes que el código (caché).
- Usuario no-root `appuser` con `chown` de `/app`.
- `CMD` placeholder (`python --version`).
- Creado `python/.dockerignore`.
- **Incidencia corregida:** `python/requirements.txt` estaba creado como
  carpeta vacía (`mkdir`); se convirtió en archivo. El primer build falló por
  eso y el segundo pasó.
- Build validado: `saas-etl:dev` (201 MB), Python 3.11.17, usuario `appuser`.

## 2026-10-19 — Fase 0.6 cerrada

- `python/requirements.txt` con 5 dependencias directas pineadas:
  Faker 40.43.0, numpy 2.4.6, pandas 3.0.6, psycopg[binary] 3.3.6,
  SQLAlchemy 2.1.5.
- Corregido el driver: `psycopg-binary` por sí solo no incluye `psycopg`;
  se usa el extra `psycopg[binary]`.
- Build reconstruido e imports de las 5 librerías verificados dentro de la
  imagen (pandas 3.0.6, numpy 2.4.6, faker 40.43.0, sqlalchemy 2.1.5,
  psycopg 3.3.6).
- Imagen final `saas-etl:dev` ≈ 490 MB.

## 2026-10-19 — Fase 0.7 cerrada

- Creado `.gitignore` con `.env`, `data/*`, `__pycache__/`, `.venv/`,
  `.ipynb_checkpoints/`, etc.
- Verificado con `git check-ignore -v docker/.env` que `.env` se ignora y
  que `.env.example` sí se versiona.
- Escaneado el historial completo: `.env` nunca fue commiteado y ninguna
  contraseña ni `SUPERSET_SECRET_KEY` aparece en él.
- Primer commit `697e66c` subido a GitHub (22 archivos).
- Autenticación migrada de HTTPS a SSH con clave `ed25519` (ADR-012,
  reemplaza ADR-005). `ssh -T git@github.com` verificado.
- `AGENTS.md` actualizado a SSH (con permiso del usuario).
- `README.md` subido vacío (opción A); se completará en Fase 5.

## 2026-10-20 — Fase 0.8 cerrada

- Primer arranque del stack con `docker compose up -d`.
- `postgres` Up en `5433->5432`; log `database system is ready to accept connections`.
- `pgadmin` Up en `5050->80`; login accesible en `http://localhost:5050`.
- Puerto `5433` accesible desde el host.
- Detectado que el proyecto Compose se llama `docker` (contenedores `docker-*`);
  se fijará un nombre propio en 0.9.

## 2026-10-22 — Fase 0.9 cerrada · FASE 0 COMPLETA

- Proyecto Compose renombrado a `saas-analytics`; contenedores
  `saas-analytics-*` y volúmenes `saas-analytics_*`.
- Persistencia verificada: se creó `persistence_test`, se hizo `down` (sin
  `-v`) y `up -d`, y el dato sobrevivió. Tabla de prueba eliminada.
- pgAdmin conectado a Postgres usando host `postgres` y puerto `5432`
  (no `localhost:5433`, que es el mapeo del host).
- **Fase 0 (0.1–0.9) cerrada por completo.**

*(Siguiente entrada al cerrar el primer sub-paso de la Fase 1)*