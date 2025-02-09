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