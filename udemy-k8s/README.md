# Docker + Kubernetesで構築するwebアプリケーション

##  `minikube`コマンド
- `minikube start` : クラスタ実行
- `minikube stop` : クラスタ停止
- `minikube status` : クラスタ状態確認
- `minikube addons list`: アドオン一覧確認
- : アドオン追加
- : アドオン削除


## dockerコマンド
- `docker image pull NAME:TAG` : イメージ取得
- `docker image ls` : イメージ一覧
- `docker image rm NAME:TAG` : イメージ削除
- `docker container run [OPTION] NAME[:TAG] [COMMAND]` : イメージを実行
  - `OPTION` : オプション
    - `-d` : バックグラウンド実行
    - `-it` : Shell実行する際にあわせて指定
    - `-e KEY=VALUE` : 環境変数を指定
    - `--name NAME` : 実行時のコンテナ名
    - `-p CONTAINER:HOST` : コンテナポートをホストにマッピング
  - `NAME` : Dockerイメージ名
  - `TAG` : Dockerイメージタグ名, 省略したらlatest
  - `COMMAND` : Dockerコンテナに与えるコマンド
- `docker container stop CONTAINER_ID` : コンテナ停止
- `docker container ls [OPTION]` : コンテナ一覧表示
  - `OPTION` : オプション
    - `-a` : 停止中のコンテナも表示
- `docker container rm CONTAINER_ID` : コンテナ削除
- `docker container prune` : 使用されていないコンテナを削除
- `docker system prune` : 使用されていないデータを削除(コンテナとイメージをまとめて削除)
- `docker build [OPTION] PATH` : イメージを作成
  - `OPTION` : オプション
    - `-t` : イメージ名を指定
    - `-f` : Dockerfileの名前を指定、`-f`で指定しなければ`Dockerfile`
  - `PATH` : Dockerfileが保存されているディレクトリへのパス
- `docker tag SRC_NAME[:TAG] TRG_NAME[:TAG]` : タグ名変更
- `docker login [-u USER] [-p PASSWORD]` : DockerHubへログイン
- `docker push IMAGE_NAME[:TAG]` : イメージ公開

## dockerコマンド省略記法
| 内容 | 元コマンド | 省略記法 |
|--------|------------|-----------|
| コンテナ一覧表示 | `docker container ls` | `docker ps` |
| 全てのコンテナ一覧表示 | `docker container ls -a` | `docker ps -a` |
| コンテナ停止 | `docker container stop` | `docker stop` |
| コンテナ起動 | `docker container start` | `docker start` |
| コンテナ削除 | `docker container rm` | `docker rm` |
| イメージ一覧表示 | `docker image ls` | `docker images` |
| イメージ削除 | `docker image rm` | `docker rmi` |
| イメージビルド | `docker image build` | `docker build` |
| ネットワーク一覧表示 | `docker network ls` | `docker network ls` |
| ボリューム一覧表示 | `docker volume ls` | `docker volume ls` |
| コンテナログ表示 | `docker container logs` | `docker logs` |
| コンテナ内でコマンド実行 | `docker container exec` | `docker exec` |
| コンテナ内にアタッチ | `docker container attach` | `docker attach` |


## kubectlコマンド
- `kubectl apply -f <filename>` : マニフェストファイルを指定してリソースを作成/変更
- `kubectl get [-f <filename>][TYPE]` : 指定したリソースの状態を確認
  - `-f <filename>` : マニフェストファイルsパス
  - `TYPE` : リソース種別 (pod, replicasetなど)
- `kubectl delete [-f <filename>][TYPE/NAME][-o [wide|yaml]]` : リソース削除コマンド
  - `-f` : マニフェストファイルパス
  - `TYPE/NAME` : リソース種別/リソース名
  - `-o [wide|yaml]` : 出力形式を指定
    - `wide` : 追加情報の表示
    - `yaml` : YAML形式で表示