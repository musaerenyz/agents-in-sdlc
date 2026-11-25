---
title: "Ejercicio 1: Instrucciones personalizadas (agente en la nube)"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← Ejercicio anterior: Requisitos previos][previous-lesson] · [Siguiente ejercicio: Agente en la nube de Copilot →][next-lesson]

El contexto es clave cuando se trabaja con inteligencia artificial (IA) generativa. Si una tarea debe hacerse de una forma concreta o Copilot necesita información adicional, conviene asegurarse de que ese contexto esté disponible. Los [archivos de instrucciones][instruction-files] proporcionan directrices para que Copilot entienda no solo *qué* debe hacer, sino también *cómo*.

En este ejercicio:

- explorarás cómo llegan a Copilot el contexto del proyecto, las directrices de programación y los estándares de documentación mediante las instrucciones del repositorio y las instrucciones específicas de rutas.
- añadirás un estándar general del repositorio a `.github/copilot-instructions.md`.

> [!NOTE]
> A diferencia de los itinerarios de VS Code y CLI, aquí no compararás prompts antes y después. El agente en la nube trabaja de forma asíncrona con incidencias de GitHub, por lo que el efecto de las instrucciones no se puede mostrar en tiempo real. Lo observarás más adelante al revisar las solicitudes de incorporación de cambios que genere.

## Archivos de instrucciones

### Escenario

Tailspin Toys tiene directrices para sus prácticas de desarrollo:

- La capa de datos siempre necesita pruebas unitarias.
- La interfaz debe usar el modo oscuro y tener un aspecto moderno.
- El código debe incluir comentarios de documentación de TypeScript (TSDoc).
- Cada archivo debe empezar con un bloque de comentarios que describa su propósito.

Los archivos de instrucciones proporcionan a Copilot el contexto necesario para aplicar estas prácticas.

### Instrucciones personalizadas

Las instrucciones personalizadas proporcionan a Copilot contexto y preferencias sobre el estilo de programación y los requisitos del proyecto. Puedes especificar convenciones, bibliotecas y comentarios. Las instrucciones pueden aplicarse a todo el repositorio o a determinados tipos de archivos.

Hay dos tipos de archivos de instrucciones:

- `.github/copilot-instructions.md`, un único archivo que se envía a Copilot en **todas** las solicitudes del repositorio. Debe contener información general del proyecto, como la pila tecnológica, una descripción del trabajo, buenas prácticas y directrices globales.
- `.github/instructions/*.instructions.md`, archivos para tareas o tipos de archivo concretos, como TypeScript, Astro, componentes de interfaz o pruebas unitarias.

> [!NOTE]
> En un IDE, los archivos de instrucciones se usan para generar código en Copilot Chat, pero no para las finalizaciones de código ni las sugerencias de siguiente edición.
>
> Copilot Chat, Copilot CLI y el agente en la nube de Copilot usan las instrucciones del repositorio y los archivos `*.instructions.md` que incluyen el frontmatter `applyTo`.
>
> Copilot también admite instrucciones en otros formatos, como AGENTS.md y CLAUDE.md. Consulta la [referencia de compatibilidad][custom-instructions-support].

### Prácticas recomendadas para gestionar archivos de instrucciones

Los ejemplos del proyecto muestran un enfoque posible:

- Mantén `copilot-instructions.md` centrado en las directrices generales del proyecto, su estructura y los estándares comunes.
- Usa archivos `*.instructions.md` para tipos de archivo, como pruebas unitarias, componentes Astro y la capa de datos, o para tareas concretas.
- Escribe directrices claras en lenguaje natural e incluye ejemplos de cómo debería ser el código y de cómo no debería ser.

No existe una única forma correcta de crear instrucciones. Experimenta para encontrar el enfoque adecuado para el proyecto.

> [!TIP]
> Los archivos de instrucciones del proyecto incluyen directrices para tareas como [actualizar la interfaz][ui-instructions] y trabajar con [Astro][astro-instructions]. Copilot también puede ayudar a crear estos archivos: por ejemplo, VS Code incluye **Configure Chat → Generate Agent Instructions** y Copilot CLI incluye `/init`. Para obtener plantillas, consulta [awesome-copilot][awesome-copilot].

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## Explora los archivos de instrucciones personalizadas del proyecto

Revisa los archivos de instrucciones incluidos en el repositorio para conocer las directrices antes de modificarlas.

1. Abre `.github/copilot-instructions.md`.
2. Revisa la descripción breve del proyecto y las secciones **Agent notes**, **Code standards**, **Scripts** y **Repository Structure**. En **Code standards**, fíjate en las directrices anidadas **GitHub Actions Workflows**. Se aplican a todas tus interacciones con Copilot.
3. Abre `.github/instructions` y explora las instrucciones para Astro, la capa de datos de Drizzle, las pruebas y otros ámbitos.
4. Abre `.github/instructions/unit-tests.instructions.md` y revisa el campo `applyTo`. Este glob, relativo a la raíz del repositorio, determina a qué archivos se aplican las instrucciones; por ejemplo, coincide con archivos TypeScript de prueba como `**/*.test.ts`.
5. Revisa las directrices para crear pruebas unitarias en este proyecto.
6. Abre `.github/instructions/drizzle.instructions.md` y desplázate hasta el final. Observa los enlaces a otros archivos de instrucciones y a archivos existentes. Las rutas son relativas al archivo de instrucciones, no a la raíz del repositorio.

> [!NOTE]
> La sección **Code formatting requirements** de `copilot-instructions.md` documenta los estándares de programación, pero todavía no exige documentación en el código. En los pasos siguientes añadirás reglas para los comentarios TSDoc y los encabezados de comentario de los archivos.

## Añade un estándar del repositorio

Añadirás al archivo `copilot-instructions.md` una regla general para que el agente en la nube también la aplique al trabajar en incidencias. Primero crea una rama.

1. En el terminal del Codespace, crea una rama nueva y cámbiate a ella:

   ```bash
   git checkout -b custom-instructions-cloud
   ```

2. Vuelve a abrir `.github/copilot-instructions.md` y localiza la sección **Code formatting requirements**, cerca de la línea 27.
3. Añade las líneas siguientes debajo de los estándares existentes:

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. Guarda `copilot-instructions.md`.

> [!TIP]
> El archivo `.github/copilot-instructions.md` es adecuado para estándares generales del proyecto. Usa archivos `*.instructions.md` para lenguajes, tipos de archivo o tareas concretos.

## Confirma, envía y fusiona la actualización de instrucciones

El agente en la nube lee las instrucciones de la rama a la que apunta la incidencia. En el siguiente ejercicio, la incidencia partirá de `main`, así que debes fusionar allí estas instrucciones para que el agente las lea.

1. Prepara y confirma el cambio:

   ```bash
   git add .github/copilot-instructions.md
   git commit -m "Add doc comment and file-header standards to copilot instructions"
   ```

2. Envía la rama:

   ```bash
   git push -u origin custom-instructions-cloud
   ```

3. En github.com, abre una solicitud de incorporación de cambios desde `custom-instructions-cloud` a `main` y fusiónala.

> [!TIP]
> Si prefieres trabajar directamente en `main` durante este taller, puedes omitir la rama y confirmar el cambio directamente en `main`. Usar una rama permite practicar un flujo habitual de trabajo.

## Resumen y siguientes pasos

Has explorado cómo obtiene contexto Copilot de los archivos de instrucciones y has añadido un estándar general del repositorio a `.github/copilot-instructions.md`. Observarás cómo influye en las solicitudes de incorporación de cambios que genere el agente en la nube.

A continuación, [asignarás tu primera incidencia al agente en la nube de Copilot][next-lesson].

## Recursos

- [Archivos de instrucciones para personalizar GitHub Copilot][instruction-files]
- [Prácticas recomendadas para crear instrucciones personalizadas][instructions-best-practices]
- [5 consejos para escribir mejores instrucciones personalizadas][copilot-instructions-five-tips]
- [Awesome Copilot: colección de archivos de instrucciones y otros recursos][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-cloud-agent/
[instruction-files]: https://docs.github.com/copilot/customizing-copilot/about-customizing-github-copilot-chat-responses
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
