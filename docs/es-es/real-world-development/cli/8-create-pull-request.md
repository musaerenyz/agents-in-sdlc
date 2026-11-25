---
title: "Lección 8: Crear y fusionar la solicitud de incorporación de cambios"
description: "Revisa el filtrado, las instrucciones, la habilidad actualizada, el perfil de control de calidad y las pruebas; después, crea una solicitud de incorporación de cambios y usa Agent Merge."
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

La implementación del filtrado, las actualizaciones de instrucciones, la habilidad actualizada, el perfil de control de calidad (QA) y las pruebas están guardados en una rama. Ahora revísalos en conjunto y abre una solicitud de incorporación de cambios (PR). Fusionaste manualmente el PR de las valoraciones; esta vez dejarás que **Agent Merge** gestione el proceso.

> [!NOTE]
> En condiciones normales, separaríamos la funcionalidad, las instrucciones, la habilidad y el agente de QA en varios PR. Para agilizar el taller, has mantenido todo el flujo de filtrado y calidad en una conversación y una rama, y lo incluirás en un único PR.

En esta lección:

- aprenderás qué es Agent Merge y cómo automatiza el ciclo de vida de una fusión.
- inspeccionarás la funcionalidad completa y las pruebas de verificación.
- crearás el PR de filtrado.
- habilitarás Agent Merge solo después de revisar el cambio y confirmarás que el PR se ha fusionado.

## Escenario

Durante el flujo de filtrado, has usado Copilot para planificar, implementar y verificar una función. Tailspin Toys quiere automatizar el trabajo restante del PR y mantener el control del desarrollador sobre la autorización de la fusión.

## Introducción a Agent Merge

**Agent Merge** automatiza el trabajo restante para incorporar un PR. Cuando lo habilitas, Copilot resuelve los bloqueos del PR —por ejemplo, corrige comprobaciones de integración continua (CI) fallidas, responde a comentarios de revisión y actualiza la rama mediante rebase— y después habilita la fusión automática de GitHub si la configuración del repositorio lo permite.

Hasta ahora has seleccionado **Merge pull request** manualmente. Agent Merge puede encargarse de esa tarea. Revisa el trabajo y confirma que está listo antes de habilitarlo.

## Usa Agent Merge para gestionar el PR

Revisa el código, crea el PR y deja que Agent Merge gestione el resto del proceso.

1. Vuelve a tu Codespace.
2. Escribe `/agent` para abrir el cuadro del agente.
3. Selecciona **Default** en la lista y pulsa <kbd>Enter</kbd>.
4. Crea un PR nuevo con `/pr create`.
5. Activa Agent Merge con `/pr agentmerge`.
6. Copilot supervisará el proceso de integración continua del PR. Cuando todas las comprobaciones se completen correctamente, realizará la fusión.
7. Confirma que Copilot muestra un mensaje parecido a «PR #14 was squash-merged successfully».

> [!IMPORTANT]
> Agent Merge no omite las aprobaciones obligatorias, la protección de ramas, las colas de fusión, la configuración del repositorio ni los permisos que falten. Si el proceso se bloquea, revisa el motivo indicado y completa manualmente la fusión revisada cuando el repositorio lo permita.

## Resumen y siguientes pasos

Has automatizado varias partes del proceso de desarrollo: la generación de código, las pruebas y la validación, y ahora el flujo del PR. En esta lección:

- aprendiste qué es Agent Merge y cómo automatiza el ciclo de vida de una fusión.
- inspeccionaste la funcionalidad completa y las pruebas de verificación.
- creaste el PR de filtrado.
- habilitaste Agent Merge después de revisar el cambio y confirmaste que el PR se ha fusionado.

A continuación, [explorarás comandos de barra útiles de Copilot CLI][next-lesson] para gestionar el contexto, los modelos, el uso compartido y la delegación opcional a cloud agent.

## Recursos

- [Gestionar solicitudes de incorporación de cambios con Copilot CLI][manage-prs]
- [Referencia de comandos de Copilot CLI][cli-reference]

[previous-lesson]: ../7-qa-agent/
[next-lesson]: ../9-cli-power-tools/
[manage-prs]: https://docs.github.com/copilot/how-tos/copilot-cli/use-copilot-cli/manage-pull-requests
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
