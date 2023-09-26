# 第5回

# 仮想環境の構築

卒業制作の環境構築について説明します。

制作物によっては、Webサーバやデータベースなどのソフトウェアを使用することになります。

教室のWindowにインストールすることができるソフトウェアは、制限されているため、必要に応じて仮想環境を構築します。

以下に、仮想環境を構築する方法をいくつか紹介します。

## Windows & Vmware Workstation( CentOS )

WindowsをホストOSとして、Vmware Workstationを使用し、CentOSをゲストOSとして使用する方法です。

デフォルトでNATモードになっており、Windows上に仮想DHCPサーバが構築され、ゲストOSのCentOSは、ホストOSのWindowsからIPアドレスを取得します。

クライアントOSからインターネットに接続する際は、プロキシを設定する必要があります。

## CentOS

端末から直接CentOSを起動する方法です。

こちらもインターネットに接続する際は、プロキシを設定する必要があります。

## Dockerコンテナ

Dockerコンテナを使用する方法です。

Dockerとは、Linux上で動作するコンテナ型の仮想化ソフトウェアです。

Dockerコンテナは、小さなLinuxカーネルを持ち、その上で動作するアプリケーションを実行することができます。

近年ではスタートアップ企業や、ベンチャーをはじめとする多くの企業で、Dockerコンテナが使われています。

Dockerのインストール方法は、[Docker公式サイト](https://docs.docker.com/engine/install/centos/)を参照してください。

### docker-compose

docker-composeは、複数のDockerコンテナを一括で管理するためのツールです。

たとえば、制作物にWebサーバとデータベースが必要な場合、次のように記述することで、Webサーバとデータベースを一括で管理することができます。

```yaml

例

```yaml:docker-compose.yaml
version: '3.8'
services:
  web:
    image: apache:latest
    ports:
      - "80:80"
    volumes: 
      - ./web:/var/www/html
  db:
    image: mysql:latest
    environment:
      MYSQL_ROOT_PASSWORD: password
      MYSQL_DATABASE: database
      MYSQL_USER: user
      MYSQL_PASSWORD: password
    volumes:
      - ./db:/var/lib/mysql
```

docker-compose.yamlファイルを配置したディレクトリで、次のコマンドを実行することで、Webサーバとデータベースを起動することができます。

```bash
$ docker compose up
```

この`docker-compose.yaml`ファイルと、プロジェクトで使用するファイルをまとめてgitで管理することで、ほかの端末でも同じ環境を構築することができます。