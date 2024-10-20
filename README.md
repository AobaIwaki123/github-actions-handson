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
- フィルターは、path, branch, tagなど様々存在する p71
  - path系とその他のフィルターを併用するとAND条件になる
  - pathとpath-ignoreは同時に使えない。そのような場合は、Globを用いる
