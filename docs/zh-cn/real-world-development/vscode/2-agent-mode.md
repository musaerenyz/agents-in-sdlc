---
title: "练习 2 - 使用 Copilot 代理模式添加新功能"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

即使是简单的应用更新，通常也需要修改多个文件并运行测试。开发人员往往需要找到所有相关文件、进行修改、运行测试、调试、发现遗漏的文件并再次修改。Copilot 代理模式可以帮助处理这些工作。

Copilot 代理模式可在 IDE 中自主工作：先探索项目结构，再完成必要更新、运行测试等任务，并自动修复发现的问题。接下来了解如何使用代理模式为网站添加新功能。

> [!NOTE]
> 代理模式和云智能体名称相似，但适用于不同场景。代理模式在 IDE 中执行任务，可快速反馈并进行交互；云智能体则像团队成员一样异步工作，通过议题和拉取请求与你协作。

在本练习中，你将了解：

- Copilot 代理模式如何探索项目、识别相关文件并协调更改。
- GitHub Copilot 代理模式如何跨 UI 和数据层实现新功能。
- 合并前如何审查 Copilot 代理模式生成的更改和测试。

## 场景

随着游戏列表增加，用户需要按类别和发行商筛选。上一练习已添加发行商辅助函数，现在将使用 Copilot 代理模式完成其余数据层、用户界面（UI）和测试工作。

## 运行 Tailspin Toys 网站

进行更改之前，先了解 Tailspin Toys 网站当前的功能。

该网站是面向开发人员的桌游众筹平台，可列出游戏并显示游戏详情。它是一个单体 Astro 应用，构建时将页面渲染为静态超文本标记语言（HTML）。页面通过 Drizzle ORM 在 frontmatter 中直接查询本地 SQLite 数据库，没有单独的后端应用程序编程接口（API）或客户端 UI 框架。可复用的数据访问辅助函数位于 `src/lib/`，交互功能通过少量使用标准文档对象模型（DOM）API 的 Astro `<script>` 实现。

### 启动网站

项目提供了 `npm run dev` 任务，用于启动单体 Astro 开发服务器。

1. 返回 codespace。上一练习完成后，当前应位于 `main`。
2. 按 <kbd>Control</kbd>+<kbd>`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux），在 Codespace 中打开终端。
3. 更新 `main`，并为筛选功能创建分支：

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. 运行以下命令启动网站：

   ```bash
   npm run dev
   ```

   Astro 开发服务器就绪后，会显示类似以下 URL 的提示：

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> 如果出现询问是否打开 `http://localhost:4321` 的对话框，请选择 **x** 将其关闭。

5. 按住 <kbd>Command</kbd>（Mac）或 <kbd>Ctrl</kbd>（Windows/Linux），然后选择终端中的 Astro 服务器地址 `http://localhost:4321`，打开网站。

> [!NOTE]
> 在 codespace 中选择终端里的 localhost 链接，会自动重定向到 `https://<your-codespace-name>-4321.app.github.dev/`。这是指向当前托管网站的 codespace 的私有隧道。

### 浏览网站

网站运行后，可以查看其主要功能：

- **主页**：显示桌游列表、标题、图片和说明。
- **游戏详情页**：选择游戏后查看标题、说明、发行商和类别等信息。

## 使用 Copilot 查看待办事项

> [!TIP]
> **打开 Copilot Chat**
>
> 返回 codespace，打开 Copilot Chat 面板并选择 **New Chat**，开始全新对话。模式和模型选择因练习而异；需要时会在相应步骤中说明。

网站的初始实现已经可用，现在查看模板自动创建的议题待办事项。

1. 在 Chat 视图的智能体下拉列表中选择 **Agent**。该智能体可以自主规划并跨文件实现更改、运行终端命令和调用工具。

   ![Chat 视图中的智能体选择器](../../../_images/shared-chat-mode-selector.png)

2. 从可用模型列表中选择 **Claude Sonnet 4.5**。

> [!CAUTION]
> 本研讨会作者不偏好特定模型。构建本研讨会时使用了 Claude Sonnet 4.5，因此将它列在说明中，以便获得较一致的体验。由于大型语言模型（LLM）具有概率性，收到的建议可能与示例不同，这是正常现象。

> [!NOTE]
> Copilot 可能使用不同的 MCP 命令，但仍应能完成任务。

3. 向 Copilot 发送以下提示，查看议题待办事项：

   ```plaintext
   Please show me the backlog of items from my GitHub repository. Help me prioritize them based on those which will be most useful to the user.
   ```

4. 选择 **Continue** 以运行列出所有议题的命令。
5. 审查生成的议题列表。

Copilot 还会根据对用户的帮助程度为议题排序。

## 查看指令文件

让代理生成代码前，先查看用于提供工作上下文的指令文件。本练习将使用[用户界面（UI）指令文件](https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md)，其中介绍了如何为网站添加功能。

1. 在 codespace 中打开 `.github/instructions/ui.instructions.md`。
2. 查看整体指导，包括应用架构、组件设计原则、可测试性、无障碍要求，以及指向 Astro、Tailwind CSS 和 Drizzle 指令文件的链接。

> [!TIP]
> 指令文件可以引用其他指令文件和项目文件。路径相对于指令文件所在位置，因此可以复用指令、拆分复杂内容，并提供示例和模板。

## 实现筛选功能

筛选功能至少需要更新数据层逻辑、添加或更新测试，并修改游戏列表页以提供筛选 UI。合并前还必须运行并通过测试。Copilot 代理模式可以完成这些工作。

1. 继续当前 Copilot 对话，或选择 **New Chat** 开始新对话。
2. 选择 **Add Context**、**Instructions** 和 **ui**，添加 UI 指令文件。

   ![选择 UI 指令文件的示例](../../../_images/ex3-select-instructions-file.png)

3. 确认 Chat 视图的智能体下拉列表仍选择 **Agent**。

   ![Chat 视图中的智能体选择器](../../../_images/shared-chat-mode-selector.png)

4. 确认模型仍选择 **Claude Sonnet 4.5**。
5. 使用以下提示，请 Copilot 根据待办事项中的相关议题实现功能：

   ```plaintext
   Please update the site to include filtering by publisher and category based on the requirements from the related GitHub issue in the backlog. A publishers helper already exists from the previous exercise, so preserve and refine that work as needed while completing the remaining data-layer, UI, and tests. Ensure all tests are passing before completion. The server is already running, so you do not need to start it up.
   ```

6. 观察 Copilot 探索项目并定位相关数据层辅助函数、UI 和测试，然后修改文件并运行测试。

   ![Copilot 正在探索项目文件](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Copilot 会探索项目、修改文件并运行测试。根据任务和代码库的复杂程度，这可能需要几分钟。过程中编辑器可能会显示 **Keep** 和 **Undo**；Copilot 完成后，可以统一接受或撤销所有更改，不需要在工作期间逐项选择。

7. Copilot 提示运行测试时，选择 **Continue**。

   ![Copilot Chat 窗格中的运行测试确认对话框](../../../_images/ex3-agent-mode-run-tests.png)

8. 过程中可能会暂停或遇到测试失败。Copilot 会在生成代码和运行测试之间迭代，直到任务完成且未发现错误。

   ![Copilot 代理模式完成的 Chat 会话](../../../_images/ex3-agent-mode-proposed-changes.png)

9. 检查生成的代码是否存在问题。

> [!CAUTION]
> 始终需要审查 Copilot 或其他 AI 工具生成的代码。

10. 返回正在运行网站的浏览器，试用新功能。
11. 确认功能正常并审查代码后，在 Copilot Chat 窗口中选择 **Keep**。

## 总结和后续步骤

本练习使用 GitHub Copilot 代理模式为 Tailspin Toys 网站添加了功能。你了解了：

- GitHub Copilot 代理模式如何跨 UI 和数据层实现新功能。
- Copilot 代理模式如何探索项目、识别相关文件并协调更改。
- 合并前如何审查 Copilot 代理模式生成的更改和测试。

接下来使用 [Playwright MCP 服务器测试功能][next-lesson]，并为更改创建拉取请求。

### 额外探索练习 - 实现分页

随着游戏列表增长，网站需要分页。使用本练习学到的技能，请 Copilot 为网站添加分页。考虑遵循现有实践和指令文件、是否允许用户选择每页项目数，以及如何清楚地描述需求。根据需要与 Copilot 迭代并提供上下文，这是正常的协作流程。

## 资源

- [Copilot 的 ask、edit 和 agent 模式：功能及适用场景][choose-mode]
- [VS Code 中的代理模式][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
