---
title: "演習 1 - カスタム指示 (Cloud agent)"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前のレッスン: 前提条件][previous-lesson] · [次のレッスン: Copilot cloud agent →][next-lesson]

生成 AI を使うときは、コンテキストが重要です。タスクの進め方に決まりがある場合や、Copilot に知っておいてほしい背景情報がある場合は、その情報を利用できるようにする必要があります。[指示ファイル][instruction-files]を使うと、Copilot に実行内容だけでなく、その進め方も伝えられます。

この演習では、次の内容を扱います。

- リポジトリのカスタム指示とパス単位の指示ファイルを通じて、プロジェクト固有のコンテキスト、コーディング ガイドライン、ドキュメント標準が Copilot にどう伝わるかを確認する。
- `.github/copilot-instructions.md` にリポジトリ全体に適用する標準を追加する。

> [!NOTE]
> VS Code や CLI のハーネスとは異なり、ここではプロンプトの実行前後を比較しません。Copilot cloud agent は GitHub issue を使って非同期で動作するため、リアルタイムの比較が難しいためです。このハーネスの後半で cloud agent が作成した pull request をレビューするときに、指示ファイルの影響を確認します。

## 指示ファイル

### シナリオ

Tailspin Toys には、開発プラクティスに関するガイドラインと要件があります。たとえば、次のとおりです。

- データ レイヤーには必ずユニットテストを用意します。
- ユーザー インターフェイス (UI) はダークモードにして、モダンな印象にします。
- TypeScript documentation (TSDoc) の doc comment を使ってコードにドキュメントを追加します。
- 各ファイルの先頭に、ファイルの役割を説明するコメント ブロックを追加します。

指示ファイルを使い、Copilot がこうしたプラクティスに沿ってタスクを進めるための情報を提供します。

### カスタム指示

カスタム指示でコンテキストや設定を伝えると、Copilot がコーディングスタイルや要件を理解しやすくなります。好みのコーディング規約やライブラリ、追加したいコメントの種類などを指定できます。リポジトリ全体に適用する指示も、特定のファイル種別に適用する指示も作成できます。

指示ファイルには次の2種類があります。

- `.github/copilot-instructions.md` は、リポジトリに関する**すべて**のリクエストで Copilot に送信される単一の指示ファイルです。技術スタック、プロジェクトの概要、ベスト プラクティスなど、多くの chat や CLI リクエストに関係するプロジェクト全体の情報を記載します。
- `.github/instructions/*.instructions.md` は、特定のタスクやファイル種別向けの指針を記載します。TypeScript や Astro などの言語、UI component の作成、ユニットテストの追加などに利用できます。

> [!NOTE]
> IDE では、指示ファイルは Copilot Chat のコード生成にのみ使われ、コード補完や next-edit suggestion には使われません。
>
> Copilot Chat、Copilot CLI、Copilot cloud agent は、コード生成時にリポジトリ レベルの指示ファイルと `applyTo` frontmatter を持つ `*.instructions.md` ファイルの両方を使用します。
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
> テンプレートや出発点を探す場合は、指示ファイル、カスタムエージェントなどを集めた [awesome-copilot][awesome-copilot] を確認してください。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## このプロジェクトのカスタム指示ファイルを確認する

リポジトリに含まれる指示ファイルを確認します。中心となる `copilot-instructions.md` と、タスク別の `*.instructions.md` ファイルがあります。エディターまたは GitHub の Web UI で開いてください。

1. `.github/copilot-instructions.md` を開きます。
2. プロジェクトの概要と、**Agent notes**、**Code standards**、**Scripts**、**Repository Structure** などのセクションを確認します。**Code standards** の **GitHub Actions Workflows** に関するガイダンスにも注目してください。これらの指示は Copilot とのやり取り全般に適用されます。
3. `.github/instructions` フォルダーを開きます。Astro ファイル、Drizzle データ レイヤー、テストなどの指示を確認してください。
4. `.github/instructions/unit-tests.instructions.md` を開きます。先頭の `applyTo` フィールドが、リポジトリのルートからの相対 glob を使って対象ファイルを決めています。この例では、`**/*.test.ts` に一致する TypeScript のテストファイルが対象です。
5. このプロジェクトのユニットテスト作成に関する指示を確認します。
6. `.github/instructions/drizzle.instructions.md` を開き、末尾まで確認します。`unit-tests.instructions.md` などの指示ファイルや、プロジェクト内の既存ファイルへのリンクに注目してください。指示を小さく再利用しやすい単位に分け、コード生成の参考例を Copilot に示せます。ここにあるパスはリポジトリのルートではなく、指示ファイルからの相対パスです。

> [!NOTE]
> `copilot-instructions.md` の **Code formatting requirements** セクションにはコーディング標準が記載されていますが、コード内のドキュメントはまだ必須ではありません。次の手順で、TSDoc の doc comment とファイル コメント ヘッダーのルールを追加します。

## リポジトリ全体に適用する標準を追加する

ここで編集するのは、プロジェクト全体に適用する標準を1つ追加することです。次の演習で issue を割り当てるときに、cloud agent がこの標準を使えるようにします。編集前にブランチを作成します。cloud agent は issue の対象ブランチの指示を読み取りますが、ここでは変更を整理して commit します。

1. codespace のターミナルで、新しいブランチを作成して切り替えます。

   ```bash
   git checkout -b custom-instructions-cloud
   ```

`.github/copilot-instructions.md` にプロジェクト全体の情報を記載し、コード提案の質を高めるため、リポジトリのコーディング標準を明記します。

1. `.github/copilot-instructions.md` を開き直します。
2. 27 行目付近にある **Code formatting requirements** セクションを探します。既存の標準に、コード内ドキュメントのルールがまだないことを確認します。
3. 既存の標準の直後に、次の Markdown 行を追加します。これにより、ファイル コメント ヘッダーと TSDoc の doc comment を Copilot に指示します。

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. `copilot-instructions.md` を保存します。

> [!TIP]
> 指示ファイルは、全体的なガイダンスを記載するリポジトリ レベル（`.github/copilot-instructions.md`）にも、特定の言語、ファイル種別、タスク向けの `*.instructions.md` にも作成できます。今回追加した doc comment のルールのようにプロジェクト全体に適用する標準は、リポジトリ レベルのファイルに記載します。

## 指示の更新を commit、push、マージする

cloud agent は、issue が対象とするブランチの指示ファイルを読み取ります。次の演習で issue を Copilot に割り当てると、cloud agent は `main` からブランチを作成します。そのため、指示の変更を `main` にマージしておく必要があります。

1. 変更をステージして commit します。

   ```bash
   git add .github/copilot-instructions.md
   git commit -m "Add doc comment and file-header standards to copilot instructions"
   ```

2. ブランチを push します。

   ```bash
   git push -u origin custom-instructions-cloud
   ```

3. github.com で `custom-instructions-cloud` から `main` への pull request を作成し、マージします。次の演習で cloud agent が issue に取り組むとき、この指示を読み取れるようになります。

> [!TIP]
> このワークショップでは `main` に直接作業してもかまいません。その場合はブランチの手順を省略して、変更を `main` に直接 commit できます。ここでは、実際のプロジェクトに近い流れにするため、ブランチを使っています。

## まとめと次のステップ

このプロジェクトで Copilot が指示ファイルからコンテキストを取得する方法を確認し、`.github/copilot-instructions.md` にリポジトリ全体の標準を追加しました。このハーネスの後半で、cloud agent が作成する pull request をレビューするときに、この標準が適用されたことを確認できます。

次は、[最初の issue を Copilot cloud agent に割り当てます][next-lesson]。

## リソース

- [GitHub Copilot のカスタマイズに使う指示ファイル][instruction-files]
- [カスタム指示を作成するためのベスト プラクティス][instructions-best-practices]
- [Copilot 向けのカスタム指示を改善する5つのヒント][copilot-instructions-five-tips]
- [Awesome Copilot — 指示ファイルなどのリソース集][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-cloud-agent/
[instruction-files]: https://docs.github.com/copilot/customizing-copilot/about-customizing-github-copilot-chat-responses
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
