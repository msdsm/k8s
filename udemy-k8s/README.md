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
- `kubectl exec -it POD sh` : 指定したPodに入ってシェル操作を行えるようにする
  - `POD` : 中に入りたいPod名
- `kubectl cp SRC DEST` : ファイル転送
  - `SRC` : 転送元ファイル
  - `DEST` : 転送先ファイル
- `kubectl cp <src> <pod-name>:<dest>` : ファイル転送(ホスト->Pod)
  - `src` : 転送元ファイル名/フォルダ名
  - `pod-name` : 転送先のPod名
  - `dest` : 転送先フォルダ名/ファイル名
- `kubectl cp <pod-name>:<src> <dest>` : ファイル転送(Pod->ホスト)
- `kubectl describe [TYPE/NAME]` : 指定したリソースの状態を確認
  - `TYPE/NAME` : リソース種別とリソース名を指定
- `kubectl logs [TYPE/NAME][--tail=n]` : 指定したリソースの状態を確認
  - `TYPE/NAME` : リソース種別とリソース名を指定
  - `--tail=n` : 直近のnレコードだけ取得
- `kubectl rollout history TYPE/NAME` : ロールアウトの履歴確認
  - `TYPE` : リソース種別
  - `NAME` : リソース名
- `kubectl rollout undo TYPE/NAME --to-revision=N` : ロールバック
  - `TYPE` : リソース種別
  - `NAME` : リソース名
  - `--to-revision=N` : 指定されたリビジョンに戻す
- `kubectl create secret generic NAME [option]` : Secret作成
  - `NAME` : Secretの名前を指定
  - オプション
    - `--from-literal=key=value` : キーバリューを指定して作成
    - `--from-file=filename` : ファイルから作成 

## マニフェストファイルの構成
- 種別、メタデータ、コンテナ定義の3構成
- (apiVersion, kind), (metadata), (spec)
- `apiVersion`でapi version指定
- `kind`でリソース指定
- `metadata.name`で一意に定まる名前を定義
- `metadata.labels`でkey-value型の任意の名前を定義

### podのマニフェストファイル
- `spec.containers.name`でコンテナ名を定義
- `spec.containers.image`でimageを定義
- `spec.containers.command`, `spec.containers.args`でコマンド指定可能
- `spec.containers.env`で環境変数を設定
- `spec.containers.volumeMounts`でコンテナへマウントするストレージを指定、このとき`spec.volumes.name`に一致させる

### ReplicaSetのマニフェストファイル
- 主要な`spec`は`replicas`, `selector`, `template`
- `replicas`はPodを複製する数を指定でき、この値を変更することでスケールアウトやスケールインが可能
- `selector`は複製するPod数を数えるために使うラベルを指定
- `template`は複製するPodのマニフェストを指定し、中身はPodと同じ
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: sample
  namespace: default
spec:
  replicas: ...
  selector: ...
  template: ...
```

### Deploymentのマニフェストファイル
- DeploymentとはReplicaSetの集合
- ReplicaSetの世代管理が可能
- 主要な`spec`は5種類
- `replicas`, `selector`, `template`はReplicaSetと同じ
- `revisionHistoryLimit`はReplicaSetの履歴保存数を指定
- `strategy`はデプロイ方法を指定
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  namespace: default
spec:
  replicas: ...
  selector: ...
  revisionHistoryLimit: ...
  strategy: ...
  template: ...
```

### Serviceのマニフェストファイル
- Serviceは外部公開、名前解決、L4ロードバランサー
- 以下の4種類がある
  - ClusterIP
  - NodePort
  - LoadBalancer
  - ExternalName
- 主要なspecは4種類
```yaml
apiVersion: v1
kind: Service
metadata:
  name: sample
  namespace: default
spec:
  type: ... # 4種類から選択
  clusterIP: ... # ClusterIPを選択した場合はNone, 空文字, IPアドレスのいずれかを指定
  ports: ...
  selector: ...
```

### ConfigMapのマニフェストファイル
- Kubernetesで利用する設定情報
- `spec`ではなく`data`にキーバリューで保存
- ConfigMapのリソース利用方法は2種類
  - 環境変数へ渡す : `spec.containers.env.valueFrom`にConfigMapを指定
  - ファイルとしてマウント : `spec.volumes`と`spec.containers.volumeMounts`に指定
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: sample
  namespace: default
data:
  KEY: VALUE
```

### Secretのマニフェストファイル
- SecretとはKubernetes上で扱う機微情報
- ConfigMapと同様に`spec`ではなく`data`にキーバリューで保存
- リソース生成方法は以下の2種類
  - コマンドで直接生成 : キーバリューは引数で複数指定
  - マニフェストファイルから生成 : マニフェストファイルから生成はkubectl applyでありBase64文字列の取得が必要
- 利用方法はConfigMapと同様に2種類あり、環境変数利用とマウント利用
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: sample
  namespace: default
data:
  KEY: VALUE
```

### PersistentVolume(PV)のマニフェストファイル
- PVとは永続データの実態でありストレージを抽象化する
- 主要3プロパティと削除時動作と保存時情報から構成される
- ストレージを抽象化定義する3プロパティ
  - `storageClassName`
  - `accessModes`
  - `capacity`
- 削除時動作を定義するプロパティ
  - `persistentVolumeReclaimPolicy`
  - RetainはPVCが消えてもPVを残すことを意味する
  - DeleteはPVCが消えたらPVも消すことを意味する
- 保存先を定義するプロパティ
  - `hostPath`
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: storage
spec:
  storageClassName: host # ストレージの種類を定義
  accessModes: ["ReadWriteMany"] # 読み書きの定義(ReadWriteOnce, ReadOnlyMany, ReadWriteMany)
  capacity: # ストレージ容量の定義
    storage: 1Gi
  persistentVolumeReclaimPolicy: Retain # 削除時動作を定義するプロパティ
  hostPath:
    path: "/data/storage"
    type: Directory
```

### PersistentVolumeClaim(PVC)のマニフェストファイル
- PVCとは永続データの要求
- PVC -> PV -> Storage
- PVの主要3プロパティから構成される
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: volume-claim
spec:
  storageClassName: slow
  accessModes: ["ReadWriteMany"]
  resources:
    requests:
      storage: 1Gi
```

### StatefulSetのマニフェストファイル
- StatefulSetはPodの集合でPodをスケールする際の名前が一定
- マニフェストファイルはdeploymentと似ている
```yaml
apiVersion: apps/v1
kind: StatefulSet
...
spec:
  ...
  updateStrategy:
    type: RollingUpdate
  serviceName: frontend
  template: 
    ...
  volumeClaimTemplaates: # PVCのテンプレートを定義
    ...
```

### Ingressのマニフェストファイル
- Ingressとは外部公開、L7ロードバランサー
- URLでサービスを切り替えられる
```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: frontend
spec:
  rules:
  - http:
    paths:
    - path: /
      backend: # ここに転送先のサービスを指定する
        serviceName: web-svc
        servicePort: 80
```