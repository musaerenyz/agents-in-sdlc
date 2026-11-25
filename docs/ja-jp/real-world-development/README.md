---
title: "実践的な開発"
slug: ja-jp/real-world-development
description: "現実的な開発シナリオと共有バックログを使って、ソフトウェア開発ライフサイクル全体で GitHub Copilot を実践します。"
authors:
  - geektrainer
lastUpdated: 2026-09-16
---

現実的なシナリオ、完成したアプリケーション、共有バックログを使って、ソフトウェア開発ライフサイクル全体で GitHub Copilot を活用します。変更の計画、コードの作成、動作のテスト、pull request のレビュー、エージェントを使った作業管理を繰り返し実践します。

## シナリオ

架空の企業 Tailspin Toys に新しく加わった開発者として、ボードゲームのクラウドファンディングを行う開発者向けテーマのサービスに取り組みます。チームのバックログは GitHub issue として登録済みで、すぐに着手できます。カテゴリーやパブリッシャーによる絞り込みなどの機能開発に加えて、アクセシビリティやコーディング標準などの品質改善も含まれています。

## 作業環境を選ぶ

GitHub Copilot は、さまざまな作業環境で利用できます。開発方法に合った環境を選んでください。各ワークショップは個別のセットアップから始まり、共通の Tailspin Toys シナリオを使用します。

### [GitHub Copilot CLI][cli]

ターミナルでエージェント型アシスタントを使います。Plan モードと Autopilot モード、指示、スキル、カスタムエージェント、Playwright MCP、Agent Merge、実用的なスラッシュコマンドを扱います。

### [GitHub Copilot app][app]

デスクトップアプリでエージェントセッションを並行して実行し、セッションモードを切り替え、キャンバスで共同作業し、GitHub issue と pull request を管理します。

### [GitHub Copilot cloud agent][cloud]

GitHub issue を非同期エージェントに割り当て、作業を案内して進捗を監視し、エージェントが作成した pull request をレビューします。

### [Visual Studio Code][vscode]

Visual Studio Code と GitHub Codespaces で GitHub Copilot を使います。Copilot Chat の agent mode、MCP サーバー、カスタムエージェントをエディターから離れずに扱います。

[cli]: cli/
[app]: app/
[cloud]: cloud/
[vscode]: vscode/
