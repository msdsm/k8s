# Kubernetesリソース

## Pod作成 : `07-01`
### 概要
1. ホストにフォルダ、ファイルを作成
2. 作成したフォルダをマウントしたPodマニフェストファイルを作成
3. リソース作成

### コマンド
- `kubectl apply -f pod.yml`
- `kubectl exec -it sample -- sh`
- `cat /home/nginx/message.txt`
- コンテナ上で`message.txt`をvimで編集して保存するとホストマシン上でも変更されていることを確認
- 次に、ホストマシン上で`message.txt`を編集後にコンテナ上で`cat`で見ると変更されていることを確認
- `kubectl delete pod/sample`

## ReplicaSet作成 : `07-02`
### 概要
1. ReplicaSetマニフェストファイル作成
2. リソース作成
3. 手動スケールアウト
### コマンド
- `kubectl apply -f replicaset.yml`
- `kubectl get pod`
- `replicaset`を2に指定しているので2個のpodが作成されている
- この値を3に編集
- 再度`kubectl apply -f replicaset.yml`
- `kubectl get pod`
- 3個作成されている

## Deployment作成 : `07-03`
### 概要
1. Deploymentマニフェストファイルを作成
2. リソース作成
3. ロールアウト履歴確認
4. Deployment修正
5. ロールアウト履歴確認
6. ロールバック
### コマンド
- `kubectl apply -f deployment.yml`
- `deployment.yml`でannotationsにコメント追加してnginxのimage version変更してapply
- `kubectl rollout history deploy/nginx`
  - 履歴表示される
- `kubectl rollout undo deploy/nginx`
  - ロールバック
- `kubectl rollout history deploy/nginx`
  - 以前のものに戻っている

## Service作成: `07-04`
### 概要
1. NodePortのServiceマニフェストファイル作成
2. リソース作成
3. ブラウザからアクセスして動作確認
### コマンド　
- `kubectl apply -f service.yml`
- `localhost:30000`のノードポートにアクセス
- nginxのページが開ければok

## ConfigMap作成: `07-05`
### 概要
1. ConfigMapとPodを含むマニフェストファイル作成
2. リソース作成
3. Podに入ってConfigMapが接続されていることを確認
### コマンド
- `kubectl apply -f configmap.yml`
- `kubectl exec -it sample -- sh`
- `cat /home/nginx/sample.cfg`でConfigMapで指定したファイルがマウントされていることを確認
- `env`を実行してConfigMapで指定した環境変数が入っていることを確認

## Secret作成: `07-06`
### 概要
1. SecretとPodを含むマニフェストファイル作成
2. リソース作成
3. Podに入ってSecretが接続されていることを確認

### コマンド
- コマンドでやる方法は以下
  - `kubectl create secret generic sample-secret --from-literal=message='Hello World !' --from-file=./keyfile`
  - `kubectl get secret/sample-secret -o yaml`
  - `kubectl delete secret/sample-secret`
- ファイルでやる方法は以下
  - `kubectl apply -f secret.yml`
  - `kubectl exec -it sample -- sh`
  - `kubectl delete -f secret.yml`

## 永続データ(PV, PVC)作成 : `07-07`
### 概要
1. PVとPVCを含むマニフェストファイルを作成
2. リソース作成
### コマンド
- `kubectl apply -f storage.yml`
- `kubectl get pvc,pv`
- `kubectl delete -f storage.yml`

## StatefulSet作成 : `07-08`
### 概要
1. StatefulSetおよびServiceのマニフェストを作成
2. デバッグ用PodからService経由でPodにアクセス
### コマンド
- `kubectl apply -f statefulset.yml`
- `kubectl run debug --image=centos:7 -it --rm --restart=Never -- sh`
- `curl http://nginx-0.sample-svc`