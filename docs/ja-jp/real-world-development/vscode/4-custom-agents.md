---
title: "演習 4 - カスタムエージェント"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

## カスタムエージェントとは

GitHub Copilot の[カスタムエージェント][custom-agents-concept]を使うと、開発ワークフローの特定のタスクや分野に合わせた人工知能 (AI) アシスタントを作成できます。リポジトリの `.github/agents` フォルダーに Markdown ファイルを置き、対象タスクに役立つ指示、ベスト プラクティス、コーディング パターン、専門知識を定義します。チームの知識を再利用できるエージェントとしてまとめられます。たとえば、[Web Content Accessibility Guidelines (WCAG)][wcag] 準拠を確認するアクセシビリティ エージェント、安全なコーディング プラクティスに従うセキュリティ エージェント、一貫したテスト パターンを守るテスト エージェントを作成できます。

カスタムエージェントは、プロジェクト内の `.github/agents` フォルダー、またはユーザー共通の `~/.copilot/agents` にある Markdown ファイルで定義します。各ファイルには、少なくとも `name` と `description` を含む YAML frontmatter と、エージェントの動作、専門知識、指示を記載する Markdown プロンプトが必要です。

### カスタムエージェントとエージェントスキルの違い

カスタムエージェントと[エージェントスキル][agent-skills-concept]には共通点があります。どちらも主に Markdown ファイルで定義され、AI に作業方法を伝えます。簡潔に言えば、**カスタムエージェントは作業者、スキルはツール**です。

カスタムエージェントは独自のコンテキストウィンドウを持ち、スキルや別のエージェントを調整しながら作業できます。この演習では、アクセシビリティ カスタムエージェントがサイトをアクセシビリティ ガイドラインに照らしてレビューし、更新します。その際、pull request ワークフローのスキルやテスト実行を管理するスキルを呼び出すこともできます。

> [!NOTE]
> カスタムエージェントの作成に唯一の正解はありません。AI のほかの使い方と同様に、環境やシナリオに合わせてテストし、改善してください。

[custom-agents-concept]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
[agent-skills-concept]: https://docs.github.com/copilot/concepts/agents/about-agent-skills
[wcag]: https://www.w3.org/WAI/standards-guidelines/wcag/

## シナリオ

Tailspin Toys は、視覚能力や好みにかかわらず、すべての人がクラウドファンディング プラットフォームを利用できるようにしたいと考えています。最近のフィードバックでは、テキストと背景色のコントラストが十分でないため、現在のダークテーマが読みにくいという意見がありました。このアクセシビリティの課題に対処するため、デザイン チームは利用者が切り替えられるハイコントラスト モードを求めています。

この演習では、次の内容を学びます。

- 既存のアクセシビリティ カスタムエージェントを確認する。
- Copilot Chat のアクセシビリティ エージェントを使ってハイコントラスト モードを実装する。

アクセシビリティは重要なため、カスタムエージェントを使って機能をすばやく作成します。

## アクセシビリティ カスタムエージェントを確認する

アクセシビリティ用のカスタムエージェントはあらかじめ用意されています。内容を確認し、Copilot への指示を把握しましょう。

アクセシビリティ作業は絞り込み PR と分けるため、`main` から新しいブランチを作成します。

1. codespace に戻り、ターミナルを開きます。
2. 次のコマンドを実行します。

   ```bash
   git checkout main
   git pull
   git checkout -b accessibility-vscode
   ```

1. `.github/agents/accessibility.md` を開きます。
2. `name` フィールドと `description` フィールドを含む YAML frontmatter を確認します。

> [!CAUTION]
> カスタムエージェントには、`name` と `description` を含む frontmatter が必要です。

3. 続くセクションを確認します。アクセシブルなサイトのコード生成に関する主要な責務、ベスト プラクティス、HyperText Markup Language (HTML)・Cascading Style Sheets (CSS)・JavaScript のコード例、よくある問題が記載されています。

## Copilot Chat でカスタムエージェントを使う

VS Code の Copilot Chat ビュー下部にある agent picker には、`.github/agents` に定義されたカスタムエージェントが表示されます。カスタムエージェントを選択すると、その指示とツールに合わせてチャット セッションが設定されます。

> [!TIP]
> **Copilot Chat を開く**
>
> 次の作業を始める前に codespace に戻り、Copilot Chat パネルを開いて **New Chat** を選択し、新しい会話を始めます。モードとモデルの選択は演習によって異なります。必要な場合は手順内で説明します。

1. Chat ビューの agent picker で **Agent** が選択されていることを確認します。

   ![Chat ビューの agent picker](../../../_images/shared-chat-mode-selector.png)

2. Chat ビュー下部にある agent picker を選択します。既定では、使用中のエージェント (**default**) が表示されます。
3. 利用可能なエージェント一覧から **Accessibility agent** を選択します。
4. 次のプロンプトをアクセシビリティ エージェントに送信します。

    ```plaintext
    Add a high-contrast mode to the site. There should be a toggle for high contrast which the user can set, and the setting should persist across page reloads using local storage on the browser.
    ```

5. Copilot Chat がコードベースを調べて変更を提案し、プロジェクト ファイルを編集します。編集はファイルパスと差分とともにチャット内に表示されます。
6. エージェントが変更したファイルに合わせて、**Files changed** の表示が更新されることを確認します。

> [!NOTE]
> 処理には数分かかることがあります。Copilot は Astro component、CSS、関連テストなどのファイルを編集します。

次の演習では、作業中の変更を確認し、エージェントに追加の指示を送ります。

## まとめと次のステップ

このレッスンでは、GitHub Copilot の[カスタムエージェント][custom-agents]を確認しました。カスタムエージェントにチームの専門知識や標準をまとめ、特定のタスクや分野に合わせて Copilot を導けます。

次は、[エージェントの作業を監視して方向づけます][next-lesson]。変更を確認しながら、同じセッションにライトモードの切り替えを追加します。

## リソース

- [カスタムエージェントについて][custom-agents]
- [IDE でカスタムエージェントを作成する][creating-custom-agents-ide]
- [VS Code のカスタムエージェント][custom-agents-vscode]
- [カスタムエージェントの構成][custom-agents-config]
- [Awesome Copilot のカスタムエージェント][awesome-copilot-agents]

---

| [← 前のレッスン: Playwright MCP サーバーで機能をテストする][previous-lesson] | [次のレッスン: エージェントの監視と管理 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-mcp/
[next-lesson]: ../5-managing-agents/
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
[creating-custom-agents-ide]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/create-custom-agents-in-your-ide
[custom-agents-vscode]: https://code.visualstudio.com/docs/copilot/customization/custom-agents
[custom-agents-config]: https://docs.github.com/copilot/reference/custom-agents-configuration
[awesome-copilot-agents]: https://github.com/github/awesome-copilot/tree/main/agents
