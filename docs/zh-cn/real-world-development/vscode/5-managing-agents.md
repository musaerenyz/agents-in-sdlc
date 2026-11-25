---
title: "练习 5 - 监控和管理智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 上一课：自定义智能体][previous-lesson] |
|:--|

Copilot 处于代理模式时，会自主探索代码库、提出更改、编辑文件并运行命令。由于工作会实时发生在本机，Visual Studio Code（VS Code）中的 Copilot Chat 会持续显示工具调用和文件编辑。可以查看每项差异、接受或拒绝更改，并通过后续提示引导会话，无需离开编辑器。

在本练习中，你将：

- 实时监控无障碍智能体的工作。
- 审查并接受建议的文件编辑。
- 在会话进行中引导智能体，添加与高对比度并列的浅色模式切换。

## 场景

[练习 4][previous-lesson]中的无障碍自定义智能体正在实现高对比度模式。工作进行期间，设计团队又提出了浅色模式需求，供觉得默认主题过暗的用户使用。你将引导现有会话扩展工作，而不是从头开始新对话。

## 监控 Copilot 进度

Copilot Chat 会显示智能体采取的每个步骤，包括探索的文件、运行的命令和建议的编辑。每项文件编辑都会在聊天中显示差异和 **Keep** / **Undo** 控件。

1. 返回练习 4 中使用的 Copilot Chat 视图。
2. 智能体工作时，滚动聊天记录并查看工具调用，包括文件读取、文件编辑和请求运行的终端命令。
3. 智能体提出文件编辑时，可选择 **Keep** 接受更改，或选择 **Undo** 撤销更改并让智能体再次尝试。
4. 选择聊天顶部的 **Files changed** 指示器，可查看此会话中修改的完整文件列表。

> [!NOTE]
> 如果工具调用请求权限（例如运行终端命令），智能体会暂停并显示 **Allow** 提示。只批准你认可的命令。

5. 等待智能体完成高对比度模式实现。完成后应会看到更改摘要。

## 引导智能体添加浅色模式

高对比度模式完成后，在同一对话中添加浅色模式切换。由于继续使用相同会话，智能体会保留之前的上下文，包括已修改的组件、样式方法和本地存储方案。

1. 在 Copilot Chat 输入框中发送以下后续提示：

    ```plaintext
    Nice work! Now add a light-mode toggle alongside the high-contrast toggle. Light mode should follow the same pattern — persist via local storage and be accessible from the same area of the UI. The user should be able to enable high-contrast and light mode independently.
    ```

2. 智能体会检查现有更改，并提出浅色模式的新增内容。
3. 每次新编辑出现时都进行审查，并像之前一样选择 **Keep** 或 **Undo**。
4. 如果智能体的实现方向不符合预期，例如试图以不希望的方式重构切换 UI，可选择 Chat 输入框旁的 **Stop** 按钮停止任务，然后发送澄清提示。

> [!TIP]
> 通过后续提示引导正在运行的会话，通常比重新开始更快。可以要求调整（例如使用层叠样式表（CSS）变量简化 CSS）、纠正方向（例如将浅色模式集成到现有设置面板，而不是增加第三个切换项），或扩展范围（例如为两个切换项添加键盘快捷键）。

## 审查工作集

提交前快速检查智能体修改的所有内容。

1. 在 VS Code 中打开 **Source Control** 视图。
2. 审查所有更改的文件。通常包括 Astro 组件、样式和相关测试。
3. 打开一些更改的文件并查看差异。确认实现体现无障碍智能体的指导，例如无障碍富互联网应用（ARIA）属性、键盘导航、语义化超文本标记语言（HTML）和本地存储。
4. 确认满意后，在 Source Control 面板中暂存并提交更改。下一课会发布这些更改。

## 总结和后续步骤

你使用 Copilot Chat 代理模式监控并引导了进行中的会话，实时审查文件编辑、接受或拒绝更改，并通过后续提示扩展任务且保留上下文。

在本课中，你：

- 实时监控智能体工作。
- 接受或拒绝单个文件编辑。
- 使用后续提示引导进行中的会话。

接下来发布无障碍更改并审查本地自定义智能体在实验中生成的更改。

## 资源

- [VS Code 中的 Copilot Chat][copilot-chat-vscode]
- [使用代理模式][agent-mode]

---

| [← 上一课：自定义智能体][previous-lesson] | [下一课：迭代 Copilot 的工作 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../4-custom-agents/
[next-lesson]: ../6-iterating/
[copilot-chat-vscode]: https://code.visualstudio.com/docs/copilot/chat/copilot-chat
[agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
