# コマンド
## コンテナの作成・起動
```powershell
C:\Users\hirok> docker run --name apa000ex1 -d httpd
Unable to find image 'httpd:latest' locally
latest: Pulling from library/httpd
26c307b5e35a: Pull complete
c271d17baba2: Pull complete
4f4fb700ef54: Pull complete
04951bf53bdd: Pull complete
2c2b87df2933: Pull complete
c4a216db6600: Pull complete
Digest: sha256:2920ed8587277d6aa8ea785e143e970835057123dc7bf1199d102c60c80a73bb
Status: Downloaded newer image for httpd:latest
edb31b2f63000993a9d83ed0153b4184f10d5cf140bec44ba253d4435093b858
C:\Users\hirok>
```

|TH|TH|
|-|-|
|--name apa000ex1|「apa000ex1」という名前のコンテナを作る|
|-d|バックグラウンドで実行する|
|httpd|Apacheのイメージ名。バージョン指定でない時は、最新版を使用する|

`docker run`は以下のコマンドの実行をひとまとめにしたもの  
イメージがなければダウンロードする

- `docker image pull`
- `docker container create`
- `docker container start`

## コンテナの稼働を確認する
```powershell title="動いているコンテナ一覧"
C:\Users\hirok> docker ps
CONTAINER ID   IMAGE     COMMAND              CREATED         STATUS         PORTS     NAMES
edb31b2f6300   httpd     "httpd-foreground"   3 minutes ago   Up 3 minutes   80/tcp    apa000ex1
C:\Users\hirok>
```

```powershell title="存在するコンテナ一覧"
C:\Users\hirok> docker ps -a
CONTAINER ID   IMAGE     COMMAND              CREATED         STATUS         PORTS     NAMES
edb31b2f6300   httpd     "httpd-foreground"   5 minutes ago   Up 5 minutes   80/tcp    apa000ex1
C:\Users\hirok>
```

## コンテナの稼働を停止する
```powershell title="コンテナの稼働を停止する"
C:\Users\hirok> docker stop apa000ex1
apa000ex1
C:\Users\hirok>
```

### コンテナの稼働を停止を確認する
```powershell
C:\Users\hirok> docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
C:\Users\hirok> docker ps -a
CONTAINER ID   IMAGE     COMMAND              CREATED          STATUS                      PORTS     NAMES
edb31b2f6300   httpd     "httpd-foreground"   12 minutes ago   Exited (0) 58 seconds ago             apa000ex1
C:\Users\hirok>
```