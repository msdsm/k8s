# qiita-k8s
- https://qiita.com/Kta-M/items/ce475c0063d3d3f36d5d
- https://speakerdeck.com/ktam1219/yaruze-kuberneteshanzuon

- ファイルすべて写経して理解しながら書く
- 知らないことはすべてググりここにメモ残す

## 準備
- 以下実行できればok
- `.aws/credential`,`.aws/config`にiam user登録されていればok
```
docker -v
kubectl version
aws --version
aws-iam-authenticator help
eksctl version
```
- docker for desktopでk8sを有効化
- Clusterをdocker-for-desktopにする
```
kubectl config use-context docker-for-desktop
kubectl config current-context # docker-for-desktopと出力されたらok
```
- docker-for-desktopでingressが使えるようにする
  - `kubectl apply -k .`
  - `kustomization.yaml`のresourcesで指定しているgithubのリンクが消えており動かない
  - qiitaのコメントに記載されている以下のコマンドを実行することで代替
  - `kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.34.1/deploy/static/provider/cloud/deploy.yaml`
  - これエラーになる
  - `deploy.yaml`としてローカルに保存して一部編集
  - `kubectl apply -f deploy.yaml`を実行

## サンプルアプリをデプロイ
- githubのkubernetesからexamplesをcloneして設定変更
- `guestbook-ingress.yaml`作成
```
kubectl apply -f examples/guestbook/
kubectl apply -f guestbook-ingress.yaml
```
- `kubectl get ingress`でaddressがlocalhostになるまで待つ
- 以下デバッグコマンド
```
kubectl get ingress
kubectl get pods -n ingress-nginx
kubectl delete pod -n ingress-nginx ingress-nginx-controller-57f6b75ff5-4tsht
kubectl describe ingress guestbook-ingress
kubectl describe pod -n ingress-nginx ingress-nginx-controller-57f6b75ff5-4tsht
kubectl logs -n ingress-nginx ingress-nginx-controller-57f6b75ff5-4tsht
```
- addressがlocalhostにならない

## 用語メモ
### ローリングアップデート(rolling update)
- 仮想マシンを含む複数のコンピュータで構成されるシステムにおいてシステム全体を停止させず一部のコンピュータから順次アップデートすること
### Blue/Green Deployment
- ブルーグリーン・デプロイメントは、アプリケーションやマイクロサービスのリリース時に旧バージョン(ブルー環境)と新バージョン(グリーン環境)の両方を稼働させた状態で、トラフィックを徐々に新しいグリーン環境に移行させるモデル
### Control Plane
- KubernetesはControl PlaneとData Planeに分けて設計されている
- Control PlaneはKubernetesクラスターの管理と調整を行う部分
- `kube-apiserver`, `kube-scheduler`, `kube-controller-manager`, `etcd`などのコンポーネントがここに含まれる
- アプリケーションの管理やリソースのスケジューリング、状態の監視を行う
### Data Plane
- Data Planeは以下の要素で構成される
  - Pod
  - Networking : pod間の通信やserviceとpod間の通信を管理
  - Storage : podが永続的なstorageを使うための管理をdata Planeがやる
  - podのライフサイクル : podの起動、停止、スケーリング、再起動
- 以下の図がわかりやすい
  - Nodeの集合がDataPlaneで、DataPlaneとControlPlaneを合わせてCluster
![cluster](./cluster.png)
(https://qiita.com/Kta-M/items/ce475c0063d3d3f36d5d より引用)

### 宣言的設定と命令的設定
- 宣言的 : Declarative : What you want
- 命令的 : Imperative : How you want
- 宣言的設定はゴールだけを記述してやり方はすべてお任せ
- 命令的設定はやり方も指定
- kubernetesはyamlファイルで宣言的設定

### `kustomization.yaml`
- kubernetesリソースのカスタマイズを行うためのツール
- `kubectl apply -k .`を実行すると`kustomization.yaml`ファイルを探して実行
- 同時にapplyしたいときに以下のように書けて便利
```yaml
resources:
  - deployment.yaml
  - service.yaml
  - ingress.yaml
```
- 他にもリソースの変更や統合が可能
- リソースの参照、パッチの適用、共通の設定の追加など
- https://zenn.dev/ring_belle/articles/kubernetes-kustomization-summary