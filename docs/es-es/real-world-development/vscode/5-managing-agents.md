---
title: "Ejercicio 5: Supervisar y gestionar agentes"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← Ejercicio anterior: Agentes personalizados][previous-lesson] |
|:--|

En el modo agente, Copilot explora el código, propone cambios, edita archivos y ejecuta comandos. Como trabaja en tu máquina, Copilot Chat en VS Code muestra cada llamada a herramientas y cada edición en tiempo real. Puedes revisar cada diff en el editor, aceptar o rechazar los cambios y guiar la conversación con prompts de seguimiento antes de publicar la solicitud de incorporación de cambios (PR).

En este ejercicio:

- supervisarás en tiempo real el trabajo del agente de accesibilidad.
- revisarás y aceptarás los cambios propuestos en los archivos.
- guiarás al agente durante la sesión para añadir un modo claro junto al modo de alto contraste.

## Escenario

El agente personalizado de accesibilidad del [ejercicio 4][previous-lesson] está implementando el modo de alto contraste. Mientras trabaja, el equipo de diseño solicita añadir también un modo claro para las personas a quienes les resulte demasiado oscuro el tema predeterminado. Guiarás la sesión existente para ampliar el trabajo sin empezar de cero.

## Supervisa el progreso de Copilot

La vista Copilot Chat muestra los pasos del agente: archivos que explora, comandos que ejecuta y cambios que propone. Las ediciones aparecen en el chat con un diff y los controles **Keep** y **Undo**.

1. Vuelve a la vista Copilot Chat del ejercicio anterior.
2. Mientras trabaja el agente, desplázate por el chat para ver las llamadas a herramientas, las lecturas y ediciones de archivos y los comandos de terminal.
3. Cuando aparezca un diff, selecciona **Keep** para aceptar el cambio o **Undo** para revertirlo y pedir otro intento.
4. Abre el indicador **Files changed** de la parte superior del chat para revisar el conjunto de archivos modificados.

> [!NOTE]
> Si una llamada a una herramienta necesita permiso —por ejemplo, para ejecutar un comando de terminal—, el agente se detendrá y mostrará **Allow**. Autoriza solo los comandos que consideres seguros.

5. Espera a que termine la implementación del modo de alto contraste. El agente mostrará un mensaje final con un resumen de los cambios.

## Guía al agente para añadir el modo claro

Amplía la misma conversación con el control del modo claro. El agente conservará el contexto de los componentes, los estilos y el uso de almacenamiento local que ha creado.

1. Envía este prompt de seguimiento en Copilot Chat:

   ```plaintext
   Nice work! Now add a light-mode toggle alongside the high-contrast toggle. Light mode should follow the same pattern — persist via local storage and be accessible from the same area of the UI. The user should be able to enable high-contrast and light mode independently.
   ```

2. Revisa las nuevas ediciones que propone el agente y selecciona **Keep** o **Undo** en cada diff.
3. Si el agente sigue un enfoque que no quieres —por ejemplo, refactoriza los controles de una forma inadecuada—, deténlo con el botón **Stop** junto al cuadro de chat y envía una aclaración.

> [!TIP]
> Guiar una sesión activa suele ser más rápido que empezar de nuevo. Puedes pedir ajustes, corregir el enfoque o ampliar el alcance; por ejemplo, simplificar el CSS, integrar el modo claro en el panel existente o añadir atajos de teclado.

## Revisa el conjunto de cambios

Antes de confirmar el trabajo, revisa todos los archivos modificados por el agente.

1. Abre la vista **Source Control** de VS Code.
2. Revisa la lista de archivos. Deberías ver cambios en componentes Astro, estilos y pruebas relacionadas.
3. Abre algunos archivos y revisa los diffs. Comprueba los atributos ARIA, la navegación con teclado, el HTML semántico y la persistencia mediante almacenamiento local.
4. Cuando estés conforme, prepara y confirma los cambios desde el panel **Source Control**. Publicarás la rama en el siguiente ejercicio.

## Resumen y siguientes pasos

Has usado el modo agente de Copilot Chat para supervisar y guiar una sesión activa. Revisaste cada edición, aceptaste o rechazaste cambios y enviaste prompts de seguimiento para ampliar el trabajo sin perder el contexto.

En este ejercicio:

- supervisaste el trabajo del agente en tiempo real.
- aceptaste y rechazaste ediciones individuales.
- guiaste una sesión activa con prompts de seguimiento.

A continuación, publicarás el trabajo de accesibilidad como un PR y revisarás los cambios del agente local en [Iterar][next-lesson].

## Recursos

- [Copilot Chat en VS Code][copilot-chat-vscode]
- [Usar el modo agente][agent-mode]

---

| [← Ejercicio anterior: Agentes personalizados][previous-lesson] | [Siguiente ejercicio: Iterar sobre el trabajo de Copilot →][next-lesson] |
|:--|--:|

[previous-lesson]: ../4-custom-agents/
[next-lesson]: ../6-iterating/
[copilot-chat-vscode]: https://code.visualstudio.com/docs/copilot/chat/copilot-chat
[agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
