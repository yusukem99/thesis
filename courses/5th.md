# 第5回

# 環境構築

卒業制作の環境構築について説明します。

制作物によっては、Webサーバやデータベースなどのソフトウェアを使用することになります。

教室のWindowにインストールすることができるソフトウェアは制限されているため、必要に応じて仮想環境を構築します。

以下に、仮想環境を構築する方法をいくつか紹介します。

## Windows & Vmware Workstation( CentOS )

WindowsをホストOSとして、Vmware Workstationを使用し、CentOSをゲストOSとして使用する方法です。

デフォルトでNATモードになっており、Windows上に仮想DHCPサーバが構築され、ゲストOSのCentOSは、ホストOSのWindowsからIPアドレスを取得します。クライアントOSからインターネットに接続する際は、プロキシを設定する必要があります。

ゲストOSに設定した内容はローカルに保存されるため、外部の端末からはアクセスできません。

## CentOS

教室の端末にはWindowsに加えて、CentOSがインストールされています。（デュアルブート）

こちらも、OSに設定した内容はローカルに保存されるため、外部の端末からはアクセスできません。学外で制作する場合は、下記の方法を参考にクラウドサービスなどを利用してください。

## その他

ほかにも、グローバルIPアドレスを持つサーバを借りて、VPSやクラウド上で開発する方法もあります。

VPSとは、Virtual Private Serverの略で、物理サーバー上で動作する仮想サーバを借りることができるサービスです。

- [さくらのVPS](https://vps.sakura.ad.jp/)
- [AWS Lightsail](https://aws.amazon.com/jp/lightsail-vps/)

クラウドとは、インターネット上にあるサーバやストレージなどのコンピュータリソースを、必要なときに必要なだけ利用することができるサービスです。

- [さくらのクラウド](https://cloud.sakura.ad.jp/specification/server-disk/#server-disk-content01)
- [Google Compute Engine](https://cloud.google.com/compute?hl=ja)（e2-micro インスタンス）を 1 か月あたり 1 台無料で利用できます。
- [Amazon EC2](https://aws.amazon.com/jp/ec2/)

どちらもよく似たサービスですが、VPSのほうが比較的安価ですむことが多いです。

Google CloudやAWSでは、一定期間使用できる無料枠がありますので、そちらを利用するのも良いでしょう。

# 発展

この内容はやや高度な内容ですが、興味がある方は取り組んでみてください。

## Dockerコンテナ

Dockerコンテナを使用する方法です。

Dockerとは、Linux上で動作するコンテナ型の仮想化ソフトウェアです。

Dockerコンテナは、小さなLinuxカーネルを持ち、その上で動作するアプリケーションを実行することができます。

近年ではスタートアップ企業や、ベンチャーをはじめとする多くの企業で、Dockerコンテナが使われています。

Dockerのインストール方法は、[Docker公式サイト](https://docs.docker.com/engine/install/centos/)を参照してください。

こちらの方法では、後述のdocker-compose作成し、gitで管理することで、ほかの端末でも容易に同じ環境を構築することができます。ただし、ほかの端末にもdockerをインストールする必要があります。
### docker-compose

docker-composeは、複数のDockerコンテナを一括で管理するためのツールです。

たとえば、制作物にWebサーバとデータベースが必要な場合、次のように記述することで、Webサーバとデータベースを一括で管理することができます。

WebサーバとしてApache、データベースとしてPostgreSQLを使用する場合のdocker-compose.yamlファイルは次のようになります。

```yaml

例:docker-compose.yaml

```yaml
version: '3.8'
services:
  web:
    image: apache:latest
    ports:
      - "80:80"
    volumes: 
      - ./web:/var/www/html
  db:
    image: postgres:10.18
    container_name: seisaku-db
    hostname: seisaku-db
    volumes:
      - seisaku-postgres:/var/lib/postgresql/data
    environment:
      - POSTGRES_PASSWORD=postgres
      - TZ=Asia/Tokyo
```

docker-compose.yamlファイルを配置したディレクトリで、次のコマンドを実行することで、Webサーバとデータベースを起動することができます。

```bash
$ docker compose up
```

この構成ではファイルを共有するために、`volumes`オプションを使用しています。

`volumes`オプションは、ホストOSのディレクトリをコンテナ内のディレクトリにマウントすることができます。

この例では、ホストOSの`./web`ディレクトリをコンテナ内の`/var/www/html`ディレクトリにマウントしています。

`./web`ディレクトリには、Webサーバの設定ファイルや、Webサーバが配信するファイルを配置します。

このディレクトリ内のファイルを作成・編集することで、Webサーバに反映されます。

また、postgresのデータにアクセスする場合は`docker exec -it seisaku-db psql -U postgres`コマンドを使用します。

この`docker-compose.yaml`ファイルと、プロジェクトで使用するファイルをまとめてgitで管理することで、ほかの端末でも同じ環境を構築することができます。

