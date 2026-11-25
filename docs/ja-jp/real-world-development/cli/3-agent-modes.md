---
title: "レッスン 3 - エージェントモード: Plan と Autopilot"
description: "Plan で方針を決め、Autopilot で issue から絞り込み機能を構築し、Interactive モードで結果をレビューします。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

最初に小さな機能を追加しました。より大きな変更には、さらにしっかりしたプロセスが必要です。GitHub Copilot CLI は組織の既存のワークフローに沿って動作し、適切な方法で必要な機能を構築できます。このレッスンから、エージェントを使った一般的な開発プロセスに沿って作業します。まず issue から機能を作成し、コードの妥当性と機能の動作を確認して、プロジェクトにマージします。

> [!NOTE]
> 機能開発を続ける間、同じ会話とブランチを使います。通常はファイル種別ごとにブランチや pull request (PR) を分けますが、ここでは主要な概念に集中するため簡略化しています。

このレッスンでは、次の内容を学習します。

- GitHub issue から新しい Copilot 会話を始める。
- Plan モードで要件を決める。
- Autopilot モードで機能を実装する。
- コードをレビューする。
- 転送されたブラウザーで機能を手動検証する。

この後の機能開発では、リポジトリの指示を更新し、既存の `quality-checks` スキルをカスタマイズし、Model Context Protocol (MCP) による検証を追加して品質保証 (QA) エージェントを作成し、機能の pull request (PR) を開きます。

## シナリオ

Tailspin Toys のカタログが増えてきたため、利用者がカテゴリーとパブリッシャーでゲームを絞り込めるようにします。バックログの issue には機能が記載されていますが、カテゴリーの組み合わせ方法など、実装前に合意すべき詳細があります。Plan モードで決めた後、範囲を限定した実装を Autopilot に許可します。

## 背景

人工知能 (AI) コーディング エージェントを開発に取り入れても、基本的な手順は変わりません。一般的な流れは次のとおりです。

1. 必要な作業を説明する issue から始めます。
2. 構築する内容を計画します。
3. コードを作成してレビューします。
4. テストを実行して検証します。
5. 新しい機能を手動で検証します。
6. pull request (PR) を作成します。
7. コードレビューと継続的インテグレーションが成功したら、コードをマージします。

> [!NOTE]
> 詳細な手順はチームや組織によって異なりますが、多くは上記の流れを基本にしています。

この標準的な進め方に従うことで、AI が生成したコードが要件を満たし、手書きのコードと同じレビューと検証を受けるようにできます。

## 会話モード

**会話モード**は、エージェントにどの程度の自律性を与えるかを制御します。<kbd>Shift</kbd>+<kbd>Tab</kbd> でモードを切り替えます。

- **Interactive**: 開発者とエージェントが協力して進めます。エージェントは変更を提案し、次の操作に進む前に入力を待ちます。
- **Plan**: エージェントが計画を作成します。プロジェクト ファイルの編集はできません。
- **Autopilot**: エージェントが自律的にコードを書き、テストを実行し、完了と判断するまで反復します。

まず Plan モードで計画を確認し、その後 Autopilot で実装します。

## issue から作業を始める

絞り込み機能に着手する前に Codespace に戻り、リポジトリとターミナルを準備します。

1. Codespace に戻ります。停止している場合は、再起動します。
2. 星評価の PR がマージ済みであることを確認します。
3. ターミナルが開いていない場合は、<kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押します。
4. `main` を更新し、絞り込み作業用のブランチを作成します。

   ```bash
   git checkout main
   git pull --ff-only
   git checkout -b game-filters-cli
   ```

5. Copilot CLI を起動します。

   ```bash
   copilot --yolo
   ```

6. <kbd>Tab</kbd> を2回押して **Issues** タブを開きます。
7. <kbd>A</kbd> を押してすべての issue を表示します。
8. 矢印キーで **Allow users to filter games by category and publisher** というタイトルの issue を選択します。
9. <kbd>C</kbd> を押して issue をプロンプトに追加し、**Session** タブに戻ります。

プロンプトに `#7` などの番号が表示されます。`#` を使うと、GitHub 上の issue や pull request (PR) をコンテキストに追加できます。

## 絞り込み機能を計画する

計画を立てると、Copilot に作業を依頼する前に機能やタスクの進め方を決められます。複雑な作業では、計画に時間をかけることをお勧めします。Plan モードに切り替え、Copilot に計画の作成を依頼します。

1. <kbd>Shift</kbd>+<kbd>Tab</kbd> を押して Plan モードに切り替えます。プロンプトの下に **Plan** と表示されることを確認します。
2. 前の手順で追加した issue の参照の後に、次のプロンプトを入力します。

   ```plaintext
   Create a plan for implementing this feature.
   ```

Copilot はプロジェクトを調べ、適切な実装方法を検討して計画を作成します。

3. Copilot が絞り込みの動作について質問した場合は、希望に応じて回答します。ここでは正解は1つではありません。
4. 計画が完成したら、<kbd>Control</kbd>+<kbd>E</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>E</kbd> (Windows/Linux) を押して計画を展開します。
5. 計画を上から下まで確認します。
6. 自分の決定と異なる部分があれば、変更を依頼します。

## Autopilot を承認する

計画を作成してレビューしたら、機能の実装を依頼します。Autopilot により、Copilot は完了と判断するまで作業を反復します。

1. **Accept plan and build on autopilot (recommended)** またはインストール済みバージョンにある同等の選択肢を選択します。
2. プロンプトの下に **Autopilot** と表示されることを確認します。
3. Copilot が決定済みの計画に沿って反復し、コードを生成してテストを実行する様子を確認します。

> [!NOTE]
> 承認すると、すぐに実装が始まる場合があります。先に計画をレビューしてください。依存関係が不足している、またはポートが競合していると Copilot が報告した場合は、チェック完了と判断する前に環境の問題を解決します。

## 実装をレビューして検証する

生成されたコードは、ほかのコードと同様にマージ前にレビューします。コードを確認し、サイトを実行して動作も確かめます。

1. <kbd>Shift</kbd>+<kbd>Tab</kbd> を押して Interactive モードに切り替えます。モード表示が **Plan** または **Autopilot** ではないことを確認します。
2. `/diff` を入力し、絞り込みの実装とテストを確認します。
3. issue と計画時に決めた内容に照らし合わせます。
4. コードのレビューが終わったら <kbd>Esc</kbd> を押して差分画面を閉じます。
5. プロジェクトのチェック結果を確認し、失敗があれば修正を依頼します。

## 新しい機能を試す

コードの見た目だけでなく、実際に動くか確認します。前と同じようにアプリを起動し、Codespaces が転送するポートからサイトを開きます。

1. 次のプロンプトで Copilot にアプリの起動を依頼します。

   ```plaintext
   Start the app so I can try the filtering feature in my browser. Tell me the URL and leave the server running.
   ```

2. Codespaces でポート `4321` が利用可能になったら、**Open in Browser** を選択します。
3. カテゴリー、パブリッシャー、および計画で決めた組み合わせを試します。
4. リセット時と結果がない場合の動作が、issue と決定内容に合っていることを確認します。
5. Codespace に戻り、Copilot が起動した開発サーバーを停止するよう依頼します。

## まとめと次のステップ

異なる会話モードを使って機能を構築し、レビューしました。次の作業を行いました。

- GitHub issue から Copilot 会話を始めました。
- Plan モードで要件を決めました。
- Autopilot モードで機能を実装しました。
- コードをレビューしました。
- 転送されたブラウザーで機能を手動検証しました。

次は、[カスタム指示を使ってコード生成をさらに詳しく確認します][next-lesson]。

## リソース

- [GitHub Copilot CLI の Autopilot][autopilot]
- [Copilot CLI コマンド リファレンス][cli-reference]

[autopilot]: https://docs.github.com/copilot/concepts/agents/copilot-cli/autopilot
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[previous-lesson]: ../2-add-star-rating/
[next-lesson]: ../4-custom-instructions/
