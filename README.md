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
- **curl**: HTTPリクエストツール
- **xz-utils**: 圧縮・解凍ツール
- **ca-certificates**: SSL証明書
- **Turso CLI**: Tursoデータベース管理ツール（v1.0.11）

## 🔧 DevContainer設定詳細

### devcontainer.json設定

#### 基本設定
```json
{
  "name": "bun-devcontainer-template",
  "build": {
    "dockerfile": "Dockerfile",
    "args": {
      "VARIANT": "latest"
    }
  }
}
```

#### ユーザー設定
- **ユーザー名**: `appuser`
- **UID/GID**: 1001/1001
- **シェル**: `/bin/bash`
- **作業ディレクトリ**: `/home/appuser/workspace`

#### マウント設定
```json
"workspaceMount": "source=${localWorkspaceFolder},target=/home/appuser/workspace,type=bind,consistency=cached",
"workspaceFolder": "/home/appuser/workspace",
"mounts": [
  "source=${localEnv:HOME}/.bashrc,target=/home/appuser/.bashrc,type=bind,readonly",
  "source=${localEnv:HOME}/.ssh,target=/home/appuser/.ssh,type=bind,readonly"
]
```

**マウント詳細**:
- **ワークスペース**: ローカルフォルダを`/home/appuser/workspace`にバインドマウント
- **Bash設定**: ホストの`.bashrc`を読み取り専用で継承
- **SSH設定**: ホストの`.ssh`ディレクトリを読み取り専用で継承

#### DevContainer Features
```json
"features": {
  "ghcr.io/devcontainers/features/git:1": {},
  "ghcr.io/devcontainers/features/github-cli:1": {}
}
```

**含まれる機能**:
- **Git**: バージョン管理システム
- **GitHub CLI**: GitHubとの統合コマンドラインツール

#### VS Code設定
```json
"customizations": {
  "vscode": {
    "extensions": [...],
    "settings": {...}
  }
}
```

**自動インストール拡張機能**:
- **Biome**: JavaScript/TypeScriptのリンター・フォーマッター
- **Code Spell Checker**: スペルチェック
- **NPM IntelliSense**: npmパッケージの自動補完
- **Path IntelliSense**: ファイルパスの自動補完
- **Tailwind CSS IntelliSense**: Tailwind CSSのクラス補完
- **Git Graph**: Git履歴の可視化
- **GitLens**: Git情報の詳細表示

**VS Code設定**:
- **ターミナル**: Bashをデフォルトシェルに設定
- **ワークスペース信頼**: 自動的にワークスペースを信頼
- **ファイル監視除外**: `node_modules`と`.git`ディレクトリを監視対象外

#### ライフサイクルコマンド
```json
"postCreateCommand": "echo 'Dev container setup complete!'",
"postStartCommand": "echo 'Dev container started successfully!'"
```

### Dockerfile設定詳細

#### ベースイメージ
```dockerfile
FROM oven/bun:latest
```
- Bunの最新公式イメージを使用

#### システムパッケージの更新とインストール
```dockerfile
RUN apt update && \
    apt upgrade -y && \
    apt install -y --no-install-recommends \
        git \
        bash-completion \
        openssh-client \
        curl \
        xz-utils \
        ca-certificates && \
    apt clean && \
    rm -rf /var/lib/apt/lists/*
```

**セキュリティ対策**:
- セキュリティ更新を含むシステムパッケージの更新
- 不要なパッケージの除外（`--no-install-recommends`）
- キャッシュのクリーンアップ

#### Turso CLIのインストール
```dockerfile
RUN curl -L https://github.com/tursodatabase/turso-cli/releases/download/v1.0.11/turso-cli_Linux_x86_64.tar.gz | tar -xz && \
    mv turso /usr/local/bin/ && \
    chmod +x /usr/local/bin/turso
```

**インストール内容**:
- Turso CLI v1.0.11をダウンロード
- 実行権限を付与して`/usr/local/bin`に配置

#### ユーザー設定
```dockerfile
# 既存のbunユーザーを削除
RUN userdel -r bun || true

# non-rootユーザーの作成
RUN groupadd -g 1001 appuser && \
    useradd -u 1001 -g appuser -s /bin/bash -m appuser
```

**セキュリティ対策**:
- デフォルトの`bun`ユーザーを削除
- 専用の`appuser`ユーザーを作成（UID/GID: 1001）
- ホームディレクトリを自動作成

#### ディレクトリと権限設定
```dockerfile
# 作業ディレクトリの作成と権限設定
RUN mkdir -p /home/appuser/workspace && \
    chown -R appuser:appuser /home/appuser/workspace

# SSHディレクトリの作成と権限設定
RUN mkdir -p /home/appuser/.ssh && \
    chown -R appuser:appuser /home/appuser/.ssh && \
    chmod 700 /home/appuser/.ssh && \
    chmod 600 /home/appuser/.ssh/* 2>/dev/null || true
```

**権限設定**:
- 作業ディレクトリの所有者を`appuser`に設定
- SSHディレクトリに厳格な権限（700）を設定
- SSHファイルに適切な権限（600）を設定

#### 環境変数設定
```dockerfile
ENV TZ=Asia/Tokyo
ENV NODE_ENV=development
ENV PATH="/home/appuser/.turso:$PATH"
```

**設定内容**:
- **タイムゾーン**: 日本時間（Asia/Tokyo）
- **Node.js環境**: 開発環境
- **PATH**: Turso CLIのパスを追加

#### タイムゾーン設定
```dockerfile
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
```

#### ユーザー切り替えと作業ディレクトリ
```dockerfile
USER appuser
WORKDIR /home/appuser/workspace
CMD ["/bin/bash"]
```

**セキュリティ対策**:
- non-rootユーザー（`appuser`）で実行
- 適切な作業ディレクトリの設定
- 安全なデフォルトコマンド

## 🛠️ VS Code拡張機能詳細

### コード品質
- **Biome**: JavaScript/TypeScriptの高速リンター・フォーマッター
- **Code Spell Checker**: コード内のスペルミスを検出

### 開発支援
- **NPM IntelliSense**: npmパッケージの自動補完とドキュメント表示
- **Path IntelliSense**: ファイルパスの自動補完
- **Tailwind CSS IntelliSense**: Tailwind CSSクラスの自動補完とプレビュー

### Git支援
- **Git Graph**: Git履歴の視覚的な表示
- **GitLens**: 行単位でのGit情報表示と高度なGit機能

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

# Turso CLIの使用
turso --help
```

## 🔒 セキュリティ詳細

### Non-root実行
- **ユーザー**: `appuser`（UID/GID: 1001）
- **権限**: 必要最小限の権限のみ付与
- **実行**: セキュリティを考慮したnon-rootユーザーでの実行

### ファイル権限
- **SSHディレクトリ**: 700（所有者のみ読み書き実行）
- **SSHファイル**: 600（所有者のみ読み書き）
- **作業ディレクトリ**: 適切な所有者設定

### ネットワークセキュリティ
- **読み取り専用マウント**: ホスト設定を安全に継承
- **証明書**: 適切なSSL証明書の設定

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

### 環境変数の追加
`.devcontainer/Dockerfile`の`ENV`セクションに追加：

```dockerfile
ENV YOUR_VARIABLE=value
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

4. **タイムゾーンエラー**
   - 環境変数`TZ`が正しく設定されているか確認
   - システム時刻の同期を確認

## 📝 ライセンス

このテンプレートはMITライセンスの下で提供されています。

## 🤝 貢献

バグ報告や機能要望は、GitHubのIssueでお知らせください。

---

**注意**: このDevContainerは開発環境専用です。本番環境での使用は推奨されません。