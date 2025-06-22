# Bun DevContainer Template

このプロジェクトは、Bunランタイムを使用した開発環境のためのDevContainerテンプレートです。

## 🚀 概要

このDevContainerは、Bunを使用したNode.js/TypeScript開発に最適化された環境を提供します。セキュリティを考慮したnon-rootユーザーでの実行、便利な開発ツールの統合、SSH設定の継承などが含まれています。

## 📋 環境仕様

### ベースイメージ
- **Bun**: `oven/bun:latest`
- **OS**: Debianベース（Bun公式イメージ）

### インストール済みツール
- **Git**: バージョン管理
- **Bash Completion**: コマンド補完機能
- **OpenSSH Client**: SSH接続機能

## 🔧 DevContainer設定

### ユーザー設定
- **ユーザー名**: `appuser`
- **UID/GID**: 1001/1001
- **シェル**: `/bin/bash`
- **作業ディレクトリ**: `/workspaces`

### マウント設定
- **ワークスペース**: ローカルフォルダを`/workspaces`にマウント
- **Bash設定**: ホストの`.bashrc`を継承
- **SSH設定**: ホストの`.ssh`ディレクトリを継承

### 環境変数
- `NODE_ENV=development`

## 🛠️ VS Code拡張機能

開発効率を向上させる以下の拡張機能が自動インストールされます：

### コード品質
- **Biome**: JavaScript/TypeScriptのリンター・フォーマッター
- **Code Spell Checker**: スペルチェック

### 開発支援
- **NPM IntelliSense**: npmパッケージの自動補完
- **Path IntelliSense**: ファイルパスの自動補完
- **REST Client**: APIテスト用クライアント

### フロントエンド開発
- **Tailwind CSS IntelliSense**: Tailwind CSSのクラス補完

### Git支援
- **Git Graph**: Git履歴の可視化
- **GitLens**: Git情報の詳細表示

## 🚀 使用方法

### 前提条件
- Docker Desktop
- VS Code
- VS Code Dev Containers拡張機能

### セットアップ手順

1. **リポジトリのクローン**
   ```bash
   git clone <repository-url>
   cd <project-name>
   ```

2. **DevContainerでの開き方**
   - VS Codeでプロジェクトフォルダを開く
   - `Ctrl+Shift+P`（または`Cmd+Shift+P`）でコマンドパレットを開く
   - `Dev Containers: Reopen in Container`を選択

3. **初回ビルド**
   - 初回はDockerイメージのビルドが実行されます
   - 完了まで数分かかる場合があります

### 開発開始

DevContainerが起動したら、以下のコマンドでBunプロジェクトを開始できます：

```bash
# 新しいBunプロジェクトの作成
bun create <template-name>

# 依存関係のインストール
bun install

# 開発サーバーの起動
bun run dev
```

## 🔒 セキュリティ

- **Non-root実行**: セキュリティを考慮し、`appuser`として実行
- **権限分離**: 適切なUID/GID設定
- **SSH継承**: ホストのSSH設定を安全に継承

## 📁 ディレクトリ構造

```
.devcontainer/
├── devcontainer.json    # DevContainer設定
└── Dockerfile          # カスタムDockerイメージ
```

## 🔄 カスタマイズ

### 拡張機能の追加
`.devcontainer/devcontainer.json`の`extensions`配列に追加：

```json
"extensions": [
  "your.extension-id"
]
```

### パッケージの追加
`.devcontainer/Dockerfile`の`RUN apt install`コマンドに追加：

```dockerfile
RUN apt update && apt install -y your-package && \
    apt clean && \
    rm -rf /var/lib/apt/lists/*
```

## 🐛 トラブルシューティング

### よくある問題

1. **権限エラー**
   - DevContainerを再ビルドしてください
   - `Dev Containers: Rebuild Container`を実行

2. **SSH接続エラー**
   - ホストのSSH設定が正しくマウントされているか確認
   - `.ssh`ディレクトリの権限を確認

3. **パッケージインストールエラー**
   - ネットワーク接続を確認
   - DevContainerを再ビルド

## 📝 ライセンス

このテンプレートはMITライセンスの下で提供されています。

## 🤝 貢献

バグ報告や機能要望は、GitHubのIssueでお知らせください。

---

**注意**: このDevContainerは開発環境専用です。本番環境での使用は推奨されません。