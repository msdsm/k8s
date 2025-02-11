# Webアプリケーション構築 実践

## アーキテクチャ
```mermaid
flowchart LR
    users([Users]) --> ingress
    
    subgraph minikube[minikube cluster]
        direction LR
        ingress[Ingress]
        web_cm[ConfigMap]
        
        subgraph web[Web nginx]
            web_svc[Service]
            subgraph web_dep[Deployment]
                web_pod[Pod]
            end
        end
        
        subgraph ap[AP Node.js]
            ap_svc[Service]
            subgraph ap_dep[Deployment]
                ap_pod[Pod]
            end
        end
        
        subgraph db[DB MongoDB]
            db_svc[Headless\nService]
            subgraph db_ss[StatefulSet]
                subgraph db_pod[Pod]
                    pvc[Persistent\nVolume Claim]
                end
            end
        end

        secret[Secret]
        
        subgraph storage[Local Storage]
            pv[Persistent\nVolume]
        end
        
        %% Connections
        ingress --> web_svc
        web_svc --> web_pod
        web_pod --> ap_svc
        ap_svc --> ap_pod
        ap_pod --> db_svc
        db_svc --> db_pod
        pvc --> pv
        
        %% Additional connections
        web_dep --> web_cm
        ap_dep --> secret
        db_ss --> secret
        
        %% Styling
        classDef default fill:#f9f9f9,stroke:#333,stroke-width:1px
        classDef pod fill:#ddd,stroke:#666
        classDef svc fill:#326ce5,stroke:#fff,color:#fff
        classDef volume fill:#ff9900,stroke:#fff,color:#fff
        classDef config fill:#33cc33,stroke:#fff,color:#fff
        classDef external fill:#f9f9f9,stroke:#333,stroke-width:2px,stroke-dasharray: 5 5
        
        class web_pod,ap_pod,db_pod pod
        class web_svc,ap_svc,db_svc svc
        class pv,pvc volume
        class web_cm,secret config
        class users external
    end
```

## **1. デバッグ用イメージ作成** : `08-01`
### 内容
1. Dockerで作成したイメージを実行
2. 実行中コンテナに入る
3. 必要なコマンドが実行可能なことを確認
4. KubernetesでPodを作成
### 手順
1. `Dockerfile`, `mongodb-org-4.0.repo`, `debug-pod.yml`を作成
2. `kubectl apply -f debug-pod.yml`
3. `kubectl exec -it debug -- sh`でpodに入る
4. `jq`, `mongodb`, `ip`コマンドを実行できるか確認

## **2. DBサーバーのイメージ作成** : `08-02`
### 内容
1. DB初期化処理実装
2. イメージ作成
3. コンテナに入って動作確認
### 手順
1. `docker-entrypoint.sh`, `.dockerignore`, `Dockerfile`を実装
2. `docker build -t weblog-db:v1.0.0 .`
3. `docker run -d weblog-db:v1.0.0`
4. `docker container ls`      
5. `docker exec -it vibrant_babbage sh`
6. `mongo`で動作確認
7. `docker container stop vibrant_babbage`
8. `docker container prune`

## **3. DBサーバーの構築 (ストレージ)** : `08-03`
### 内容
1. PersistentVolumeとPersistentVolumeClaimのペアを作成
2. PVCはDB(MongoDB)にあり, PVはhostpathを設定するためLocal Storageにある
### 手順
1. `weblog-db-storage.yml`を作成
2. `kubectl apply -f weblog-db-storage.yml`
3. `kubectl get pv,pvc`

## **4. DBサーバーの構築 (Pod)** : `08-04`
### 内容
1. DBサーバーを構築する
2. Podを立ててPVCと接続させる
### 手順
1. `weblog-db-storage.yml`を持ってくる
2. pv, pvcに加えてPodを実装
3. `kubectl apply -f weblog-db-storage.yml`
4. `kubectl exec -it mongodb -- sh`
5. `mongodb`
6. `show dbs`
7. `exit`でdbから抜ける
8. `exit`でPodから抜ける
9. `kubectl delete -f weblog-db-storage.yml`

## **5. DBサーバーの構築 (Pod + Secret)** : `08-05`
### 概要
- Secretを作成してmongodb内のPodに接続する
1. keyfile (ランダム文字列) を作成
2. Secretリソースを作成
3. SecretリソースのYAMLを取得
4. `weblog-db-pod.yml`へマージ
5. Secretリソースを削除
### 手順
1. `openssl rand -base64 1024 | tr -d '\r\n' | cut -c 1-1024 > keyfile`で文字列生成
2. `kubectl create secret generic mongo-secret --from-literal=root_username=admin --from-literal=root_password=Passw0rd --from-file=./keyfile`でsecret作成
3. `kubectl get secret/mongo-secret -o yaml`でSecretの内容をyamlで得る
    - これによりSecretのyamlファイルには暗号化された文字列が記述される
    - 実際に環境変数に入る値はSecretのyamlファイルで記述された文字列をdecodeしたもの
4. `weblog-db-storage.yml`を更新
5. `kubectl apply -f weblog-db-storage.yml`
6. `kubectl exec -it mongodb -- sh`で入る
7. `mongo`でdbに入る
8. `show dbs`を実行するとエラーになる
9. `use admin`でdbをadminに変更
10. `db.auth("admin", "Passw0rd")`でログイン
11. `show dbs`で閲覧できる

## **6. DBサーバーの構築 (StatefulSet)** : `08-06`
### 概要
- StatefulSetを利用することでレプリケーションを実現
  - レプリケーションとは読み書き可能なプライマリと読み取り可能なセカンダリから構成される冗長化されたDBのこと
1. PV, Secret, StatefulSetを作成
2. 作成したPodに入る
3. MongoDBへ接続
4. 設定したユーザー名/パスワードで認証
5. DB一覧を表示
### 
1. `weblog-db-statefulset.yml`でStatefulSetを定義してPodをStatefulSetのtemplateに移動
2. `kubectl applpy -f weblog-db-statefulset.yml`
3. `kubectl get pod`で3個(replicasでしたいした個数)作成される
4. `kubectl exec -it mongodb-0 -- sh`
5. `mongo`, `use admin`, `db.auth("admin", "Passw0rd")`
6. `exit`, `exit`
7. `kubectl delete -f weblog-db-statefulset.yml`
8. `persistentvolume/volume-00`を02まで