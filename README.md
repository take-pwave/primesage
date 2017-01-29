# プライマリsagemath環境の構築

sageのPPAを使ってsagemathの環境を提供するためのイメージです。

基本は、sageユーザでしかコマンドを実行できません。

また、sageユーザは、以下のコマンドしかsudoで実行することができません。
- apt-get
- sage

### dockerの実行
キャラクターベースでsageを使用する場合には、以下のコマンドを実行します。
```bash
$ docker run -i -t takepwave/primesage
```

jupyterのノートブックを使用する場合には、以下のコマンドを実行します。
(通常ならこれで起動するはずですが、ノートブックでsage kernelを起動すると落ちる障害が発生します)

```bash
$ docker run -p 127.0.0.1:8888:8888 -d -t takepwave/primesage /opt/sage_launcher \
	--notebook=ipython --ip='*' --port=8888
```

補足情報：
- https://github.com/ipython/ipython/issues/7062

暫定処置として、以下のように起動してください。
```bash
$ docker run -p 127.0.0.1:8888:8888 -d -t takepwave/primesage /opt/sage_launcher \
	-sh -c "ipython notebook --no-browser --ip='0.0.0.0' --port=8888"
```

これで、ブラウザーで以下のURLを入力するとjupyterの画面になります。
```
http://localhost:8888/
```

## ローカルディスクのノートブックを使う
dockerを起動しているマシンにあるノートブックをdockerのsage jupyterで使用するには、dockerの-vオプションを使用します。

```
-v ローカルのノートブックのパス:/home/sage/notebook
```

以下は、ローカルの$HOME/notebookを/home/sage/notebookにマウントした時の例です。

```bash
$ docker run -v $HOME/notebook/:/home/sage/notebook \
	-p 127.0.0.1:8888:8888 -d -t takepwave/primesage /opt/sage_launcher \
	-sh -c "ipython notebook --no-browser --ip='0.0.0.0' --port=8888"
```

## コンテナーの操作
現在動いているコンテナーは、docker psコマンドで確認することができます。
-aのオプションを付けると停止しているコンテナーも知ることができます。

ここでキーとなるのは、sage_launcherのCONTAINER IDです。これを控えておいて次の操作をしてみましょう。

```bash
$ docker ps -a
CONTAINER ID        IMAGE                COMMAND                  CREATED             STATUS              PORTS                      NAMES
4a704622db7a        takepwave/sagemath   "/opt/sage_launcher -"   45 minutes ago      Up 45 minutes       127.0.0.1:8888->8888/tcp   berserk_stonebraker
```

### dockerコンテナーの停止
実行中のコンテナーを停止するには、docker stopコマンドを使用します。

この時、先ほど控えたCONTAINER IDを使用します。

```bash
$ docker stop 4a704622db7a
4a704622db7a
```

### dockerコンテナー再開
停止したコンテナーを再開するには、docker startコマンドを使用します。

docker startの後にdocker ps -aで調べたCONTAINER IDを指定します。

```bash
$ docker ps -a
CONTAINER ID        IMAGE                COMMAND                  CREATED             STATUS                       PORTS               NAMES
4a704622db7a        takepwave/sagemath   "/opt/sage_launcher -"   55 minutes ago      Exited (137) 2 minutes ago 
$ docker start 4a704622db7a
4a704622db7a
```

このように一度起動したjupyter環境を簡単に停止、再開することができます。

## Dockerのインストール
以下のサイトからご使用の環境にあったDockerをダウンロードし、インストールしてください。

- Mac OSX: https://docs.docker.com/docker-for-mac/
- Windows 10: https://docs.docker.com/docker-for-windows/

CentOSの場合は、以下のコマンドでインストールできます。
```bash
$ sudo yum install docker
```

Ubuntuの場合には、以下のコマンドでインストールできます。
```bash
$ sudo apt-get install docker.io
```
