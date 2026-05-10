# Docker Reverse Proxy Practice

## 概要

Docker上でNginxをReverse Proxyとして構築し、
Apacheコンテナへ通信を転送する環境を作成した。

Docker Networkを使用することで、
コンテナ名による名前解決を行い、
NginxからApacheコンテナへ通信できることを確認した。

---

# 構成図

```text
Browser
   ↓
Nginx (Reverse Proxy)
   ↓
Apache Container
```

---

# 使用技術

- Docker
- Nginx
- Apache(httpd)
- Docker Network

---

# 実施内容

## 1. Docker Network作成

コンテナ間通信を行うため、
Docker Networkを作成した。

```bash
docker network create my-network
```

確認：

```bash
docker network ls
```

---

## 2. Nginx設定ファイル作成

NginxからApacheコンテナへ通信するため、
Reverse Proxy設定を作成した。

```bash
mkdir nginx
touch nginx/default.conf
```

### nginx/default.conf

```nginx
server {
    listen 80;

    location / {
        proxy_pass http://apache-server:80;
    }
}
```

---

## 3. Nginxコンテナ起動

bind mountを使用し、
ホスト側の設定ファイルをコンテナへマウントした。

```bash
docker run -d \
--name nginx-proxy \
--network my-network \
-p 80:80 \
-v $(pwd)/nginx/default.conf:/etc/nginx/conf.d/default.conf \
nginx
```

---

## 4. Apacheコンテナ起動

```bash
docker run -d \
--name apache-server \
--network my-network \
httpd
```

---

## 5. 動作確認

ブラウザからアクセスし、
Nginx経由でApacheコンテナの画面表示を確認した。

```text
http://<Server-IP>
```

表示結果：

```text
It works!
```

---

# 学んだこと

- Reverse Proxyの基本構成
- Docker Networkによるコンテナ間通信
- コンテナ名による名前解決
- bind mountの使い方
- Nginx設定ファイルの反映方法
- Nginxがリクエストを別コンテナへ転送する仕組み

---

# 苦戦した点

## bind mountエラー

### 原因

`default.conf` を誤ってディレクトリとして作成していた。

そのため、
Dockerが設定ファイルを正常にマウントできなかった。

### 対応

ディレクトリ削除後、
`touch nginx/default.conf`
で正しいファイルを作成し修正した。

---

# 今後やること

- Docker Compose化
- 複数コンテナの一括管理
- Ansibleによる自動化
- Kubernetesとの関連理解
