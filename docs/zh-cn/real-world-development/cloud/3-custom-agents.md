---
title: "练习 3 - 自定义智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

## 什么是自定义智能体？

GitHub Copilot 中的[自定义智能体][custom-agents-concept]是针对开发工作流中特定任务或领域定制的人工智能（AI）助手。可在存储库的 `.github/agents` 文件夹中通过 Markdown 文件定义智能体，并提供专用指令、最佳实践、编码模式和领域知识，帮助 Copilot 更有效地完成特定工作。团队可以将专业知识整理成可复用智能体，例如依据《Web 内容无障碍指南》（WCAG）进行合规检查的无障碍智能体、遵循安全编码实践的安全智能体，或维护统一测试模式的测试智能体。

自定义智能体可以定义在项目的 `.github/agents` 中，也可以全局定义在 `~/.copilot/agents` 中。每个文件都包含 YAML frontmatter（至少有 `name` 和 `description`），后续 Markdown 提示用于定义智能体的行为、专业能力和指令。

### 自定义智能体与智能体技能

自定义智能体和[智能体技能][agent-skills-concept]都主要通过 Markdown 文件定义，并告诉 AI 如何完成工作。简单来说，**自定义智能体是执行工作的角色，技能是它使用的工具**。

自定义智能体拥有自己的上下文窗口，并负责协调技能（甚至其他智能体）完成工作。本实验中的无障碍自定义智能体会根据无障碍指南审查和更新网站，也可以调用拉取请求工作流技能或测试管理技能。

> [!NOTE]
> 自定义智能体没有唯一正确的编写方式。与其他 AI 功能一样，应针对自己的环境和场景测试并迭代。

[custom-agents-concept]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
[agent-skills-concept]: https://docs.github.com/copilot/concepts/agents/about-agent-skills
[wcag]: https://www.w3.org/WAI/standards-guidelines/wcag/

本练习将介绍：

- 自定义智能体的定义方式。
- 如何向自定义智能体分配任务。

## 场景

Tailspin Toys 致力于让所有用户都能使用其众筹平台，不受视力能力或偏好的影响。用户反馈指出，当前暗色主题的文本与背景对比度不足，导致难以阅读。设计团队因此要求添加可切换的高对比度模式。

无障碍功能至关重要，因此将使用自定义智能体尽快生成此功能。

## 查看无障碍自定义智能体

项目已提供无障碍自定义智能体。先查看它的内容，了解它将如何引导 Copilot。

返回 codespace，然后查看无障碍智能体文件：

1. 打开 `.github/agents/accessibility.md`。
2. 查看包含 `name` 和 `description` 字段的 YAML frontmatter。

> [!CAUTION]
> 自定义智能体必须包含 `name` 和 `description` frontmatter。

3. 浏览后续部分，了解生成无障碍网站代码时的核心职责、最佳实践、HTML/CSS/JavaScript 示例，以及常见问题。

## 创建并分配议题

智能体控制面板是管理环境中所有智能体的中心位置。可以在其中分配任务、监控任务并引导进行中的会话。现在将创建高对比度模式任务并分配给 Copilot。

1. 打开自己的存储库。
2. 选择 **Issues** 选项卡，选择 **New issue**，然后选择 **Blank issue**。
3. 将 **Title** 设置为 `Add high contrast mode to website`。
4. 将 **Description** 设置为：

    ```plaintext
    We need a high contrast mode for the site. There should be a toggle for high contrast which the user can set. It should store the setting in local storage on the browser.
    ```

5. 选择 **Create** 创建议题。
6. 在右侧选择 **Assign to Copilot**，然后从自定义智能体列表中选择 **Accessibility agent**。

    ![云智能体分配界面，突出显示自定义智能体和无障碍智能体](../../../_images/ex5-select-custom-agent.png)

7. 选择 **Assign**。
8. Copilot 将在后台开始处理任务。

## 总结和后续步骤

本课介绍了 GitHub Copilot 中的[自定义智能体][custom-agents]，它们是针对特定任务和领域定制的 AI 助手。自定义智能体可以将团队经验和标准编码为可复用配置，引导 Copilot 更有效地完成工作。

你探索了以下内容：

- 自定义智能体的定义方式。
- 如何分配任务给自定义智能体。

Copilot 正在实现高对比度模式。接下来[从智能体控制面板监控并引导会话][next-lesson]。

## 资源

- [关于自定义智能体][custom-agents]
- [在组织中使用自定义智能体的准备工作][org-custom-agents]
- [在企业中使用自定义智能体的准备工作][enterprise-custom-agents]

---

| [← 上一课：GitHub Copilot 云智能体][previous-lesson] | [下一课：监控和管理智能体 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../2-cloud-agent/
[next-lesson]: ../4-managing-agents/
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
[org-custom-agents]: https://docs.github.com/copilot/how-tos/administer-copilot/manage-for-organization/prepare-for-custom-agents
[enterprise-custom-agents]: https://docs.github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/prepare-for-custom-agents
