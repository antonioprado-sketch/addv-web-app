---
name: addv-web-app
description: Aplica el protocolo corporativo addv-web-app para diseñar, construir, revisar o desplegar sitios y aplicaciones web (frontend, backend, UX/UI, Docker). Impone un flujo estricto de analizar-antes-de-implementar con confirmación explícita del usuario, prohíbe asumir requisitos ambiguos, y exige mejores prácticas de UX/UI, accesibilidad, rendimiento (Core Web Vitals), seguridad anti-hackeo, contenedorización segura con Docker/Docker Compose, pruebas unitarias ejecutables en local, y calidad de código nivel auditoría SonarQube. Usa esta skill siempre que el usuario mencione "addv-web-app", pida crear, modificar, auditar o desplegar un sitio o app web corporativa/reputacional, pida ajustes de UI/UX/accesibilidad/performance/seguridad en un proyecto web, trabaje con Docker o docker-compose para un servicio web, o pida generar/actualizar project_state.md, CLAUDE.md o el README.md de un proyecto web.
---

# addv-web-app

Protocolo de trabajo para proyectos de aplicaciones web corporativas donde un error de UX, de seguridad o una regresión tiene costo reputacional real. La skill existe para que cada petición pase primero por análisis y aprobación explícita, y para que cada entrega cumpla un piso de calidad fijo (UX/UI, accesibilidad, rendimiento, seguridad, Docker, pruebas, calidad de código) sin tener que repetir esas reglas en cada mensaje.

## Flujo obligatorio: Analizar → Proponer → Confirmar → Implementar

Ante cualquier petición dentro de un proyecto bajo este protocolo:

1. **Analiza primero, sin ejecutar nada.** Identifica qué se pide, qué archivos o módulos se ven afectados, qué riesgos existen y qué opciones hay. No escribas ni modifiques archivos del proyecto todavía. Esto evita gastar tokens y tiempo implementando algo que el usuario en realidad no quería.
2. **Responde en segmentos completos y acotados.** Divide el trabajo en bloques que el usuario pueda revisar y aprobar de forma independiente, en vez de intentar resolver todo de una sola vez. Un segmento debe quedar funcionalmente completo, no a medias.
3. **Espera confirmación explícita antes de implementar.** Presentar un plan, un diagnóstico, una alternativa o un fragmento de código de ejemplo es libre; escribir/modificar archivos reales del proyecto, ejecutar comandos que cambien estado, o hacer commits no lo es. Solo procede a implementar cuando el usuario dé una instrucción explícita de tipo "impleméntalo", "procede", "hazlo" o equivalente. Si el usuario ya dio luz verde a un plan completo con anterioridad, no vuelvas a pedir permiso para cada paso trivial de ese mismo plan — pero sí detente si aparece una decisión nueva no cubierta por esa aprobación.
4. **Con luz verde, implementa el segmento aprobado completo** y confirma cuando esté listo, antes de avanzar al siguiente segmento.

## Regla anti-suposición

No asumas requisitos, alcances, nombres, valores o decisiones de negocio que no te hayan dado explícitamente. Ante cualquier ambigüedad, pregunta antes de continuar — es preferible una pregunta puntual a construir sobre un supuesto incorrecto en un sitio de producción. Esto incluye nombres de herramientas o skills que el usuario mencione y que no reconozcas: en vez de inventar su función, pregunta o investiga la fuente real antes de integrarlas.

**Excepción — no te detengas de inmediato:** si resolver la petición se complica por una limitación técnica, un conflicto de dependencias o una decisión de arquitectura no trivial, antes de preguntar o detenerte, investiga y propón **al menos 3 alternativas viables** con sus ventajas y desventajas (por ejemplo: impacto en performance, complejidad de mantenimiento, riesgo de seguridad, costo de migración). Pregunta solo si, tras evaluar esas alternativas, ninguna es razonable sin una decisión del usuario.

## Estándares de calidad — piso no negociable

Toda entrega de este protocolo debe cumplir:

- **UX/UI**: usabilidad, jerarquía visual clara, accesibilidad y diseño responsivo en todos los breakpoints relevantes (no solo desktop).
- **Seguridad de contenedores**: imágenes base mínimas, usuario no-root dentro del contenedor, secretos fuera de las imágenes (variables de entorno o secret managers, nunca hardcodeados), y atención a vulnerabilidades conocidas de las dependencias/imágenes usadas.
- **Anti-hackeo general**: validación y sanitización de toda entrada de usuario, cabeceras de seguridad HTTP correctas, mitigación de los riesgos del OWASP Top 10 relevantes al stack, y rate limiting donde el endpoint lo amerite. El objetivo es que el servicio no pueda ser interrumpido ni se pueda filtrar información desde él. Esto también aplica a cualquier script de instalación de terceros (ver sección de herramientas de eficiencia): revísalo antes de ejecutarlo, no lo corras a ciegas.
- **Rendimiento**: carga eficiente del sitio; las animaciones deben sentirse fluidas y no degradar Core Web Vitals — animar solo propiedades de bajo costo (`transform`, `opacity`), nunca `top/left/width/height` o propiedades que disparen layout/paint.
- **Aplica esto de forma proactiva, no como checklist final**: estos estándares (UX/UI, seguridad, rendimiento, calidad de código) se aplican desde el primer diseño o implementación, sin que el usuario tenga que pedirlos cada vez — no son algo que se revisa solo antes de cerrar la entrega. Para diseño/UI, invoca activamente la skill `impeccable` (ver tabla de fases) en vez de improvisar composición visual desde cero.
- **Calidad de código**: escribe como si el código fuera a pasar una auditoría de SonarQube — sin code smells evidentes, sin vulnerabilidades conocidas, manejo de errores robusto y explícito (no silenciar excepciones), sin duplicación innecesaria.
- **Pruebas unitarias siempre**: toda funcionalidad solicitada se entrega con sus pruebas unitarias correspondientes, ejecutables en local con un solo comando documentado (por ejemplo `npm test` o `pytest`, según el stack). Un cambio sin sus pruebas no está terminado.
- **Cero regresiones**: antes de dar un cambio por terminado, verifica que no rompe funcionalidad existente. Si el alcance del cambio hace imposible garantizar esto con certeza, dilo explícitamente en vez de asumir que está bien.
- **Continuidad del servicio**: al tratarse de un sitio corporativo y reputacional, valida antes y después de cada cambio que los servicios sigan activos, y ejecuta todas las pruebas posibles (build, lint, tests automatizados, smoke test del contenedor levantado) para evitar intermitencias.

## Skills a invocar según la fase del trabajo

Usa las siguientes skills instaladas cuando la tarea entre en su dominio. Si alguna no está disponible, dilo antes de continuar en vez de improvisar su función:

| Fase | Skills |
|---|---|
| Diseño / UX | `design-critique`, `research-synthesis`, `user-research`, `ux-copy` |
| UI / Frontend | `accessibility-review`, `design-handoff`, `design-system`, `frontend-design`, `frontend-design-direction`, `impeccable` |
| Backend / Infraestructura | `api-integration-consumption`, `backend-robustness-architecture`, `docker-containerization` |
| Checklist final (siempre antes de cerrar una entrega visual) | `web-performance-accessibility`, `low-impact-motion` |
| Eficiencia (herramientas externas, no skills nativas) | `caveman`, `graphify`, `claude-mem`, `superpowers`, `find-skill`, `task-observer` — ver secciones siguientes |

Aplica el orden natural del proyecto: research/critique de UX antes de construir la UI, `docker-containerization` y `backend-robustness-architecture` al tocar servidor o infraestructura, y el checklist de performance/accesibilidad/motion como última pasada antes de dar cualquier entrega visual por terminada.

## Resolución de skills: dónde buscar antes de instalar

Antes de dar una skill por "no disponible", sigue este orden:

1. **Catálogo de skills activas de la sesión** — la lista que ya conoces en este entorno.
2. **`D:\cc`** (repositorio local de skills/herramientas del usuario en su máquina Windows) — revisa tanto carpetas ya extraídas (por ejemplo `skills-ui/`, `skills-ux/`, `caveman/`) como archivos `.zip` sin extraer (por ejemplo `frontend-design-direction.zip`, `backend-robustness-architecture.zip`). Si encuentras un `.zip` con el nombre de la skill, extráelo antes de asumir que falta.
3. **Repositorio oficial** de la herramienta (ver tabla de la sección de eficiencia, o el que el usuario indique) — instálala desde ahí, revisando el instalador antes de ejecutarlo (regla anti-hackeo).
4. Si aun así no aparece o su propósito no es evidente, **pregunta** — no lo inventes.

Nota: `D:\cc` es una ruta local en la máquina del usuario. Una sesión cloud efímera solo puede verla si esa carpeta fue conectada explícitamente vía el puente de dispositivo; si trabajas en un entorno sin ese acceso, dilo en vez de asumir que la skill no existe ahí.

## Herramientas complementarias de eficiencia

Ninguna de las siguientes es una skill nativa de Claude con `SKILL.md` propio empaquetable — son herramientas/plugins externos de terceros, cada uno con su propio instalador, que se instalan en el entorno real del usuario (su máquina, su Claude Code), no dentro de una sesión cloud temporal. Estado verificado en `D:\cc` la última vez que se revisó (2026-08-02) — vuelve a verificar si ha pasado tiempo:

- **caveman** ([github.com/juliusbrussee/caveman](https://github.com/juliusbrussee/caveman)) — ✅ **instalado y confirmado activo** (verificado en sesión 2026-08-18, modo `full`), clonado en `D:\cc\caveman` con `dist/caveman.skill` ya construido y varias sub-skills (`caveman`, `caveman-commit`, `caveman-compress`, `caveman-review`, `caveman-stats`, `cavecrew`). Hace que las respuestas del agente sean ~65% más concisas sin perder precisión técnica. Uso: `/caveman` para activarlo/cambiar de nivel, `/caveman-commit` para mensajes de commit, `/caveman-stats` para ver el ahorro. Si en una sesión nueva no aparece activo, instalarlo desde el clon local no requiere red: `bash D:\cc\caveman\install.sh` (o `install.ps1` en PowerShell) — revísalo antes de correrlo. Mientras esté activo, mantén las respuestas técnicas de este proyecto breves y directas — refuerza la regla de segmentos acotados del flujo obligatorio; código, commits y avisos de seguridad se escriben siempre en formato normal, sin comprimir.

- **impeccable** ([github.com/pbakaus/impeccable](https://github.com/pbakaus/impeccable)) — ✅ **instalado y activo** (confirmado disponible como skill en sesión 2026-08-18). Sistema de guía de diseño con comandos (`/impeccable audit`, `polish`, `critique`, `animate`, etc.) y reglas detectoras de anti-patrones de diseño genérico ("AI slop"), complementario a `frontend-design-direction`. Úsalo activamente para cualquier trabajo de diseño/UI de este protocolo (ver tabla de fases arriba), no solo cuando el usuario lo pida por nombre.

- **graphify** ([github.com/Graphify-Labs/graphify](https://github.com/Graphify-Labs/graphify)) — ❌ no encontrado en `D:\cc`, instalar desde el repo. Convierte código, esquemas SQL, configuración y documentación en un grafo de conocimiento consultable. Instalación: `uv tool install graphifyy` seguido de `graphify install`. Uso: `/graphify .` para construir el grafo, `graphify query "pregunta"` para consultarlo, `graphify path "A" "B"` para trazar conexiones. Úsalo en el paso "Analiza primero" del flujo obligatorio para mapear el proyecto antes de proponer cambios.

- **claude-mem** ([github.com/thedotmack/claude-mem](https://github.com/thedotmack/claude-mem)) — ❌ no encontrado en `D:\cc`, instalar desde el repo. **Ojo:** en `D:\cc` existe una carpeta `claude-counter`, pero es una extensión de navegador distinta (contador de tokens en claude.ai) — no confundirla con `claude-mem`, no es lo mismo. `claude-mem` comprime y persiste el contexto de sesiones anteriores para inyectarlo en sesiones futuras, útil para que el protocolo de este proyecto (`project_state.md`, decisiones tomadas) no se pierda entre sesiones. Instalación: `npx claude-mem install`.

- **superpowers** ([github.com/obra/superpowers](https://github.com/obra/superpowers)) — ❌ no encontrado en `D:\cc`, instalar desde el repo. Metodología completa de desarrollo (brainstorm → plan → implementación por tareas de 2-5 min → TDD red-green-refactor → revisión de código). Complementa bien el flujo Analizar → Proponer → Confirmar → Implementar de este protocolo. Instalación (Claude Code, vía marketplace): `/plugin install superpowers@claude-plugins-official`.

- **find-skill** ([github.com/fockus/claude-skill-find-skill](https://github.com/fockus/claude-skill-find-skill)) — ❌ no encontrado en `D:\cc`, instalar desde el repo. Busca e instala skills entre 4800+ de 14 fuentes distintas — es literalmente la herramienta que automatiza el paso 3 de "Resolución de skills" de arriba. **Recomendado instalar primero**, porque una vez activo simplifica el resto de instalaciones de esta lista. Instalación: `curl -sSL https://raw.githubusercontent.com/fockus/claude-skill-find-skill/main/quick-install.sh | bash` (o `pipx install find-skill`). Uso: `/find-skill <búsqueda>`, `/install-skill <fuente>`.

- **task-observer** ("one skill to rule them all", [github.com/rebelytics/one-skill-to-rule-them-all](https://github.com/rebelytics/one-skill-to-rule-them-all)) — ❌ no encontrado en `D:\cc`. Meta-skill que observa las sesiones de trabajo, detecta patrones repetidos y propone mejoras a skills existentes (incluida, potencialmente, `addv-web-app`) para revisión y aprobación del usuario. No tiene instalador estándar: se instala dando el contenido del repo (skill file, README, guía) al agente para que lo configure según el entorno.

Antes de correr cualquier instalador de terceros, revísalo tú mismo (o pide que se revise) — son proyectos no auditados por Anthropic, y este protocolo exige prácticas anti-hackeo; instalar algo a ciegas contradice esa misma regla.

## Docker siempre

Todo el desarrollo corre en Docker y Docker Compose — no propongas ni construyas nada que dependa de un entorno local no contenedorizado. El `README.md` debe explicar, paso a paso, cómo levantar el proyecto completo desde cero: requisitos previos, variables de entorno necesarias, comandos exactos (`docker compose up`, migraciones, seed, etc.) y cómo verificar que el servicio quedó arriba correctamente (por ejemplo, un healthcheck o una URL de prueba).

## Entregables obligatorios de documentación

Mantén siempre actualizados estos tres archivos en la raíz del proyecto:

- **`project_state.md`** — estado actual del proyecto: qué existe, qué está en progreso, qué falta, decisiones ya tomadas y decisiones pendientes de confirmar con el usuario. Actualízalo al cerrar cada segmento implementado, no solo al final del proyecto.
- **`CLAUDE.md`** — contexto operativo persistente del repo: arquitectura, convenciones de código, comandos frecuentes (build, test, lint, levantar Docker) y las reglas de este protocolo, para que cualquier sesión futura de Claude en este proyecto las siga sin que se le tengan que repetir.
- **`README.md`** — instrucciones de instalación y ejecución paso a paso con Docker/Docker Compose, escritas para alguien que nunca vio el proyecto.

## Resumen del contrato

- No asumas: pregunta ante cualquier ambigüedad genuina, incluida la función de herramientas o skills que no reconozcas.
- No implementes sin confirmación explícita del segmento correspondiente.
- No entregues nada que no cumpla UX/UI, accesibilidad, rendimiento, seguridad, Docker, pruebas unitarias ejecutables en local y calidad nivel SonarQube.
- No rompas funcionalidad existente sin decirlo explícitamente.
- Documenta siempre en `project_state.md`, `CLAUDE.md` y `README.md`.
