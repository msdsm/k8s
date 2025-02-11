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

## **1. デバッグ用イメージ作成**
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

## **2. DBサーバーのイメージ作成**
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

## **3. DBサーバーの構築(ストレージ)**
### 内容
1. PersistentVolumeとPersistentVolumeClaimのペアを作成
2. PVCはDB(MongoDB)にあり, PVはhostpathを設定するためLocal Storageにある
### 手順
1. `weblog-db-storage.yml`を作成
2. `kubectl apply -f weblog-db-storage.yml`
3. `kubectl get pv,pvc`

##