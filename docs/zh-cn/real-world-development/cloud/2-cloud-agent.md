---
title: "练习 2 - GitHub Copilot 云智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 上一课：自定义指令][previous-lesson] |
|:--|

许多组织都面临技术债务，例如尚未解决的安全问题、需要更新的旧代码，或长期搁置的功能请求。GitHub Copilot 云智能体可以自主更新代码和添加功能。完成后，它会创建草稿拉取请求（PR），供开发人员审查。这有助于处理繁琐任务，让开发人员专注于更重要的工作。

本课将介绍如何使用 Copilot 云智能体：

- 自定义代码生成环境。
- 确保操作安全。
- 编写范围明确的议题。
- 将议题分配给 Copilot。

## 场景

Tailspin Toys 有一些技术债务。最初开发网站的承包商没有为导出的函数编写 TypeScript 文档（TSDoc）注释。团队希望先为所有导出函数补充注释。

此外，设计团队希望改进游戏发现体验：在每款游戏的详情页显示同类别的其他游戏，方便用户继续浏览。当前不需要完善的实现，只需要能用于用户体验验收测试的版本。这项任务目前不是最高优先级，可能会被搁置。

这些都是适合分配给 Copilot 云智能体的任务。云智能体可在后台异步处理，开发人员可以继续其他工作，之后再回来审查结果。

## 了解 GitHub Copilot 云智能体

[GitHub Copilot 云智能体](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent)可以像开发人员一样在后台执行任务。可以通过多种方式分配工作，例如[将 GitHub 议题分配给 Copilot](https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions)。分配后，Copilot 会创建草稿拉取请求、设置环境并开始处理任务。你可以在会话进行中或完成后查看详情。解决方案准备好供审查时，Copilot 会在拉取请求中提及你。

## 编写范围明确的指令

GitHub Copilot 并非魔法，使用它仍然需要思考。即使看似简单的任务也可能有复杂细节，因此应谨慎确定云智能体任务的范围，并像使用 AI 结对编程助手一样分阶段开展工作、学习和调整。生成式 AI 不会改变软件开发的基本原则。

## 本存储库中的自定义指令

前面的练习介绍了自定义指令及其作用。如果尚未完成相关实践，此时可快速查看存储库中已有的指令文件。分配任务前先只读查看，以便之后观察它们的影响。

在 GitHub 网站或正在运行的 codespace 中打开以下文件：

- `.github/copilot-instructions.md`：查看 **Code standards**，尤其是 TypeScript 约定和 TSDoc 文档注释要求。
- `.github/instructions/unit-tests.instructions.md`：注意 `applyTo` frontmatter，它将指令限定在 `**/*.test.ts` 文件。

在下一节将 `Code lacks documentation` 议题分配给云智能体时，留意它创建的拉取请求是否采用了 TSDoc 文档注释、TypeScript 约定和文件注释头。稍后的练习还会再次审查这些内容。

## 为 Copilot 云智能体设置开发环境

无论由谁编写代码，通常都需要准备特定环境并运行设置脚本。将任务分配给 Copilot 时也一样。

云智能体使用 [GitHub Actions][github-actions] 设置工作环境。可在 `.github/workflows/copilot-setup-steps.yml` 中创建[专用设置工作流][setup-workflow]，在它开始工作前安装必要的开发工具和依赖项。本实验已预先配置该工作流，其中为单体 Astro 应用安装 Node.js、项目依赖、端到端测试所需的浏览器，并迁移和填充 SQLite 数据库：

```yaml
name: "Copilot Setup Steps"

# 允许你在存储库的“Actions”选项卡中测试设置步骤
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # 为你的步骤设置所需的最低权限。Copilot 会使用自己的令牌执行操作。
    permissions:
      # 如果设置步骤需要克隆存储库（例如安装依赖项），则需要 `contents: read` 权限。如果未在设置步骤中克隆存储库，Copilot 会在步骤完成后自动克隆。
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # 前端/应用设置 - Node.js（整个应用现在基于 Astro + Drizzle/libSQL）
      - name: Set up Node.js
        uses: actions/setup-node@v6
        with:
          node-version: "lts/*"
          cache: "npm"
          cache-dependency-path: "./package-lock.json"

      - name: Install JavaScript dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps chromium

      # 迁移并填充本地 SQLite 数据库，为构建和测试准备数据。
      - name: Set up the database
        run: npm run db:setup
```

该文件与其他 GitHub 工作流类似，但有两点值得留意：

- 工作流只有一个名为 `copilot-setup-steps` 的作业。Copilot 开始处理拉取请求前，GitHub Actions 会运行此作业。
- `workflow_dispatch` 触发器允许你在存储库的 **Actions** 选项卡中手动运行工作流，以便先验证它是否成功。

## 添加文档

许多项目的文档过时或缺失，这类技术债务会降低效率、增加维护难度，也不利于新成员加入。Copilot 适合处理文档任务，可以在后台生成文档，稍后再审查结果。

1. 在新的浏览器标签页中打开 github.com 上的存储库。
2. 选择 **Issues** 选项卡，然后选择 **New issue**。
3. 选择 **Blank issue**。
4. 将 **Title** 设置为 `Code lacks documentation`。
5. 将 **Description** 设置为：

    ```plaintext
    Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
    ```

6. 选择 **Create** 创建议题。
7. 在右侧选择 **Assign to Copilot** 打开分配对话框。

  ![将议题分配给 Copilot](../../../_images/shared-assign-copilot.png)

8. 选择 **Assign**。
9. 选择 **Pull Requests** 选项卡。
10. 打开新生成的拉取请求（PR），标题可能类似于 `[WIP]: Code lacks documentation`。如果列表中尚未出现，请等待一两分钟并刷新页面。
11. 几分钟后，Copilot 应会在 PR 中创建待办事项列表。

> [!NOTE]
> Copilot 可能需要几分钟才会在 PR 中显示待办事项列表。它需要先设置环境（运行前面介绍的工作流）、分析项目并确定处理方式。

12. 审查任务列表及 Copilot 准备执行的工作。
13. 向下滚动 PR 时间线，查看 Copilot 已开始处理议题的更新。
14. 选择 **View session** 按钮。

  ![Copilot 会话视图](../../../_images/ex4-view-session.png)

> [!CAUTION]
> 可能需要刷新页面才能看到更新后的状态。

15. 浏览实时会话，观察 Copilot 如何探索代码、了解当前状态、制定方案并编写代码。

整个过程可能需要几分钟。云智能体的主要优势之一是能够异步执行任务，让你同时处理其他工作。接下来利用这一点，再分配一项任务，然后转而编写应用功能。

## 在游戏详情页添加相关游戏

云智能体可以与开发人员并行处理不同任务。现在将设计团队的相关游戏功能分配给它。

1. 返回 github.com 上的存储库，选择 **Issues**，再选择 **New issue** 和 **Blank issue**。
2. 将 **Title** 设置为 `Show related games on the game details page`。
3. 将 **Description** 设置为：

    ```markdown
    We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:

   - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
   - Show a "Related games" section on the game details page that uses the helper
   - Handle the case where a game has no related games
   - There should be unit tests created for the new helper
   - Before creating the PR, ensure all tests pass
    ```

4. 选择 **Create** 创建议题。
5. 在右侧选择 **Assign to Copilot**，再选择 **Assign**。

稍后，议题的首条评论中会出现 👀，表示 Copilot 已开始处理。

![Copilot 使用眼睛表情符号表示正在处理议题](../../../_images/ex4-issue-eyes-emoji.png)

Copilot 正在处理第二项任务。云智能体的工作方式类似于软件工程师，因此不需要持续监视；完成后再审查即可。现在可以转向创建和使用自定义智能体。

## 总结和后续步骤

本课介绍了 [GitHub Copilot 云智能体][copilot-agents]。可以将议题分配给云智能体，让它异步处理技术债务、开发功能或迁移框架等任务。

你探索了以下内容：

- 自定义代码生成环境。
- 确保操作安全。
- 编写范围明确的议题。
- 将议题分配给 Copilot。

云智能体在后台工作时，现在可以转向创建和使用自定义智能体。[Copilot 云智能体也支持模型上下文协议（MCP）服务器][cloud-agent-mcp]，并可使用前面练习中介绍的自定义指令。

## 资源

- [关于 Copilot 云智能体][copilot-agents]
- [将 GitHub 议题分配给 Copilot][assign-issue]
- [Copilot 云智能体设置工作流最佳实践][cloud-agent-best-practices]

| [下一课：自定义智能体 →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
