## Docker学習内容の保存用リポジトリ

学習内容 : https://www.udemy.com/course/aidocker/

### Dockerfileの書き方
FROM <元となるImage>

RUN <Imageを構成する際に必要なコマンド、DockerImageに保存したいコマンド>
>RUNコマンドのTips
・RUN毎にImageLayerが作成されファイルサイズが大きくなる
・ImageLayerは必要最小限にするのがベストプラクティス
・Layerを作るのは、RUN / COPY / ADD の3つ
・複数コマンドを実行する場合は、&&でコマンドを繋げて一つのRUNに記載する (&&が多く一行が長くなる場合は\で改行する、コマンドごとに\で区切るのもよい)
・ただし、Dockerfileを作成する過程では、RUNコマンドを複数に分割してCacheを利用したほうが開発効率が良い
・RUNのコマンドはWORKDIRで指定しない場合、全てroot直下で実行される

COPY <HOST側のコピー元ディレクトリ名(ファイル名)> <Container側のコピー先ディレクトリ>
>単純なコピーの場合

ADD <HOST側のコピー元ディレクトリ名(ファイル名)> <Container側のコピー先ディレクトリ>
>tarの圧縮ファイルをコピー＆解凍する場合

ENV <Imageに設定する環境変数>

WORKDIR <作業したいディレクトリ名(Container側)>
>このコマンド以降のRUNコマンドを全て指定したディレクトリ直下で行う
>指定したディレクトリがない場合は作成される

ENTRYPOINT <["コマンド"]>
CMD <["引数"]>
>run時に上書きしてほしくないコマンドをENTRYPOINTで指定

CMD <["DockerImageのデフォルトのコマンド、`docker run` 時に起動するコマンド", "Param"]>
>CMDはImageLayerを作らない


### Dockerfileのビルド
`docker build <指定するDockerfileのディレクトリ(Host側)>`
`docker build -f <dockerfile名> <指定するDockerfileのディレクトリ(Host側)>`
>`docker build`でディレクトリを指定するのは、Docker daemonがディレクトリをBuild Contextとして処理するため


### docker runコマンドのオプション
-d : バックグラウンドで実行

-it : インタラクティブ＋見やすい画面表示

-v < Host direcroty >:< Container directory > : ホストのファイルシステムをコンテナにマウント (※ ホストのファイルが実際にコンテナ内にあるわけではない‼)
>指定する Container directory がない場合は、コンテナ側で自動で作成される

-u \$(id -u) : $(id -g) -v < Host direcroty >:< Container directory > : ホストのファイルシステムをコンテナにマウント (注）ホストのファイルが実際にコンテナ内にあるわけではない‼)
>ユーザIDとグループIDを指定してコンテナ起動

-p < Host port >:< Container port > : ホストのポートをコンテナのポートに繋げる

--cpus < cpu num > --memory < memory num > : CPUやMemoryのサイズを指定してRun
>コンテナのCPUやメモリの情報は`docker inspect コンテナ名`で表示可能

### その他のTips
sh -x < shファイル >で取得したshファイルで指定できるオプションの確認可能
⇒ Dockerfile内でshファイルをバッチ処理する方法を調べるときに使用した