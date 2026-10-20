# COMANDOS_UTILES.md

> Comandos frecuentes del proyecto. Se actualiza a medida que avanza el proyecto.

---

## Docker / Docker Compose

```bash
# Levantar todo el stack en segundo plano
docker compose up -d

# Ver estado de los servicios
docker compose ps

# Ver logs de un servicio (ej: postgres)
docker compose logs -f postgres

# Entrar a la shell de un contenedor
docker compose exec postgres bash

# Ejecutar un comando puntual en un contenedor
docker compose exec -T postgres psql -U $POSTGRES_USER -d $POSTGRES_DB

# Bajar el stack (conserva volúmenes)
docker compose down

# Bajar el stack y borrar volúmenes (⚠️ DESTRUCTIVO — pierde datos)
docker compose down -v

# Reconstruir imagen de un servicio
docker compose build --no-cache <servicio>

# Ejecutar un script en el contenedor Python sin dejarlo arriba
docker compose run --rm python-etl python etl/generate_data.py
```

## PostgreSQL (dentro del contenedor)

```bash
# Conectar a la DB
docker compose exec -T postgres psql -U $POSTGRES_USER -d $POSTGRES_DB

# Ejecutar un archivo SQL
docker compose exec -T postgres psql -U $POSTGRES_USER -d $POSTGRES_DB < sql/ddl/01_create_dim_users.sql

# Listar tablas
docker compose exec -T postgres psql -U $POSTGRES_USER -d $POSTGRES_DB -c "\dt"

# Describir tabla
docker compose exec -T postgres psql -U $POSTGRES_USER -d $POSTGRES_DB -c "\d fact_subscriptions"

# Salir de psql
\q
```

## Git

```bash
# Ver estado
git status

# Ver últimos 5 commits en formato compacto
git log --oneline -5

# Añadir todo
git add .

# Añadir un archivo específico
git add ruta/archivo.ext

# Commit
git commit -m "tipo: descripción breve"

# Push
git push origin main

# Ver el remoto configurado
git remote -v

# Ver diferencias antes de commitear
git diff
```

**Convención de commits (Conventional Commits):**
- `feat:` nueva funcionalidad
- `fix:` corrección
- `docs:` solo documentación
- `chore:` tareas de mantenimiento (config, deps)
- `refactor:` reorganización sin cambio de comportamiento
- `test:` añadir o modificar tests

## Generar `SUPERSET_SECRET_KEY`

```bash
# Opción 1
openssl rand -base64 42

# Opción 2
python -c "import secrets; print(secrets.token_urlsafe(42))"
```

## Utilidades varias

```bash
# Ver el árbol de carpetas (si tienes tree instalado)
tree -a -I '.git|__pycache__|.venv'

# Alternativa sin tree
find . -type d -not -path './.git*' | sort

# Renombrar archivo
mv viejo.ext nuevo.ext
```