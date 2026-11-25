---
title: "演習 2 - GitHub Copilot cloud agent"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 前のレッスン: カスタム指示][previous-lesson] |
|:--|

多くの組織が技術的負債を抱えています。解決されていないセキュリティ問題、更新が必要なレガシー コード、実装する時間がなくバックログに残った機能要求などがその例です。GitHub Copilot cloud agent は、コードの更新や機能の追加などを自律的に実行できます。作業が終わると、人の開発者がレビューできる draft pull request (PR) を作成します。時間のかかる作業を委任し、開発者がより重要な作業に集中できるようになります。

このレッスンでは、Copilot cloud agent を使って次の内容を扱います。

- コード生成環境をカスタマイズする。
- 操作を安全に行う。
- issue の範囲を明確にする重要性を理解する。
- issue を Copilot に割り当てる。

## シナリオ

Tailspin Toys は、解消したい技術的負債を抱えています。最初のサイトを作成した開発者は、ドキュメントを十分に整備していませんでした。まず、アプリケーションのすべての export 関数に TSDoc の doc comment を追加します。

また、デザイン チームはゲームを見つけやすくしたいと考えています。ゲーム詳細ページに、同じカテゴリーのほかのゲームを表示する関連ゲーム機能を追加し、ユーザー体験 (UX) の受け入れテストに使える状態にします。これは現在のブロッカーですが、優先度がより高い issue がほかにあります。

こうした作業は優先順位が下がりやすい一方、Copilot cloud agent に適しています。非同期で作業を依頼し、開発者は別のタスクに取り組み、あとから成果をレビューできます。

## GitHub Copilot cloud agent について

[GitHub Copilot cloud agent](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent) は、人の開発者と同様にバックグラウンドで作業します。[GitHub issue を Copilot に割り当てる][assign-issue]方法などがあります。割り当て後、Copilot は進捗を記録する draft PR を作成し、環境を準備してタスクに取り組みます。作業中または完了後にセッションを確認できます。解決案をレビューできる状態になると、PR で通知されます。

## 範囲を明確にした指示の重要性

GitHub Copilot に魔法はありません。考える作業を省略できるわけではありません。一見簡単な作業にも複雑さがあるため、[cloud agent に依頼するタスクの範囲を慎重に決めてください][cloud-agent-best-practices]。AI ペア プログラマーとして扱い、段階的に進めながら、学び、試し、改善します。生成 AI を加えてもソフトウェア開発の基本は変わりません。

## このリポジトリのカスタム指示

前の演習では、カスタム指示と Copilot への影響を確認しました。まだ確認していない場合は、cloud agent に作業を依頼する前に、このリポジトリに用意された指示ファイルを読み取り専用で確認してください。後で結果をレビューするときに、指示の影響を把握できます。

GitHub Web UI または起動済みの codespace で次のファイルを開きます。

- `.github/copilot-instructions.md` — **Code standards** セクションを確認します。特に TypeScript の規約と TSDoc doc comment に関する要件に注目してください。
- `.github/instructions/unit-tests.instructions.md` — `applyTo` frontmatter を確認します。指示が `**/*.test.ts` に適用されます。

次のセクションで `Code lacks documentation` issue を cloud agent に割り当てます。生成された PR では、これらの指示に基づく TSDoc doc comment、TypeScript の規約、コメント ヘッダーを確認してください。後の演習でも PR をレビューするときに改めて確認します。

## Copilot cloud agent の開発環境を準備する

コードの作成には、通常、適切な状態を整えるための環境設定やスクリプトが必要です。Copilot にタスクを割り当てる場合も同様です。cloud agent は [GitHub Actions][github-actions] を使って作業環境を準備します。

`.github/workflows/copilot-setup-steps.yml` に設定する[専用のセットアップ ワークフロー][setup-workflow]を作成すると、必要な開発ツールと依存関係を利用できるようになります。このワークショップでは、Node.js、プロジェクトの依存関係、エンドツーエンドテスト用のブラウザー、単一 Astro アプリ用にマイグレーションと seed を適用した SQLite データベースを利用できるよう、あらかじめ設定されています。

```yaml
name: "Copilot Setup Steps"

# リポジトリの "Actions" タブからセットアップ手順をテストできます。
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # *手順*に必要な最小限の権限を設定します。Copilot の操作には専用のトークンが付与されます。
    permissions:
      # 依存関係のインストールなど、セットアップ手順の一部としてリポジトリを clone する場合は `contents: read` 権限が必要です。セットアップ手順で clone しない場合は、手順の完了後に Copilot が自動的に clone します。
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # フロントエンド / アプリのセットアップ - Node.js (アプリ全体は Astro + Drizzle/libSQL)
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

      # ビルドとテストでデータを使えるように、ローカル SQLite データベースをマイグレーションして seed します。
      - name: Set up the database
        run: npm run db:setup
```

このファイルは一般的な GitHub ワークフローと同じ形式ですが、次の点を確認してください。

- `copilot-setup-steps` という単一の job があり、Copilot が pull request の作業を始める前に GitHub Actions で実行されます。
- `workflow_dispatch` トリガーにより、リポジトリの **Actions** タブから手動で実行できます。Copilot の実行を待たずにワークフローをテストできます。

## ドキュメントを追加する

多くのプロジェクトでは、ドキュメントが古いか不足しています。こうした技術的負債は放置されやすく、生産性を下げ、コードベースの保守や新しいメンバーの受け入れを難しくします。Copilot はドキュメント作成を支援できるため、cloud agent に依頼するのに適しています。成果は後の演習で確認します。

1. 新しいブラウザータブで github.com のリポジトリを開きます。
2. **Issues** タブを選択します。
3. **New issue** を選択して、新しい issue ダイアログを開きます。
4. **Blank issue** を選択します。
5. **Title** に `Code lacks documentation` と入力します。
6. **Description** に次の内容を入力します。

    ```plaintext
    Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
    ```

7. **Create** を選択して issue を作成します。
8. 右側の **Assign to Copilot** を選択して割り当てダイアログを開きます。

  ![Issue に Copilot を割り当てる画面](../../../_images/shared-assign-copilot.png)

9. **Assign** を選択します。
10. **Pull Requests** タブを選択します。
11. 新しく作成された PR を開きます。タイトルは `[WIP]: Code lacks documentation` のような名前です。一覧に PR が表示されない場合は、少し待ってからブラウザーを更新します。
12. 数分後、Copilot が TODO リストを作成したことを確認します。

> [!NOTE]
> Copilot が PR に TODO リストを追加するまで数分かかる場合があります。前述のワークフローを実行して環境を作り、プロジェクトを分析して、作業方法を検討しているためです。

13. TODO リストと、Copilot が実行する作業を確認します。
14. pull request のタイムラインを下にスクロールし、Copilot が issue に取り組み始めたことを示す更新を確認します。
15. **View session** ボタンを選択します。

  ![Copilot のセッションを表示する画面](../../../_images/ex4-view-session.png)

> [!CAUTION]
> 更新された表示を確認するため、ページの再読み込みが必要な場合があります。

16. 実行中のセッションをスクロールして、Copilot の作業を確認します。コードを調査して状態を把握し、計画を検討したうえでコードを作成する様子を確認できます。

完了まで数分かかる場合があります。Copilot cloud agent の主な目的は、非同期に作業して開発者が別のタスクに取り組めるようにすることです。この利点を活かすため、別のタスクも Copilot cloud agent に割り当て、その間にアプリケーションの機能を作成します。

## ゲーム詳細ページに関連ゲームを追加する

GitHub Copilot cloud agent の利点の1つは、複数の作業を分けられることです。デザイン チーム向けに関連ゲームを追加する作業を Copilot に割り当て、別のタスクに時間を使いましょう。

1. github.com のリポジトリを開きます。
2. **Issues** タブを選択します。
3. **New issue** を選択して新しい issue ダイアログを開きます。
4. **Blank issue** を選択します。
5. **Title** に `Show related games on the game details page` と入力します。
6. **Description** に次の内容を入力します。

    ```markdown
    We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:

   - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
   - Show a "Related games" section on the game details page that uses the helper
   - Handle the case where a game has no related games
   - There should be unit tests created for the new helper
   - Before creating the PR, ensure all tests pass
    ```

7. **Create** を選択して issue を作成します。
8. 右側の **Assign to Copilot** を選択して割り当てダイアログを開きます。

  ![Issue に Copilot を割り当てる画面](../../../_images/shared-assign-copilot.png)

9. **Assign** を選択します。

まもなく、Copilot が作業を開始したことを示す 👀 が issue の最初のコメントに表示されます。

![Copilot が issue に取り組んでいることを示す目の絵文字](../../../_images/ex4-issue-eyes-emoji.png)

Copilot は2つ目の依頼に取り組んでいます。Copilot cloud agent は software engineer (SWE) と同様に作業するため、常に監視する必要はありません。完了後に成果をレビューします。その間に、カスタムエージェントの作成と使用を進めます。

## まとめと次のステップ

このレッスンでは、[GitHub Copilot cloud agent][copilot-agents]を確認しました。cloud agent に issue を割り当て、非同期に作業させることができます。技術的負債への対応、新機能の作成、フレームワーク間のコード移行などに利用できます。

次の内容を扱いました。

- コード生成環境をカスタマイズする。
- 操作を安全に行う。
- issue の範囲を明確にする重要性を理解する。
- issue を Copilot に割り当てる。

cloud agent がバックグラウンドで作業している間に、カスタムエージェントを作成して使います。[Copilot cloud agent は MCP サーバーも利用でき][cloud-agent-mcp]、前のモジュールで扱ったカスタム指示も適用されます。

## リソース

- [Copilot cloud agent について][copilot-agents]
- [GitHub issue を Copilot に割り当てる][assign-issue]
- [Copilot cloud agent のセットアップ ワークフローに関するベスト プラクティス][cloud-agent-best-practices]

| [次のレッスン: カスタムエージェント →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
