---
title: "Ejercicio 1: Instrucciones personalizadas (VS Code)"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← Ejercicio anterior: Requisitos previos][previous-lesson] · [Siguiente ejercicio: Modo agente →][next-lesson]

El contexto es clave cuando se trabaja con inteligencia artificial (IA) generativa. Si una tarea debe hacerse de una forma concreta o Copilot necesita información adicional, conviene asegurarse de que ese contexto esté disponible. Los [archivos de instrucciones][instruction-files] proporcionan directrices para la generación de código.

En este ejercicio:

- explorarás cómo llegan a Copilot el contexto del proyecto, las directrices de programación y los estándares de documentación mediante las instrucciones del repositorio y las instrucciones específicas de rutas.
- generarás la primera parte de los datos para el filtrado (un helper de editoriales) con las instrucciones actuales.
- añadirás un estándar general a `.github/copilot-instructions.md`.
- repetirás el mismo prompt y observarás cómo el código generado sigue el estándar nuevo.
- confirmarás los cambios de instrucciones y del filtrado en `main` para que Copilot pueda usarlos en el ejercicio siguiente.

> [!CAUTION]
> El código generado puede no seguir todos los estándares que hayas indicado. Copilot es probabilístico. El objetivo es observar la tendencia del cambio después de actualizar las instrucciones, no obtener siempre el mismo resultado carácter por carácter.

## Archivos de instrucciones

### Escenario

Tailspin Toys tiene directrices para sus prácticas de desarrollo:

- La capa de datos siempre necesita pruebas unitarias.
- La interfaz debe usar el modo oscuro y tener un aspecto moderno.
- El código debe incluir comentarios de documentación de TypeScript (TSDoc).
- Cada archivo debe empezar con un bloque de comentarios que describa su propósito.

Usarás archivos de instrucciones para proporcionar a Copilot el contexto necesario para aplicar estas prácticas.

### Instrucciones personalizadas

Las instrucciones personalizadas ayudan a Copilot a entender el estilo de programación y los requisitos del proyecto. Puedes especificar convenciones, bibliotecas y comentarios, y aplicar las instrucciones a todo el repositorio o a determinados tipos de archivo.

Hay dos tipos de archivos de instrucciones:

- `.github/copilot-instructions.md`, un único archivo que se envía a Copilot en **todas** las solicitudes del repositorio. Debe contener información general del proyecto, como la pila tecnológica, una descripción del trabajo, buenas prácticas y directrices globales.
- `.github/instructions/*.instructions.md`, archivos para tareas o tipos de archivo concretos, como TypeScript, Astro, componentes de interfaz o pruebas unitarias.

> [!NOTE]
> En un entorno de desarrollo integrado (IDE), los archivos de instrucciones se usan para generar código en Copilot Chat, pero no para las finalizaciones de código ni las sugerencias de siguiente edición.
>
> Copilot Chat, Copilot CLI y el agente en la nube de Copilot usan las instrucciones del repositorio y los archivos `*.instructions.md` que incluyen el frontmatter `applyTo`.
>
> Copilot también admite formatos como AGENTS.md y CLAUDE.md. Consulta la [referencia de compatibilidad][custom-instructions-support].

### Prácticas recomendadas para gestionar archivos de instrucciones

Los ejemplos del proyecto muestran un enfoque posible:

- Mantén `copilot-instructions.md` centrado en las directrices generales, la estructura del proyecto y los estándares comunes.
- Usa `*.instructions.md` para tipos de archivo como pruebas unitarias, componentes Astro y la capa de datos, o para tareas concretas.
- Escribe directrices claras en lenguaje natural e incluye ejemplos de cómo debería ser el código y de cómo no debería ser.

No existe una única forma correcta de crear instrucciones. Experimenta para encontrar el enfoque adecuado para el proyecto.

> [!TIP]
> Los archivos de instrucciones del proyecto incluyen directrices para tareas como [actualizar la interfaz][ui-instructions] y trabajar con [Astro][astro-instructions]. Copilot también puede ayudar a crear estos archivos: por ejemplo, VS Code incluye **Configure Chat → Generate Agent Instructions** y Copilot CLI incluye `/init`. Para obtener plantillas, consulta [awesome-copilot][awesome-copilot].

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## Explora los archivos de instrucciones personalizadas del proyecto

Revisa los archivos de instrucciones del repositorio en el editor o en la interfaz web de GitHub.

1. Abre `.github/copilot-instructions.md`.
2. Revisa la descripción breve del proyecto y las secciones **Agent notes**, **Code standards**, **Scripts** y **Repository Structure**. En **Code standards**, fíjate en las directrices anidadas **GitHub Actions Workflows**. Se aplican a todas tus interacciones con Copilot.
3. Abre `.github/instructions` y explora las instrucciones para Astro, la capa de datos de Drizzle, las pruebas y otros ámbitos.
4. Abre `.github/instructions/unit-tests.instructions.md` y revisa `applyTo`, que define el glob de archivos aplicable respecto a la raíz del repositorio. Por ejemplo, coincide con archivos de prueba TypeScript como `**/*.test.ts`.
5. Revisa las directrices para crear pruebas unitarias en este proyecto.
6. Abre `.github/instructions/drizzle.instructions.md` y revisa los enlaces a otros archivos de instrucciones y a archivos existentes. Las rutas son relativas al archivo de instrucciones, no a la raíz del repositorio.

> [!NOTE]
> **Code formatting requirements** en `copilot-instructions.md` describe los estándares del proyecto, pero aún no exige documentación dentro del código. Añadirás reglas para comentarios TSDoc y encabezados de comentario.

## Crea una rama para los cambios

Crea una rama para trabajar en este ejercicio.

1. Vuelve al Codespace y abre un terminal con <kbd>Control</kbd>+<kbd>\`</kbd> (Mac) o <kbd>Ctrl</kbd>+<kbd>\`</kbd> (Windows/Linux).
2. Crea y selecciona una rama nueva:

   ```bash
   git checkout -b custom-instructions
   ```

## Usa Copilot Chat antes de actualizar las instrucciones

Para observar el efecto de las instrucciones personalizadas, empieza con el archivo actual. Más adelante actualizarás las instrucciones y enviarás el mismo prompt.

> [!TIP]
> **Abre Copilot Chat**
>
> Vuelve al Codespace, abre el panel Copilot Chat y selecciona **New Chat** para iniciar una conversación nueva. El modo y el modelo varían según el ejercicio; las instrucciones indican qué seleccionar cuando sea necesario.

1. Cierra las pestañas de editor que no necesites para que Copilot use el contexto adecuado.
2. Abre `src/lib/publishers.ts` para indicar dónde debe estar el helper.
3. En el selector de agentes de Chat, selecciona **Agent** para que Copilot pueda aplicar cambios.

   ![Selector de agente en la vista Chat](../../../_images/shared-chat-mode-selector.png)

4. Envía este prompt:

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

5. Copilot explorará el proyecto y aplicará cambios, posiblemente al helper y a sus pruebas.
6. Observa que el helper propuesto es una función tipada que recibe un cliente `db` como primer argumento y devuelve un array tipado de editoriales. Esta convención procede de `.github/instructions/drizzle.instructions.md`, aplicable a `src/lib/*.ts`.
7. Observa si al código propuesto le faltan comentarios TSDoc y un encabezado de comentario a nivel de archivo.

> [!CAUTION]
> Copilot puede añadir comentarios aunque no se lo pidas. No pasa nada: lo importante es observar si mejora la coherencia después de actualizar las instrucciones.

## Añade un estándar del repositorio

El archivo `.github/copilot-instructions.md` proporciona información general del proyecto. Añade el estándar para mejorar las sugerencias de código.

1. Vuelve a abrir `.github/copilot-instructions.md`.
2. Busca **Code formatting requirements**, cerca de la línea 27. Revisa los estándares existentes y observa que todavía no incluyen documentación en el código.
3. Añade estas líneas Markdown debajo de los estándares existentes:

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. Guarda `copilot-instructions.md`.

> [!TIP]
> Usa `.github/copilot-instructions.md` para estándares generales y `*.instructions.md` para lenguajes, tipos de archivo o tareas concretos.

## Repite el prompt y observa el cambio

Envía el mismo prompt en una conversación limpia para que Copilot aplique las instrucciones actualizadas.

1. En Copilot Chat, selecciona **New Chat**.
2. Selecciona de nuevo `src/lib/publishers.ts` para que Copilot se centre en el archivo.
3. Envía el mismo prompt:

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

4. Comprueba si el archivo propuesto empieza con un bloque de comentarios parecido a este:

   ```typescript
   /**
    * Helpers de acceso a datos de editoriales para la plataforma de financiación colectiva de Tailspin Toys.
    * Proporciona funciones para recuperar información de editoriales de la base de datos.
    */
   ```

5. Comprueba si la función incluye un comentario TSDoc parecido a este:

   ```typescript
   /**
    * Devuelve una lista de todas las editoriales con su id y nombre.
    *
    * @param db - Cliente de base de datos de Drizzle.
    * @returns Promesa que se resuelve en un array de objetos de editoriales.
    */
   ```

Copilot ya sigue un estándar nuevo del proyecto en código que servirá de base para el ejercicio siguiente.

## Confirma las instrucciones y envía la rama

Confirma las instrucciones y todos los cambios que Copilot haya hecho para el helper.

1. Abre un terminal con <kbd>Control</kbd>+<kbd>\`</kbd> (Mac) o <kbd>Ctrl</kbd>+<kbd>\`</kbd> (Windows/Linux).
2. Comprueba los cambios:

   ```bash
   git status
   ```

3. Añade y confirma las instrucciones y los archivos modificados para el helper:

   ```bash
   git add .github/copilot-instructions.md src/lib/publishers.ts
   # Si git status muestra otros cambios relacionados, como pruebas, añade también esos archivos.
   git commit -m "Add doc comment standards and publishers helper foundation"
   ```

4. Envía la rama:

   ```bash
   git push -u origin custom-instructions
   ```

## Crea y fusiona una solicitud de incorporación de cambios

Abre una solicitud de incorporación de cambios (PR) desde la rama publicada y relaciónala con la incidencia sobre la actualización de las instrucciones.

1. Abre Copilot Chat en el Codespace, selecciona **New Chat** y confirma que está seleccionado **Agent**.
2. Pulsa <kbd>Control</kbd>+<kbd>Command</kbd>+<kbd>I</kbd> (Mac) o <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>I</kbd> (Windows/Linux) para abrir Copilot Chat.
3. Envía este prompt:

   ```plaintext
   Find the issue related to updating the instructions file. Create a new PR from the current branch, highlight that the PR closes that issue, and include that we also added the publishers helper foundation for the upcoming filtering work.
   ```

4. Cuando Copilot pida permiso para usar herramientas de GitHub, revisa la acción y selecciona **Allow** si estás de acuerdo.
5. Cuando cree el PR, pídele que lo fusione y actualice `main` local:

   ```plaintext
   Merge the PR into main. Then return to main locally, and pull the latest code so we are up to date.
   ```

6. Revisa y autoriza las acciones de GitHub y los comandos de terminal cuando Copilot lo solicite.

## Resumen y siguientes pasos

Has explorado cómo Copilot obtiene contexto de los archivos de instrucciones y has usado Copilot Chat en VS Code para:

- explorar cómo llegan a Copilot el contexto del proyecto, las directrices de programación y los estándares de documentación.
- enviar un prompt de generación de código y observar el resultado con las instrucciones existentes.
- añadir un estándar general a `.github/copilot-instructions.md`.
- repetir el prompt y observar cómo el código propuesto sigue el estándar nuevo.
- confirmar las instrucciones y el helper en `main` para que el siguiente ejercicio pueda usarlos.

A continuación, aplicarás las instrucciones en el [modo agente][next-lesson] mientras Copilot añade una función al proyecto.

## Recursos

- [Archivos de instrucciones para personalizar GitHub Copilot][instruction-files]
- [Prácticas recomendadas para crear instrucciones personalizadas][instructions-best-practices]
- [5 consejos para escribir mejores instrucciones personalizadas][copilot-instructions-five-tips]
- [Instrucciones personalizadas personales para GitHub Copilot][personal-instructions]
- [Awesome Copilot: colección de archivos de instrucciones y otros recursos][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-agent-mode/
[instruction-files]: https://code.visualstudio.com/docs/copilot/copilot-customization
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[personal-instructions]: https://docs.github.com/copilot/customizing-copilot/adding-personal-custom-instructions-for-github-copilot
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
