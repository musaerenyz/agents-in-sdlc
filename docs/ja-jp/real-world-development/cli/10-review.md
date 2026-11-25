---
title: "レッスン 10 - 振り返りと次のステップ"
description: "Copilot CLI のワークフロー、2つの pull request、再利用可能なカスタマイズ、追加リソースを振り返ります。"
authors:
  - geektrainer
lastUpdated: 2026-09-29
---

GitHub Copilot CLI を使い、Tailspin Toys の一連のワークフローに取り組みました。次の作業を行いました。

- Codespace を準備し、Copilot CLI をインストールしてプロジェクトを確認し、絞り込みの issue を見つけました。
- 星評価を追加し、転送されたブラウザーで結果を確認して、最初の pull request (PR) を手動でマージしました。
- 絞り込みの issue から始めて、Plan モードで方針を決め、Autopilot モードで構築し、Interactive モードでレビューしました。
- カスタム指示でエージェントを導き、既存の `quality-checks` スキルをカスタマイズして、ユニットテスト、lint、型チェックを実行しました。
- Playwright Model Context Protocol (MCP) サーバーを追加し、実際のブラウザーで絞り込みを確認しました。
- 品質保証 (QA) カスタムエージェントを作成して選択し、要件、カバレッジ、スキルの結果、ブラウザーでの根拠を確認しました。
- 絞り込み機能全体をレビューし、Agent Merge による PR のマージを許可しました。
- コンテキスト、モデル、共有、任意の cloud agent への委任に使うスラッシュコマンドを確認しました。

## 作成したもの

このワークショップには、更新済みの `main` からそれぞれ別のブランチで作成する2つの PR があります。

1. **星評価:** 既存の `starRating` と未評価の状態をゲームカードに表示します。
2. **絞り込みと品質ワークフロー:** 絞り込み機能を実装し、指示を更新して機能に適用し、`quality-checks` の報告をカスタマイズし、QA プロファイルと関連テストを追加します。

絞り込みの計画から PR 作成まで、同じ会話とブランチを使いました。ワークショップの進行を簡潔にするため、作業を1つの PR にまとめています。

## さまざまな検証方法

自動テスト、自分でのブラウザー確認、MCP を使った Copilot のブラウザー確認など、複数の方法でコードを検証しました。`quality-checks` スキルはユニットテスト、lint、型チェックを実行し、新しい形式で報告しました。QA はこれらの結果を、要件とテスト カバレッジのレビューにまとめました。

追加するテストは、実際の不足を補うものである必要があります。QA の実行で追加テストが不要と判断される場合もあります。マージを許可する前にコードと根拠をレビューし、変更後は影響する検証結果を更新してください。

## ベスト プラクティス

Copilot に与えるコンテキストとツールが、作業に影響します。このワークショップでは、指示を更新し、スキルをカスタマイズし、QA プロファイルを作成し、MCP サーバーを設定しました。会話をまたいでカスタマイズを再利用し、チームのニーズに合わせて調整できます。指示は標準を設定し、スキルは反復可能な作業を説明し、カスタムエージェントは専門家の役割を定義し、MCP サーバーは外部ツールに接続します。エージェントの要約だけでなく、実際の変更とツールの結果を確認してください。

タスクに合わせて**モードとモデル**を選びます。**Plan** は実装前に方針を決めるとき、**Interactive** は対象を絞った変更に参加し続けるとき、**Autopilot** は範囲が明確なタスクを進めるときに使います。定型的な編集には高速なモデルを、複雑な作業には高性能なモデルを選びます。

ツールやインフラストラクチャだけでなく、コンテキストも重要です。何を、なぜ、どのように作るかを明確に説明すると、結果が変わります。

## さらに確認する

主要なワークフローを完了しました。Copilot CLI には、次のような機能もあります。

- `/review` でコードレビュー エージェントに変更を分析させる。
- `/rubber-duck` で問題を話し合い、別の視点を得る。
- `/fleet` で独立したサブタスクを並列に調整する。
- `/worktree` で別のタスクを分離する。
- `/delegate` でタスクを Copilot cloud agent に送る。

## 次のステップ

どのツールも、使い続けることで活用方法が上達します。実際のコード、個人のプロジェクト、作ろうと思いながら手をつけていなかった小さなアプリなどで試してください。得た知識をチームと共有し、チームの経験からも学びましょう。ドキュメントも引き続き確認してください。

ターミナルから Tailspin Toys をさらに拡張する場合は、任意の[Foundry Backer Concierge シリーズ][foundry]に進みます。別の作業環境と比較するには、[VS Code ワークショップ][vscode]、[GitHub Copilot app ワークショップ][app]、または [Copilot cloud agent ワークショップ][cloud]を確認してください。

## リソース

- [GitHub Copilot CLI について][about-cli]
- [Copilot CLI コマンド リファレンス][cli-reference]
- [Copilot CLI をカスタマイズする][customize-cli]
- [Copilot CLI で pull request を管理する][manage-prs]

[previous-lesson]: ../9-cli-power-tools/
[foundry]: ../8-foundry-agent/
[vscode]: ../../vscode/
[app]: ../../app/
[cloud]: ../../cloud/
[about-cli]: https://docs.github.com/copilot/concepts/agents/about-copilot-cli
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[customize-cli]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot
[manage-prs]: https://docs.github.com/copilot/how-tos/copilot-cli/use-copilot-cli/manage-pull-requests
