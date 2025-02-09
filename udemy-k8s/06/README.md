# Kubernetes基本操作


## Podの作成/確認/削除 : `06-01`
### 概要
1. hello-worldコンテナを含むPodを作成
1. Podが起動していることを確認
1. Podを削除
### コマンド
- `kubectl apply -f pod.yml`
- `kubectl get -f pod.yml`
- `kubectl get pod`
- `kubectl delete -f pod.yml`

## nginx Podの作成 : `06-02`
### 概要
1. nginxを含むPodのマニフェストファイルを作成
2. Kubernetesにリソース作成
3. Podが起動していることを確認
4. Podを削除
### コマンド
- `kubectl apply -f pod.yml`
- `kubectl get -f pod.yml`
- `kubectl get pod`
- `kubectl delete -f pod.yml`

## 終了しないPodを作成 : `06-03`
### 概要
1. CentOS:7 にコマンドを渡して終了しないマニフェストを作成
2. Podを作成
3. Podが終了しないことを確認
4. Podを削除
### コマンド
- `kubectl apply -f pod.yml`
- `kubectl get -f pod.yml`
- `kubectl get pod`
- `kubectl delete -f pod.yml`

## 起動中のPodの中に入る : `06-04`
### 概要
1. CentOSとnginxを起動するマニフェストを作成
2. CentOSとnginxのPodを起動
3. PodのIPアドレスを確認
4. 起動したCentOSコンテナ内に入る
5. nginxに対してcurlを実行
6. CentOSから出る
7. CentOSとnginxのPodを削除
### コマンド
- `kubectl apply -f pods.yml`
- `kubectl get pod -o wide`
  - これで各podのIPアドレスを取得できる
- `kubectl exec -it debug -- sh` : CentOSコンテナに入る
- `curl http://10.244.0.8` : CentOSコンテナ内からnginx podにhttpリクエスト送る
  - nginxのhtmlが返ってきたらok
- `kubectl delete -f pods.yml`

## Podとホスト間でファイル転送
### 概要
1. CentOSのPodを起動
2. ホスト上でファイル作成してPodへ転送
3. Podへ入って確認
4. Pod上でファイル作成
5. Podからホストへファイルを転送して確認
6. CentOSのPodを削除
### コマンド
- `kubectl apply -f pod.yml`
- `kubectl cp ./sample.txt debug:/var/tmp/sample.txt`
- `kubectl exec -it debug -- sh`
- `cat /var/tmp/sample.txt`
- `vi sample2.txt`
- `kubectl cp debug:/root/sample2.txt ./sample2.txt`
- `cat sample2.txt`