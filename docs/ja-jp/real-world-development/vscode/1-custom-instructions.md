---
title: "演習 1 - カスタム指示 (VS Code)"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前のレッスン: 前提条件][previous-lesson] · [次のレッスン: Agent mode →][next-lesson]

生成 AI を使うときは、コンテキストが重要です。特定の方法でタスクを進める必要がある場合や、Copilot に知っておいてほしい背景情報がある場合は、その情報を利用できるようにします。コンテキストを共有する方法はいくつかあります。その中心となるのが[指示ファイル][instruction-files]です。指示ファイルを使うと、コード生成の指針を Copilot に伝えられます。

この演習では、次の内容を扱います。

- リポジトリのカスタム指示とパス単位の指示ファイルを通じて、プロジェクト固有のコンテキスト、コーディング ガイドライン、ドキュメント標準が Copilot にどう伝わるかを確認する。
- *現在の*指示を使って、フィルタリングに必要な最初のデータ部分（publishers helper）を作成する。
- `.github/copilot-instructions.md` にリポジトリ全体に適用する標準を追加する。
- 同じプロンプトを再実行し、生成コードに新しい標準が反映されることを確認する。
- 指示の更新とフィルタリングの基礎を `main` に commit し、次の演習で使えるようにする。

> [!CAUTION]
> 生成コードが、設定した標準から外れる場合があります。Copilot は確率的に動作します。この演習の目的は、指示を更新した後の動作の傾向を確認することであり、出力を1文字ずつ一致させることではありません。

## 指示ファイル

### シナリオ

Tailspin Toys には、開発プラクティスに関するガイドラインと要件があります。たとえば、次のとおりです。

- データ レイヤーには必ずユニットテストを用意します。
- ユーザー インターフェイス (UI) はダークモードにして、モダンな印象にします。
- TypeScript documentation (TSDoc) の doc comment を使ってコードにドキュメントを追加します。
- 各ファイルの先頭に、ファイルの役割を説明するコメント ブロックを追加します。

指示ファイルを使い、Copilot がこうしたプラクティスに沿ってタスクを進めるための情報を提供します。

### カスタム指示

カスタム指示でコンテキストや設定を伝えると、Copilot がコーディングスタイルや要件を理解しやすくなります。コーディング規約、ライブラリ、追加したいコメントの種類などを指定できます。リポジトリ全体に適用する指示も、特定のファイル種別に適用する指示も作成できます。

指示ファイルには次の2種類があります。

- `.github/copilot-instructions.md` は、リポジトリに関する**すべて**のリクエストで Copilot に送信される単一の指示ファイルです。技術スタック、プロジェクトの概要、ベスト プラクティスなど、多くの chat や CLI リクエストに関係するプロジェクト全体の情報を記載します。
- `.github/instructions/*.instructions.md` は、特定のタスクやファイル種別向けの指針を記載します。TypeScript や Astro などの言語、UI component の作成、ユニットテストの追加などに利用できます。

> [!NOTE]
> 統合開発環境 (IDE) では、指示ファイルは Copilot Chat のコード生成にのみ使われ、コード補完や next-edit suggestion には使われません。
>
> Copilot Chat、Copilot CLI、Copilot cloud agent は、コード生成時にリポジトリ レベルの指示ファイルと、`applyTo` frontmatter を持つ `*.instructions.md` ファイルの両方を使います。
>
> Copilot は [AGENTS.md や CLAUDE.md など、別の標準に基づく指示ファイル][custom-instructions-support]もサポートしています。

### 指示ファイルを管理するためのベスト プラクティス

指示ファイルの作成方法を詳しく扱うことは、このワークショップの範囲外です。サンプル プロジェクトには、代表的な方法が含まれています。

- `copilot-instructions.md` には、プロジェクトの説明、構造、全体に適用するコーディング標準などのガイダンスを記載します。
- `*.instructions.md` は、ユニットテスト、Astro component、データ レイヤーなどのファイル種別や、特定のタスク向けの指示に使います。
- 自然な言葉で明確に書き、コードの望ましい例と避けるべき例を示します。

指示ファイルの作り方にも、AI の使い方にも唯一の正解はありません。プロジェクトに合う方法を試して見つけてください。

> [!TIP]
> GitHub Copilot を使うプロジェクトでは、指示ファイルを充実させることをお勧めします。このプロジェクトには、[UI 更新][ui-instructions]や [Astro][astro-instructions]など、さまざまなタスク向けのファイルがあります。
>
> Copilot に指示ファイルを作成させることもできます。VS Code の **Configure Chat → Generate Agent Instructions** や Copilot CLI の `/init` など、環境によって操作が異なります。該当する操作は、それぞれの環境のレッスンで説明します。
>
> テンプレートや出発点を探す場合は、指示ファイルやカスタムエージェントなどを集めた [awesome-copilot][awesome-copilot] を確認してください。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## このプロジェクトのカスタム指示ファイルを確認する

このリポジトリには、中心となる `copilot-instructions.md` と、タスク別の `*.instructions.md` ファイルがあります。エディターまたは GitHub の Web UI で開いて確認します。

1. `.github/copilot-instructions.md` を開きます。
2. プロジェクトの概要と、**Agent notes**、**Code standards**、**Scripts**、**Repository Structure** などのセクションを確認します。**Code standards** にある **GitHub Actions Workflows** の指示にも注目してください。これらは Copilot とのやり取り全般に適用されます。
3. `.github/instructions` フォルダーを開きます。Astro ファイル、Drizzle データ レイヤー、テストなどの指示を確認します。
4. `.github/instructions/unit-tests.instructions.md` を開きます。先頭の `applyTo` フィールドが、リポジトリのルートからの相対 glob を使って対象ファイルを決めています。この例では、`**/*.test.ts` に一致する TypeScript のテストファイルが対象です。
5. このプロジェクトのユニットテスト作成に関する指示を確認します。
6. `.github/instructions/drizzle.instructions.md` を開き、末尾まで確認します。ほかの指示ファイルやプロジェクト内の既存ファイルへのリンクに注目してください。指示を再利用しやすい単位に分け、コード生成の参考例を Copilot に示せます。ここにあるパスはリポジトリのルートではなく、指示ファイルからの相対パスです。

> [!NOTE]
> `copilot-instructions.md` の **Code formatting requirements** セクションにはコーディング標準が記載されていますが、コード内のドキュメントはまだ必須ではありません。次の手順で、TSDoc の doc comment とファイル コメント ヘッダーのルールを追加します。

## 変更用のブランチを作成する

変更にブランチを使います。

1. 前の演習で作成した codespace に戻ります。
2. <kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押して、新しいターミナルを開きます。
3. 新しいブランチを作成して切り替えます。

   ```bash
   git checkout -b custom-instructions
   ```

## 指示を更新する*前に* Copilot Chat を使う

> [!TIP]
> **Copilot Chat を開く**
>
> 次の作業を始める前に codespace に戻り、Copilot Chat パネルを開いて **New Chat** を選択し、新しい会話を始めます。モードとモデルの選択は演習によって異なります。必要な場合は手順内で説明します。

カスタム指示の効果を確認するため、まず現在の指示でプロンプトを送信します。その後、指示を更新して同じプロンプトをもう一度送信します。

1. 前の演習で開いたエディター タブを閉じ、Copilot に不要なコンテキストを渡さないようにします。
2. `src/lib/publishers.ts` を開き、helper の配置先を示します。
3. Chat ビューの agent picker で **Agent** を選択し、Copilot がファイルを変更できるようにします。

   ![Chat ビューの agent picker](../../../_images/shared-chat-mode-selector.png)

4. 次のプロンプトを送信します。

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

5. Copilot がプロジェクトを調べ、helper とテストなどのファイルを更新します。
6. 提案された helper が型付き関数で、最初の引数に `db` client を受け取り、型付き publishers 配列を返すことを確認します。これは `src/lib/*.ts` に適用される `.github/instructions/drizzle.instructions.md` のデータ レイヤー規約に基づいています。
7. 提案されたコードに TSDoc doc comment とファイル コメント ヘッダーが**ない**ことを確認します。

> [!CAUTION]
> Copilot は確率的に動作するため、指示がなくても doc comment を追加する場合があります。その場合も問題ありません。指示を更新した後に動作がより一貫することが重要です。

## リポジトリ全体に適用する標準を追加する

`.github/copilot-instructions.md` にプロジェクト全体の標準を記載し、コード提案の質を高めます。

1. `.github/copilot-instructions.md` を開き直します。
2. 27 行目付近にある **Code formatting requirements** セクションを探します。既存の標準にコード内ドキュメントのルールがないことを確認します。
3. 既存の標準の直後に、次の Markdown 行を追加します。

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. `copilot-instructions.md` を保存します。

> [!TIP]
> 指示ファイルは、全体的なガイダンスを記載するリポジトリ レベル（`.github/copilot-instructions.md`）にも、特定の言語、ファイル種別、タスク向けの `*.instructions.md` にも作成できます。今回追加した doc comment のルールのようにプロジェクト全体に適用する標準は、リポジトリ レベルのファイルに記載します。

## プロンプトを再実行して変化を確認する

1. Copilot Chat に戻り、**New Chat** を選択して新しい会話を始めます。
2. `src/lib/publishers.ts` を選択し、Copilot が対象ファイルに注目できるようにします。
3. 先ほどと**同じプロンプト**を送信します。

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

4. 提案されたファイルの先頭に、次のようなコメント ブロックがあることを確認します。

   ```typescript
   /**
    * Publisher data-access helpers for the Tailspin Toys Crowd Funding platform.
    * Provides functions to retrieve publisher information from the database.
    */
   ```

5. 提案された関数に、次のような TSDoc comment があることを確認します。

   ```typescript
   /**
    * Returns a list of all publishers with their id and name.
    *
    * @param db - The Drizzle database client.
    * @returns A promise that resolves to an array of publisher objects.
    */
   ```

この標準を Copilot に伝え、次の演習で使う実際のコードに適用しました。

## 指示とブランチを commit、push する

指示ファイルもほかの資産と同じように、ソース管理で管理します。変更を commit してリポジトリに push します。

1. <kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押してターミナルを開きます。
2. 次のコマンドを実行し、指示と helper の変更を確認します。

   ```bash
   git status
   ```

3. 指示の更新と helper の基礎、さらに Copilot が helper のために変更したファイルを stage して commit します。

   ```bash
   git add .github/copilot-instructions.md src/lib/publishers.ts
   # If git status shows additional supporting updates (for example tests), add those files too.
   git commit -m "Add doc comment standards and publishers helper foundation"
   ```

4. ブランチをリポジトリに push します。

   ```bash
   git push -u origin custom-instructions
   ```

## pull request を作成してマージする

ブランチを push したら、バックログにあるドキュメント標準の issue に関連付けて pull request を作成します。GitHub ツールを使うよう Copilot に依頼します。

> [!NOTE]
> この演習では、Copilot に GitHub 上の操作を初めて依頼します。テンプレート プロジェクトには GitHub ツールがあらかじめ接続されています。Copilot が初めてツールを使うときに、VS Code から GitHub へのサインインを求められる場合があります。画面の案内に従ってください。後の演習では、この接続に使われる Model Context Protocol (MCP) を扱います。

1. codespace で Copilot Chat を開きます。
2. <kbd>Control</kbd>+<kbd>Command</kbd>+<kbd>I</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>I</kbd> (Windows/Linux) を押して Copilot Chat ビューを開き、agent picker で **Agent** が選択されていることを確認します。
3. 指示ファイルの更新に関する issue を探し、現在のブランチから pull request (PR) を作成して、指示の更新と今後のフィルタリング作業に使う publishers helper の基礎の両方を説明するよう Copilot に依頼します。

   ```text
   Find the issue related to updating the instructions file. Create a new PR from the current branch, highlight that the PR closes that issue, and include that we also added the publishers helper foundation for the upcoming filtering work.
   ```

4. Copilot が GitHub 操作の許可を求めたら、コマンドを確認し、適切な場合に **Allow** を選択します。
5. PR が作成されたら、次のプロンプトでマージし、ローカル ブランチを `main` に戻すよう依頼します。

   ```text
   Merge the PR into main. Then return to main locally, and pull the latest code so we are up to date.
   ```

6. Copilot が GitHub 操作やシェル コマンドの許可を求めたら、内容を確認して適切な場合に **Allow** を選択します。

GitHub Copilot の支援で pull request を作成し、マージしました。

## まとめと次のステップ

このプロジェクトで Copilot が指示ファイルからコンテキストを取得する方法を確認し、VS Code の Copilot Chat を使って次の作業を行いました。

- *既存の*指示を使ってコード生成プロンプトを送り、Copilot の出力を確認しました。
- `.github/copilot-instructions.md` にリポジトリ全体の標準を追加しました。
- 同じプロンプトを再実行し、提案されたコードに新しい標準が反映されることを確認しました。
- 指示の更新と helper の基礎を `main` に commit し、次の演習で使えるようにしました。

次は、Copilot がコードベース全体に機能を追加するときに、[agent mode で指示を活用します][next-lesson]。

## リソース

- [GitHub Copilot のカスタマイズに使う指示ファイル][instruction-files]
- [カスタム指示を作成するためのベスト プラクティス][instructions-best-practices]
- [Copilot 向けのカスタム指示を改善する5つのヒント][copilot-instructions-five-tips]
- [GitHub Copilot の個人用カスタム指示][personal-instructions]
- [Awesome Copilot — 指示ファイルなどのリソース集][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-agent-mode/
[instruction-files]: https://code.visualstudio.com/docs/copilot/copilot-customization
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[personal-instructions]: https://docs.github.com/copilot/customizing-copilot/adding-personal-custom-instructions-for-github-copilot
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
[awesome-copilot]: https://github.com/github/awesome-copilot
