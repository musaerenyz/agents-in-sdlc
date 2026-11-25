---
title: "第 4 课 - 使用自定义指令引导 Copilot"
description: "了解存储库指令，添加文档标准，并将其应用到筛选代码。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

使用生成式 AI 时，上下文至关重要。如果任务必须以特定方式完成，应让 Copilot 能够获取相应指导。[指令文件][instruction-files]不仅描述需要编写什么代码，也说明代码应如何组织。现在你已完成筛选功能，接下来将了解 Copilot 使用的指令、添加文档标准，并将其应用到代码中。

在本课中，你将：

- 了解存储库指令和按路径限定的指令文件如何传递给智能体。
- 更新指令文件，确保遵循编码标准。
- 观察指令文件对代码的影响。

## 场景

Tailspin Toys 有一套开发实践指南和要求：

- 注释应解释意图和不明显的决策，而不是复述代码。
- `db/` 和 `src/lib/` 中导出的函数应使用 TypeScript 文档（TSDoc）/JavaScript 文档（JSDoc）说明用途、参数和返回值；如果函数接受可注入的 `db` 参数，也应予以说明。
- 可复用 Astro 组件应记录其 `Props` 契约；相关代码更改时，注释也应保持最新。
- 保留现有格式和 lint 指南。

通过指令文件，可以让 Copilot 按照这些实践完成任务。

## 指令文件

自定义指令为 Copilot 提供上下文和偏好设置，帮助它理解编码风格和要求，从而生成更相关的建议和代码片段。可以指定编码约定、库以及注释风格，并为整个存储库或特定文件类型创建指令。

指令文件有两种：

- `.github/copilot-instructions.md`：每次针对该存储库的请求都会发送此单一指令文件。内容应是适用于大多数请求的项目级信息。
- `.github/instructions/*.instructions.md`：为特定语言、文件类型或任务提供指导。

> [!NOTE]
> 其他指令格式及其支持情况因环境而异。依赖特定格式前，请查看[自定义指令支持参考][custom-instructions-support]。

## 查看本项目的自定义指令文件

示例项目已包含一组指令文件。先了解现有内容，再进行更改并观察效果。

1. 返回 Codespace。
2. 在 Codespaces 编辑器（而非终端）中打开 `.github/copilot-instructions.md`。
3. 查看项目简介和编码指导。此文件适用于该存储库中的每次 Copilot 交互。
4. 打开 `.github/instructions` 文件夹并查看其中的文件，包括 Astro、Drizzle 数据层、测试等相关指令。
5. 打开 `.github/instructions/unit-tests.instructions.md`。注意顶部的 `applyTo` 字段，它通过 glob 指定适用文件。
6. 打开 `.github/instructions/drizzle.instructions.md`，查看它对其他指令文件和项目现有文件的链接。较大的指令集可以拆分为可复用的小文件，并链接到供 Copilot 参考的示例。

## 更新指令文件以符合团队指南

现有文件还缺少一项要求：为新生成的 TypeScript 添加 TSDoc 注释。请更新核心 `copilot-instructions.md` 文件。

1. 在 `.github/copilot-instructions.md` 中找到 **Code formatting guidance** 部分（大约位于第 35 行）。
2. 在该部分末尾添加以下项目：

   ```markdown
   - All new TypeScript should contain TSDocs comments for documentation purposes.
   ```

文件会自动保存。

## 使用更新后的指导

Copilot CLI 会在对话启动时加载存储库指令。保存后，退出并恢复筛选对话，让 Copilot 在保留功能上下文的同时加载新指导。

1. 如果筛选对话仍处于打开状态，输入 `/exit` 退出。Copilot CLI 会自动保存对话。
2. 在存储库根目录的 `game-filters-cli` 分支上恢复已保存的对话：

   ```bash
   copilot --resume
   ```

   如果出现提示，请选择筛选对话，并确认当前检出的是 `game-filters-cli` 分支。
3. 请 Copilot 应用更新后的指导：

   ```plaintext
   We just updated our instructions and code guidance. Can you please update the code you generated to match that guidance?
   ```

4. 输入 `/diff`，检查更改的 TypeScript 文件。查看新生成的 TSDoc 注释，并确认它们准确描述代码。

## 总结和后续步骤

你了解了 Copilot CLI 如何从指令文件获取上下文，并将新的标准应用到功能中。具体来说，你：

- 了解存储库指令和按路径限定的指令文件如何传递给智能体。
- 更新指令文件，确保遵循编码标准。
- 观察了指令文件对代码的影响。

接下来[自定义并运行可复用的 quality-checks 技能][next-lesson]，确保一致地运行 lint 和测试。

## 资源

- [为 Copilot CLI 添加自定义指令][instruction-files]
- [自定义指令支持][custom-instructions-support]
- [创建有效自定义指令的最佳实践][instructions-best-practices]
- [Awesome Copilot：指令文件及其他资源集合][awesome-copilot]

[instruction-files]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions
[instructions-best-practices]: https://docs.github.com/copilot/concepts/prompting/response-customization#writing-effective-custom-instructions
[awesome-copilot]: https://awesome-copilot.github.com/
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support
[previous-lesson]: ../3-agent-modes/
[next-lesson]: ../5-agent-skills/
