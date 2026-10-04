# Agent Skills

AIがコードや説明を作るときの判断を支えるskillsです。
仕組みの理解、責務の配置、設計レビュー、変更の経緯調査、PR本文、相手に合わせた説明を扱います。

目的に合うものを選んでください。`base-aware-edit` は `trace-design-origin` とセットで使い、それ以外は単独でも使えます。

## どれを使うか

### 基本スキル

PR運用を前提とせず、設計・実装・レビュー・説明に使えます。

| Skill | 役割 | 使う場面 |
|---|---|---|
| [design-model](skills/design-model/SKILL.md) | コードの編集手順より先に、仕組み・関係・保証を捉える | 設計書、実装計画、設計を伴う委任 |
| [boundary-first](skills/boundary-first/SKILL.md) | 処理や状態をどこに持たせるか判断する | 実装、修正、リファクタリング |
| [boundary-review](skills/boundary-review/SKILL.md) | 既存コードから責務・配置・表現のずれを探す | 読み取り専用の設計レビュー |
| [reader-first](skills/reader-first/SKILL.md) | 目的と共有理解に合わせて、文章・表・短いテキスト図で説明する | Mermaid非対応の会話環境 |
| [reader-first-mermaid](skills/reader-first-mermaid/SKILL.md) | 同じ説明方針で、Mermaidも表示手段として選べる | Mermaid対応の会話環境 |

### 設計の3つの違い

- **design-model：何を実現する仕組みか。** 概念、関係、守るべき条件、採用理由を整理します。
- **boundary-first：何をどこに持たせるか。** 現在の配置を前提にせず、責務・状態・依存関係から変更場所を選びます。
- **boundary-review：既存のどこにずれがあるか。** コード上の根拠と改善先を示します。修正は行いません。

### 説明用の2つの違い

どちらも、今回の目的と対象コードの共有理解から、必要な内容と伝え方を選びます。
内部名だけで説明せず、役割や動作を伝え、コードとの照合に必要な正式名称を添えます。

違いは表示環境です。Mermaid対応版でも、内容に合う文章・表・図を選びます。すべてを図にするものではありません。

### 高度スキル — ブランチ・PR運用向け

Gitのブランチや履歴、GitHubのPRを使う作業向けです。GitとGitHub CLI（`gh`）を利用します。
`base-aware-edit` と `trace-design-origin` はPRがない場合の手順もありますが、ブランチや変更履歴を扱うため、こちらに分類しています。

| Skill | 役割 | 使う場面 |
|---|---|---|
| [base-aware-edit](skills/base-aware-edit/SKILL.md) | 今回追加した行とベースブランチ由来の行を区別し、変更前の確認を切り替える | ブランチ上での編集・削除・リファクタリング |
| [trace-design-origin](skills/trace-design-origin/SKILL.md) | コミット・PR・レビュー・issueから、コードが置かれた理由を調べる | 意図が不明な既存コードの変更前 |
| [pr-description](skills/pr-description/SKILL.md) | 変更の全体像と挙動の違いが分かるPR本文を作る | PR本文の作成・更新 |

## 併用について

### 同時に使わない組み合わせ

**`reader-first` と `reader-first-mermaid` は、同じ会話ではどちらか一方を使ってください。**

説明方針は共通ですが、Mermaidを表示できるかという前提が異なります。
利用する環境に合う方だけを配置・読み込みするのがおすすめです。
切り替える場合は、もう一方の指示が残らないよう、新しい会話で使ってください。

### 併用できる組み合わせ

- **`design-model` + `boundary-first`：** 仕組みと保証を捉えながら、責務の配置を検討できます。
- **設計・レビュー用のskill + 説明用のどちらか1つ：** 設計判断と伝え方をそれぞれ扱います。
- **`boundary-review` + `design-model` / `boundary-first`：** 設計の観点をレビューに使えます。ただし、レビュー中は読み取り専用です。

**レビューと修正は段階を分けます。**
`boundary-review` の調査・報告を終え、修正を依頼する際に実装の段階へ切り替えてください。
これはskill同士の併用禁止ではなく、レビュー中に自動修正しないための区別です。

### 追加の依存・使い分け

- **`base-aware-edit` → `trace-design-origin`：** 既存コードの理由が不明なときに履歴調査を呼ぶため、2つをセットで配置してください。`trace-design-origin` は単独でも使えます。
- **`base-aware-edit` を使うとき：** ベースブランチ由来のコード変更は、理由と影響を示して了承を得る運用です。説明用skillの「範囲内は自分で進める」と併用しても、この変更前の確認は省きません。
- **`pr-description` + 説明用skill：** PR本文は掲載先の表示機能に合わせ、会話での説明は会話環境に合わせます。Mermaid非対応の会話ではPR本文の図をそのまま読ませず、必要な要約を伝えます。

## 導入

必要なskillのフォルダを、利用するエージェントのskill保存先へコピーします。
各フォルダの本体は `SKILL.md` です。`base-aware-edit` を選ぶ場合は、`trace-design-origin` のフォルダもコピーしてください。

### piの場合

保存先は、全プロジェクト共通なら `~/.pi/agent/skills/`、プロジェクト限定なら `.pi/skills/` です。
Windowsのユーザー共通の保存先は `%USERPROFILE%\.pi\agent\skills\` です。

例として、設計判断用とMermaid非対応の説明用を入れる場合：

```text
~/.pi/agent/skills/
├── boundary-first/
│   └── SKILL.md
└── reader-first/
    └── SKILL.md
```

配置後にpiを起動し直すと、skillが検出されます。プロジェクト側のskillは、プロジェクトを信頼済みにする必要があります。
名前と説明から自動で選ばれる場合もありますが、確実に読み込ませたい場合は `/skill:名前` を使います。

```text
/skill:design-model この機能の仕組みと守るべき条件を整理して
/skill:boundary-first この不具合の修正場所を、状態の所有者から検討して
/skill:boundary-review src/ の責務や配置のずれを調べて
/skill:base-aware-edit このブランチの変更を見直して
/skill:trace-design-origin この分岐が追加された理由を履歴から調べて
/skill:pr-description このPRの本文を整えて
/skill:reader-first この処理が何をしているか説明して
```

Mermaid対応環境で説明用を使う場合は、`/skill:reader-first-mermaid` を選びます。

他のエージェントでは、そのエージェントが案内する保存先と呼び出し方法を使ってください。

## 適用範囲

- 設計用のskillsは、無条件に大きな再設計や文書作成を求めるものではありません。対象の仕組みや責務に合う変更を選びます。
- レビュー版は、指摘と改善案を報告します。コード変更やコミットは行いません。
- 説明用のskillsは、必要な調査や検証を行ったうえで、ユーザーへ渡す内容と形を整えます。
- これらはAIへの判断指針です。ツールの権限を制限したり、動作を強制したりする機能ではありません。

詳しい判断ルールは、各 `SKILL.md` を参照してください。
