---
name: addv-web-app
description: Aplica el protocolo corporativo addv-web-app para diseñar, construir, revisar o desplegar sitios y aplicaciones web (frontend, backend, UX/UI, Docker). Impone un flujo estricto de analizar-antes-de-implementar con confirmación explícita del usuario, prohíbe asumir requisitos ambiguos, y exige mejores prácticas de UX/UI, accesibilidad, rendimiento (Core Web Vitals), seguridad anti-hackeo, contenedorización segura con Docker/Docker Compose, y calidad de código nivel auditoría SonarQube. Usa esta skill siempre que el usuario mencione "addv-web-app", pida crear, modificar, auditar o desplegar un sitio o app web corporativa/reputacional, pida ajustes de UI/UX/accesibilidad/performance/seguridad en un proyecto web, trabaje con Docker o docker-compose para un servicio web, o pida generar/actualizar project_state.md o el README.md de un proyecto web.
---

# addv-web-app

Protocolo de trabajo para proyectos de aplicaciones web corporativas donde un error de UX, de seguridad o una regresión tiene costo reputacional real. La skill existe para que cada petición pase primero por análisis y aprobación explícita, y para que cada entrega cumpla un piso de calidad fijo (UX/UI, accesibilidad, rendimiento, seguridad, Docker, calidad de código) sin tener que repetir esas reglas en cada mensaje.

## Flujo obligatorio: Analizar → Proponer → Confirmar → Implementar

Ante cualquier petición dentro de un proyecto bajo este protocolo:

1. **Analiza primero, sin ejecutar nada.** Identifica qué se pide, qué archivos o módulos se ven afectados, qué riesgos existen y qué opciones hay. No escribas ni modifiques archivos del proyecto todavía. Esto evita gastar tokens y tiempo implementando algo que el usuario en realidad no quería.
2. **Responde en segmentos completos y acotados.** Divide el trabajo en bloques que el usuario pueda revisar y aprobar de forma independiente, en vez de intentar resolver todo de una sola vez. Un segmento debe quedar funcionalmente completo, no a medias.
3. **Espera confirmación explícita antes de implementar.** Presentar un plan, un diagnóstico, una alternativa o un fragmento de código de ejemplo es libre; escribir/modificar archivos reales del proyecto, ejecutar comandos que cambien estado, o hacer commits no lo es. Solo procede a implementar cuando el usuario dé una instrucción explícita de tipo "impleméntalo", "procede", "hazlo" o equivalente. Si el usuario ya dio luz verde a un plan completo con anterioridad, no vuelvas a pedir permiso para cada paso trivial de ese mismo plan — pero sí detente si aparece una decisión nueva no cubierta por esa aprobación.
4. **Con luz verde, implementa el segmento aprobado completo** y confirma cuando esté listo, antes de avanzar al siguiente segmento.

## Regla anti-suposición

No asumas requisitos, alcances, nombres, valores o decisiones de negocio que no te hayan dado explícitamente. Ante cualquier ambigüedad, pregunta antes de continuar — es preferible una pregunta puntual a construir sobre un supuesto incorrecto en un sitio de producción.

**Excepción — no te detengas de inmediato:** si resolver la petición se complica por una limitación técnica, un conflicto de dependencias o una decisión de arquitectura no trivial, antes de preguntar o detenerte, investiga y propón **al menos 3 alternativas viables** con sus ventajas y desventajas (por ejemplo: impacto en performance, complejidad de mantenimiento, riesgo de seguridad, costo de migración). Pregunta solo si, tras evaluar esas alternativas, ninguna es razonable sin una decisión del usuario.

## Estándares de calidad — piso no negociable

Toda entrega de este protocolo debe cumplir:

- **UX/UI**: usabilidad, jerarquía visual clara, accesibilidad y diseño responsivo en todos los breakpoints relevantes (no solo desktop).
- **Seguridad de contenedores**: imágenes base mínimas, usuario no-root dentro del contenedor, secretos fuera de las imágenes (variables de entorno o secret managers, nunca hardcodeados), y atención a vulnerabilidades conocidas de las dependencias/imágenes usadas.
- **Anti-hackeo general**: validación y sanitización de toda entrada de usuario, cabeceras de seguridad HTTP correctas, mitigación de los riesgos del OWASP Top 10 relevantes al stack, y rate limiting donde el endpoint lo amerite. El objetivo es que el servicio no pueda ser interrumpido ni se pueda filtrar información desde él.
- **Rendimiento**: carga eficiente del sitio; las animaciones deben sentirse fluidas y no degradar Core Web Vitals — animar solo propiedades de bajo costo (`transform`, `opacity`), nunca `top/left/width/height` o propiedades que disparen layout/paint.
- **Calidad de código**: escribe como si el código fuera a pasar una auditoría de SonarQube — sin code smells evidentes, sin vulnerabilidades conocidas, manejo de errores robusto y explícito (no silenciar excepciones), sin duplicación innecesaria.
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

Aplica el orden natural del proyecto: research/critique de UX antes de construir la UI, `docker-containerization` y `backend-robustness-architecture` al tocar servidor o infraestructura, y el checklist de performance/accesibilidad/motion como última pasada antes de dar cualquier entrega visual por terminada.

## Docker siempre

Todo el desarrollo corre en Docker y Docker Compose — no propongas ni construyas nada que dependa de un entorno local no contenedorizado. El `README.md` debe explicar, paso a paso, cómo levantar el proyecto completo desde cero: requisitos previos, variables de entorno necesarias, comandos exactos (`docker compose up`, migraciones, seed, etc.) y cómo verificar que el servicio quedó arriba correctamente (por ejemplo, un healthcheck o una URL de prueba).

## Entregables obligatorios de documentación

Mantén siempre actualizados estos dos archivos en la raíz del proyecto:

- **`project_state.md`** — estado actual del proyecto: qué existe, qué está en progreso, qué falta, decisiones ya tomadas y decisiones pendientes de confirmar con el usuario. Actualízalo al cerrar cada segmento implementado, no solo al final del proyecto.
- **`README.md`** — instrucciones de instalación y ejecución paso a paso con Docker/Docker Compose, escritas para alguien que nunca vio el proyecto.

## Resumen del contrato

- No asumas: pregunta ante cualquier ambigüedad genuina.
- No implementes sin confirmación explícita del segmento correspondiente.
- No entregues nada que no cumpla UX/UI, accesibilidad, rendimiento, seguridad, Docker y calidad nivel SonarQube.
- No rompas funcionalidad existente sin decirlo explícitamente.
- Documenta siempre en `project_state.md` y `README.md`.