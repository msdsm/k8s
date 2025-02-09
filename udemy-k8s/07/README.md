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
