# GitHub Actionsの勉強

- GitHub CI/CD 実践ガイド

# ベスプラ

- タイムアウトは常に設定すべき p78
  - デフォルトは360min
- 明示的にシェル指定すべき p80
  - デフォルトでは、パイプエラーが拾われない
    - デフォルトの起動オプション: `bash -e {0}`
    - 明示的に指定した時の起動オプション: `bash --noprofile --norc -eo pipefail {0}`
- PR起因とアクションは、最新のPRだけを対象にすればいいことが多い p82
```yaml
concurrency:
  group: ${{ github.head_ref }}
  cancel-in-progress: true
```
- シェルコマンドから直接コンテキストを参照しない。特殊文字を含む場合、意図せぬ影響が発生する恐れがある。 p40
  - 代わりに、環境変数を経由して渡すようにし、全ての環境変数を""で囲む
```yaml
jobs:
  job:
    env:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
    steps:
      - name: Run
        run: |
          echo "${GITHUB_TOKEN}"
```
- 特に理由がなければ可読性のためにGITHUB_ENVよりもGITHUB_OUTPUTを使う方がいい p55
