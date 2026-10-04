# Agent Skills

AIに、目の前のエラーを消すだけでなく「どこを直すべきか」まで考えてほしい。
説明は、コードの中身を知らなくても分かる形にしてほしい。

そうした作業や回答の改善を目的とした、AI向けの指示集です。
必要なskillの `SKILL.md` を読み込ませて使います。

## 基本スキル

コードの設計・修正・レビュー・説明に使います。困っていることから選んでください。

| こんなときに | Skill | 使うとどう変わるか |
|---|---|---|
| 設計を頼んでも、編集するファイルと手順しか出てこない | [design-model](skills/design-model/SKILL.md) | 編集手順の前に、どんな仕組みにするか、何を守る必要があるか、なぜその設計にするかを説明します。 |
| 修正が、その場しのぎの条件追加になってしまう | [boundary-first](skills/boundary-first/SKILL.md) | 条件分岐を足す前に、処理やデータの置き場所を変えることで問題を解消できないか検討します。 |
| 既存コードの、どこを整理すべきか知りたい | [boundary-review](skills/boundary-review/SKILL.md) | 同じ補正を各所で繰り返すなど、配置が原因で無理が生じている箇所を探し、根拠と改善案を示します。修正はしません。 |
| 内部の名前ばかりで説明され、何の話か分かりにくい | [reader-first](skills/reader-first/SKILL.md) | 何を知りたいか、コードをどこまで知っているかに合わせ、役割や動作から説明します。文章・表・短いテキスト図を選びます。 |
| 説明に、表示できる図も使ってほしい | [reader-first-mermaid](skills/reader-first-mermaid/SKILL.md) | `reader-first` と同じ説明方針で、関係や流れが図の方が伝わる場合はMermaidを使います。 |

説明用の2つは、Mermaidの図を表示できる環境なら `reader-first-mermaid`、表示できない環境なら `reader-first` のどちらか一方を選んでください。

## 高度スキル — ブランチ・PR運用向け

Gitのブランチや変更履歴、GitHubのPR（変更提案）を使う作業向けです。GitとGitHub CLI（`gh`）を利用します。

| こんなときに | Skill | 使うとどう変わるか |
|---|---|---|
| 今回書いたコードの見直しで、既存コードまで勝手に変えてほしくない | [base-aware-edit](skills/base-aware-edit/SKILL.md) | 今回追加した行と変更元のブランチにある行を区別します。既存コードの動作を変える前に、理由と影響を示して確認します。 |
| 一見不要な処理が、なぜ残っているのか分からない | [trace-design-origin](skills/trace-design-origin/SKILL.md) | 追加時のコミット・PR・議論をたどり、守るべき条件を調べます。理由が見つからなければ、そのまま報告します。 |
| PR本文を読んでも、何がどう変わるか伝わらない | [pr-description](skills/pr-description/SKILL.md) | 変更前後の動作を図や表で示し、全体像から確認結果まで読み進められる本文に整えます。 |

`base-aware-edit` と `trace-design-origin` は、PRがない場合もブランチやコミット履歴を使って調べます。

## 使い方

使いたいskillのフォルダを、利用するエージェントのskill保存先へコピーします。
`base-aware-edit` は履歴調査に `trace-design-origin` を使うため、この2つはセットで入れてください。

### piの場合

- 全プロジェクトで使う：`~/.pi/agent/skills/`
- 特定のプロジェクトで使う：そのプロジェクトの `.pi/skills/`
- Windowsのユーザー共通の保存先：`%USERPROFILE%\.pi\agent\skills\`

たとえば、修正方針を改善するskillを入れる場合：

```text
~/.pi/agent/skills/
└── boundary-first/
    └── SKILL.md
```

配置後にpiを起動し直し、次のように呼び出します。
プロジェクト側に配置した場合は、プロジェクトを信頼済みにする必要があります。

```text
/skill:boundary-first この不具合を、修正する場所から検討して
/skill:boundary-review src/ の整理すべき箇所を探して
/skill:reader-first この処理が何をしているか説明して
```

他のエージェントでは、そのエージェントが案内する保存先と呼び出し方法を使ってください。

---

これらはAIへの指示であり、結果の正しさやツール操作を強制する仕組みではありません。
詳しい判断ルールは、各 `SKILL.md` に記載しています。
