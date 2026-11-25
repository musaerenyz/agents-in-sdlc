---
title: "演習 3 - Playwright MCP サーバーで機能をテストする"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前のレッスン: Agent mode][previous-lesson] · [次のレッスン: カスタムエージェント →][next-lesson]

agent mode で絞り込み機能を作成しました。pull request を開く前に、ブラウザーで動作を確認します。自分でアプリを操作する代わりに、**Playwright MCP サーバー**を接続し、Copilot に実際のブラウザーを操作させます。その後、ブランチを公開して PR を作成します。

この演習では、次の内容を学習します。

- Model Context Protocol (MCP) と、MCP サーバーが Copilot にツールを追加する仕組みを理解する。
- Playwright MCP サーバーをワークスペースに追加する。
- Copilot にブラウザーで絞り込み機能を手動テストさせる。
- ブランチを公開し、絞り込み作業の pull request を作成する。

## Model Context Protocol (MCP) とは

agent mode は、エディターの外部にあるツールを利用すると、さらに強力になります。Model Context Protocol (MCP) は Copilot が外部のツールやサービスと通信するための標準です。

![agent mode の内部構造と、コンテキスト、大規模言語モデル (LLM)、MCP サーバーや VS Code 拡張機能が提供するツールとのやり取りを示す図](../../../_images/ex1-mcp-diagram.png)

[Model Context Protocol (MCP)](https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/) を使うと、AI エージェントが外部のツールやサービスと通信できます。最新情報を取得したり、ツールを使って利用者に代わって操作したりできます。

これらのツールやリソースには、AI エージェントと外部のツールやサービスをつなぐ MCP サーバーを介してアクセスします。MCP サーバーは、既存の API や npm パッケージなどのローカル ツールとの通信を管理します。各 MCP サーバーは、AI エージェントが利用できるツールやリソースを提供します。

よく使われる MCP サーバーには、次のものがあります。

- **[GitHub MCP Server](https://github.com/github/github-mcp-server)**: GitHub リポジトリ、issue、pull request を管理する API を利用できます。
- **[Playwright MCP Server](https://github.com/microsoft/playwright-mcp)**: Playwright を使ったブラウザー自動化機能を利用できます。

ほかにもさまざまな MCP サーバーがあります。GitHub は、発見や貢献を促進するために [MCP registry](https://github.com/mcp) を提供しています。

> [!CAUTION]
> MCP サーバーはプロジェクト内のほかの依存関係と同様に扱ってください。利用前にソース コードをレビューし、公開元を確認して、セキュリティへの影響を検討します。信頼できる MCP サーバーだけを使い、機密リソースや操作へのアクセスを許可する際は注意してください。

## MCP の構成を確認する

`.vscode/mcp.json` ファイルで、この VS Code ワークスペースが利用できる MCP サーバーを設定します。

1. codespace で `.vscode/mcp.json` を開きます。
2. 次のような `github` サーバーが設定されていることを確認します。

    ```json
    {
      "servers": {
        "github": {
          "type": "http",
          "url": "https://api.githubcopilot.com/mcp/"
        }
      }
    }
    ```

この `github` エントリはプロジェクト テンプレートに含まれています。そのため、前の演習で Copilot がバックログの issue を参照できました。[リモート GitHub MCP サーバー][remote-github-mcp-server]を使うため、ローカルにインストールする必要はありません。VS Code は HTTP 経由で接続し、GitHub ツールを Copilot が初めて使うときに認証を求めます。

## Playwright MCP サーバーを追加する

次に、2つ目のサーバーを追加します。[Playwright MCP サーバー][playwright-mcp-server]は Copilot が操作できるブラウザーを提供し、機能のテストに使えます。

1. `.vscode/mcp.json` の `github` と並べて `playwright` エントリを追加し、ファイルが次のようになるようにします。

    ```json
    {
      "servers": {
        "github": {
          "type": "http",
          "url": "https://api.githubcopilot.com/mcp/"
        },
        "playwright": {
          "command": "npx",
          "args": ["@playwright/mcp@latest", "--headless"]
        }
      }
    }
    ```

2. ファイルを保存します。

リモートの `github` サーバーと異なり、`playwright` は**ローカル**サーバーです。VS Code が `npx @playwright/mcp@latest` を実行して起動します。`--headless` フラグを指定すると、画面にウィンドウを表示せずにブラウザーを実行します。デスクトップ環境がない codespace では、この設定が必要です。

> [!NOTE]
> Tailspin Toys ではエンドツーエンドテストに Playwright を使っているため、必要なブラウザーは通常インストール済みです。Copilot がブラウザー不足を報告した場合は、`npx playwright install chromium` を実行するよう依頼して、もう一度試してください。

## サーバーを起動して信頼する

VS Code は必要に応じて MCP サーバーを起動します。Copilot がサーバーのツールを初めて使うとき、VS Code はサーバーを起動して信頼するか確認します。

1. `.vscode/mcp.json` の `playwright` エントリの上に表示される **Start** 操作を選択し、サーバーを起動して設定を確認します。
2. VS Code からサーバーを信頼するか尋ねられた場合は、設定を確認して信頼し、サーバーを起動します。

手動での起動は省略できます。その場合、次の手順で Copilot がツールを初めて使うときに VS Code がサーバーを起動し、信頼の確認を求めます。

## 絞り込み機能をテストする

[Playwright MCP サーバー][playwright-mcp-server]を使うと、Copilot が実際のブラウザーを操作できます。エージェントはページを開き、移動し、絞り込みを適用して、その結果を確認して報告します。会話を離れずに機能を確認できます。

Playwright MCP サーバーは、スクリーンショットではなくページの[アクセシビリティ ツリー][playwright-mcp-server]を使います。ボタン、リンク、リスト項目などのラベル付き要素を扱うため、支援技術と同様の構造をエージェントが利用します。機能の確認に加えて、基本的なアクセシビリティの確認にもなります。

サーバーを接続し、アプリを実行してから、次のプロンプトでテストを依頼します。

1. Copilot Chat に次のプロンプトを送信します。

   ```text
   Using the Playwright MCP server, open a browser to the running app at http://localhost:4321 and verify the new game filtering feature:

   1. Go to the games page and note how many games are listed.
   2. Apply a category filter and confirm the list updates to only show games in that category.
   3. Clear it, then apply a publisher filter and confirm the list updates to that publisher.
   4. Combine a category and a publisher filter and confirm the results respect both.

   Report what you observe at each step, and call out anything that does not behave as expected.
   ```

Copilot は Playwright MCP サーバーを介してブラウザーを起動し、各手順を実行して結果を報告します。issue の受け入れ条件と照らして報告を確認してください。問題がある場合は、追加の質問をするか、コードを修正させてから pull request を作成します。

> [!NOTE]
> このテストには、`http://localhost:4321` で実行中のアプリが必要です。開発サーバーを停止した場合は、プロンプトを送る前に再起動します。Copilot が Playwright MCP サーバーを初めて使うときに、ブラウザーをダウンロードする場合があります。ブラウザー不足が報告されたら、`npx playwright install chromium` を実行するよう依頼して再試行します。

[playwright-mcp-server]: https://github.com/microsoft/playwright-mcp

## ブランチを公開して pull request を作成する

機能の動作を確認したので、チームがレビューできるよう pull request (PR) を作成します。まず `filtering-vscode` ブランチを公開します。

1. codespace の **Source Control** パネルを開き、Copilot が行った変更をレビューします。
2. **+** アイコンを選択して変更を stage します。
3. **Sparkle** ボタンを選択して commit メッセージを生成します。

   ![Copilot が行った変更を表示する Source Control パネル](../../../_images/ex3-source-control-changes.png)

4. **Publish** を選択してブランチをリポジトリに push します。

## pull request を作成する

github.com や GitHub command-line interface (CLI) を使って PR を作成できますが、ここでは GitHub Copilot に依頼します。関連する issue を探し、PR に関連付けてもらいます。

1. Copilot Chat パネルを開き、**New Chat** を選択します。
2. agent picker で **Agent** が選択されていることを確認します。Copilot が GitHub ツールを利用できます。
3. 次のプロンプトで PR の作成を依頼します。

   ```plaintext
   Find the issue in the repo related to filtering by category and publisher. Create a new pull request for the current branch, and associate it with the correct issue.
   ```

4. 必要に応じて **Continue** を選択し、情報の取得や操作を許可します。GitHub ツールを初めて使うときに認証を求められた場合は、画面の案内に従います。
5. Copilot が issue を検索し、該当する issue を見つけて PR を作成することを確認します。
6. Copilot が生成したリンクを選択して PR をレビューします。まだマージしないでください。

## まとめと次のステップ

この演習では、次の内容を学びました。

- Model Context Protocol (MCP) と、MCP サーバーが Copilot にツールを追加する仕組みを理解しました。
- Playwright MCP サーバーをワークスペースに追加しました。
- 機能を公開する前に、ブラウザーで手動テストしました。
- ブランチを公開し、絞り込み作業の pull request を作成しました。

機能のテストに MCP サーバーを使いましたが、MCP は Copilot を拡張する方法の1つです。次は、アクセシビリティ レビューなどに使える[カスタムエージェントを作成します][next-lesson]。

## リソース

- [MCP とは何か、なぜ注目されているのか][mcp-blog-post]
- [Microsoft Playwright MCP Server][playwright-mcp-server]
- [GitHub MCP Server][github-mcp-server]
- [GitHub MCP Registry][mcp-registry]
- [VS Code の MCP サーバー][vscode-mcp-config]

[previous-lesson]: ../2-agent-mode/
[next-lesson]: ../4-custom-agents/
[mcp-blog-post]: https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/
[github-mcp-server]: https://github.com/github/github-mcp-server
[mcp-registry]: https://github.com/mcp
[remote-github-mcp-server]: https://github.blog/changelog/2025-06-12-remote-github-mcp-server-is-now-available-in-public-preview/
[vscode-mcp-config]: https://code.visualstudio.com/docs/agents/reference/mcp-configuration
