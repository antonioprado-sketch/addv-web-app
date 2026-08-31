---
name: addv-web-app
description: >-
  Aplica el protocolo corporativo addv-web-app para diseñar, construir, revisar o desplegar sitios y apps web (frontend, backend, UX/UI, Docker). Flujo obligatorio — analizar, revisar impacto, criticar y mejorar el requerimiento, antes/después visual, confirmación explícita, implementar, probar (unitarias y funcionales), asegurar (nada sensible en el frontend, todo cifrado). Prohíbe asumir requisitos ambiguos y exige UX/UI, accesibilidad, Core Web Vitals, anti-hackeo, Docker/Docker Compose y calidad nivel SonarQube. Usa esta skill siempre que se mencione "addv-web-app", se pida crear, modificar, auditar o desplegar un sitio o app web corporativa, ajustes de UI/UX/accesibilidad/performance/seguridad, trabajo con Docker, o generar/actualizar project_state.md, CLAUDE.md o README.md de un proyecto web.
---

# addv-web-app

Protocolo de trabajo para proyectos de aplicaciones web corporativas donde un error de UX, de seguridad o una regresión tiene costo reputacional real. La skill existe para que cada petición pase primero por análisis y aprobación explícita, y para que cada entrega cumpla un piso de calidad fijo (UX/UI, accesibilidad, rendimiento, seguridad, Docker, pruebas, calidad de código) sin tener que repetir esas reglas en cada mensaje.

## Flujo obligatorio: Analizar → Revisar impacto → Criticar y mejorar → Propuesta visual → Confirmar → Implementar → Probar → Asegurar

Ante cualquier petición dentro de un proyecto bajo este protocolo, sigue estos ocho pasos en orden — ninguno se salta, aunque la petición parezca trivial:

1. **Analiza primero, sin ejecutar nada.** Identifica qué se pide, qué archivos o módulos se ven afectados y qué opciones hay. No escribas ni modifiques archivos del proyecto todavía. Esto evita gastar tokens y tiempo implementando algo que el usuario en realidad no quería.
2. **Revisa el impacto.** Antes de aceptar el requerimiento tal cual, identifica qué podría romperse, qué depende de lo que vas a tocar y qué riesgo trae (seguridad, performance, UX, datos). Esto alimenta la crítica del paso siguiente — no es un trámite, es la base para poder opinar con criterio.
3. **Critica y mejora el requerimiento.** No implementes literalmente lo pedido sin evaluarlo primero: si detectas una forma mejor de resolver el problema real detrás de la petición — más simple, más segura, más accesible, más mantenible, con menos deuda técnica — dilo explícitamente y preséntala junto con la opción original. El usuario decide con las dos sobre la mesa; no te calles una mejora obvia solo por ejecutar rápido.
4. **Da una propuesta visual de antes y después** para todo cambio que afecte la interfaz: un mockup, wireframe o descripción visual concreta del estado actual comparado con el propuesto (apóyate en `design-critique` y `frontend-design-direction` de la tabla de skills). No apliques cambios visuales a partir de una descripción en texto sin haberlos mostrado antes.
5. **Responde en segmentos completos y acotados.** Divide el trabajo en bloques que el usuario pueda revisar y aprobar de forma independiente, en vez de intentar resolver todo de una sola vez. Un segmento debe quedar funcionalmente completo, no a medias.
6. **Espera confirmación explícita antes de implementar.** Presentar el análisis, la crítica, las alternativas o la propuesta visual es libre; escribir/modificar archivos reales del proyecto, ejecutar comandos que cambien estado, o hacer commits no lo es. Solo procede a implementar cuando el usuario dé una instrucción explícita de tipo "impleméntalo", "procede", "hazlo" o equivalente — sobre la versión (original o mejorada) que haya elegido. Si el usuario ya dio luz verde a un plan completo con anterioridad, no vuelvas a pedir permiso para cada paso trivial de ese mismo plan — pero sí detente si aparece una decisión nueva no cubierta por esa aprobación.
7. **Con luz verde, implementa el segmento aprobado completo** y confirma cuando esté listo, antes de avanzar al siguiente segmento.
8. **Prueba y asegura antes de dar por cerrado.** Todo lo implementado se entrega con sus pruebas unitarias (ver estándares de calidad) **y** una verificación funcional real — levantar el contenedor y ejercitar el flujo, no solo confiar en que "debería funcionar". Junto con eso, revisa explícitamente la seguridad: que no haya datos sensibles expuestos en el frontend (claves, tokens, credenciales, PII o lógica de negocio crítica visibles en el HTML/JS del cliente) y que todo dato sensible viaje y se almacene cifrado, además de las prácticas anti-hackeo generales de la sección siguiente.

## Regla anti-suposición

No asumas requisitos, alcances, nombres, valores o decisiones de negocio que no te hayan dado explícitamente. Ante cualquier ambigüedad, pregunta antes de continuar — es preferible una pregunta puntual a construir sobre un supuesto incorrecto en un sitio de producción. Esto incluye nombres de herramientas o skills que el usuario mencione y que no reconozcas: en vez de inventar su función, pregunta o investiga la fuente real antes de integrarlas.

**Excepción — no te detengas de inmediato:** si resolver la petición se complica por una limitación técnica, un conflicto de dependencias o una decisión de arquitectura no trivial, antes de preguntar o detenerte, investiga y propón **al menos 3 alternativas viables** con sus ventajas y desventajas (por ejemplo: impacto en performance, complejidad de mantenimiento, riesgo de seguridad, costo de migración). Pregunta solo si, tras evaluar esas alternativas, ninguna es razonable sin una decisión del usuario.

## Estándares de calidad — piso no negociable

Toda entrega de este protocolo debe cumplir:

- **UX/UI**: usabilidad, jerarquía visual clara, accesibilidad y diseño responsivo en todos los breakpoints relevantes (no solo desktop).
- **Seguridad de contenedores**: imágenes base mínimas, usuario no-root dentro del contenedor, secretos fuera de las imágenes (variables de entorno o secret managers, nunca hardcodeados), y atención a vulnerabilidades conocidas de las dependencias/imágenes usadas.
- **Anti-hackeo general**: validación y sanitización de toda entrada de usuario, cabeceras de seguridad HTTP correctas, mitigación de los riesgos del OWASP Top 10 relevantes al stack, y rate limiting donde el endpoint lo amerite. El objetivo es que el servicio no pueda ser interrumpido ni se pueda filtrar información desde él. Esto también aplica a cualquier script de instalación de terceros (ver sección de herramientas de eficiencia): revísalo antes de ejecutarlo, no lo corras a ciegas.
- **Nada sensible en el frontend**: ninguna clave, token, credencial, secreto de API, dato personal (PII) o lógica de negocio crítica debe quedar visible en el HTML, JS del cliente, código fuente de la página o respuestas de red inspeccionables desde el navegador. Todo eso vive y se valida en el backend.
- **Cifrado siempre**: datos sensibles cifrados en tránsito (TLS/HTTPS en todo el proyecto, sin excepciones) y en reposo cuando corresponda (contraseñas con hash+salt, datos personales o financieros cifrados en base de datos) — nunca en texto plano.
- **Rendimiento**: carga eficiente del sitio; las animaciones deben sentirse fluidas y no degradar Core Web Vitals — animar solo propiedades de bajo costo (`transform`, `opacity`), nunca `top/left/width/height` o propiedades que disparen layout/paint.
- **Calidad de código**: escribe como si el código fuera a pasar una auditoría de SonarQube — sin code smells evidentes, sin vulnerabilidades conocidas, manejo de errores robusto y explícito (no silenciar excepciones), sin duplicación innecesaria.
- **Pruebas siempre, unitarias y funcionales**: toda funcionalidad solicitada se entrega con sus pruebas unitarias correspondientes, ejecutables en local con un solo comando documentado (por ejemplo `npm test` o `pytest`, según el stack), **más** una verificación funcional del flujo real (no solo mocks). Un cambio sin sus pruebas no está terminado.
- **Cero regresiones**: antes de dar un cambio por terminado, verifica que no rompe funcionalidad existente. Si el alcance del cambio hace imposible garantizar esto con certeza, dilo explícitamente en vez de asumir que está bien.
- **Continuidad del servicio**: al tratarse de un sitio corporativo y reputacional, valida antes y después de cada cambio que los servicios sigan activos, y ejecuta todas las pruebas posibles (build, lint, tests automatizados, smoke test del contenedor levantado) para evitar intermitencias.

## Skills a invocar según la fase del trabajo

Usa las siguientes skills instaladas cuando la tarea entre en su dominio. Si alguna no está disponible, dilo antes de continuar en vez de improvisar su función:

| Fase | Skills |
|---|---|
| Diseño / UX | `design-critique`, `research-synthesis`, `user-research`, `ux-copy` |
| UI / Frontend | `accessibility-review`, `design-handoff`, `design-system`, `frontend-design`, `frontend-design-direction` |
| Backend / Infraestructura | `api-integration-consumption`, `backend-robustness-architecture`, `docker-containerization` |
| Checklist final (siempre antes de cerrar una entrega visual) | `web-performance-accessibility`, `low-impact-motion` |
| Eficiencia (herramientas externas, no skills nativas) | `agent-skills` **(prioridad: instalar primero)**, `caveman`, `graphify`, `claude-mem`, `superpowers`, `impeccable`, `find-skill`, `task-observer`, `ruflo`, `prompt-master` — ver secciones siguientes |

Aplica el orden natural del proyecto: research/critique de UX antes de construir la UI, `docker-containerization` y `backend-robustness-architecture` al tocar servidor o infraestructura, y el checklist de performance/accesibilidad/motion como última pasada antes de dar cualquier entrega visual por terminada.

## Origen de las skills nativas (repositorio, cuando existe)

Verificado el 2026-08-02 contactando cada fuente directamente (no asumido):

| Skill | Repositorio |
|---|---|
| `accessibility-review` | [anthropics/knowledge-work-plugins — design/skills/accessibility-review](https://github.com/anthropics/knowledge-work-plugins/blob/main/design/skills/accessibility-review/SKILL.md) |
| `design-critique` | [anthropics/knowledge-work-plugins — design/skills/design-critique](https://github.com/anthropics/knowledge-work-plugins/blob/main/design/skills/design-critique/SKILL.md) |
| `design-handoff` | [anthropics/knowledge-work-plugins — design/skills/design-handoff](https://github.com/anthropics/knowledge-work-plugins/blob/main/design/skills/design-handoff/SKILL.md) |
| `design-system` | [anthropics/knowledge-work-plugins — design/skills/design-system](https://github.com/anthropics/knowledge-work-plugins/blob/main/design/skills/design-system/SKILL.md) |
| `research-synthesis` | [anthropics/knowledge-work-plugins — design/skills/research-synthesis](https://github.com/anthropics/knowledge-work-plugins/blob/main/design/skills/research-synthesis/SKILL.md) |
| `user-research` | [anthropics/knowledge-work-plugins — design/skills/user-research](https://github.com/anthropics/knowledge-work-plugins/blob/main/design/skills/user-research/SKILL.md) |
| `ux-copy` | [anthropics/knowledge-work-plugins — design/skills/ux-copy](https://github.com/anthropics/knowledge-work-plugins/blob/main/design/skills/ux-copy/SKILL.md) |
| `frontend-design` | [anthropics/skills — skills/frontend-design](https://github.com/anthropics/skills/blob/main/skills/frontend-design/SKILL.md) |
| `frontend-design-direction` | Sin repositorio público confirmado — marcada `"source": "custom"` en el manifiesto de skills. Respaldo local: `D:\cc\frontend-design-direction.zip`. |
| `api-integration-consumption` | Sin repositorio público confirmado — `"source": "custom"`. Respaldo local: `D:\cc\api-integration-consumption.zip`. |
| `backend-robustness-architecture` | Sin repositorio público confirmado — `"source": "custom"`. Respaldo local: `D:\cc\backend-robustness-architecture.zip`. |
| `docker-containerization` | Sin repositorio público confirmado — `"source": "custom"`. Respaldo local: `D:\cc\docker-containerization.zip`. |
| `low-impact-motion` | Sin repositorio público confirmado — `"source": "custom"`. Respaldo local: `D:\cc\low-impact-motion.zip`. |
| `web-performance-accessibility` | Sin repositorio público confirmado — `"source": "custom"`. Respaldo local: `D:\cc\web-performance-accessibility.zip`. |

Las seis skills marcadas "custom" no tienen un repositorio público conocido — no se les inventó uno. Si en algún momento aparece su origen real (por ejemplo, si las creó alguien de tu equipo y sabe de dónde salieron), reemplaza esa fila con el link real en vez de dejar el respaldo local como única fuente.

## Resolución de skills: dónde buscar antes de instalar

Antes de dar una skill por "no disponible", sigue este orden:

1. **Catálogo de skills activas de la sesión** — la lista que ya conoces en este entorno.
2. **`D:\cc`** (repositorio local de skills/herramientas del usuario en su máquina Windows) — revisa tanto carpetas ya extraídas (por ejemplo `skills-ui/`, `skills-ux/`, `caveman/`) como archivos `.zip` sin extraer (por ejemplo `frontend-design-direction.zip`, `backend-robustness-architecture.zip`). Si encuentras un `.zip` con el nombre de la skill, extráelo antes de asumir que falta.
3. **Repositorio oficial** de la herramienta (ver tabla de la sección de eficiencia, o el que el usuario indique) — instálala desde ahí, revisando el instalador antes de ejecutarlo (regla anti-hackeo).
4. Si aun así no aparece o su propósito no es evidente, **pregunta** — no lo inventes.

Nota: `D:\cc` es una ruta local en la máquina del usuario. Una sesión cloud efímera solo puede verla si esa carpeta fue conectada explícitamente vía el puente de dispositivo; si trabajas en un entorno sin ese acceso, dilo en vez de asumir que la skill no existe ahí.

## Herramientas complementarias de eficiencia

Ninguna de las siguientes es una skill nativa de Claude con `SKILL.md` propio empaquetable — son herramientas/plugins externos de terceros, cada uno con su propio instalador, que se instalan en el entorno real del usuario (su máquina, su Claude Code), no dentro de una sesión cloud temporal. Estado verificado en `D:\cc` la última vez que se revisó (2026-08-02) — vuelve a verificar si ha pasado tiempo.

**Orden de instalación:** si ninguna de estas herramientas está instalada todavía, instala primero `agent-skills` — el resto de la lista puede instalarse después, en cualquier orden.

- **agent-skills** ([github.com/addyosmani/agent-skills](https://github.com/addyosmani/agent-skills)) — ⭐ **instalar primero, antes que cualquier otra de esta lista.** No encontrado en `D:\cc`. 24 skills que cubren todo el ciclo de vida de desarrollo (Define → Plan → Build → Verify → Review → Ship), con tablas "anti-racionalización" que frenan atajos — encaja directo con los pasos 2-4 y 8 del flujo obligatorio (revisar impacto, criticar el requerimiento, probar, asegurar). Trae comandos `/spec`, `/plan`, `/build`, `/test`, `/review`, `/webperf`, `/code-simplify`, `/ship`. Instalación: `npx skills add addyosmani/agent-skills` (o una skill puntual: `npx skills add addyosmani/agent-skills --skill code-review-and-quality`).

- **caveman** ([github.com/juliusbrussee/caveman](https://github.com/juliusbrussee/caveman)) — ✅ **ya clonado en `D:\cc\caveman`**, con `dist/caveman.skill` ya construido y varias sub-skills (`caveman`, `caveman-commit`, `caveman-compress`, `caveman-review`, `caveman-stats`, `cavecrew`). Hace que las respuestas del agente sean ~65% más concisas sin perder precisión técnica. Uso: `/caveman` para activarlo, `/caveman-commit` para mensajes de commit, `/caveman-stats` para ver el ahorro. Si no está activo aún en el Claude Code del usuario, instalarlo desde el clon local no requiere red: `bash D:\cc\caveman\install.sh` (o `install.ps1` en PowerShell) — revísalo antes de correrlo. Cuando esté activo, mantén las respuestas técnicas de este proyecto breves y directas — refuerza la regla de segmentos acotados del flujo obligatorio.

- **graphify** ([github.com/Graphify-Labs/graphify](https://github.com/Graphify-Labs/graphify)) — ❌ no encontrado en `D:\cc`, instalar desde el repo. Convierte código, esquemas SQL, configuración y documentación en un grafo de conocimiento consultable. Instalación: `uv tool install graphifyy` seguido de `graphify install`. Uso: `/graphify .` para construir el grafo, `graphify query "pregunta"` para consultarlo, `graphify path "A" "B"` para trazar conexiones. Úsalo en el paso "Analiza primero" del flujo obligatorio para mapear el proyecto antes de proponer cambios.

- **claude-mem** ([github.com/thedotmack/claude-mem](https://github.com/thedotmack/claude-mem)) — ❌ no encontrado en `D:\cc`, instalar desde el repo. **Ojo:** en `D:\cc` existe una carpeta `claude-counter`, pero es una extensión de navegador distinta (contador de tokens en claude.ai) — no confundirla con `claude-mem`, no es lo mismo. `claude-mem` comprime y persiste el contexto de sesiones anteriores para inyectarlo en sesiones futuras. Este protocolo ya replica esa misma idea sin depender de la herramienta instalada: `addv/cmem.md` (ver sección de entregables) es el registro comprimido de la conversación que cumple esa función dentro del proyecto. Si `claude-mem` llega a instalarse, trátalo como un complemento de `addv/cmem.md`, no como su reemplazo — sigue manteniendo `addv/cmem.md` de todas formas, porque es lo que viaja con el repo y no depende de que la herramienta esté instalada en cada máquina. Instalación: `npx claude-mem install`.

- **superpowers** ([github.com/obra/superpowers](https://github.com/obra/superpowers)) — ❌ no encontrado en `D:\cc`, instalar desde el repo. Metodología completa de desarrollo (brainstorm → plan → implementación por tareas de 2-5 min → TDD red-green-refactor → revisión de código). Complementa bien el flujo Analizar → Proponer → Confirmar → Implementar de este protocolo. Instalación (Claude Code, vía marketplace): `/plugin install superpowers@claude-plugins-official`.

- **impeccable** ([github.com/pbakaus/impeccable](https://github.com/pbakaus/impeccable)) — ❌ no encontrado en `D:\cc`, instalar desde el repo. Sistema de guía de diseño con 23 comandos (`/impeccable audit`, `polish`, `critique`, `animate`, etc.) y 59 reglas detectoras de anti-patrones de diseño genérico ("AI slop"), complementario a `frontend-design-direction`. Instalación: `npx impeccable install`.

- **find-skill** ([github.com/fockus/claude-skill-find-skill](https://github.com/fockus/claude-skill-find-skill)) — ❌ no encontrado en `D:\cc`, instalar desde el repo. Busca e instala skills entre 4800+ de 14 fuentes distintas — es literalmente la herramienta que automatiza el paso 3 de "Resolución de skills" de arriba. **Recomendado instalar primero**, porque una vez activo simplifica el resto de instalaciones de esta lista. Instalación: `curl -sSL https://raw.githubusercontent.com/fockus/claude-skill-find-skill/main/quick-install.sh | bash` (o `pipx install find-skill`). Uso: `/find-skill <búsqueda>`, `/install-skill <fuente>`.

- **task-observer** ("one skill to rule them all", [github.com/rebelytics/one-skill-to-rule-them-all](https://github.com/rebelytics/one-skill-to-rule-them-all)) — ❌ no encontrado en `D:\cc`. Meta-skill que observa las sesiones de trabajo, detecta patrones repetidos y propone mejoras a skills existentes (incluida, potencialmente, `addv-web-app`) para revisión y aprobación del usuario. No tiene instalador estándar: se instala dando el contenido del repo (skill file, README, guía) al agente para que lo configure según el entorno.

- **ruflo** ([github.com/ruvnet/ruflo](https://github.com/ruvnet/ruflo)) — ❌ no encontrado en `D:\cc`. Orquestador multi-agente para Claude Code: coordina swarms de +100 agentes especializados (codear, testear, auditar seguridad, documentar, decidir arquitectura), con memoria vectorial y workers en segundo plano. Es una herramienta pesada — úsala solo si el proyecto realmente necesita coordinar múltiples agentes en paralelo, no por defecto en cambios simples. Instalación (Claude Code, ligera): `/plugin install ruflo-core@ruflo`. Instalación completa (CLI): `npx ruflo@latest init wizard`.

- **prompt-master** ([github.com/nidhinjs/prompt-master](https://github.com/nidhinjs/prompt-master)) — ❌ no encontrado en `D:\cc`. Skill que ayuda a redactar prompts optimizados para cualquier herramienta de IA — extrae la intención, hace máximo 3 preguntas aclaratorias y aplica una plantilla según el caso. Útil para pulir el propio prompt de una petición antes de que este protocolo la procese, no para el desarrollo del sitio en sí. Instalación: clonar en la carpeta de skills — `git clone https://github.com/nidhinjs/prompt-master.git ~/.claude/skills/prompt-master` — o subir el zip vía Claude.ai → Customize → Skills → Upload.

Antes de correr cualquier instalador de terceros, revísalo tú mismo (o pide que se revise) — son proyectos no auditados por Anthropic, y este protocolo exige prácticas anti-hackeo; instalar algo a ciegas contradice esa misma regla.

## Docker siempre

Todo el desarrollo corre en Docker y Docker Compose — no propongas ni construyas nada que dependa de un entorno local no contenedorizado. El `README.md` debe explicar, paso a paso, cómo levantar el proyecto completo desde cero: requisitos previos, variables de entorno necesarias, comandos exactos (`docker compose up`, migraciones, seed, etc.) y cómo verificar que el servicio quedó arriba correctamente (por ejemplo, un healthcheck o una URL de prueba).

## Entregables obligatorios de documentación

Mantén siempre actualizados estos cuatro archivos:

- **`addv/project_state.md`** — estado actual del proyecto: qué existe, qué está en progreso, qué falta, decisiones ya tomadas y decisiones pendientes de confirmar con el usuario. Actualízalo al cerrar cada segmento implementado, no solo al final del proyecto.
- **`addv/CLAUDE.md`** — contexto operativo persistente del repo: arquitectura, convenciones de código, comandos frecuentes (build, test, lint, levantar Docker) y las reglas de este protocolo, para que cualquier sesión futura de Claude en este proyecto las siga sin que se le tengan que repetir.
- **`addv/cmem.md`** — registro de la conversación del proyecto, comprimido, al estilo de la herramienta `claude-mem` (ver sección de eficiencia): no es una transcripción literal, es un resumen optimizado de cada segmento de trabajo — qué se pidió, qué se discutió/criticó (paso 3 del flujo), qué se decidió, qué se implementó y qué quedó pendiente. Se actualiza al cerrar cada segmento del flujo obligatorio (mismo momento en que se actualiza `project_state.md`), agregando una entrada nueva con fecha, nunca reescribiendo el historial previo. Su función es que una sesión futura de Claude pueda leerlo y recuperar contexto sin tener que rehacer las mismas preguntas ni reconstruir decisiones ya tomadas.
- **`README.md`** — instrucciones de instalación y ejecución paso a paso con Docker/Docker Compose, escritas para alguien que nunca vio el proyecto. **Este es el único de los cuatro que va en la raíz del proyecto**, no dentro de `addv/` — moverlo rompería el renderizado automático de GitHub/GitLab/GitHub Pages en la portada del repo, que solo detecta el README si está en la raíz.

### Carpeta `addv/`

`project_state.md`, `CLAUDE.md` y `cmem.md` viven siempre dentro de una carpeta `addv/` en la raíz del proyecto (`addv/project_state.md`, `addv/CLAUDE.md`, `addv/cmem.md`) — no sueltos en la raíz. El motivo es evitar que información interna del protocolo (decisiones de negocio, historial de conversación, contexto operativo) quede expuesta al mismo nivel que el código y el `README.md` público. `README.md` es la única excepción y se queda en la raíz (ver arriba).

**Migración en un proyecto ya iniciado sin esta skill:** si al adoptar `addv-web-app` en un proyecto existente encuentras `project_state.md`, `CLAUDE.md` o `cmem.md` sueltos en la raíz (o en cualquier otra ubicación fuera de `addv/`), no los muevas de inmediato. Documenta explícitamente en el análisis del paso 1 del flujo que existen y que se moverán a `addv/` para que no queden expuestos, y trata ese movimiento como un cambio más: pasa por crítica (paso 3, si aplica) y por confirmación explícita (paso 6) antes de ejecutarlo, igual que cualquier otro cambio de este protocolo.

## Resumen del contrato

- No asumas: pregunta ante cualquier ambigüedad genuina, incluida la función de herramientas o skills que no reconozcas.
- No implementes lo pedido sin antes criticarlo y ofrecer una versión mejorada si la hay.
- No apliques cambios visuales sin haber mostrado antes/después y sin confirmación explícita del segmento correspondiente.
- No des un cambio por terminado sin pruebas unitarias y funcionales, y sin revisar que no exponga datos sensibles en el frontend ni viajen/almacenen sin cifrar.
- No entregues nada que no cumpla UX/UI, accesibilidad, rendimiento, seguridad, Docker y calidad nivel SonarQube.
- No rompas funcionalidad existente sin decirlo explícitamente.
- Documenta siempre en `addv/project_state.md`, `addv/CLAUDE.md`, `addv/cmem.md` y `README.md` (este último en la raíz, no en `addv/`).
- Si el proyecto ya existía sin esta skill y esos archivos están sueltos, documenta que se moverán a `addv/` antes de moverlos, y espera confirmación.
