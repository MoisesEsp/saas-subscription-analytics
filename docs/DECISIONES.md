# DECISIONES.md

> Registro de Decisiones de Arquitectura (ADR — Architecture Decision Records).
> Formato ligero: una entrada por decisión. No editar entradas pasadas.
>
> Formato:
> - **Fecha:** YYYY-MM-DD
> - **Estado:** Propuesta | Aceptada | Reemplazada por ADR-XXX
> - **Contexto:** por qué surge la decisión
> - **Decisión:** qué se decidió
> - **Consecuencias:** ventajas, desventajas, impacto

---

## ADR-001 — Usar DBeaver como cliente SQL nativo

- **Fecha:** 2026-10-15
- **Estado:** Aceptada
- **Contexto:** El desarrollador usa Linux y tenía MariaDB client instalado,
  que no sirve para conectarse a PostgreSQL.
- **Decisión:** Instalar DBeaver Community como cliente nativo, y usar
  pgAdmin en contenedor como respaldo.
- **Consecuencias:** Cliente universal (soporta múltiples motores).
  Mayor familiaridad con herramienta usada en la industria.

## ADR-002 — Usar Apache Superset en lugar de Power BI

- **Fecha:** 2026-10-15
- **Estado:** Aceptada
- **Contexto:** El desarrollador trabaja en Linux y Power BI no es nativo.
  Se necesita una herramienta de BI.
- **Decisión:** Usar Apache Superset desplegado en Docker desde el inicio.
- **Consecuencias:** Open-source, Linux-friendly, se integra con Docker
  Compose. Menos demanda en ofertas que Power BI, pero demuestra dominio
  técnico y adaptabilidad. Se mencionará en el README que se puede migrar
  a Power BI sin fricción.

## ADR-003 — Usar `psycopg` v3 en lugar de `psycopg2`

- **Fecha:** 2026-10-15
- **Estado:** Aceptada
- **Contexto:** El Python local es 3.14.7 y `psycopg2-binary` no tiene
  wheels compatibles.
- **Decisión:** Usar `psycopg` v3, el reemplazo oficial recomendado.
- **Consecuencias:** Compatibilidad total con Python 3.11 (contenedor) y
  3.14 (local). API ligeramente distinta a psycopg2.

## ADR-004 — Python del pipeline en contenedor 3.11

- **Fecha:** 2026-10-15
- **Estado:** Aceptada
- **Contexto:** El Python local es 3.14.7, muy reciente, con ecosistema
  inestable.
- **Decisión:** Ejecutar el pipeline en contenedor con `python:3.11-slim`.
- **Consecuencias:** Aislamiento del entorno local. Mayor estabilidad de
  dependencias. Reproducibilidad garantizada.

## ADR-005 — Git por HTTPS en lugar de SSH

- **Fecha:** 2026-10-15
- **Estado:** Reemplazada por ADR-012
- **Contexto:** El desarrollador no tiene configurado SSH.
- **Decisión:** Usar HTTPS con Personal Access Token.
- **Consecuencias:** Ligeramente más fricción al hacer push (pide token).
  Se puede migrar a SSH en el futuro sin impacto en el proyecto.

## ADR-006 — Licencia MIT

- **Fecha:** 2026-10-15
- **Estado:** Aceptada
- **Contexto:** Proyecto de portafolio público.
- **Decisión:** Usar licencia MIT.
- **Consecuencias:** Permisiva, estándar en portafolios, reconocible por
  reclutadores.

## ADR-007 — Archivos guía versionados en Git

- **Fecha:** 2026-10-15
- **Estado:** Aceptada
- **Contexto:** El proyecto se desarrolla con asistencia de agentes de IA
  entre múltiples sesiones. Se pierde contexto fácilmente.
- **Decisión:** Crear y versionar `PLAN_PROYECTO.md`, `AGENTS.md`,
  `PROGRESO.md`, `docs/DECISIONES.md` y `docs/COMANDOS_UTILES.md`.
- **Consecuencias:** Contexto preservado entre sesiones y agentes.
  Señal de madurez profesional (documentación como parte del repo).

## ADR-008 — Aplazar la protección de `.env` en `.gitignore` al paso 0.7

- **Fecha:** 2026-10-15
- **Estado:** Aceptada
- **Contexto:** Al cerrar el paso 0.3 se detectó que `.gitignore` está vacío
  y que `docker/.env` (con credenciales reales) aún no está protegido. El
  plan contempla crear `.gitignore` en el paso 0.7.
- **Decisión:** El desarrollador decidió posponer la creación del
  `.gitignore` funcional al paso 0.7, en lugar de adelantarlo.
- **Consecuencias:** Riesgo temporal: si se ejecuta `git add .` antes del
  paso 0.7, las credenciales de `.env` podrían subirse a GitHub. Mitigación:
  no ejecutar `git add .` (usa rutas explícitas) hasta cerrar 0.7. Si en
  algún momento `.env` llegara a subirse, rotar todas las credenciales y
  limpiar el historial con `git filter-repo`.

## ADR-009 — Diseño del `docker-compose.yml` (postgres + pgadmin)

- **Fecha:** 2026-10-16
- **Estado:** Aceptada
- **Contexto:** Primer diseño del stack. Se necesitan Postgres y pgAdmin,
  con persistencia de datos y credenciales fuera del compose.
- **Decisión:**
  - Imágenes: `postgres:16-alpine` y `dpage/pgadmin4:7.7` (versión pineada).
  - Credenciales vía `env_file: .env`, sin secretos hardcodeados.
  - Puertos parametrizados con variables de `.env` (`${...}`), manteniendo
    el `.env` como única fuente de verdad.
  - Volúmenes nombrados `postgres_data` y `pgadmin_data` (la DB no usa bind
    mount para evitar problemas de permisos y ensuciar el repo).
  - Red explícita `saas_net` en lugar de la red por defecto, para aislamiento
    y para añadir futuros servicios (Superset, Python ETL).
  - `pgadmin` con `depends_on: postgres`.
- **Consecuencias:** Entorno reproducible con un solo comando. `depends_on`
  solo ordena el arranque, no garantiza disponibilidad: se añadirá
  `healthcheck` si es necesario. El `.env` se inyecta completo a ambos
  contenedores (inofensivo hoy; se refinará con `environment:` si molesta).

## ADR-010 — Diseño del `Dockerfile` de Python

- **Fecha:** 2026-10-18
- **Estado:** Aceptada
- **Contexto:** Se necesita una imagen reproducible para ejecutar el ETL sin
  depender del Python 3.14.7 del host.
- **Decisión:**
  - Base `python:3.11-slim` (Debian/glibc, no Alpine/musl).
  - Copiar `requirements.txt` e instalar dependencias **antes** de copiar el
    código, para aprovechar la caché de capas.
  - `pip install --no-cache-dir` para reducir el tamaño de la imagen.
  - Crear usuario no-root `appuser` y cederle `/app` con `chown`.
  - `CMD` placeholder (`python --version`) hasta definir el entrypoint real;
    `docker compose run` lo sobrescribe.
  - `.dockerignore` para excluir `__pycache__`, `*.pyc`, `.ipynb_checkpoints`,
    `.venv`, `.DS_Store`.
- **Consecuencias:** Imagen de ~201 MB. Builds rápidos al cambiar solo código.
  Proceso sin privilegios de root. El `CMD` deberá actualizarse cuando exista
  el entrypoint del pipeline.

## ADR-011 — Pinear solo dependencias directas en `requirements.txt`

- **Fecha:** 2026-10-19
- **Estado:** Aceptada
- **Contexto:** `pip freeze` devuelve también las dependencias transitivas.
  Hay que decidir qué se versiona.
- **Decisión:** Pinear con `==` solo las **5 dependencias directas**
  (`pandas`, `numpy`, `faker`, `sqlalchemy`, `psycopg[binary]`). Las
  transitivas las resuelve `pip`.
- **Consecuencias:** Archivo legible y suficiente para el proyecto. No hay
  reproducibilidad *total*: una transitiva podría actualizarse y cambiar el
  comportamiento. Si se necesita, se añadirá un lock file con `pip-tools`,
  `uv` o `poetry`. Para el driver se usa el extra `psycopg[binary]`, que
  instala `psycopg` + `psycopg-binary` en la misma versión.

## ADR-012 — Migrar la autenticación de Git de HTTPS a SSH

- **Fecha:** 2026-10-19
- **Estado:** Aceptada (reemplaza ADR-005)
- **Contexto:** Con HTTPS, cada `push` exige el Personal Access Token. El
  desarrollador ya disponía de una clave SSH `ed25519` y prefirió usarla.
- **Decisión:** Autenticación por **SSH** con clave `ed25519`. Remoto:
  `git@github.com:MoisesEsp/saas-subscription-analytics.git`.
- **Consecuencias:** No se solicita token en cada push. La clave privada
  (`~/.ssh/id_ed25519`) nunca debe compartirse ni versionarse; solo la
  pública (`id_ed25519.pub`) se registra en GitHub. Verificado con
  `ssh -T git@github.com` → autenticación correcta (el código de salida 1 es
  normal porque GitHub no ofrece shell).