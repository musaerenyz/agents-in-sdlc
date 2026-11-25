---
title: "レッスン 12 - GitHub Pages でサイトをデプロイする"
description: "GitHub Copilot app を使って Pages ワークフローを設定し、pull request をレビューして、公開された Space Quiz を確認します。"
authors:
  - jamesmontemagno
lastUpdated: 2026-10-12
---

Space Quiz を GitHub Pages にデプロイして、誰でもアクセスできる場所に公開します。Copilot はワークフローを準備し、デプロイを案内できますが、変更をレビューし、サイトが公開されたことを確認するのは開発者の役割です。

このレッスンでは、次の内容を学習します。

- Copilot に GitHub Pages のデプロイ設定を依頼する。
- マージ前にワークフローと pull request をレビューする。
- デプロイの進行を確認し、公開されたサイトを検証する。

## シナリオ

Space Quiz はすでに GitHub リポジトリにありますが、利用者が簡単に試せる方法はまだありません。単一ファイルのサイトを GitHub Pages で公開すれば、サーバーや実行時の依存関係を追加せずに公開 URL からアクセスできるようになります。

## Copilot にデプロイの準備を依頼する

> [!WARNING]
> pull request の作成により、コードが commit および push される場合があります。このプロンプトを送る前に、承認設定と送信先のリポジトリを確認してください。**Approve all** を有効にしている場合、個別の確認は表示されません。

`space-quiz` の Copilot app セッションで、デプロイ準備を依頼します。

1. 次のプロンプトを送信します。

   ```plaintext
   Prepare a GitHub Pages deployment for this repository. Create a GitHub Actions workflow that builds the website if needed and deploys it to GitHub Pages after changes reach the default branch. Add a README describing the app and how to run it locally if one does not already exist. Create a pull request with these changes and report its link and any required Pages settings or permissions. Do not merge, enable auto-merge, change Pages settings, or deploy. Stop and wait for my review.
   ```

## ワークフローと pull request をレビューする

1. README は、リポジトリに既存の README がない場合にのみ追加されていることを確認します。
2. pull request の変更をレビューします。ワークフローのビルド手順とデプロイ手順、トリガー、権限を確認してください。
3. ワークフローが、マージされていない pull request ではなく、デフォルト ブランチからデプロイすることを確認します。
4. 必要な修正を依頼し、更新された差分をレビューします。マージを許可する前に、必須の pull request チェックが成功するまで待ちます。

## マージとデプロイを許可する

> [!WARNING]
> 次のプロンプトは、レビュー済みの pull request のマージ、リポジトリの Pages 設定の変更、サイトの公開をエージェントに許可します。レビューを完了し、リポジトリと pull request を確認してから送信してください。承認設定によっては、これらの操作が追加確認なしで実行されます。

レビューした pull request の番号を使って、別のプロンプトを送信します。

1. `<PR_NUMBER>` をレビューした pull request の番号に置き換えます。
2. 次のプロンプトを送信します。

   ```plaintext
   I have reviewed pull request <PR_NUMBER> in this repository and authorize its merge and deployment. Enable GitHub Pages using GitHub Actions, merge that pull request only after required checks pass, and monitor the deployment until it succeeds. Report the final URL of the live website. If you lack permission to enable Pages or merge, tell me exactly what I need to approve or do; do not report success until the site is live.
   ```

![マージされた space-quiz pull request、Build website、Upload artifact、Deploy to Pages の各ステップが成功した GitHub Actions の Deploy website 実行、および yourname.github.io/space-quiz/ で公開された Space Quiz の3段階の図](../../../_images/first-steps-app-pages-deploy.svg)

## デプロイを確認する

デプロイの実行と公開されたサイトを確認し、エージェントの報告だけで完了と判断しないようにします。

1. GitHub Actions のデプロイ実行を開き、成功するまで確認します。
2. 報告された URL を開き、Space Quiz が読み込まれることを確認します。

エージェントが Pages の有効化や pull request のマージを実行できない場合は、提示された具体的な手順に従います。デプロイが成功し、公開サイトが読み込まれるまでは完了として扱わないでください。

## まとめと次のステップ

Space Quiz が公開され、エージェントの報告に頼らずデプロイを確認しました。続けて[レッスン 13「レビューと次のステップ」][next-lesson]に進みます。

[next-lesson]: ../13-review/
