# Kubernetes入門

## Kubernetes上でhello world
- kubernetes上でhello-worldイメージを動かす
- `minikube stop && minikube delete && minikube start`
- `kubectl run hello-world --image hello-world --restart=Never`
- `kubectl get pod`
- `kubectl logs pod/hello-world`
- `kubectl delete pod/hello-world`

## Kubernetesリソース
| 分類      | 種別                           |
|-----------|--------------------------------|
| **ワークロード** | <ul><li>Pod</li><li>ReplicaSet</li><li>Deployment</li><li>StatefulSet</li></ul> |
| **サービス**   | <ul><li>Service</li><li>Ingress</li></ul> |
| **設定**      | <ul><li>ConfigMap</li><li>Secret</li></ul> |
| **ストレージ**  | <ul><li>PersistentVolume</li><li>PersistentVolumeClaim</li></ul> |

- Pod : 最小単位、コンテナを複数もつ
- ReplicaSet : podの集合。podをスケールできる
- Deployment : ReplicaSetの集合。ReplicaSetの世代管理が可能。
- StatefulSet : Podの集合。Podをスケールする際の名前が一定。
- Service : 外部公開、名前解決、L4ロードバランサー。
- Ingress : 外部公開。L7ロードバランサー。
- ConfigMap : 設定情報
- Secret : 機微情報
- PersistentVolume : 永続データの実態
- PersistentVolumeClaim : 永続データの要求。