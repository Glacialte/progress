# Docker_Tutorial
## 初めに
CTFをやっていてDockerを使う問題が出てきたので簡単にではありますがDockerについて調べてみました！
年齢は？彼女はいるのか？気になる年収は？そもそも何なのか？これから一緒に見ていきましょう！

## Dockerとは何か？
DockerはDocker社が作成した、仮想環境を作成・配布・実行するためのプラットフォーム。
自分が作成した環境を他のパソコンへと配布することで、その環境を他のパソコンからも利用出来るようにする。
仮想マシンと違ってハードウェア上で作動するOSは1つだけであり、OSに起因するオーバーヘッドがかからず
リソースを活用できる。あと高速に起動できる。

## Dockerの用語
Dockerの説明にはさまざまな用語が用いられる。その中でもよく見るものについて以下説明する。
### コンテナ
CPU・メモリ・プロセス空間などが独立した仮想環境のこと。ホストOSからは1つのプロセスとして認識されるので
ゲストOSをなしで独立した仮想環境が構築できる。
### 仮想マシン
PC上にホストOS以外に新たにCPUやメモリを割り当てて作った仮想的なPC。
### Dockerイメージ
Dockerコンテナに入れるライブラリやアプリケーション本体、シェルコマンドなどが揃ったパッケージ。"Docker Hub"からダウンロードできる。
### Dockerfile
Dockerイメージを作成するための手順が示されたテキストファイル。これによってイメージの作成が可能となる。
### Docker Engine
アプリケーションと環境をコンテナ化する、及びdockerイメージに基づいてコンテナを構築・実行するアプリケーション。
### Dockerボリューム
Dockerコンテナで利用するデータを保持するためにDockerEngine上で確保された領域。普通はコンテナの破棄と同時にコンテナ内のデータは破棄されるが、DokcerボリュームにマウントしておくとDokcerコンテナを破棄した際にもデータは保持される。

## Dockerコマンド
### docker pull
Docker Hubからコンテナイメージやリポジトリを取得する。docker image pullと同じ？
```
$ docker pull ubuntu:latest
```
上記のコマンドではubuntuイメージを取得する。タグ名（:latestなど）を省略した場合デフォルトで:latestタグが使われる。

### docker create
新しいDockerコンテナを停止状態で作成する。オプションはdocker runと共通。

### docker start
作成済みのDockerコンテナを起動する。
```
$ docker start <CONTAINER IDまたはNAME>
```

### docker run
新しいDockerコンテナを起動状態で作成する。
httpd:latestイメージからcont1コンテナを作成するときの例。
```
$ docker run -d -it --name cont1 httpd:latest
```
- -dオプション
    - コンテナ作成後コンテナプロセスの標準入出力をコンソールからデタッチ（切り離す）する。（=ホストのプロンプトで作業を継続する）
    - -dがなければコンテナのプロンプトになる（ctrl-P → ctrl-Qのデタッチキーを使うとホストのプロンプトに戻る）
    - 実態としてはバックグラウンドでコンテナを起動している
- -iオプション
    - コンテナのSTDINにアタッチする（=コンテナプロセスの標準入力を開いたままにする）
- -tオプション
    - 疑似ターミナルを割り当てる
- --nameオプション
    - コンテナに名前を割り当てる、この例ではcont1という名前を付けている

まとめると上記のコマンドではhttpd:latestイメージからcont1という名前のコンテナを作成・バックグラウンドで実行しており、その際コンテナの標準入力を開いたままにしているということになる。

### docker attach
デタッチされたコンテナに再度ログインする。
```
$ docker attach <container ID もしくは name>
```
コンテナの標準入力でexitを打ち込むことでコンテナを抜けて停止させることができる。

### docker stop
起動しているコンテナを停止する。
```
$ docker stop <CONTAINER IDまたはNAME>
```

### docker rm
1つ、または複数のコンテナを削除する。
```
$ docker rm [OPTIONS] CONTAINER [CONTAINER...]
```
- -fオプション
    - 実行中のコンテナを強制的に削除（プロセスなのでSIGKILLが使える）

```
docker container prune
```
を使うことで停止中の全てのコンテナを削除することが出来る。

### docker ps
現在稼働しているコンテナの状況を表示する。

### docker inspect
Dockerコンテナの詳細を表示する。

## docker image <子コマンド>
```
$ docker image <子コマンド>
```
などのように使う。

### ls
Dockerイメージを一覧表示する。

### pull
docker pullと同じ？

### build
DockerfileからDockerイメージを作成する。

### load
ファイル、もしくは標準入力を通してtarアーカイブファイルから（gzip,bzip2, xzで圧縮されていてもよい）イメージやレポジトリを取り込む。docker loadと同じ？
```
$ docker load < busybox.tar.gz
Loaded image: busybox:latest
```
```
$ docker load --input fedora.tar
Loaded image: fedora:rawhide
Loaded image: fedora:20
```
- --input
    - 標準入力の代わりに、tarアーカイブファイルから読み込む

### save
1つまたは複数のDockerイメージをtarアーカイブに保存する。

### inspect
Dockerイメージの詳細表示。

## docker volume　<子コマンド>
### ls
Dockerボリュームの一覧を参照する。

### create
Dockerボリュームを作成する。

### rm
Dockerボリュームを削除する。

### prune
DockerコンテナからマウントされていないDockerボリュームを全て削除する。

### inspect
Dockerボリュームの詳細情報を参照する。

## Docker composeとは？
複数のDockerコンテナをまとめて起動・停止するためのツール。"Dockerfile"と"docker-compose.yml"というファイルを使ってアプリケーションのサービスを設定する。このファイルでは主に以下が定義される。
- Dockerイメージをビルドするための情報
- Dockerコンテナが利用するネットワークの情報
- 利用するDockerボリュームの情報

## 参考文献
Docker初心者はまずこれを見よう→[Dockerとは一体何なんだ？](https://tech-blog.rakus.co.jp/entry/20221007/docker)

Dockerの流れ→[Dockerコンテナの作成、起動～停止まで](https://qiita.com/kooohei/items/0e788a2ce8c30f9dba53)

Dockerの仕組み→ [Docker Engineとは何か](https://zenn.dev/ryoatsuta/articles/64dcc2e2b4e0cf)

docker run/createについて→[docker run/createコマンド](https://www.tohoho-web.com/docker/docker_run.html#:~:text=docker%20run%20%E3%81%AF%E3%82%B3%E3%83%B3%E3%83%86%E3%83%8A%E3%82%92,create%20%E3%82%92%E4%BD%BF%E7%94%A8%E3%81%97%E3%81%BE%E3%81%99%E3%80%82)

Dockerコマンド集→[Dockerコマンドリファレンス](https://docs.docker.jp/v19.03/engine/reference/commandline/index.html)