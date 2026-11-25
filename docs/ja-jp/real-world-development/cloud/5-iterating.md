---
title: "演習 5: GitHub Copilot の作業を改善する"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 前のレッスン: エージェントの管理][previous-lesson] |
|:--|

## 作業内容をレビューする

このワークショップでは、ユーザー体験の改善と機能追加を目的に、GitHub Copilot に複数の作業を依頼しました。コードへのドキュメント追加、デザイン チームが改善できる関連ゲーム機能、高コントラストとライトモードを含むアクセシビリティ機能を実装しました。ここでは変更を確認し、必要に応じて Copilot にフィードバックを送ります。

### シナリオ

生成 AI を利用しても、ソフトウェア設計や開発・運用 (DevOps) の基本は変わりません。生成されたコードをレビューし、通常の DevOps プロセスを進めます。チーム全体にレビューを依頼する前に、ドキュメント、関連ゲーム機能、アクセシビリティ機能のために GitHub Copilot が作成した変更を確認しましょう。

## GitHub Copilot cloud agent のセキュリティ

Copilot cloud agent は非同期かつ人の監視なしで作業するため、安全に作業できるよう次の制約があります。

- Copilot はリポジトリへの読み取りアクセス権を持ち、書き込みは作業に使うブランチに限られます。
- Cloud agent は GitHub Actions 内で動作し、作業用の一時的な環境を作成します。
- GitHub Actions ワークフローの実行には、人による承認が必要です。
- MCP サーバーを含む外部リソースへの[アクセスは既定で制限されています](https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall)。

## 生成されたドキュメントをレビューする

最初に、コードへドキュメントを追加するために GitHub Copilot が作成した pull request (PR) を確認します。github.com の標準的な PR インターフェイスを使います。

> [!NOTE]
> PR に、GitHub Copilot がファイアウォールでブロックされたという警告が表示されることがあります。Copilot は MCP サーバーへの呼び出しなど、外部リソースへのアクセスが既定で制限されているため、これは想定された動作です。必要に応じて、[Copilot cloud agent のファイアウォールを構成または無効化][agent-firewall]できます。

1. 新しいブラウザータブで github.com のリポジトリを開きます。
2. **Pull Requests** を選択して PR の一覧を開きます。
3. **Add missing documentation** などのタイトルが付いた PR を開きます。

> [!NOTE]
> Copilot が作業中の場合、PR のタイトルに **[WIP]** が付いています。その場合は作業の完了を待ちます。数分かかることがあるため、休憩したり、これまでに学んだことを振り返ったりしてください。

4. 準備ができたら **Files changed** タブを選択し、変更をレビューします。

    ![Files changed タブ](../../../_images/shared-pr-files-changed.png)

5. TSDoc の doc comment など、更新されたコードとドキュメントを確認します。変更内容は状況によって異なります。Exercise 2 の最初に確認したカスタム指示ファイルに基づき、TSDoc の doc comment、TypeScript の規約、コメント ヘッダーが追加されているかを確認してください。
6. 更新内容のレビュー後、**Conversation** タブに戻り、下にスクロールします。
7. ワークフローが承認待ちであることを示す表示を探します。
8. ワークフローが承認待ちの場合は、**Approve and run workflows** を選択します。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

9. PR の checks セクションにワークフローが登録されます。問題がなければ、単一の Astro アプリに対するプロジェクトのチェックが成功します。完了まで数分かかることがあります。

## GitHub Copilot に変更を依頼する

PR で Copilot とやり取りすることもできます。チーム メンバーと同様に、PR のコメントやコードの行コメントで Copilot にメンションできます。Copilot はコメントを確認し、対応するセッションを開始します。結果は確定的ではないため、必須のプロンプトはありません。たとえば、次のような変更を依頼できます。

- 各コードファイルの先頭に、役割を説明するコメント ヘッダーを追加する。
- TypeScript と Astro のファイルに TSDoc の doc comment を追加する。
- Astro アプリの構造を説明する README を作成する。

1. 上記の例などを使って、生成されたドキュメントの変更を **@copilot** に依頼するコメントを追加します。
2. **View Session** を選択し、Copilot が変更を行う様子を確認します。Copilot は更新用の新しいセッションを開始します。
3. **Back to pull request** を選択すると PR に戻れます。

    ![Back to pull request](../../../_images/ex7-back-to-pr.png)

4. Copilot の変更後、PR に新しい commit が追加されたことを確認します。
5. **Files changed** タブを選択し、変更をレビューします。

満足するまで改善を繰り返せます。準備ができたら、PR を draft からレビュー可能な状態にして、`main` ブランチにマージします。

![PR をレビュー可能な状態に変更する画面](../../../_images/ex7-ready-for-review.png)

## 関連ゲーム機能をレビューする

ゲーム詳細ページに関連ゲームを表示する issue に対して、Copilot が作成した PR を確認します。

1. GitHub.com のリポジトリを開きます。
2. **Pull Requests** タブを選択します。
3. **Show related games on the game details page** などのタイトルが付いた PR を選択します。
4. **Files changed** タブを選択し、生成されたコードをレビューします。
5. 変更内容のレビュー後、**Conversation** タブに戻り、下にスクロールします。
6. ワークフローが承認待ちであることを示す表示を探します。
7. ワークフローが承認待ちの場合は、**Approve and run workflows** を選択します。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

8. PR の checks セクションにワークフローが登録されます。問題がなければ、単一の Astro アプリに対するプロジェクトのチェックが成功します。完了まで数分かかることがあります。
9. **任意:** codespace でこのブランチに切り替えて、関連ゲーム機能を手動でテストできます。codespace に移動してターミナルを開き、Copilot が作成したブランチ名（例: **copilot/fix-8**）に合わせて `<branch-name>` を置き換え、次のコマンドを実行します。

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

Copilot が関連ゲーム機能を作成しました。前の PR と同様に、コメントで更新を依頼して cloud agent の作業を繰り返し改善できます。たとえば、表示する関連ゲームの数の変更や、コメント ヘッダーと TSDoc doc comment の追加を依頼できます。この機能はカスタム指示を更新する前に割り当てたため、指示が反映されていない場合があります。**Conversation** タブにコメントを追加すると、Copilot はそれを確認して新しいセッションを開始します。

## アクセシビリティ機能をレビューする

最後に、アクセシビリティ カスタムエージェントが実装した機能を確認します。この PR には、Exercise 3 で依頼したハイコントラスト モードと、Exercise 4 の mission control から追加依頼したライトモードの両方が含まれているはずです。

1. GitHub.com のリポジトリを開きます。
2. **Pull Requests** タブを選択します。
3. **Add high contrast mode to website** などのタイトルが付いた PR を選択します。

> [!NOTE]
> Copilot が作業中の場合、PR のタイトルに **[WIP]** が付いています。作業が完了するまで待ちます。数分かかる場合があります。

4. **Files changed** タブを選択し、生成されたコードをレビューします。
5. 実装を確認し、特に次の点を確認します。
   - モード切り替え用の UI component
   - 利用者の設定を保持する local storage の使用
   - ハイコントラスト モードとライトモードの CSS またはスタイルの変更
   - アクセシビリティ属性（Accessible Rich Internet Applications (ARIA) ラベル、キーボード操作など）
   - モード切り替えを管理する JavaScript または TypeScript コード
6. 更新内容のレビュー後、**Conversation** タブに戻り、下にスクロールします。
7. ワークフローが承認待ちであることを示す表示を探します。
8. ワークフローが承認待ちの場合は、**Approve and run workflows** を選択します。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

9. PR の checks セクションにワークフローが登録されます。問題がなければ、単一の Astro アプリに対するプロジェクトのチェックが成功します。完了まで数分かかることがあります。
10. **任意:** codespace でこのブランチに切り替えて、アクセシビリティ機能を手動でテストできます。codespace に移動してターミナルを開き、Copilot が作成したブランチ名に合わせて `<branch-name>` を置き換え、次のコマンドを実行します。

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

    その後、アプリケーションを起動し、ブラウザーでハイコントラスト モードとライトモードを切り替えます。ページを再読み込みしても設定が保持されることを確認してください。

アクセシビリティ カスタムエージェントが、アクセシビリティのベスト プラクティスに沿って Copilot の実装を導いたことを確認します。問題や改善点があれば、前の PR と同様にコメントで **@copilot** に変更を依頼できます。

## 任意の演習 — さらに作業を委任する

cloud agent には、実際のバックログ項目を割り当てて、別の作業に取り組む方法が適しています。この方法を身につけるために、リポジトリに issue を追加して Copilot に割り当ててみてください。

- ゲーム詳細ページに支援者向けの関心フォームを作成する。
- ゲーム一覧ページにページネーションを実装する。
- データアクセス helper に入力検証とエラー処理を追加する。

## まとめ

Cloud agent ハーネスを完了しました。各レッスンを通じて、次の内容を扱いました。

- **リポジトリに含まれるカスタム指示ファイルを確認し、cloud agent の出力への影響を確認しました。**
- **issue を Copilot cloud agent に割り当て、環境のセットアップ、計画、非同期の実行を確認しました。**
- **カスタムエージェントを作成して使用し、ハイコントラストとライトモードの切り替えを追加しました。**
- **Copilot agents ページを mission control として使い、進行中のアクセシビリティ セッションを監視して方向づけました。**
- **cloud agent の pull request をレビューして改善し、変更を依頼するために `@copilot` にメンションし、ワークフローを承認しました。**

## レビューと次のステップ

Cloud agent ハーネスを完了しました。ほかのハーネスでは、ここで実践した内容を別の環境で試せます。

- 🖥️ **[VS Code ハーネス](../../vscode/)** — IDE 内で Copilot Chat の agent mode と MCP 統合を試します。
- 💻 **[CLI ハーネス](../../cli/)** — Plan と Autopilot モード、エージェント スキル、QA カスタムエージェント、Playwright MCP、Agent Merge、任意の `/delegate` による cloud agent との連携を使い、ターミナルからレビュー済みの変更を2つリリースします。

自分のリポジトリでは、次の内容も試してください。

- リファクタリングやテスト カバレッジの issue を cloud agent に割り当てる。
- 別の分野向けのカスタムエージェントを作成する。
- 異なる技術スタック向けに `copilot-setup-steps.yml` を設定する。

## リソース

- [GitHub Copilot][github-copilot]
- [Copilot agent について][copilot-agents]
- [GitHub issue を Copilot に割り当てる][assign-issue]
- [Copilot cloud agent のセットアップ ワークフローに関するベスト プラクティス][cloud-agent-best-practices]
- [Copilot cloud agent のファイアウォールを構成する][agent-firewall]

---

| [← 前のレッスン: エージェントの管理][previous-lesson] |
|:--|

[github-copilot]: https://github.com/features/copilot
[cloud-agent-overview]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[agent-firewall]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall

[previous-lesson]: ../4-managing-agents/
