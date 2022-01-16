<span style="font-size: 80%">

## Docker学習内容の保存用リポジトリ

学習内容 : https://www.udemy.com/course/aidocker/

### Dockerfileの書き方

FROM <元となるImage>
<br>

RUN <Imageを構成する際に必要なコマンド、DockerImageに保存したいコマンド>
>RUNコマンドのTips
・RUN毎にImageLayerが作成されファイルサイズが大きくなる<br>
・ImageLayerは必要最小限にするのがベストプラクティス<br>
・Layerを作るのは、RUN / COPY / ADD の3つ<br>
・複数コマンドを実行する場合は、&&でコマンドを繋げて一つのRUNに記載する (&&が多く一行が長くなる場合は\で改行する、コマンドごとに\で区切るのもよい)<br>
・ただし、Dockerfileを作成する過程では、RUNコマンドを複数に分割してCacheを利用したほうが開発効率が良い<br>
・RUNのコマンドはWORKDIRで指定しない場合、全てroot直下で実行される

<br>

COPY <HOST側のコピー元ディレクトリ名(ファイル名)> <Container側のコピー先ディレクトリ>
>単純なコピーの場合

<br>

ADD <HOST側のコピー元ディレクトリ名(ファイル名)> <Container側のコピー先ディレクトリ>
>tarの圧縮ファイルをコピー＆解凍する場合

<br>

ENV <Imageに設定する環境変数>
<br>

WORKDIR <作業したいディレクトリ名(Container側)>
>このコマンド以降のRUNコマンドを全て指定したディレクトリ直下で行う<br>
>指定したディレクトリがない場合は作成される

<br>

ENTRYPOINT <["コマンド"]>
CMD <["引数"]>
>run時に上書きしてほしくないコマンドをENTRYPOINTで指定

<br>

CMD <["DockerImageのデフォルトのコマンド、`docker run` 時に起動するコマンド", "Param"]>
>CMDはImageLayerを作らない

<br>

### Dockerfileのビルド
- `docker build <指定するDockerfileのディレクトリ(Host側)>`
- `docker build -f <dockerfile名> <指定するDockerfileのディレクトリ(Host側)>`
>`docker build`でディレクトリを指定するのは、Docker daemonがディレクトリをBuild Contextとして処理するため

<br>

### docker runコマンドのオプション
-d : バックグラウンドで実行
<br>

-it : インタラクティブ＋見やすい画面表示
<br>

-v < Host direcroty >:< Container directory > : ホストのファイルシステムをコンテナにマウント (※ ホストのファイルが実際にコンテナ内にあるわけではない‼)
>指定する Container directory がない場合は、コンテナ側で自動で作成される

<br>

-u \$(id -u) : $(id -g) -v < Host direcroty >:< Container directory > : ホストのファイルシステムをコンテナにマウント (注）ホストのファイルが実際にコンテナ内にあるわけではない‼)
>ユーザIDとグループIDを指定してコンテナ起動

<br>

-p < Host port >:< Container port > : ホストのポートをコンテナのポートに繋げる

<br>

--cpus < cpu num > --memory < memory num > : CPUやMemoryのサイズを指定してRun
>コンテナのCPUやメモリの情報は`docker inspect コンテナ名`で表示可能

<br>

### その他のTips
sh -x < shファイル >で取得したshファイルで指定できるオプションの確認可能<br>
⇒ Dockerfile内でshファイルをバッチ処理する方法を調べるときに使用した
<br>

AWSのEC2にssh接続する際 (ssh-In)<br>
コマンドは`ssh -i <xxx.pem(sshKey)> <username>@<hostname>`<br>
sshはリモート接続でシェルを操作したいときに使用するコマンド<br>
上で指定するusernameはサービスの名前(今回はUbuntu)、hostnameはEC2のパブリックDNS<br>
<br>

#### AWSのインスタンス内にDockerの環境をセットアップ
１．DockerのCommunity EditionをUbuntuサーバー(EC2)にインストールする際は以下のコマンド<br>
`sudo apt-get update`で`apt-get`コマンドを更新し、ubuntuで`apt-get`コマンドを使用できるようにする<br>
２．つぎに以下のコマンド<br>
`sudo apt-get install docker.io`<br>
３．これでdockerコマンドを使えるようにはなるが、この状態では毎回sudoをコマンドに入力必要がある。それは手間なので、Dockerグループを作成<br>
`sudo gpasswd -a ubuntu docker`<br>
⇒ user ubuntu を group docker に追加<br>
この操作後、一度`exit`でログアウトし、再度ssh-Inを行う<br>
一度`exit`でログアウトし、再度ssh-Inを行うことで、グループ作成の変更が反映される<br>
<br>

**Docker imageを共有する方法**
1. Docker HubにImageをPush
    - 環境構築する側でインターネット接続が必要
1. Dockerfileを共有
    - 環境構築する側でインターネット接続が必要
    - `sftp -i <xxx.pem(sshKey)> <username>@<hostname>`でリモートにアクセス
        - `put <ローカルファイルorディレクトリ> <リモートディレクトリ>`でローカルのファイルやディレクトリをリモートのディレクトリにコピー
        - `get <リモートファイルorディレクトリ> <ローカルディレクトリ>`でリモートのファイルやディレクトリをローカルのディレクトリにコピー
1. Docker imageをtarファイルに圧縮して共有
    - 容量が大きいものは圧縮・共有・解凍それぞれで時間がかかる
    - tarファイルに圧縮 : `docker save < image ID > > < xxx.tar >`
    - tarファイルを展開 : `docker load < < xxx.tar >`

</span>