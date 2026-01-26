# github-actions-book-dependabot

[GitHub CI/CD 実践ガイド | 技術評論社](https://gihyo.jp/book/2024/978-4-297-14173-8)
の「8章: Dependabot」のあたりのコード

## auto merge が死ぬ

> GraphQL: Pull request Protected branch rules not configured for this branch (enablePullRequestAutoMerge)

本に書かれている:

- Allow auto-merge
- Allow GitHub Actions to create and approve pull requests

を有効にする以外に ブランチ保護ルール(Branch protection rules)を設定することが必要。

> `--auto` フラグを使用した自動マージには、ブランチ保護ルールの設定が必須です。

### 必要な設定手順

リポジトリの Settings → Branches に移動

"New branch ruleset" (または既存のルールを編集)

今回は新しく作った

- Ruleset Name: "Protect default branch"
- Enforcement status: "Active"
- Branch targeting criteria: "Include default branch"
- Restrict deletions を✅
- Block force pushes を✅
- 一番下の"SAVE"ボタンをクリック

で、追加するのは上に加えて

- ✅ Require a pull request before merging

これの詳細設定(additional settings)で

- "Require approvals" の数を 0 に設定(Dependabot の場合、ワークフロー内で approve しているため)
- ✅ "Allow specified actors to bypass required pull requests"(必要に応じて)

### 注意

"Require a pull request before merging (ターゲットブランチに直接 push するのを禁止し、必ず Pull Request 経由で merge させる)" をチェックしたんだから、今後はmainブランチに直接pushできない。
