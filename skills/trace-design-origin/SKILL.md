---
name: trace-design-origin
description: ベースブランチにあるコードの「なぜこうなっているか」を、git blame → コミット → PR → レビューコメント → issue と辿って確かめる。コードを読んでも理由が分からず、知らずに変えると壊れうる行（一見不要な分岐・順序・値、hotfix や workaround の痕跡、お金・権限・テナント境界・外部連携に関わる箇所、移行途中の二重経路）を消す・条件を変える・並べ替える前に使う。base-aware-edit から呼ばれる。
---

# trace-design-origin — 行が入った PR まで辿って設計意図を確かめる

## 前提

コードは「何をしているか」の記録で、PR の本文は「人が知るべきこと（なぜ・何を守っているか・何を捨てたか）」への最短経路。
理由はコードではなく PR に残っていることが多いので、コードを読み込むより先に PR を読む。

## いつ辿るか（適度に）

辿るのは次のどれかに当たり、かつ周辺のコメント・テスト・名前から理由が分からないときだけ。

- ベースにある行を消す・条件を変える・順序を変える
- 一見不要に見える分岐、特定の値や ID の特別扱い、処理順への依存
- 周辺に `hotfix` / `workaround` / `for ...` / `TODO` / 障害番号 などの痕跡
- お金、権限、テナント境界、外部サービスとの取り決め（レスポンス形・イベント）
- 新旧 2 つの経路やフラグ分岐が並存している

辿らない: この PR で足した行、名前・整形だけの変更、理由がコメントやテストで既に分かるもの。

## 手順（上から順に、分かった時点で止める）

```bash
base=$(git merge-base origin/<ベース> HEAD)
git blame $base -L <開始>,<終了> -- <file>              # 行を入れたコミット sha
repo=$(gh repo view --json nameWithOwner -q .nameWithOwner)
gh api repos/$repo/commits/<sha>/pulls -q '.[].number'   # そのコミットを含む PR
```

1. **PR 本文**: `gh pr view <番号> --json title,body,url,closingIssuesReferences`
2. **その行へのレビューコメント**（本文で足りないとき）: `gh api --paginate repos/$repo/pulls/<番号>/comments -q '.[] | select(.path=="<file>") | {line, body, html_url}'`
3. **紐づく issue**（PR 内で結論が出ていないときだけ）: `gh issue view <番号> --repo <owner/repo>`

コミットが整形や移動だけなら、`git log -L <開始>,<終了>:<file> $base` で一つ前の実質的な変更まで遡ってから同じ手順を踏む。
PR が見つからない（直 push・squash で消えた等）ときはコミットメッセージで代える。

## 報告

変更の前に、次の形で短く返す。

- 対象: `file:line`
- 入れた PR: URL（無ければコミット sha）
- 理由: PR やコメントに書かれていたことを要約する。**見つからなければ「理由は見つからなかった」と書き、推測で埋めない**
- 今回の変更への影響: 理由を踏まえて、変えてよいか / 守るべき制約 / 変えるなら何を確認するか

変えるかどうかの判断はユーザーに委ねる（base-aware-edit の了承の材料になる）。

## しないこと

- 毎回辿らない。上の条件に当たる行だけ
- PR 本文で分かったのに差分全体やコメント全件を読み込まない
- private リポジトリの PR 内容を、公開リポジトリの PR や issue に転記しない
