---
title: "Ejercicio 5: Iterar sobre el trabajo de GitHub Copilot"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← Ejercicio anterior: Gestionar agentes][previous-lesson] |
|:--|

## Revisa el trabajo

Durante este taller has pedido a GitHub Copilot que documente el código, añada una sección de juegos relacionados y cree funciones de accesibilidad con modos de alto contraste y claro. Revisa los cambios y, si hace falta, pide al agente que los mejore antes de activar la revisión del equipo en la solicitud de incorporación de cambios (PR).

### Escenario

Los fundamentos del diseño de software y DevOps no cambian al añadir inteligencia artificial (IA) generativa. Revisa el código generado y sigue el proceso habitual antes de aprobar los cambios de Copilot.

## Seguridad y agente en la nube de GitHub Copilot

El agente en la nube trabaja de forma asíncrona y sin supervisión directa. Para mantener la seguridad:

- Copilot solo tiene acceso de lectura al repositorio y acceso de escritura **únicamente** a la rama en la que trabaja.
- El agente se ejecuta en GitHub Actions, en un entorno efímero independiente.
- Una persona debe aprobar los flujos de trabajo de GitHub Actions antes de que se ejecuten.
- De forma predeterminada, el acceso a recursos externos, incluidos los servidores MCP, está limitado. Consulta cómo [configurar el firewall del agente][agent-firewall].

## Revisa la documentación generada

Empieza por el primer PR del agente, que añade documentación al código. Usa la interfaz habitual de PR de GitHub.com.

> [!NOTE]
> Es posible que el PR muestre una advertencia de que el firewall bloquea el acceso de GitHub Copilot. Es el comportamiento previsto: el acceso a recursos externos está limitado de forma predeterminada. Si quieres, puedes [configurar o deshabilitar el firewall][agent-firewall].

1. Abre el repositorio en github.com y selecciona la pestaña **Pull Requests**.
2. Abre el PR titulado, por ejemplo, **Add missing documentation**.

   Si el título aún contiene **[WIP]**, espera a que Copilot termine.

3. Selecciona la pestaña **Files changed** y revisa los cambios.

   ![Pestaña Files changed](../../../_images/shared-pr-files-changed.png)

4. Comprueba los comentarios TSDoc, las convenciones de TypeScript y los encabezados de comentario. Estas directrices proceden de los archivos de instrucciones que revisaste al principio del ejercicio 2.
5. Cuando hayas revisado los cambios, vuelve a **Conversation** y desplázate hacia abajo.
6. Si hay flujos de trabajo pendientes de aprobación, selecciona **Approve and run workflows**.

   ![Aprobar y ejecutar flujos de trabajo](../../../_images/shared-approve-workflows.png)

7. Espera a que se ejecuten las comprobaciones del proyecto para la aplicación Astro. El proceso puede tardar varios minutos.

### Pide cambios a GitHub Copilot

Puedes mencionar a Copilot en comentarios del PR o en comentarios insertados en el código para pedir otra sesión y solicitar cambios.

1. Añade un comentario y menciona a **@copilot**. Puedes pedir encabezados de comentario en los archivos, comentarios TSDoc en TypeScript y Astro o un README que describa la estructura de la aplicación Astro.
2. Selecciona **View Session** para seguir la nueva sesión.
3. Cuando Copilot termine, revisa el nuevo commit en la pestaña **Files changed**.
4. Itera hasta que el resultado te satisfaga. Después, puedes marcar el PR como listo para revisión y fusionarlo.

   ![Marcar el PR como listo para revisión](../../../_images/ex7-ready-for-review.png)

## Revisa la función de juegos relacionados

Revisa el PR que resuelve la incidencia para mostrar juegos relacionados en la página de detalles.

1. Abre la pestaña **Pull Requests** del repositorio y selecciona el PR cuyo título sea parecido a **Show related games on the game details page**.
2. Selecciona **Files changed** y revisa el código generado.
3. Vuelve a **Conversation**. Si hay flujos de trabajo pendientes de aprobación, selecciona **Approve and run workflows**.
4. Espera a que finalicen correctamente las comprobaciones de la aplicación Astro.
5. **Opcional:** Para probar la función manualmente, abre el terminal del Codespace y sustituye `<branch-name>` por la rama creada por Copilot, por ejemplo, `copilot/fix-8`:

   ```bash
   git fetch origin
   git checkout <branch-name>
   ```

Puedes pedir más cambios a Copilot mediante un comentario en **Conversation**, como ajustar el número de juegos relacionados o añadir encabezados y comentarios TSDoc. Recuerda que esta tarea se asignó antes de actualizar las instrucciones personalizadas.

## Revisa las funciones de accesibilidad

El último PR debe incluir el modo de alto contraste que asignaste en el ejercicio 3 y el modo claro que solicitaste desde mission control en el ejercicio 4.

1. En la pestaña **Pull Requests**, abre el PR titulado, por ejemplo, **Add high contrast mode to website**. Si aparece **[WIP]**, espera a que Copilot termine.
2. Selecciona **Files changed** y revisa la implementación. Comprueba los controles para cambiar de modo, el almacenamiento local de las preferencias, los estilos, los atributos de accesibilidad (como las etiquetas ARIA y la navegación con teclado) y el código que gestiona los modos.
3. Vuelve a **Conversation** y, si hay flujos pendientes, selecciona **Approve and run workflows**.
4. Espera a que las comprobaciones de la aplicación Astro terminen correctamente.
5. **Opcional:** Cambia al branch de Copilot desde el terminal del Codespace, iniciando con `git fetch origin` y `git checkout <branch-name>`. Inicia la aplicación y prueba que los modos de alto contraste y claro funcionan y se mantienen al recargar la página.
6. Si detectas problemas de accesibilidad o mejoras posibles, menciona a **@copilot** en un comentario para solicitar cambios.

## Ejercicio opcional: sigue delegando

El agente en la nube resulta útil para trabajar en incidencias reales del backlog mientras tú te ocupas de otras tareas. Crea nuevas incidencias y asígnaselas a Copilot. Por ejemplo:

- crear un formulario de interés de patrocinadores en la página de detalles del juego.
- implementar la paginación en la página de juegos.
- añadir validación de entradas y gestión de errores a los helpers de acceso a datos.

## Resumen

Has completado el itinerario del agente en la nube. En estos ejercicios:

- revisaste los archivos de instrucciones del repositorio y observaste su efecto en el trabajo del agente.
- asignaste incidencias al agente en la nube y viste cómo configura el entorno, planifica y trabaja de forma asíncrona.
- creaste y usaste un agente personalizado de accesibilidad para añadir los modos de alto contraste y claro.
- usaste la página de agentes de Copilot para supervisar y guiar una sesión activa.
- revisaste e iteraste sobre los PR del agente, solicitaste cambios con `@copilot` y aprobaste flujos de trabajo.

## Revisión y siguientes pasos

Los otros itinerarios complementan lo que has practicado:

- **[Itinerario de VS Code](../../vscode/)**: explora el modo agente de Copilot Chat y la integración MCP desde el IDE.
- **[Itinerario de CLI](../../cli/)**: entrega dos cambios revisados desde el terminal con Plan, Autopilot, habilidades de agente, un agente de QA, Playwright MCP, Agent Merge y la opción `/delegate`.

En tu propio repositorio, puedes asignar al agente una incidencia de refactorización o cobertura de pruebas, crear un agente personalizado para otro ámbito o configurar `copilot-setup-steps.yml` para otra pila tecnológica.

## Recursos

- [GitHub Copilot][github-copilot]
- [Acerca de los agentes de Copilot][copilot-agents]
- [Asignar incidencias de GitHub a Copilot][assign-issue]
- [Prácticas recomendadas para el flujo de configuración del agente en la nube][cloud-agent-best-practices]
- [Configurar el firewall del agente en la nube][agent-firewall]

---

| [← Ejercicio anterior: Gestionar agentes][previous-lesson] |
|:--|

[github-copilot]: https://github.com/features/copilot
[cloud-agent-overview]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[agent-firewall]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall
[previous-lesson]: ../4-managing-agents/
