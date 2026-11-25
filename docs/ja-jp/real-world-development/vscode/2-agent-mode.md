---
title: "演習 2 - Copilot Agent Mode で機能を追加する"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

小さなアプリケーションの変更でも、複数のファイルを更新したり、テストを実行したりすることがあります。開発者は、必要なファイルを探し、変更を加え、テストを実行し、問題を調査して不足した変更を補います。作業は何度も続くことがあります。

このような作業には Copilot Agent Mode が役立ちます。

Copilot Agent Mode は統合開発環境 (IDE) 内で、より自律的に作業します。開発者と同様に、プロジェクト構造を調べ、必要な変更を複数のファイルに加え、テストなどのタスクを実行し、見つかった問題を修正します。ここでは、Agent Mode を使ってサイトに機能を追加します。

> [!NOTE]
> 名前は似ていますが、agent mode と cloud agent は異なる用途の機能です。Agent mode は IDE 内で作業し、すばやいフィードバックと対話を可能にします。Cloud agent は issue と pull request を介してやり取りし、チームの一員のように非同期で作業します。

この演習では、次の内容を学習します。

- Copilot Agent Mode がプロジェクトを調べ、関連ファイルを特定し、複数ファイルを連携して変更する。
- GitHub Copilot Agent Mode でユーザー インターフェイス (UI) とデータ レイヤーに機能を実装する。
- マージ前に、Copilot Agent Mode が作成した変更とテストをレビューする。

## シナリオ

ゲームの一覧が増えてきたため、カテゴリーとパブリッシャーで絞り込めるようにします。前の演習では publishers helper を追加しました。ここでは Copilot Agent Mode を使って、残りのデータ レイヤー、UI、テストの作業を仕上げます。

## Tailspin Toys の Web サイトを実行する

変更を加える前に、現在の機能を把握するため Tailspin Toys の Web サイトを確認します。

この Web サイトは、開発者向けテーマのボードゲーム クラウドファンディング プラットフォームです。ゲーム一覧と詳細を表示する単一の Astro アプリで、ビルド時に静的 HyperText Markup Language (HTML) を生成します。ページは frontmatter 内で Drizzle object-relational mapping (ORM) を使ってローカル SQLite データベースを直接検索します。別のバックエンド application programming interface (API) やクライアント側 UI フレームワークはありません。再利用可能なデータアクセス helper は `src/lib/` に置き、インタラクティブな機能は標準 Document Object Model (DOM) API を使う小さな Astro `<script>` で追加します。

### Web サイトを起動する

Web サイトを実行するには、`npm run dev` タスクを使います。

1. codespace に戻ります。前の演習後であれば、`main` にいるはずです。
2. <kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押して、codespace 内にターミナルを開きます。
3. 絞り込み作業用のブランチを作成して切り替えます。

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. 次のコマンドで Web サイトを起動します。

   ```bash
   npm run dev
   ```

   Astro 開発サーバーの準備ができると、次のような URL が表示されます。

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> `http://localhost:4321` を開くよう促すダイアログが表示された場合は、**x** を選択して閉じます。

5. ターミナルに表示された Astro サーバーのアドレス `http://localhost:4321` を、<kbd>Command</kbd> (Mac) または <kbd>Ctrl</kbd> (Windows/Linux) を押しながら選択して、Web サイトを開きます。

> [!NOTE]
> codespace のターミナルで localhost の URL を選択すると、自動的に `https://<your-codespace-name>-4321.app.github.dev/` に転送されます。これは Web サーバーをホストする codespace へのプライベート トンネルです。

### Web サイトを確認する

サイトを起動したら、機能を確認します。主なページは次のとおりです。

- **Home Page**: ボードゲームのタイトル、画像、説明を一覧表示します。
- **Game Details Page**: ゲームを選択すると、タイトル、説明、パブリッシャー、カテゴリーなどの情報を表示します。

## Copilot でバックログを確認する

> [!TIP]
> **Copilot Chat を開く**
>
> 次の作業を始める前に codespace に戻り、Copilot Chat パネルを開いて **New Chat** を選択し、新しい会話を始めます。モードとモデルの選択は演習によって異なります。必要な場合は手順内で説明します。

サイトの初期実装は動作しますが、機能を拡張します。テンプレートからプロジェクトを作成すると、issue のバックログも自動的に作成されています。GitHub Copilot に内容を表示するよう依頼します。

1. Chat ビューの agent picker で **Agent** を選択します。Agent は自律的に計画し、複数ファイルを変更し、ターミナル コマンドを実行してツールを呼び出します。

   ![Chat ビューの agent picker](../../../_images/shared-chat-mode-selector.png)

2. 利用可能なモデル一覧から **Claude Sonnet 4.5** を選択します。

> [!CAUTION]
> このワークショップの作成者が特定のモデルを推奨するものではありません。ワークショップ作成時に Claude Sonnet 4.5 を使用したため、手順にも記載しています。提案をある程度揃えることが目的ですが、LLM は確率的に動作するため、異なる提案が表示されることがあります。これは通常の動作です。

> [!NOTE]
> 確率的な動作のため、Copilot が別の MCP コマンドを使うことがありますが、タスクを完了できるはずです。

3. 次のプロンプトを Copilot に送信して、バックログについて尋ねます。

   ```plaintext
   Please show me the backlog of items from my GitHub repository. Help me prioritize them based on those which will be most useful to the user.
   ```

4. **Continue** を選択して、すべての issue を一覧表示するコマンドを実行します。
5. Copilot が生成した issue の一覧を確認します。

Copilot が利用者にとって有益だと判断した順に、項目を優先順位付けしたことを確認します。

## 指示ファイルを確認する

コード生成を開始する前に、Copilot にコンテキストを渡す指示ファイルを確認します。Web サイトに機能を追加する方法を説明する [user interface (UI)][ui-instructions] ファイルを使います。

1. codespace で `.github/instructions/ui.instructions.md` を開きます。
2. 機能追加に関する全体的なガイダンスを確認します。アーキテクチャの概要、component 設計、テスト容易性、アクセシビリティの原則、Astro、Tailwind CSS、Drizzle などのファイル種別向けの指示へのリンクが含まれます。

> [!TIP]
> 指示ファイルから、ほかの指示ファイルやプロジェクト内のファイルを参照できます。パスは指示ファイルからの相対パスです。指示を再利用しやすく小さな単位に分け、例やテンプレートを示せます。

## 絞り込み機能を実装する

絞り込み機能を仕上げるには、少なくとも次の3種類の変更が必要です。

- データ レイヤー (`src/lib/`) の絞り込みロジックを追加または調整する。
- 絞り込みの動作をテストする。
- ゲーム一覧ページに絞り込み UI を追加する。

さらに、コードベースにマージする前にテストを実行し、成功させる必要があります。Copilot Agent Mode にこれらの作業を依頼します。

1. 現在の Copilot 会話を続けるか、**New Chat** を選択して新しい会話を開始します。
2. **Add Context**、**Instructions**、**ui** の順に選択します。

   ![UI 指示ファイルを選択する例](../../../_images/ex3-select-instructions-file.png)

3. Chat ビューの agent picker で **Agent** が選択されていることを確認します。

   ![Chat ビューの agent picker](../../../_images/shared-chat-mode-selector.png)

4. モデルが **Claude Sonnet 4.5** であることを確認します。
5. バックログ内の関連 issue に基づいて機能を実装するよう、次のプロンプトで依頼します。

   ```plaintext
   Please update the site to include filtering by publisher and category based on the requirements from the related GitHub issue in the backlog. A publishers helper already exists from the previous exercise, so preserve and refine that work as needed while completing the remaining data-layer, UI, and tests. Ensure all tests are passing before completion. The server is already running, so you do not need to start it up.
   ```

6. Copilot がプロジェクトを調べ、データ レイヤー、UI、テストに関係するファイルを探してから、ファイルを変更しテストを実行する様子を確認します。

   ![Copilot がプロジェクト ファイルを調べている画面](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Copilot はプロジェクトの調査、ファイルの編集、テストの実行などを行うため、複雑さやコードベースによって数分かかることがあります。作業中、エディターに **Keep** と **Undo** が表示される場合があります。完了後に変更全体を確認できるため、作業中に選択する必要はありません。

7. Copilot に求められたら、**Continue** を選択してテストを実行します。

   ![Copilot Chat がテスト実行の確認を求める画面](../../../_images/ex3-agent-mode-run-tests.png)

8. 作業中に待機したり、テストが失敗したりする場合があります。Copilot はコード生成とテストを繰り返し、エラーがないと判断するまで作業を続けます。

   ![Copilot Agent Mode の会話が完了した画面](../../../_images/ex3-agent-mode-proposed-changes.png)

9. 生成されたコードに問題がないか確認します。

> [!CAUTION]
> Copilot やほかの AI ツールが生成したコードは、必ずレビューしてください。

10. Web サイトを開いているブラウザーに戻り、新しい機能を確認します。
11. 動作とコードを確認したら、Copilot Chat ウィンドウで **Keep** を選択します。

## まとめと次のステップ

この演習では、GitHub Copilot Agent Mode を使って Tailspin Toys に機能を追加しました。次の内容を学びました。

- Copilot Agent Mode が UI とデータ レイヤーに機能を実装する。
- Copilot Agent Mode がプロジェクトを調べ、関連ファイルを特定し、複数ファイルを連携して変更する。
- コードベースにマージする前に、Copilot Agent Mode が作成した変更とテストをレビューする。

次は、[Playwright MCP サーバーで機能をテストし][next-lesson]、pull request を作成します。

### おまけの演習 — ページ分割を実装する

ゲーム一覧が増えると、ページ分割が必要になります。この演習で学んだことを使い、Copilot にページ分割の実装を依頼します。既存の指示ファイルを使うこと、ページサイズを利用者が選択するか固定値にするかを検討すること、希望する実装に必要な情報をプロンプトに含めることに注意してください。Copilot に追加のコンテキストを渡し、変更を繰り返し依頼することもできます。これは Copilot を使う通常のワークフローです。

## リソース

- [Copilot の ask、edit、agent mode: それぞれの役割と使いどころ][choose-mode]
- [VS Code の agent mode][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
