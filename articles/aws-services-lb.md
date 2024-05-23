---
title: "AWS試験対策: ELB"
emoji: "💨"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: []
published: false
---

# 概要

- ELBはマネージドなロードバランサー

- 多くのAWSサービスと統合可能

## 主な設定項目や仕様

### ELBの種類

- Application Load Balancer (V2)
  
  - L7で動作する
  
  - 同じEC2インスタンス上で複数のアプリケーションに分散できる
  
  - HTTP/2とwebsocketに対応
  
  - リダイレクトにも対応
  
  - URLやホスト名、クエリやヘッダーの文字列に基づいてルーティングできる
  
  - ポートマッピング機能があり、EC2インスタンスの動的ポートにリダイレクト可能
  
  - Target Groups
    
    - EC2 instances - HTTP
    
    - ECS tasks - HTTP
    
    - Lambda functions
    
    - IP addresses
  
  - アプリケーションサーバはALBと通信するので、クライアントのIPアドレスがわからない。クライアントのIPアドレスをサーバに知らせるためにX-Forwart-Forヘッダーを利用する

- Network Load Balancer (v2)
  
  - L4で動作する
  
  - UDPとTCPに対応
  
  - 高性能なため1秒に何百万のリクエストを裁き、ALBと比較して低遅延
  
  - NLBはAZごとにIPをひとつもつ。またElastic IPに対応している
  
  - Target Groups
    
    - EC2 instances
    
    - IP Addresses - プライベートIPである必要がある
    
    - ALBの前に配置すると有効。(NLBで固定IPを手にいれ、ALBでHTTPに対応する)
    
    - ヘルスチェックはTCP, HTTP, HTTPSに対応している

- Gateway Load Balancer (v2)
  
  - FWやIDPを経由させたい場合は利用する
  
  - 最新のロードバランサー
  
  - VPCのルートテーブルを更新し、クライアントからの通信がGLBを経由するようにする。GLBはサードパーティのセキュリティ仮想アプライアンスにトラフィックを転送し、アプライアンスから戻ってきたトラフィックをアプリケーションに転送する。よって、すべてのトラフィックの出入り口となる
  
  - Target Groups
    
    - EC2 instances
    
    - IP addresses - プライベートIP

### Health Check

- インスタンスへのポート番号やエンドポイントを指定してヘルスチェックを行う。HTTPレスポンスが200であることを確認する

### Sticky Sessions

- 同じクライアントからのリクエストを同じインスタンスに割り振るようにする

- CLB, ALB, NLBで利用可能

- クッキーを使ってコントロールする
  
  - Application-based Cookies
    
    - Custom Cookieはアプリケーションが生成する。アプリケーションごとにカスタム属性を利用可能
    
    - Aplication CookieはLBが生成する。AWSALBAPPという名前
  
  - Duration-based Cookies
    
    - AWSALB by ALB, AWSELB by CLB
    
    - LBが生成する

### Cross-Zone Load Balancing

- 複数のLBが存在し、LBの後ろにあるインスタンス数に偏りがあるとき、クライアントが各LBに対して均等にリクエストしても、LBが各LBの後ろにあるインスタンスすべてに対して均等になるようにロードバランシングする

- ALBではデフォルトで有効、無料

- NLBとGLBはデフォルトで無効、有効にすると有料

- CLBはデフォルトで無効、有効にしても無料

### SSL/TLS

- クライアントとロードバランサー間で通信を暗号化する

- Public SSL証明書はCAが発行する。SSL証明書をLBにアタッチする

- LBはX.509証明書を利用する。AWS Certificate Mangeで管理できる

- 自身の証明書をアップロードすることも可能

- SNI
  
  - ALB, NLB, Cloudfrontで使える
  
  - 異なるSSL証明書を使う複数のTarget Groupに1つのLBに対応可能になる

### Connection Draining

- Target Groupsにヘルスチェックに引っかかっているインスタンスがあったら、そのインスタンスへのリクエストが完了するまでの時間待機する

- 該当インスタンスへのリクエストがすべて完了したら、それ以降の通信はすべて他のインスタンスに割り振られるようになる

### Status Code

- 200: Success

- 4XX: Unsuccess at client side
  
  - 400: Bad Request
  
  - 401: Unauthorized
  
  - 403: Forbidden
  
  - 460: Client Closed Connection
  
  - 463: X-Forwarded For header with > 30IP

- 5XX: Unsuccess at server side
  
  - 500: Internal server error → some error on the ELB itself
  
  - 502: Bad gateway
  
  - 503: Service Unavailable
  
  - 504: Gatewayt tiemout

### Monitoring

- CloudWatchにメトリクスを送信可能
  
  - BackendConnecntionErrors
  
  - Latency
  
  - Healty/Unhealty Host Count
  
  - RequestCount
  
  - RequestCountPerTarget

- アクセスログはS3に保存可能
  
  - TIme
  
  - Client IP Address
  
  - Latencies
  
  - Request Paths

- Request Tracing
  
  - X--Amzn-Trace-IdというHTTPヘッダーで分散処理システムなどでもリクエストを追跡できる

### Target Groups Settings

- Slow Start Mode: EC2インスタンスに徐々にリクエストを送る方法。スロースタート継続時間を0にすると無効化できる

- Least Outstanding Requests: リクエスト数が最も少ないインスタンスにリクエストを割り振る

- Round Robin: バックインスタンスに順番に割り振る

- Flow Hash: IPやPort、シーケンス番号からハッシュを計算して割り振る先を決める
