---
title: "练习 5：迭代 GitHub Copilot 的工作"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 上一课：管理智能体][previous-lesson] |
|:--|

## 审查工作

在本实验中，你与 GitHub Copilot 合作完成了多项任务，包括为代码添加文档、实现供设计团队迭代的相关游戏功能，以及实现高对比度和浅色模式等无障碍功能。现在审查代码更改；必要时向 Copilot 提供反馈，改进其工作。

### 场景

加入生成式 AI 并不会改变软件设计和 DevOps 的基本原则。始终需要审查生成的代码，并遵循正常的 DevOps 流程。接下来审查 Copilot 为代码文档、相关游戏功能和无障碍功能创建的更改，然后再为团队开启审查。

## GitHub Copilot 云智能体的安全性

云智能体会在无人监督的情况下异步执行任务，因此设有安全限制：

- Copilot 只能读取存储库，并且只能写入它用于提交代码的分支。
- 云智能体在 GitHub Actions 中运行，并在单独的临时环境中工作。
- GitHub Actions 工作流必须经人工批准后才能运行。
- 默认情况下，[对外部资源的访问受到限制](https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall)，包括 MCP 服务器。

## 审查生成的文档

先审查 GitHub Copilot 云智能体创建的第一个拉取请求（PR），其中包含代码文档更新。

> [!NOTE]
> PR 中可能会出现 GitHub Copilot 被防火墙阻止的警告。这是预期行为，因为默认情况下 Copilot 对外部资源的访问受限，包括访问外部 MCP 服务器。可以选择[自定义或禁用云智能体防火墙][agent-firewall]。

1. 返回 github.com 上的存储库。
2. 选择 **Pull Requests** 选项卡，打开拉取请求列表。
3. 打开标题类似 **Add missing documentation** 的 PR。如果 Copilot 仍在处理任务，PR 标题会包含 **[WIP]**；请等待它完成。
4. 选择 **Files changed** 选项卡，审查更改。

    ![Files changed 选项卡](../../../_images/shared-pr-files-changed.png)

5. 查看新增的 TSDoc 文档注释和其他文档。具体更改可能不同；留意 TSDoc 注释、TypeScript 约定和文件注释头，它们来自前面查看的自定义指令文件。
6. 审查完成后，返回 **Conversation** 选项卡并向下滚动。
7. 如果看到有工作流等待批准，选择 **Approve and run workflows**。

   ![批准并运行工作流](../../../_images/shared-approve-workflows.png)

8. 查看 PR 检查区域中的工作流。成功时，单体 Astro 应用的项目检查应通过。检查可能需要几分钟。

## 请求 GitHub Copilot 更改

你可以在 PR 评论或代码行内评论中提及 Copilot，让它启动新会话处理反馈。由于结果具有非确定性，无法规定唯一的提示内容。可以要求 Copilot：

- 在每个代码文件开头添加简短的文件说明注释。
- 为 TypeScript 和 Astro 文件添加 TSDoc 文档注释。
- 创建介绍 Astro 应用结构的 README。

1. 添加评论并提及 **@copilot**，提出一项文档更改请求。可以使用上述建议，也可以提出其他需求。
2. 选择 **View Session**，观察 Copilot 如何启动新会话并更新内容。
3. 选择 **Back to pull request** 返回 PR。

    ![返回拉取请求](../../../_images/ex7-back-to-pr.png)

4. Copilot 完成更改后，PR 中应出现新的提交。
5. 选择 **Files changed** 选项卡审查更改。

可以继续迭代，直到满意为止。之后将 PR 从草稿转换为准备审查状态，并合并到主分支。

![将 PR 转为准备审查状态](../../../_images/ex7-ready-for-review.png)

## 审查相关游戏功能

接下来审查 Copilot 为游戏详情页相关游戏议题创建的 PR。

1. 返回 GitHub.com 上的存储库。
2. 选择 **Pull Requests** 选项卡，并打开标题类似 **Show related games on the game details page** 的 PR。
3. 选择 **Files changed** 选项卡，审查生成的代码。
4. 审查完成后，返回 **Conversation** 选项卡并向下滚动。
5. 如果有工作流等待批准，选择 **Approve and run workflows**。

   ![批准并运行工作流](../../../_images/shared-approve-workflows.png)

6. 查看 PR 检查区域中的工作流。单体 Astro 应用的项目检查应通过，这可能需要几分钟。
7. **可选：** 在 Codespace 中检出此分支，手动测试相关游戏功能。将 `<branch-name>` 替换为 Copilot 创建的分支名称，例如 **copilot/fix-8**：

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

Copilot 已实现相关游戏功能。可以像之前一样通过在 **Conversation** 选项卡中添加评论来要求云智能体继续迭代，例如调整相关游戏数量，或补充文件注释头和 TSDoc 文档注释。注意，此任务是在自定义指令更新前分配的。

## 审查无障碍功能

最后审查使用无障碍自定义智能体实现的功能。此 PR 应包含练习 3 中分配的高对比度模式，以及练习 4 中在控制面板要求的浅色模式。

1. 返回 GitHub.com 上的存储库，打开 **Pull Requests** 选项卡。
2. 打开标题类似 **Add high contrast mode to website** 的 PR。如果 Copilot 仍在工作，请等待 PR 标题中的 **[WIP]** 标记消失。
3. 选择 **Files changed** 选项卡，审查生成的代码，特别检查：
   - 用于切换模式的 UI 组件。
   - 使用本地存储保存用户偏好。
   - 高对比度和浅色模式的 CSS 或样式更改。
   - 无障碍属性（ARIA 标签、键盘导航等）。
   - 管理模式切换的 JavaScript/TypeScript 代码。
4. 审查完成后，返回 **Conversation** 选项卡并向下滚动。
5. 如果有工作流等待批准，选择 **Approve and run workflows**。

   ![批准并运行工作流](../../../_images/shared-approve-workflows.png)

6. 确认 PR 检查区域中的工作流已排队，并等待单体 Astro 应用的项目检查通过。
7. **可选：** 在 Codespace 中检出 Copilot 创建的分支，手动测试无障碍功能：

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

    启动应用，在浏览器中测试高对比度和浅色模式切换，并确认页面重新加载后设置仍然有效。

无障碍自定义智能体帮助 Copilot 根据无障碍最佳实践实现这些功能。如果发现问题或改进点，可以像处理其他 PR 一样在评论中提及 **@copilot** 请求更新。

## 可选练习 — 继续委派任务

云智能体适合处理待办事项，让你可以转而完成其他工作。可在存储库中创建更多议题并分配给 Copilot，例如：

- 在游戏详情页创建支持者意向表单。
- 为游戏列表页实现分页。
- 为数据访问辅助函数添加输入验证和错误处理。

## 总结

你已完成云智能体环境的练习。在这些课程中，你：

- **检查了存储库附带的自定义指令文件**，以便之后观察它们对云智能体输出的影响。
- **将议题分配给 Copilot 云智能体**，观察它设置环境、制定计划并异步执行任务。
- **创建并使用自定义智能体**实现高对比度和浅色模式切换。
- **使用 Copilot 智能体页面作为控制面板**，监控并引导进行中的无障碍会话。
- **审查并迭代云智能体创建的拉取请求**，通过提及 `@copilot` 请求更改并批准工作流。

## 回顾和后续步骤

云智能体环境的练习已完成。其他环境可以补充你在此处实践的内容：

- 🖥️ **[VS Code 环境](../../vscode/)** — 在 IDE 中探索 Copilot Chat 代理模式和 MCP 集成。
- 💻 **[CLI 环境](../../cli/)** — 从终端交付两项经过审查的更改，体验 Plan 和 Autopilot 模式、智能体技能、自定义 QA 智能体、Playwright MCP、Agent Merge，以及可选的 `/delegate` 云智能体桥接。

在自己的存储库中，还可以尝试：

- 将重构或测试覆盖率议题分配给云智能体。
- 为其他领域创建自定义智能体。
- 为其他技术栈设置 `copilot-setup-steps.yml`。

## 资源

- [GitHub Copilot][github-copilot]
- [关于 Copilot 智能体][copilot-agents]
- [将 GitHub 议题分配给 Copilot][assign-issue]
- [Copilot 云智能体设置工作流最佳实践][cloud-agent-best-practices]
- [配置 Copilot 云智能体防火墙][agent-firewall]

---

| [← 上一课：管理智能体][previous-lesson] |
|:--|

[github-copilot]: https://github.com/features/copilot
[cloud-agent-overview]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[agent-firewall]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall
[previous-lesson]: ../4-managing-agents/
