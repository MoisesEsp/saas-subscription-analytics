# CONTRIBUTING.md

> Guía de contribución para este repositorio. Aunque el proyecto es
> mayoritariamente individual, se documenta el flujo para mantener
> estándares profesionales.

---

## Estructura del repositorio

Ver sección 3 de `PLAN_PROYECTO.md`.

## Convención de commits

Se usa **Conventional Commits**:

- `feat:` nueva funcionalidad
- `fix:` corrección de bug
- `docs:` solo documentación
- `chore:` mantenimiento (deps, config)
- `refactor:` reestructuración sin cambio de comportamiento
- `test:` tests

Ejemplo: `feat: add cohort retention view for monthly analysis`

## Convención de nombres

- Carpetas: `kebab-case`
- Archivos Python: `snake_case.py`
- Scripts SQL: `NN_descripcion.sql` (NN = número de orden de ejecución)
- Ramas Git: `tipo/descripcion-corta` (ej: `feat/cohort-analysis`)

## Flujo de trabajo

1. Crear rama desde `main` si es un cambio grande.
2. Hacer commits atómicos y descriptivos.
3. Actualizar documentación si aplica (`docs/`, `README.md`).
4. Actualizar `PROGRESO.md` si cierra un sub-paso del plan.
5. Push y (si aplica) Pull Request.

## Idioma

- Código y nombres de archivos: **inglés**
- Comentarios en código: **inglés**
- Documentación (`README.md`, `docs/`): **español**
- Commits: **inglés** (convención de la industria)

## Antes de hacer push

- Verificar que `.env` no está staged: `git status` no debe mostrarlo.
- Verificar que los archivos generados en `data/` no están staged.
- Verificar que `requirements.txt` tiene versiones pinneadas.