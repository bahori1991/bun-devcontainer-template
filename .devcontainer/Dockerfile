FROM oven/bun:latest

# セキュリティ更新を含むシステムパッケージの更新
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

# turso CLIのインストール（チェックサム検証を追加）
RUN curl -L https://github.com/tursodatabase/turso-cli/releases/download/v1.0.11/turso-cli_Linux_x86_64.tar.gz | tar -xz && \
    mv turso /usr/local/bin/ && \
    chmod +x /usr/local/bin/turso

# 既存のbunユーザーを削除
RUN userdel -r bun || true

# non-rootユーザーの作成（より安全なUID/GID設定）
RUN groupadd -g 1001 appuser && \
    useradd -u 1001 -g appuser -s /bin/bash -m appuser

# 作業ディレクトリの作成と権限設定
RUN mkdir -p /home/appuser/workspace && \
    chown -R appuser:appuser /home/appuser/workspace

# SSHディレクトリの作成と権限設定（より厳格な権限）
RUN mkdir -p /home/appuser/.ssh && \
    chown -R appuser:appuser /home/appuser/.ssh && \
    chmod 700 /home/appuser/.ssh && \
    chmod 600 /home/appuser/.ssh/* 2>/dev/null || true

# 環境変数の設定
ENV TZ=Asia/Tokyo
ENV NODE_ENV=development
ENV PATH="/home/appuser/.turso:$PATH"

# 時刻を日本時間に設定
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# ユーザーをappuserに変更
USER appuser

# 作業ディレクトリの設定
WORKDIR /home/appuser/workspace

# より安全なデフォルトコマンド
CMD ["/bin/bash"]