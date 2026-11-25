---
title: "レッスン 4 - カスタム指示で Copilot を導く"
description: "リポジトリの指示を確認し、ドキュメント標準を追加して、絞り込み機能のコードに適用します。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

生成 AI を使うときは、コンテキストが重要です。特定の方法でタスクを進める必要がある場合は、Copilot がその指針を利用できるようにします。[指示ファイル][instruction-files]を使うと、必要なコードだけでなく、その構成方法も Copilot に伝えられます。ここでは、絞り込み機能を実装した後、Copilot が使った指示を確認し、ドキュメント標準を追加して、コードに適用します。

このレッスンでは、次の内容を学習します。

- リポジトリの指示とパス単位の指示ファイルがエージェントにどう伝わるかを確認する。
- コーディング標準に従うよう指示ファイルを更新する。
- 指示ファイルがコードに与える影響を確認する。

## シナリオ

Tailspin Toys には、開発プラクティスに関するガイドラインがあります。

- コメントにはコードの内容を繰り返さず、意図や自明でない判断を説明します。
- `db/` と `src/lib/` の export 関数には、目的、パラメーター、戻り値を説明する TypeScript documentation (TSDoc)/JavaScript documentation (JSDoc) を記載します。該当する場合は、注入可能な `db` 引数も説明します。
- 再利用可能な Astro component には `Props` の契約を記載し、関連コードが変わったらコメントを最新状態に保ちます。
- 既存の書式設定と lint のガイダンスを維持します。

指示ファイルでこれらのプラクティスを伝え、Copilot が沿って作業できるようにします。

## 指示ファイル

カスタム指示を使うと、コンテキストや設定を伝えて、Copilot がコーディングスタイルや要件を理解しやすくなります。コーディング規約、ライブラリ、追加するコメントの種類などを指定できます。リポジトリ全体に適用する指示も、特定のファイル種別に適用する指示も作成できます。

指示ファイルには次の2種類があります。

- `.github/copilot-instructions.md` は、リポジトリに関する**すべて**のリクエストで Copilot に送信される単一の指示ファイルです。技術スタック、プロジェクトの概要、ベスト プラクティスなど、ほとんどのリクエストに関係する情報を記載します。
- `.github/instructions/*.instructions.md` は、特定の言語、ファイル種別、タスク向けの指針を記載します。

> [!NOTE]
> ほかの指示形式への対応状況は、環境によって異なります。利用する形式については、[カスタム指示のサポート状況][custom-instructions-support]を確認してください。
>
> 統合開発環境 (IDE) では、指示ファイルは Copilot Chat のコード生成にのみ使われ、コード補完や next-edit suggestion には使われません。Copilot Chat、Copilot CLI、Copilot cloud agent は、コード生成時にリポジトリ レベルの指示ファイルと、`applyTo` frontmatter を持つ `*.instructions.md` ファイルの両方を使います。
>
> Copilot は [AGENTS.md や CLAUDE.md など、別の標準に基づく指示ファイル][custom-instructions-support]もサポートしています。

## このプロジェクトのカスタム指示ファイルを確認する

スターター プロジェクトには、すでに指示ファイルが含まれています。変更前に確認し、コードへの影響を把握しましょう。

1. Codespace に戻ります。
2. Codespaces のエディターで（ターミナルではなく）`.github/copilot-instructions.md` を開きます。
3. プロジェクトの短い説明とコーディング ガイダンスを確認します。これらの指示は、このリポジトリでの Copilot とのやり取りに適用されます。
4. `.github/instructions` フォルダーを開きます。Astro ファイル、Drizzle データ レイヤー、テストなどの指示を確認します。
5. `.github/instructions/unit-tests.instructions.md` を開きます。先頭の `applyTo` フィールドが、適用対象のファイルを決める glob を設定していることを確認します。
6. `.github/instructions/drizzle.instructions.md` を開き、ほかの指示ファイルや既存プロジェクト ファイルへのリンクを確認します。大きな指示を再利用しやすい単位に分けられます。

## チームのガイダンスに合わせて指示ファイルを更新する

既存の指示ファイルに加えて、新しく生成される TypeScript に TSDoc comment が追加されるよう、中心となる `copilot-instructions.md` を更新します。

1. `.github/copilot-instructions.md` で、35 行目付近にある **Code formatting guidance** セクションを探します。
2. 最後の箇条書きとして、次の内容を追加します。

   ```markdown
   - All new TypeScript should contain TSDocs comments for documentation purposes.
   ```

変更は自動的に保存されます。

## 更新したガイダンスを使う

Copilot CLI は会話の開始時にリポジトリの指示を読み込みます。更新した指示を使いながら絞り込みのコンテキストを保つため、保存済みの会話を閉じて再開します。

1. 絞り込みの会話が開いている場合は、`/exit` を入力して閉じます。Copilot CLI は会話を自動的に保存します。
2. リポジトリのルートで `game-filters-cli` ブランチにいることを確認し、保存済みの会話を再開します。

   ```bash
   copilot --resume
   ```

   選択を求められた場合は、絞り込みの会話を選び、`game-filters-cli` ブランチがチェックアウトされていることを確認します。
3. 次のプロンプトで更新したガイダンスを適用するよう依頼します。

   ```plaintext
   We just updated our instructions and code guidance. Can you please update the code you generated to match that guidance?
   ```

4. `/diff` を入力し、変更された TypeScript ファイルを確認します。新しい TSDoc comment が追加され、コードを正しく説明していることを確認します。

## まとめと次のステップ

Copilot CLI が指示ファイルからコンテキストを取得する方法を確認し、新しい標準を機能に適用しました。次の作業を行いました。

- リポジトリの指示とパス単位の指示ファイルがエージェントにどう伝わるかを確認しました。
- 指示ファイルを更新し、コーディング標準を追加しました。
- 指示ファイルがコードに与える影響を確認しました。

次は、[再利用可能な `quality-checks` スキルをカスタマイズして実行します][next-lesson]。lint とテストが一貫して実行されることを確認します。

## リソース

- [Copilot CLI のカスタム指示を追加する][instruction-files]
- [カスタム指示のサポート状況][custom-instructions-support]
- [カスタム指示を作成するためのベスト プラクティス][instructions-best-practices]
- [Awesome Copilot — 指示ファイルなどのリソース集][awesome-copilot]

[instruction-files]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions
[instructions-best-practices]: https://docs.github.com/copilot/concepts/prompting/response-customization#writing-effective-custom-instructions
[awesome-copilot]: https://awesome-copilot.github.com/
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support
[previous-lesson]: ../3-agent-modes/
[next-lesson]: ../5-agent-skills/
