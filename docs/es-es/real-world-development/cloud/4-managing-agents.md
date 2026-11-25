---
title: "Ejercicio 4: Supervisar y gestionar agentes"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

En los ejercicios anteriores has asignado tres tareas al agente en la nube de Copilot para mejorar la experiencia y añadir funciones. Aunque el agente trabaja de forma asíncrona y autónoma, es importante poder supervisar estas tareas.

Puedes gestionar las tareas desde la [página de agentes][agents-page] de GitHub.com. Este centro de control muestra las tareas con solicitudes de incorporación de cambios (PR) abiertas, permite consultar las operaciones realizadas y ofrece la posibilidad de guiar una sesión activa.

En este ejercicio:

- explorarás la página de agentes para supervisar las tareas.
- guiarás una sesión activa para pedir una función adicional.

## Escenario

Después de pedir al agente que implemente un modo de alto contraste, el equipo decidió añadir también un modo claro. Como ya se están actualizando los estilos y añadiendo controles de modo, pedirás al agente que incluya el modo claro además del modo de alto contraste.

## Revisa las tareas del agente en la nube

Consulta el estado de las tareas asignadas al agente.

1. Abre la [página de agentes de Copilot](https://github.com/copilot/agents).
2. Revisa las tareas que aparecen en los paneles principal e izquierdo. Deberías ver las tareas que asignaste, entre ellas la actualización de la documentación, la generación de API para modificar juegos y el modo de alto contraste.
3. Selecciona una tarea activa y revisa las operaciones que ha realizado Copilot, como descargar el repositorio, preparar el entorno, configurar servidores de Model Context Protocol (MCP) y completar el trabajo.

> [!NOTE]
> Los pasos exactos varían según el estado del trabajo de Copilot y el enfoque que haya seguido.

4. Revisa también el panel de PR de la derecha para supervisar la solicitud y los archivos modificados.

## Guía al agente en la nube

Pide a Copilot que añada el modo claro mientras trabaja en el modo de alto contraste.

1. Selecciona la sesión relacionada con el modo de alto contraste. El título puede variar según el nombre que Copilot haya asignado y el estado del trabajo.

   ![Sesión de accesibilidad en mission control](../../../_images/ex6-accessibility-session.png)

2. Espera unos minutos hasta que la sesión indique que Copilot ha terminado de configurar el entorno y ha empezado a trabajar.
3. En el cuadro **Steer active session while Copilot is working**, escribe este prompt:

   ```plaintext
   While we are working on a high contrast mode, let's also add a light mode. There should be a switch for this mode as well where users can select their desired display mode.
   ```

   ![Tarea del agente en la nube con el cuadro para guiar la sesión activa resaltado](../../../_images/ex6-steer-cloud-agent-task.png)

4. Pulsa <kbd>Enter</kbd> para enviar el prompt.
5. Comprueba que Copilot confirma la solicitud y la incorpora al trabajo.

## Deja que Copilot trabaje

Copilot incorporará la nueva solicitud al terminar el paso en curso. El proceso puede tardar varios minutos; puedes aprovechar ese tiempo para repasar lo que has aprendido.

## Resumen y siguientes pasos

Has explorado la página de agentes de Copilot, el centro de control para supervisar y guiar las tareas del agente en la nube. Puedes seguir las tareas activas y completadas, revisar el trabajo y cambiar el alcance de una sesión en curso.

En este ejercicio:

- exploraste mission control y la página de agentes para supervisar el trabajo del agente en la nube.
- guiaste una sesión activa para solicitar una función adicional.

Cuando Copilot termine las funciones de accesibilidad, [iterarás sobre los PR que ha creado][next-lesson].

## Recursos

- [Página de agentes de Copilot][agents-page]
- [Acerca de los agentes personalizados][custom-agents]

---

| [← Ejercicio anterior: Agentes personalizados][previous-lesson] | [Siguiente ejercicio: Iterar sobre el trabajo de Copilot →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
