---
title: "レッスン 7 - 品質保証 (QA) エージェントを作成して使う"
description: "issue の要件、quality-checks スキル、Playwright MCP を組み合わせる QA カスタムエージェントを作成します。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

これまでに `quality-checks` スキルを使って自動チェックを実行し、Playwright Model Context Protocol (MCP) でブラウザーの絞り込み操作を確認しました。次は、これらの機能を明確な品質保証 (QA) プロセスを持つカスタムエージェントにまとめます。

このレッスンでは、次の内容を学習します。

- カスタムエージェントが指示、スキル、MCP ツールをどのように使うかを確認する。
- 再利用可能な品質保証プロファイルを作成して確認する。
- QA エージェントを選択し、絞り込み issue に照らした報告を確認する。

## シナリオ

Tailspin Toys は pull request (PR) を作成する前に、要件、コード品質、自動チェック、テスト カバレッジ、ブラウザーでの動作を一貫して確認したいと考えています。カスタムエージェントを使うと、QA の手順を調整し、再利用できる報告を作成できます。

## カスタムエージェントとは

カスタムエージェントは、Markdown プロファイルで定義する専門化された Copilot です。プロファイルにはエージェントの目的、指示、利用できるツールを記述します。このワークショップでは `.github/agents/qa.agent.md` に QA の役割を定義し、Copilot CLI で選択します。

これまでに使ったカスタマイズには、それぞれ異なる役割があります。リポジトリの指示はチームの標準を説明し、`quality-checks` スキルは繰り返し実行できるチェックをまとめ、Playwright MCP はブラウザー ツールを提供します。QA プロファイルは、それらを使って要件を評価し、結果を報告するよう Copilot に伝えます。ほかのカスタマイズを置き換えたり、別の会話を必要としたりするものではありません。

## QA プロファイルを作成する

機能の PR を開く前に、再利用できる QA プロファイルを Copilot に作成させます。プロファイルに含めるチェックと守るべき境界を明確にします。

1. Codespace に戻り、絞り込みの会話が Interactive モードであることを確認します。
2. 既定のエージェントに、次のプロンプトでカスタムエージェントの作成を依頼します。

   ```plaintext
   Create a custom agent named QA in .github/agents/qa.agent.md. It should check features against their issues and agreed requirements, follow the repository instructions, run the quality-checks skill, use Playwright MCP to verify behavior, and add tests when coverage is missing.

   Have it report each requirement as pass, fail, or blocked with supporting evidence. It must ask before changing implementation code, and it must not commit changes or open pull requests.

   Just create the profile for now so I can review it.
   ```

## プロファイルを確認する

新しいエージェントを使う前に、意図した QA の手順と作業範囲が含まれていることを確認します。検証だけを依頼したときに、エージェントが不完全な手順を実行したり、実装コードを広範囲に変更したりするのを防ぎます。

1. `/diff` を入力し、`.github/agents/qa.agent.md` を開きます。
2. frontmatter を確認します。`description` は必須で、`name` は省略可能です。`name` を含めると表示名が明確になります。
3. プロファイルの指示を読み、QA が要件を確認し、リポジトリの指示に従い、`quality-checks` スキルと Playwright MCP を使うことを確認します。
4. 根拠を示した報告を行い、実装コードの変更前に確認を求め、commit や pull request の作成を行わないことを確認します。
5. いずれかの責務や境界が抜けていた場合は、続行する前に既定のエージェントへ修正を依頼します。

## issue に対して QA を実行する

Copilot CLI は会話の開始時にプロジェクトのエージェントを読み込みます。プロファイルを作成した後、同じ絞り込みの会話を再開し、QA を選択します。これにより、issue と計画の内容を使ってレビューできます。

1. 次のプロンプトで QA エージェントを有効にします。

   ```plaintext
   /agent QA
   ```

> [!NOTE]
> 作成直後は、利用可能なエージェント一覧に表示されない場合があります。その場合も、上記のコマンドで有効にできます。

2. 次のプロンプトで、機能のレビューを QA エージェントに依頼します。

   ```plaintext
   Review the filtering feature against the issue and the decisions in our plan. Is it ready for a PR?
   ```

3. QA エージェントが作業する様子を確認します。
4. 完了したら、報告を読みます。

## まとめと次のステップ

再利用可能な専門家の役割をワークフローに追加し、その成果をレビューしました。次の内容を行いました。

- カスタムエージェントが指示、スキル、MCP ツールを使う方法を確認しました。
- 再利用可能な品質保証プロファイルを作成して確認しました。
- QA エージェントを選択し、絞り込み issue に照らした報告を確認しました。

実装、スキルの更新、QA プロファイル、テスト、検証報告の準備ができました。次は、[変更を機能 PR にまとめ、Agent Merge を使います][next-lesson]。

## リソース

- [Copilot CLI 用のカスタムエージェントを作成する][create-agents]
- [カスタムエージェントの構成][agent-config]

[previous-lesson]: ../6-mcp-playwright/
[next-lesson]: ../8-create-pull-request/
[create-agents]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/create-custom-agents-for-cli
[agent-config]: https://docs.github.com/copilot/reference/custom-agents-configuration
