---
title: "演習 4 - エージェントの監視と管理"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

これまでの演習では、ユーザー体験の改善と機能追加を目的に、GitHub Copilot cloud agent に3つのタスクを依頼しました。cloud agent は非同期かつ自律的に動作しますが、タスクの進捗を監視することも大切です。

GitHub.com の[agents ページ][agents-page]など、cloud agent に割り当てたタスクを管理するためのツールがあります。この mission control では、オープンな pull request (PR) があるエージェント タスクを確認できます。実行内容を調べたり、進行中のセッションに追加の指示を送ったりできます。

このレッスンでは、次の内容を学習します。

- agents ページで cloud agent のタスクを監視する。
- 実行中のセッションに追加機能を依頼する。

## シナリオ

ハイコントラスト モードの作成を依頼したあと、チームはライトモードも追加したいと考えました。サイトのスタイルと切り替え機能をすでに更新しているため、同じ作業に含めるのが自然です。エージェントに追加の指示を送り、両方のモードを実装するよう依頼します。

## Copilot cloud agent のタスクを確認する

Copilot cloud agent に割り当てたタスクの状況を確認します。

1. [Copilot agents ページ](https://github.com/copilot/agents)を開きます。
2. メイン領域と左側の領域にあるタスク一覧を確認します。コードのドキュメント更新、ゲーム変更用 API の生成、サイトへのハイコントラスト モード追加など、割り当てたタスクが表示されます。
3. 実行中のタスクを選択し、Copilot が行った作業を確認します。リポジトリのチェックアウト、作業環境の作成、MCP サーバーの設定、タスクを完了するための各種手順などが表示されます。

> [!NOTE]
> 表示される手順は、Copilot の作業状況や採用した方法によって異なります。

4. 右側に表示される PR ペインも確認します。PR と変更されたファイルを監視できます。

## cloud agent に追加の指示を送る

実行中のタスクを確認したので、ハイコントラスト モードの作業中にライトモードも追加するよう Copilot に依頼します。

1. ハイコントラスト モードを追加するセッションを選択します。セッション名は、Copilot が付けた名前や作業状況によって異なります。

    ![Mission control に表示されるアクセシビリティ セッション](../../../_images/ex6-accessibility-session.png)

2. セットアップが完了し、作業が始まるまで数分間セッションを確認します。作業開始後は、次のようなメッセージが表示されます。
3. **Steer active session while Copilot is working** ダイアログに次のプロンプトを入力します。

    ```plaintext
    While we are working on a high contrast mode, let's also add a light mode. There should be a switch for this mode as well where users can select their desired display mode.
    ```

    ![エージェント ページで、実行中の Copilot cloud agent に追加の指示を送るダイアログが強調表示された画面](../../../_images/ex6-steer-cloud-agent-task.png)

4. <kbd>Enter</kbd> を押してプロンプトを送信します。
5. Copilot がプロンプトを受け取り、作業に追加したことを確認します。

## Copilot の作業を待つ

Copilot は、更新されたタスクに取り組みます。メッセージを送信した時点で実行していた手順が終わると、追加の依頼を作業に取り込みます。

この処理には数分かかります。その間に、ここまでに学んだ内容を振り返りましょう。

## まとめと次のステップ

このレッスンでは、Copilot agents ページを使って GitHub Copilot cloud agent のタスクを監視し、案内しました。mission control では、実行中と完了済みのタスクを追跡し、作業内容を確認し、進行中のタスクの方向を変更できます。

次の内容を扱いました。

- mission control と agents ページで cloud agent のタスクを監視する。
- 実行中のセッションに追加機能を依頼する。

Copilot がアクセシビリティ機能を作成している間に、[Copilot が作成した pull request を改善します][next-lesson]。

## リソース

- [Copilot agents ページ][agents-page]
- [カスタムエージェントについて][custom-agents]

---

| [← 前のレッスン: カスタムエージェント][previous-lesson] | [次のレッスン: Copilot の作業を改善する →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
