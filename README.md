# github-actions-book-dependabot

[GitHub CI/CD 実践ガイド | 技術評論社](https://gihyo.jp/book/2024/978-4-297-14173-8)
の「8章: Dependabot」のあたりのコード

## auto merge が死ぬ

こんなエラー

> GraphQL: Pull request Protected branch rules not configured for this branch (enablePullRequestAutoMerge)

本に書かれている:

- Allow auto-merge
- Allow GitHub Actions to create and approve pull requests

を有効にする**以外に** ブランチ保護ルール(Branch protection rules)を設定することが必要。

> `--auto` フラグを使用した自動マージには、ブランチ保護ルールの設定が必須です。

### 必要な設定手順

リポジトリの Settings → Branches に移動

"New branch ruleset" (または既存のルールを編集)

今回は新しく作った。以下の通り

- Ruleset Name: "Protect default branch"
- Enforcement status: "Active"
- Branch targeting criteria: "+Include default branch"
- Restrict deletions を✅(デフォルト)
- Block force pushes を✅(デフォルト)
- 一番下の"SAVE"ボタンをクリック

で、追加するのは上に加えて

- ✅ Require a pull request before merging

これの詳細設定(additional settings)で

- "Require approvals" の数を 0か1 に設定 (Dependabot の場合、ワークフロー内で approve しているため)

### 注意

"Require a pull request before merging (ターゲットブランチに直接 push するのを禁止し、必ず Pull Request 経由で merge させる)" をチェックしたんだから、今後はmainブランチに直接pushできない。

### additional settings の詳細

- **Required approvals**  
  Pull Request を merge するために必要な承認数を設定する。
  0〜n の数を指定可能。`gh pr review "${GITHUB_HEAD_REF}" --approve` しているから1あればOKのはず。

### 代替手段

```yaml
- run: |
    gh pr review "${GITHUB_HEAD_REF}" --approve
    gh pr merge "${GITHUB_HEAD_REF}" --merge
```

`--auto` なしにすれば `Require a pull request before merging` の設定は不要。ただし
即座にマージされるため、他のチェックが完了するのを待つことができなくなる。
