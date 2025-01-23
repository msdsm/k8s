# Myk8s

## 準備
- Docker for Desktopでk8sを有効化
- kubectlコマンドのインストール
- `helm`コマンドのインストール
- [kubernetes dashboardのインストール](https://github.com/kubernetes/dashboard)
- [`dashboard-adminuser.yaml`の作成](https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md)
- ダッシュボード起動(`kubectl -n kubernetes-dashboard port-forward svc/kubernetes-dashboard-kong-proxy 8443:443`)

## Node
- クラスタが持つリソースで最も大きい概念
```
kubectl get nodes
```
## Namespace
- k8sクラスタの内部の仮想的なクラスタ
```
kubectl get namespace
```

## Pod
- k8sの最小単位
- コンテナの集合体であり少なくとも1つのコンテナを持つ
```
kubectl get pod
kubectl apply -f pod.yml
kubectl config current-context
```

## RepricaSet
- 指定された数のpodのreprica(コピー)を維持することを目的としたリソース
- 必要に応じてPodをスケールアップまたはスケールダウンできる
```
kubectl get rs
kubectl apply -f rs.yml
kubectl get rs
kubectl get pod
```

## Deployment
- ReplicaSetを管理・操作するためのリソース
```
kubectl get deployments
kubectl apply -f dep.yml
kubectl get deployments
kubectl get rs
kubectl get pods
```

## Service
- Cluster内のPodへのネットワークアクセスを提供するための抽象化
- ServiceはPodのIPアドレスが変わっても安定したアクセスを提供
- これによりクライアントは常にサービス名でアクセスできる
### ClusterIP
- Cluster内部のIPでServiceを公開
```
kubectl apply -f dep2.yml
kubectl get pod
kubectl apply -f service.yml
kubectl run --image=centos:6 --restart=Never --rm -i testpo -- curl -s http://echoserver.default.svc.cluster.local:80
```
### NodePort
- 各NodeのIPにて静的なport上でserviceを公開
```
kubectl apply -f svc_node_port.yml
kubectl get svc
curl http://localhost:{ここにポート番号}
```

## 参考文献
- https://github.com/yagikota/Myk8s
