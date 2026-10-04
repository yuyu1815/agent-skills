# Agent Skills

AIに、目の前のエラーを消すだけでなく「どこを直すべきか」まで考えてほしい。
説明は、コードの中身を知らなくても分かる形にしてほしい。

そうした作業や回答の改善を目的とした、AI向けの指示集です。
必要なskillの `SKILL.md` を読み込ませて使います。

## 基本スキル

コードの設計・修正・レビュー・説明に使います。困っていることから選んでください。

| Skill | 何を改善するか |
|---|---|
| [design-model](skills/design-model/SKILL.md) | GPT系AIの細かすぎる計画を、関数や引数の編集ではなく、仕組み・役割・流れから考える設計に整えるskill。 |
| [boundary-first](skills/boundary-first/SKILL.md) | AIが最小限の修正で問題にふたをするのを防ぎ、その対処が必要になっている原因や、処理・データの置き場所まで見直すskill。 |
| [boundary-review](skills/boundary-review/SKILL.md) | 既存コードから、その場しのぎの補正や継ぎ足しで問題をふさいでいる箇所を探し、改善案を示すskill。修正は行いません。 |
| [reader-first](skills/reader-first/SKILL.md) / [reader-first-mermaid](skills/reader-first-mermaid/SKILL.md) | コードや仕組みを人に説明するとき、相手の理解に合わせて読みやすくするskill。Mermaidの表示に対応しているかで使い分けます。 |

説明用の2つは、Mermaidの図を表示できる環境なら `reader-first-mermaid`、表示できない環境なら `reader-first` のどちらか一方を選んでください。

## 高度スキル — ブランチ・PR運用向け

Gitのブランチや変更履歴、GitHubのPR（変更提案）を使う作業向けです。GitとGitHub CLI（`gh`）を利用します。

| Skill | 何を改善するか |
|---|---|
| [base-aware-edit](skills/base-aware-edit/SKILL.md) | 今回書いたコードを見直す勢いで、既存コードまで勝手に変えるのを防ぐskill。既存コードの動作を変える前に、理由と影響を示して確認します。 |
| [trace-design-origin](skills/trace-design-origin/SKILL.md) | 一見不要なコードを消す前に、過去の変更やPRをたどり、なぜ必要になったのかを確かめるskill。 |
| [pr-description](skills/pr-description/SKILL.md) | PRを読む人が、何がどう変わるのかをすぐ把握できるように、本文を図・表・短い説明で整えるskill。 |

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
