# GitHub Actionsの勉強

- GitHub CI/CD 実践ガイド

# 目次

- [GitHub Actionsの勉強](#github-actionsの勉強)
- [目次](#目次)
- [ベスプラ](#ベスプラ)
- [ベスプラかはわからないがやった方が良さそうなこと](#ベスプラかはわからないがやった方が良さそうなこと)
- [汎用的なWorkflow](#汎用的なworkflow)
- [ロギング](#ロギング)

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
- 認知負荷の低減にコストを払う p132
  - 名前や概要、入出力インターフェースの概要をわかりやすくする
- きちんとログを出す p95
- ステップの実装が長くなってきたら適宜metadataと同じディレクトリにスクリプトなどを切り出す p133
  - ファイルを使用する際は、`GITHUB_ACTION_PATH`を使う 
  - `GITHUB_ACTION_PATH`: `.../.github/actions/dump/`
    - metadataのディレクトリ情報が格納されている
- 環境変数による暗黙の依存は、コードが追いづらくなるため、`input`や`output`で明示的にやり取りする p134
- ロググループなどを用いて適切にログ管理を行う p134

# ベスプラかはわからないがやった方が良さそうなこと

- tokenは、`secret.GITHUB_TOKEN`でも`github.token`でも使えるが、`github.token`を使う方が良さそう。理由は2つ p133 p57
  - 両方使うと可読性が下がるからどちらかに統一すべき
  - 特殊文字があるとスクリプトで用いる場合に意図しない挙動を起こす場合があるので、envを使いたいがenvでアクセスできるのは`github.token`だけ
```yaml
env:
  GITHUB_TOKEN: ${{ github.token }}
```

# 汎用的なWorkflow

- [shell-check](./.github/workflows/shecll-check.yml)
  - shellcheckを使ってシェルスクリプトの静的解析を行う
- [static-check](./.github/workflows/static-check.yml)
  - actionlintを使ってGitHub Actionsの静的解析を行う
- [release](./.github/workflows/release.yml)
  - 自動的にタグづけを行うためのWorkflow
- [dump](./.github/workflows/dump.yml)
  - Actions内のコンテキストや環境変数を出力するためのDebug用Workflow

# ロギング

- 手軽にコマンドの実行履歴を確認する
  - `set -x`
