# Docker Reverse Proxy Practice

## 概要

Docker上でNginxをReverse Proxyとして構築し、
Apacheコンテナへ通信を転送する構成を作成した。

---

# 構成図

Browser
↓
Nginx (Reverse Proxy)
↓
Apache Container

---

# 使用技術

- Docker
- Nginx
- Apache(httpd)
- Docker Network

---

# 実施内容

## 1. Docker Network作成

```bash
docker network create my-network
```

確認：

```bash
docker network ls
```

---

## 2. Nginx設定ファイル作成

```bash
mkdir nginx
touch nginx/default.conf
```

### default.conf

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
Nginx経由でApacheの画面表示を確認。

---

# 学んだこと

- Reverse Proxyの基本構成
- Docker Networkによるコンテナ間通信
- コンテナ名で名前解決できる仕組み
- bind mountの使い方
- Nginx設定ファイルの反映方法

---

# 苦戦した点

## bind mountエラー

### 原因

`default.conf` がファイルではなくディレクトリになっていた。

### 対応

ディレクトリ削除後、正しいファイルを作成。

---

# 今後やること

- Docker Compose化
- 複数コンテナ管理
- Ansibleによる自動化
