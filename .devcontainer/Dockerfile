FROM oven/bun:latest

# システムパッケージの更新とgit&bash-completionのインストール
RUN apt update && apt install -y git bash-completion openssh-client && \
    apt clean && \
    rm -rf /var/lib/apt/lists/*

# 既存のbunユーザーを削除
RUN userdel -r bun || true

# non-rootユーザーの作成(UID/GIDを分けて設定)
RUN groupadd -g 1001 appuser && useradd -u 1001 -g appuser -s /bin/bash appuser

# ホームディレクトリの作成と権限設定（root権限で実行）
RUN mkdir -p /home/appuser && \
    chown -R appuser:appuser /home/appuser

# 権限設定
RUN mkdir -p /workspaces && \
    chown -R appuser:appuser /workspaces

# SSHディレクトリの作成と権限設定
RUN mkdir -p /home/appuser/.ssh && \
    chown -R appuser:appuser /home/appuser/.ssh && \
    chmod 700 /home/appuser/.ssh

# git bash completionの設定
RUN echo "source /usr/share/bash-completion/completions/git" >> /home/appuser/.bashrc

# 作業ディレクトリの作成
WORKDIR /workspaces

# ユーザーをappuserに変更
USER appuser

# 環境変数の設定
ENV NODE_ENV=development

CMD ["sleep", "infinity"]