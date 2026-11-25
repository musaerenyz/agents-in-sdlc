---
title: "Ejercicio 2: Añadir funciones con el modo agente de Copilot"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

Incluso los cambios sencillos suelen requerir modificar varios archivos y ejecutar pruebas. El modo agente de Copilot puede explorar la estructura del proyecto, coordinar cambios entre archivos, ejecutar tareas como las pruebas y corregir errores que detecte. En este ejercicio lo usarás para añadir una función al sitio.

> [!NOTE]
> Aunque los nombres se parecen, el modo agente y el agente en la nube ofrecen experiencias distintas. El modo agente trabaja en el IDE y permite ciclos de respuesta rápidos. El agente en la nube trabaja de forma asíncrona como un miembro del equipo y se comunica contigo mediante incidencias y solicitudes de incorporación de cambios.

En este ejercicio aprenderás cómo el modo agente explora el proyecto, actualiza la interfaz y la capa de datos, y genera cambios y pruebas que debes revisar antes de fusionarlos.

## Escenario

El catálogo de juegos crece y quieres que los usuarios puedan filtrarlo por categoría y editorial. Ya has creado un helper de editoriales en el ejercicio anterior. Ahora completarás la capa de datos, la interfaz y las pruebas con el modo agente de Copilot.

## Ejecuta el sitio web de Tailspin Toys

Antes de hacer cambios, explora el sitio para entender sus funciones actuales.

Tailspin Toys es una plataforma de financiación colectiva de juegos de mesa con temática de desarrollo. Permite mostrar juegos y consultar sus detalles. Es una única aplicación Astro que genera páginas HTML estáticas durante la compilación. Las páginas consultan una base de datos SQLite local mediante Drizzle ORM (mapeador objeto-relacional); no hay una interfaz de programación de aplicaciones (API) de backend independiente ni un framework de interfaz en el cliente. Los helpers reutilizables de acceso a datos están en `src/lib/`, y la interactividad se implementa con scripts Astro acotados que usan las API estándar del modelo de objetos del documento (DOM).

### Inicia el sitio web

Usa la tarea `npm run dev` para iniciar el servidor Astro de desarrollo en tu Codespace.

1. Vuelve a tu Codespace. Después del ejercicio anterior, deberías estar en `main`.
2. Abre un terminal con <kbd>Control</kbd>+<kbd>\`</kbd> (Mac) o <kbd>Ctrl</kbd>+<kbd>\`</kbd> (Windows/Linux).
3. Actualiza la rama y crea una rama para el filtrado:

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. Inicia el sitio:

   ```bash
   npm run dev
   ```

   Cuando el servidor Astro esté listo, aparecerá una URL parecida a `http://localhost:4321`.

> [!NOTE]
> Si aparece un cuadro que te ofrece abrir `http://localhost:4321` en un navegador, ciérralo con la **x**.

5. Mantén pulsada la tecla <kbd>Command</kbd> (Mac) o <kbd>Ctrl</kbd> (Windows/Linux) y selecciona la dirección del servidor Astro en el terminal.

   En Codespaces, el enlace local se redirige a una dirección privada parecida a `https://<your-codespace-name>-4321.app.github.dev/`.

### Explora el sitio web

Con el sitio abierto, explora sus funciones principales:

- **Página principal**: muestra juegos de mesa con títulos, imágenes y descripciones.
- **Página de detalles del juego**: muestra información adicional, como el título, la descripción, la editorial y la categoría.

## Explora el backlog con Copilot

El sitio funciona, pero quieres añadir funciones. El repositorio creado a partir de la plantilla ya contiene un backlog de incidencias.

> [!TIP]
> **Abre Copilot Chat**
>
> Vuelve al Codespace, abre el panel Copilot Chat y selecciona **New Chat** para iniciar una conversación nueva. El modo y el modelo varían según el ejercicio.

1. En el selector de agentes de Chat, selecciona **Agent**.

   ![Selector de agente en la vista Chat](../../../_images/shared-chat-mode-selector.png)

2. En la lista de modelos, selecciona **Claude Sonnet 4.5**.

> [!CAUTION]
> La selección del modelo no expresa una preferencia de los autores. Se incluye porque se usó al preparar el taller y se espera que ayude a mantener una experiencia coherente. Como los modelos de lenguaje grandes (LLM) son probabilísticos, las sugerencias pueden variar; es normal.

> [!NOTE]
> Copilot puede usar un comando MCP diferente, pero debería poder completar la tarea.

3. Envía este prompt para consultar las incidencias del backlog:

   ```plaintext
   Please show me the backlog of items from my GitHub repository. Help me prioritize them based on those which will be most useful to the user.
   ```

4. Selecciona **Continue** para ejecutar el comando que enumera las incidencias.
5. Revisa la lista y la priorización que propone Copilot.

## Revisa los archivos de instrucciones

Antes de pedir al agente que genere código, revisa el archivo de instrucciones de interfaz que dará contexto a su trabajo.

1. En el Codespace, abre `.github/instructions/ui.instructions.md`.
2. Revisa la arquitectura, los principios de diseño de componentes, las indicaciones sobre pruebas y accesibilidad y los enlaces a instrucciones específicas de Astro, Tailwind CSS y Drizzle.

> [!TIP]
> Los archivos de instrucciones pueden enlazar otros archivos de instrucciones y archivos del proyecto. Las rutas son relativas al archivo que contiene el enlace, lo que permite reutilizar contenido y dividir instrucciones complejas.

## Implementa el filtrado

Completar el filtrado requiere actualizar la lógica de la capa de datos (`src/lib/`), las pruebas y la página de listado de juegos. Copilot Agent Mode puede coordinar estos cambios.

1. Continúa la conversación actual o selecciona **New Chat**.
2. Selecciona **Add Context**, **Instructions** y **ui** para añadir el archivo de instrucciones.

   ![Ejemplo de selección de las instrucciones de interfaz](../../../_images/ex3-select-instructions-file.png)

3. Confirma que **Agent** sigue seleccionado.
4. Confirma que **Claude Sonnet 4.5** sigue seleccionado.
5. Envía este prompt para que Copilot implemente la función según la incidencia del backlog:

   ```plaintext
   Please update the site to include filtering by publisher and category based on the requirements from the related GitHub issue in the backlog. A publishers helper already exists from the previous exercise, so preserve and refine that work as needed while completing the remaining data-layer, UI, and tests. Ensure all tests are passing before completion. The server is already running, so you do not need to start it up.
   ```

6. Observa cómo Copilot explora el proyecto, localiza los helpers de datos, la interfaz y las pruebas, modifica los archivos y ejecuta las pruebas.

   ![Copilot explora los archivos del proyecto](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> El proceso puede tardar varios minutos. Mientras Copilot trabaja, pueden aparecer **Keep** y **Undo** en el editor. No tienes que seleccionarlos hasta que Copilot termine.

7. Cuando Copilot lo solicite, selecciona **Continue** para ejecutar las pruebas.

   ![Aviso de Copilot Chat que pide permiso para ejecutar pruebas](../../../_images/ex3-agent-mode-run-tests.png)

8. Es normal que haya pausas o que algunas pruebas fallen durante el proceso. Copilot puede alternar entre generar código y ejecutar pruebas hasta completar la tarea.
9. Revisa el código generado para detectar posibles problemas.

   ![Sesión completa de Copilot Agent Mode](../../../_images/ex3-agent-mode-proposed-changes.png)

> [!CAUTION]
> Revisa siempre el código generado por Copilot y otras herramientas de IA.

10. Vuelve al navegador con el sitio en ejecución y explora la nueva función.
11. Cuando hayas revisado el código y confirmado que funciona, selecciona **Keep** en Copilot Chat.

## Resumen y siguientes pasos

Has usado el modo agente de GitHub Copilot para añadir funciones al sitio de Tailspin Toys y has aprendido cómo explorar el proyecto, coordinar cambios en la interfaz y la capa de datos, y revisar las pruebas generadas.

A continuación, [probarás la función con el servidor MCP de Playwright][next-lesson] y abrirás una solicitud de incorporación de cambios (PR).

### Ejercicio de exploración adicional: implementar paginación

El catálogo necesitará paginación a medida que crezca. Usa las habilidades de este ejercicio para pedir a Copilot que la implemente.

- Sigue las prácticas recomendadas y usa los archivos de instrucciones existentes.
- Decide si el usuario podrá elegir el tamaño de página o si será fijo.
- Incluye en el prompt las directrices necesarias.
- Itera y proporciona contexto adicional cuando Copilot necesite ajustes.

## Recursos

- [Copilot: modos Ask, Edit y Agent, y cuándo usarlos][choose-mode]
- [Modo agente en VS Code][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
