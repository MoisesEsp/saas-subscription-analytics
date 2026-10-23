# PLAN_PROYECTO.md

> **Documento maestro del proyecto.** Contiene el plan completo, las decisiones
> tomadas, el progreso actual y el siguiente paso a ejecutar. Se actualiza al
> finalizar cada sub-paso.
>
> **Para reanudar rápido:** Fase 0 cerrada por completo (0.1–0.9).
> Siguiente: Fase 1 — Ingesta y generación de datos sintéticos.
> Stack operativo: postgres (`5433`), pgadmin (`5050`), persistencia verificada.
>
> Última actualización: 2026-10-22

---

## 0. Contexto Mínimo de Reanudación

Si solo tienes 30 segundos, lee esto:

- **Fase actual:** 1 — Ingesta y generación de datos sintéticos
- **Sub-paso en curso:** por definir (propuesta de desglose pendiente de confirmación)
- **Completado:** Fase 0 completa (0.1–0.9)
- **Pendiente en este paso:** acordar el desglose de la Fase 1
- **Siguiente paso:** 1.1 — Diseño del modelo de datos

---

## 1. Descripción del Proyecto

**Nombre:** SaaS Subscription Analytics & Retention Cohorts

**Objetivo:** Simular el problema central de una empresa SaaS moderna:
entender el comportamiento de usuarios en el tiempo, calcular churn,
métricas de recurrencia monetaria (MRR/ARR), LTV, y análisis de cohortes
de retención.

**Propósito:** Portafolio profesional para búsqueda de empleo como
Data Analyst / Analytics Engineer.

**Diferenciador:** No es un análisis de un CSV de Kaggle. Es un pipeline
end-to-end con datos sintéticos realistas, modelado dimensional, SQL
avanzado (window functions, CTEs), contenerización con Docker y BI
con Superset.

---

## 2. Stack Tecnológico (Decisiones Cerradas)

| Capa | Herramienta | Justificación | Estado |
|---|---|---|---|
| Base de datos | PostgreSQL 16 (Alpine) | Estándar de industria | Decidido |
| Cliente SQL nativo | DBeaver Community | Nativo en Linux, universal | Instalado |
| Cliente SQL web | pgAdmin 4 (contenedor) | Cliente oficial de PostgreSQL | Por levantar |
| ETL / Generación datos | Python 3.11 (contenedor) + Faker + NumPy + Pandas | Pipeline reproducible | Por configurar |
| Driver PostgreSQL | `psycopg` v3 (NO `psycopg2`) | Compatible con Python 3.14 local | Decidido |
| Orquestación | Docker + Docker Compose v5 | Reproducibilidad y portabilidad | Instalado |
| BI / Visualización | Apache Superset (contenedor) | Open-source, Linux-friendly, encaja con stack técnico | Por levantar |
| Control de versiones | Git + GitHub (SSH) | Estándar universal | Configurado |
| Licencia | MIT | Permisiva, estándar en portafolios | Pendiente crear |

### Detalles del entorno del desarrollador

- **SO:** Linux
- **Python local:** 3.14.7 (solo para editor; el pipeline usa 3.11 en contenedor)
- **Docker:** 29.8.2
- **Docker Compose:** v5.6.0
- **Git:** 2.56.0
- **Ruta del proyecto local:** `~/Proyectos/saas-subscription-analytics`
- **Repo GitHub:** cuenta profesional ya existente, con otros proyectos
- **Autenticación Git:** SSH con clave `ed25519` (migrado desde HTTPS el 2026-10-19, ver ADR-012)

---

## 3. Estructura del Repositorio (Ya Creada)

```
saas-subscription-analytics/
├── data/                          ← CSVs generados (NO va a Git)
├── sql/
│   ├── ddl/                       ← CREATE TABLE / constraints
│   ├── dml/                       ← INSERT / UPDATE / TRUNCATE
│   └── analytics/                 ← vistas y queries de negocio
├── python/
│   ├── etl/                       ← pipeline de generación y carga
│   ├── notebooks/                 ← exploración Jupyter
│   ├── Dockerfile
│   └── requirements.txt
├── docker/
│   ├── docker-compose.yml
│   ├── .env                       ← real, NUNCA va a Git
│   ├── .env.example               ← plantilla, SÍ va a Git
│   └── superset/                  ← configuración de Superset
├── dashboards/                    ← exports Superset, screenshots
├── docs/                          ← diagramas, notas técnicas
│   ├── DECISIONES.md              ← ADRs (Architecture Decision Records)
│   ├── COMANDOS_UTILES.md         ← comandos frecuentes
│   └── ARQUITECTURA.md            ← (se creará en Fase 5)
├── .gitignore
├── AGENTS.md                      ← reglas para agentes de IA
├── CONTRIBUTING.md                ← guía de contribución
├── LICENSE                        ← licencia MIT
├── PLAN_PROYECTO.md               ← este archivo
├── PROGRESO.md                    ← log cronológico
└── README.md
```

**Nota:** las carpetas vacías contienen `.gitkeep` para que Git las rastree.

---

## 4. Plan Completo por Fases

### FASE 0 — Configuración del Entorno con Docker
- **0.1** Instalación de herramientas base → ✅ COMPLETADO
- **0.2** Estructura del repositorio + Git init → ✅ COMPLETADO
- **0.3** Configuración de variables de entorno (.env / .env.example) → ✅ COMPLETADO
- **0.4** Diseño del `docker-compose.yml` → ✅ COMPLETADO
- **0.5** Diseño del `Dockerfile` de Python → ✅ COMPLETADO
- **0.6** `requirements.txt` inicial con versiones pinneadas → ✅ COMPLETADO
- **0.7** `.gitignore` y primer commit + push → ✅ COMPLETADO
- **0.8** Primer arranque del stack (postgres + pgadmin) → ✅ COMPLETADO
- **0.9** Verificación de persistencia y conexión pgAdmin → Postgres → ✅ COMPLETADO

### FASE 1 — Ingesta y Generación de Datos Sintéticos
- Diseño del modelo de datos (dim_users, dim_plans, dim_dates, stg_subscription_events)
- Generador en Python con Faker + NumPy (12-24 meses de historia)
- Cargador con Pandas + SQLAlchemy
- Ejecución desde contenedor con `docker compose run`

### FASE 2 — Modelado de Datos (SQL DDL)
- Scripts DDL ordenados numéricamente
- Modelo estrella: fact_subscriptions + dimensiones
- Restricciones: PK, FK, CHECK
- Script de inicialización ordenado

### FASE 3 — Consultas Analíticas Avanzadas
- Análisis de cohortes con DATE_TRUNC, ROW_NUMBER, DENSE_RANK
- Churn y LTV con LAG, LEAD
- Desglose de MRR (New, Expansion, Contraction, Churned) con CTEs
- Vistas SQL para consumo directo de Superset

### FASE 4 — Visualización (Apache Superset en Docker)
- Añadir servicio Superset al docker-compose
- Conexión de Superset a PostgreSQL
- Dashboards:
  - Página 1: Resumen ejecutivo (KPIs)
  - Página 2: Retención por cohortes (heatmap)
  - Página 3: Movimiento de MRR (barras apiladas)
  - Página 4: Adquisición vs LTV por canal

### FASE 5 — Documentación y Publicación
- README excepcional (problema, arquitectura, stack, reproducción, insights)
- Diagrama de arquitectura (Mermaid o draw.io) → `docs/ARQUITECTURA.md`
- Screenshots del dashboard
- `Makefile` opcional con atajos
- Push final y limpieza del repo

---

## 5. Estado Actual Detallado

**Fase en curso:** 1 — Ingesta y generación de datos sintéticos (desglose por confirmar)

**Ya completado:**
- ✅ Docker, Compose, Git, Python, DBeaver, VS Code instalados y verificados
- ✅ Superset decidido como herramienta de BI (en Docker)
- ✅ Driver `psycopg` v3 decidido para Python 3.14
- ✅ Estructura de carpetas creada idéntica a la especificada
- ✅ Git inicializado, rama `main`, remoto HTTPS configurado
- ✅ Repo vacío creado en GitHub
- ✅ `.gitkeep` en carpetas vacías
- ✅ `README.md` en mayúsculas
- ✅ Archivos guía creados: `PLAN_PROYECTO.md`, `AGENTS.md`, `PROGRESO.md`, `docs/DECISIONES.md`, `docs/COMANDOS_UTILES.md`, `CONTRIBUTING.md`, `LICENSE`

**Cerrado en el paso 0.3 (2026-10-15):**
- ✅ `docker/.env` escrito con valores reales (postgres, pgAdmin, Superset)
- ✅ `docker/.env.example` escrito con valores ficticios + comentarios
- ✅ `SUPERSET_SECRET_KEY` generado con `openssl rand -base64 42`
- ✅ Verificada la coincidencia exacta de los 15 nombres de variable entre
  `.env` y `.env.example`
- ✅ Corregido nombre del archivo plantilla (era `.env,example`)
- ✅ Corregido nombre `docs/DESICIONES.md` → `docs/DECISIONES.md`

**Cerrado en el paso 0.4 (2026-10-16):**
- ✅ `docker/docker-compose.yml` creado y validado (`docker compose config` sin errores)
- ✅ Servicios `postgres` (`postgres:16-alpine`) y `pgadmin` (`dpage/pgadmin4:7.7`)
- ✅ Credenciales vía `env_file: .env` (sin secretos hardcodeados)
- ✅ Puertos parametrizados con `${POSTGRES_HOST_PORT}:${POSTGRES_CONTAINER_PORT}` y `${PGADMIN_HOST_PORT}:80`
- ✅ Volúmenes nombrados `postgres_data` y `pgadmin_data` declarados
- ✅ Red explícita `saas_net` usada por ambos servicios y declarada
- ✅ `pgadmin` con `depends_on: postgres`
- ✅ Comprendido el doble uso de `env_file` (entorno del contenedor) vs `${...}`
  (sustitución en el compose)

**Cerrado en el paso 0.5 (2026-10-18):**
- ✅ `python/Dockerfile` con base `python:3.11-slim`
- ✅ `WORKDIR /app`, orden de capas optimizado (requirements antes del código)
- ✅ `pip install --no-cache-dir -r requirements.txt`
- ✅ Usuario no-root `appuser` con `chown` de `/app`
- ✅ `CMD` placeholder (`python --version`); se sustituirá al definir el entrypoint del ETL
- ✅ `python/.dockerignore` creado (`__pycache__`, `*.pyc`, `.ipynb_checkpoints`, `.venv`, `.DS_Store`)
- ✅ Build validado: imagen `saas-etl:dev` (201 MB), Python 3.11.17, usuario `appuser`
- ✅ Corregido `python/requirements.txt`, que estaba creado como **carpeta** vacía; ahora es archivo

**Cerrado en el paso 0.6 (2026-10-19):**
- ✅ `python/requirements.txt` con 5 dependencias directas pineadas:
  `Faker==40.43.0`, `numpy==2.4.6`, `pandas==3.0.6`,
  `psycopg[binary]==3.3.6`, `SQLAlchemy==2.1.5`
- ✅ Corregido el driver: `psycopg-binary` solo no incluye `psycopg`; se usa
  el extra `psycopg[binary]`
- ✅ Build validado e imports verificados dentro de la imagen (las 5 librerías)
- ✅ Imagen final `saas-etl:dev` ≈ 490 MB
- ✅ Entendido: se pinean dependencias directas; las transitivas las resuelve
  `pip` (para reproducibilidad total se usaría un lock file tipo pip-tools/uv)

**Cerrado en el paso 0.7 (2026-10-19):**
- ✅ `.gitignore` creado con `.env`, `data/*`, `__pycache__/`, `.venv/`, etc.
- ✅ Verificado que `.env` se ignora y que `.env.example` sí se versiona
- ✅ Escaneado el historial completo: ninguna credencial real commiteada
- ✅ Primer commit `697e66c` subido a GitHub (22 archivos)
- ✅ Autenticación migrada de HTTPS a SSH (ADR-012)
- ✅ `README.md` subido vacío (opción A; se completará en Fase 5)

**Resuelto en el paso 0.7:**
- ✅ `.gitignore` ya protege `docker/.env`. Ya es seguro usar `git add .`.

**Cerrado en el paso 0.8 (2026-10-20):**
- ✅ `docker compose up -d` levanta el stack correctamente
- ✅ `postgres` Up en `5433->5432`; log `database system is ready to accept connections`
- ✅ `pgadmin` Up en `5050->80`; login accesible en `http://localhost:5050`
- ✅ Comprendido `-d`, `down` vs `down -v`, y el error de escribir `up -d` sin `docker compose`
- ⚠️ Detectado: el proyecto Compose se llama `docker` (contenedores `docker-*`). Se corregirá en 0.9

**Cerrado en el paso 0.9 (2026-10-22):**
- ✅ Proyecto Compose renombrado a `saas-analytics` (contenedores `saas-analytics-*`)
- ✅ Volúmenes `saas-analytics_postgres_data` y `saas-analytics_pgadmin_data`
- ✅ Persistencia verificada: un dato sobrevivió a `down` + `up` (sin `-v`)
- ✅ pgAdmin conectado a Postgres con host `postgres`, puerto `5432`
- ✅ Tabla de prueba visible desde pgAdmin y eliminada después
- ✅ **FASE 0 COMPLETA (0.1–0.9)**

**Decisiones tomadas en este paso:**
- Variables agrupadas por servicio con comentarios separadores
- Sin espacios alrededor de `=`
- `POSTGRES_HOST=postgres` (nombre del servicio Docker, no localhost)
- Distinción clara entre `POSTGRES_HOST_PORT` (host) y `POSTGRES_PORT` (interno Docker)
- Aplazar la protección de `.env` en `.gitignore` al paso 0.7 (ver ADR-008)

**Siguiente paso:** definir el desglose de la Fase 1 y comenzar por el diseño del modelo de datos

---

## 6. Convenciones del Proyecto

- **Idioma de comunicación con el agente:** Español
- **Idioma de código y nombres de archivos:** Inglés
- **Idioma de comentarios en código:** Inglés
- **Idioma de documentación (README, docs):** Español
- **Formato de carpetas:** `kebab-case`
- **Formato de archivos Python:** `snake_case.py`
- **Formato de scripts SQL:** `NN_descripcion.sql` (NN = número de orden)
- **Nunca** subir `.env` a Git
- **Siempre** documentar decisiones técnicas en `docs/DECISIONES.md`
- **Siempre** pinear versiones en `requirements.txt`
- **Siempre** actualizar `PROGRESO.md` al cerrar un paso
- **Siempre** cerrar pasos con checkpoint verificable
- **Nunca** dar código completo al usuario (ver `AGENTS.md`)

---

## 7. Registro de Decisiones (resumen — detalle completo en `docs/DECISIONES.md`)

| Fecha | Decisión | Razón |
|---|---|---|
| 2026-10-15 | DBeaver como cliente SQL nativo | Nativo en Linux, universal, reemplaza MariaDB client |
| 2026-10-15 | Superset en lugar de Power BI | Linux-friendly, open-source, se integra en Docker |
| 2026-10-15 | `psycopg` v3 en lugar de `psycopg2` | Python 3.14 local no soporta wheels de psycopg2 |
| 2026-10-15 | Python del pipeline en contenedor 3.11 | Aislar del Python 3.14 local, más estable para libs |
| 2026-10-15 | Git por HTTPS, no SSH | Preferencia del desarrollador (reemplazada el 2026-10-19) |
| 2026-10-19 | Migración de Git HTTPS a SSH | Evitar el PAT en cada push; clave `ed25519` configurada (ADR-012) |
| 2026-10-15 | Licencia MIT | Permisiva, estándar en portafolios |
| 2026-10-15 | Crear `AGENTS.md` y archivos guía versionados | Preservar contexto entre sesiones y agentes |

---

## 8. Preguntas Abiertas / Bloqueos

*(Vacío actualmente)*

---

## 9. Referencias Rápidas

- Docker Hub PostgreSQL: `postgres:16-alpine`
- Docker Hub pgAdmin: `dpage/pgadmin4`
- Docker Hub Superset: `apache/superset`
- Documentación Compose v5: variable syntax `${VAR}`
- Generar secret: `openssl rand -base64 42`
- Comandos frecuentes del proyecto: ver `docs/COMANDOS_UTILES.md`

---

## 10. Instrucciones de Uso para el Agente

1. **Antes de responder cualquier cosa**, leer este archivo completo.
2. **Leer también `AGENTS.md`** para reglas de comportamiento.
3. **Verificar la sección 5 (Estado Actual)** para saber dónde estamos.
4. **Consultar la sección 4 (Plan)** para saber qué sigue.
5. **Ejecutar `git status` y `git log --oneline -5`** para confirmar el estado real del repo. Si difiere del plan, reportarlo antes de continuar.
6. **Nunca avanzar al siguiente sub-paso** sin confirmación explícita del usuario.
7. **Actualizar este archivo** al cerrar cada sub-paso: marcar ✅, actualizar estado, registrar decisiones nuevas.
8. **Añadir entrada a `PROGRESO.md`** al cerrar cada sub-paso.

---

## 11. Registro de Cambios de Este Documento

| Fecha | Cambio | Autor |
|---|---|---|
| 2026-10-15 | Creación inicial con contexto completo | Usuario + Agente |
| 2026-10-15 | Añadidas secciones 0, 10 y 11 por recomendación del agente | Usuario + Agente |