---
title: "练习 4 - 监控和管理智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

在前几次练习中，你让 Copilot 云智能体处理了三项改进用户体验和添加功能的任务。云智能体可以异步自主工作，但监控这些任务仍然很重要。

可通过多种工具管理分配给云智能体的任务，包括 GitHub.com 上的[智能体页面][agents-page]。在这个控制面板中，可以查看所有带有未关闭拉取请求（PR）的智能体任务，检查执行情况，并引导进行中的会话。

在本课中，你将：

- 使用智能体页面监控云智能体任务。
- 在进行中的会话中请求添加其他功能。

## 场景

分配高对比度模式任务后，团队意识到也应添加浅色模式。由于智能体已经在更新网站样式并添加切换功能，可以引导它同时实现浅色模式。

## 查看 Copilot 云智能体任务

查看所有分配给 Copilot 云智能体的任务状态。

1. 打开 [Copilot 智能体页面](https://github.com/copilot/agents)。
2. 查看主区域和左侧区域中的任务列表，其中应包括为代码库补充文档、生成用于修改游戏的应用程序编程接口（API），以及为网站添加高对比度模式等任务。
3. 选择一个正在运行的任务，查看 Copilot 已执行的操作。这些操作可能包括检出存储库代码、设置 Copilot 工作环境、配置 MCP 服务器，以及完成所分配任务的其他步骤。

> [!NOTE]
> 具体步骤会根据 Copilot 的工作状态和执行方式而变化。

4. 查看右侧显示的 PR 面板，以监控 PR 和更改的文件。

## 引导云智能体

现在让 Copilot 在实现高对比度模式时也添加浅色模式。

1. 选择与高对比度模式相关的会话。会话标题会根据 Copilot 采用的名称和当前进度而变化。

    ![控制面板中的无障碍会话](../../../_images/ex6-accessibility-session.png)

2. 观察会话几分钟，直到 Copilot 完成设置并开始工作。
3. 在 **Steer active session while Copilot is working** 对话框中输入以下提示：

    ```plaintext
    While we are working on a high contrast mode, let's also add a light mode. There should be a switch for this mode as well where users can select their desired display mode.
    ```

    ![智能体页面中的云智能体任务，突出显示 Steer active session while Copilot is working 对话框](../../../_images/ex6-steer-cloud-agent-task.png)

4. 按 <kbd>Enter</kbd> 发送提示。
5. 观察 Copilot 如何确认提示并将其纳入工作流程。

## 让 Copilot 继续工作

Copilot 会继续处理更新后的任务。收到提示后，它会先完成当前步骤，再将新请求加入工作流程。

这可能需要几分钟，可以利用这段时间回顾已学内容。

## 总结和后续步骤

本课介绍了 Copilot 智能体页面，它是监控和引导 GitHub Copilot 云智能体任务的中心。可跟踪进行中和已完成的任务、审查工作，并调整进行中任务的范围或提供额外指导。

你探索了以下内容：

- 使用控制面板和智能体页面监控云智能体任务。
- 在进行中的会话中请求添加其他功能。

Copilot 正在完成无障碍功能。接下来[迭代 Copilot 创建的拉取请求][next-lesson]。

## 资源

- [Copilot 智能体页面][agents-page]
- [关于自定义智能体][custom-agents]

---

| [← 上一课：自定义智能体][previous-lesson] | [下一课：迭代 Copilot 的工作 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
