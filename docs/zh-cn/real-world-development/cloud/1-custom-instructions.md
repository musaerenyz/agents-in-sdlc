---
title: "练习 1 - 自定义指令（云智能体）"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 上一课：先决条件][previous-lesson] · [下一课：Copilot 云智能体 →][next-lesson]

使用生成式 AI 时，上下文至关重要。如果任务必须以特定方式完成，或 Copilot 需要了解背景信息，就应确保它能访问这些上下文。[指令文件][instruction-files]可提供此类指导，帮助 Copilot 理解不仅要做什么，还要如何完成任务。

在本练习中，你将：

- 了解项目上下文、编码指南和文档标准如何通过存储库自定义指令及按路径限定的指令文件传递给 Copilot。
- 向 `.github/copilot-instructions.md` 添加新的全局存储库标准。

> [!NOTE]
> 与 VS Code 和 CLI 环境不同，本练习不会并排比较提示执行前后的结果。Copilot 云智能体会异步处理 GitHub 议题，因此较难实时对比。之后审查云智能体创建的拉取请求时，可以看到指令文件的影响。

## 指令文件

### 场景

Tailspin Toys 有一套开发实践指南和要求：

- 数据层必须始终有单元测试。
- UI 应采用暗色模式并具有现代感。
- 代码应通过 TypeScript 文档（TSDoc）注释提供说明。
- 每个文件开头都应有描述文件用途的注释块。

通过指令文件，可以让 Copilot 按照这些实践完成任务。

### 自定义指令

自定义指令为 Copilot 提供上下文和偏好设置，帮助它理解编码风格和要求。可以指定编码约定、库以及注释风格，并为整个存储库或特定文件类型创建指令。

指令文件有两种：

- `.github/copilot-instructions.md`：每次针对该存储库的请求都会发送此单一文件。内容应是适用于大多数聊天或 CLI 请求的项目级信息，例如技术栈、项目简介、最佳实践和全局指导。
- `.github/instructions/*.instructions.md`：为特定任务或文件类型提供指导，例如 TypeScript、Astro、UI 组件或单元测试。

> [!NOTE]
> 在 IDE 中，指令文件仅用于 Copilot Chat 的代码生成，不用于代码补全或下一步编辑建议。
>
> Copilot Chat、Copilot CLI 和 Copilot 云智能体在生成代码时都会使用存储库级指令和带有 `applyTo` frontmatter 的 `*.instructions.md` 文件。
>
> Copilot 还支持其他标准的指令文件格式，包括 AGENTS.md 和 CLAUDE.md。

### 管理指令文件的最佳实践

示例项目展示了一种有代表性的做法：

- 将 `copilot-instructions.md` 限定为项目级指导，例如项目简介、项目结构和全局编码标准。
- 使用 `*.instructions.md` 为文件类型（单元测试、Astro 组件、数据层）或具体任务提供指导。
- 使用自然语言，保持说明清楚，并提供正反示例。

指令文件没有唯一的编写方法。应通过实践找到适合项目的方式。

> [!TIP]
> 每个使用 GitHub Copilot 的项目都应具备完善的指令文件。浏览本项目文件时，可以看到 UI 更新和 [Astro][astro-instructions]等任务的指令。
>
> Copilot 也可以帮助生成指令文件。不同产品界面的操作方式不同，例如 VS Code 中的 **Configure Chat → Generate Agent Instructions**，或 Copilot CLI 中的 `/init`。相应课程会在需要时说明具体操作。
>
> 可从 [awesome-copilot][awesome-copilot] 查找指令文件、自定义智能体和其他资源。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## 查看本项目的自定义指令文件

花些时间查看存储库附带的指令文件：一个核心 `copilot-instructions.md` 文件，以及一组针对不同任务的 `*.instructions.md` 文件。可在编辑器或 GitHub 网站中打开。

1. 打开 `.github/copilot-instructions.md`。
2. 查看项目简介及 **Agent notes**、**Code standards**、**Scripts** 和 **Repository Structure** 等部分。注意 **Code standards** 下的 **GitHub Actions Workflows** 指导。这些内容适用于与 Copilot 的各种交互。
3. 打开 `.github/instructions` 文件夹，查看 Astro 文件、Drizzle 数据层、测试等相关指令。
4. 打开 `.github/instructions/unit-tests.instructions.md`。注意顶部的 `applyTo` 字段，它通过相对于存储库根目录的 glob 指定适用文件。任何符合 `**/*.test.ts` 等模式的 TypeScript 测试文件都适用。
5. 查看该文件中针对本项目单元测试的说明。
6. 打开 `.github/instructions/drizzle.instructions.md` 并查看文件末尾指向其他指令文件（如 `unit-tests.instructions.md`）和项目现有文件的链接。这样可以拆分较大的指令集、重复使用小型文件，并提供生成代码时可参考的示例。文件中的路径相对于指令文件本身，而不是存储库根目录。

> [!NOTE]
> `copilot-instructions.md` 中的 **Code formatting requirements** 部分介绍了项目编码标准，但尚未要求添加代码文档。接下来会加入 TSDoc 文档注释和文件注释头的规则。

## 添加新的存储库标准

接下来需要编辑一次：添加项目级规则，要求使用 TSDoc 文档注释，并在文件开头添加注释头。稍后的练习中，云智能体会读取该规则。

编辑前先创建分支。云智能体会读取议题目标分支中的指令；在此处提交更改可以保持历史清晰。

1. 在 codespace 终端中创建并切换到新分支：

   ```bash
   git checkout -b custom-instructions-cloud
   ```

存储库级 `.github/copilot-instructions.md` 用于向 Copilot 提供项目级信息。添加编码标准可以改善代码建议。

1. 重新打开 `.github/copilot-instructions.md`。
2. 找到 **Code formatting requirements** 部分（大约位于第 27 行）。留意它尚未规定代码文档要求。
3. 在现有标准下方添加以下 Markdown 内容，要求 Copilot 添加文件注释头和 TSDoc 文档注释：

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. 保存 `copilot-instructions.md`。

> [!TIP]
> 指令可以放在存储库级 `.github/copilot-instructions.md` 中，作为全局指导；也可以放在 `*.instructions.md` 文件中，适用于特定语言、文件类型或任务。刚添加的文档注释规则属于项目级标准，因此放在存储库级文件中。

## 提交、推送并合并指令更新

云智能体会读取议题目标分支中的指令。下一练习中将议题分配给 Copilot 时，它会从 `main` 创建分支，因此必须先将指令更新合并到 `main`。

1. 暂存并提交更改：

   ```bash
   git add .github/copilot-instructions.md
   git commit -m "Add doc comment and file-header standards to copilot instructions"
   ```

2. 推送分支：

   ```bash
   git push -u origin custom-instructions-cloud
   ```

3. 在 github.com 上创建从 `custom-instructions-cloud` 到 `main` 的拉取请求并合并。下一练习中，云智能体便会读取这些指令。

> [!TIP]
> 如果希望在本研讨会中直接使用 `main`，可以跳过分支操作并直接提交到 `main`。此处创建分支是为了模拟实际项目的工作方式。

## 总结和后续步骤

你了解了 Copilot 如何从项目指令文件获取上下文，并向 `.github/copilot-instructions.md` 添加了新的存储库级标准。后续练习中，可以观察云智能体生成的拉取请求是否遵循该标准。

接下来[将首个议题分配给 Copilot 云智能体][next-lesson]。

## 资源

- [用于自定义 GitHub Copilot 的指令文件][instruction-files]
- [创建自定义指令的最佳实践][instructions-best-practices]
- [编写更好的 Copilot 自定义指令的 5 个技巧][copilot-instructions-five-tips]
- [Awesome Copilot：指令文件及其他资源集合][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-cloud-agent/
[instruction-files]: https://docs.github.com/copilot/customizing-copilot/about-customizing-github-copilot-chat-responses
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
