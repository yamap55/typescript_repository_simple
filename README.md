# typescript_repository_simple

本リポジトリはシンプルな TypeScript 環境のテンプレートリポジトリです
Dev Container の設定をしていますので、VS Code と Docker、Git さえあれば各種開発用設定が行われた Python の開発環境が構築され、即時開発が可能です
GitHub のリポジトリページの「Use this template」を押下して使用してください

## 内容

- [devcontainer](https://code.visualstudio.com/docs/remote/containers)
- [typescript](https://www.typescriptlang.org/)
- [node.js](https://nodejs.org/ja/)
- [jest](https://jestjs.io/ja/)
- [eslint](https://eslint.org/)
- [prettier](https://prettier.io/)

## 環境詳細

- Node.js: v22

### 事前準備

- Docker インストール
- VS Code インストール
- VS Code の拡張機能「Remote - Containers」インストール
  - https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers
- 本リポジトリの clone
- `.env` ファイルを空ファイルでプロジェクト直下に作成
- ssh-agent の設定
  - https://code.visualstudio.com/docs/devcontainers/containers#_using-a-credential-helper
- 以下をプロジェクト名に合わせて変更
  - `.devcontainer/devcontainer.json`
    - `name`
  - `compose.yaml`
    - `image`, `container_name`
    - `env_file`
      - 環境変数を使用しない場合は除去
  - `src/index.ts`
    - `src/utils/message.ts`
  - `README.md`
  - `LICENSE`
  - dependabot
    - `.github/dependabot.yml`
    - `.github/workflows/auto_merge_depandabot.yml`
  - package.json
    - `name`

### 開発手順

1. VS Code 起動
2. 左下のアイコンクリック
3. 「Dev Containers: Reopen in Container」クリック
4. しばらく待つ
   - 初回の場合コンテナー image の取得や作成が行われる
5. 起動したら開発可能
   - 初回起動時は `npm install` を実行してください

## NOTE

- ビルド
  - `npm run build`
- 実行
  - `npm start`
- テスト
  - `npm test`
