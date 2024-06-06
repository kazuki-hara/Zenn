---
title: "AWS試験対策: S3"
emoji: "💬"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: []
published: false
---

## 概要

## 主な設定項目と仕様

### Storage Class

| Class                           |                                                                        |
| ------------------------------- | ---------------------------------------------------------------------- |
| Standard                        | 頻繁にアクセスするデータに利用する。低遅延で高いスループット                                         |
| Standard-IA (Infrequent Access) | アクセス頻度は低いが素早くアクセスが必要。バックアップ用途など                                        |
| One Zone-IA                     | 単一AZなのでAZ障害でデータが壊れる。再現可能なデータのセカンダリーコピー                                 |
| Glacier Instant Retrieval       | 3ヶ月に1回くらいの頻度でアクセスされるデータ。最低保存期間は90日。ミリ秒単位での検索が可能。バックアップ用途               |
| Glacier Flexible Retrieval      | Expedited (1-5mins)、Standard(3-5hours)、Bulk(5-12hours)の段階がある。最低保存期間90日 |
| Glacier Deep Archive            | Standard(12hours)、Bulk(48hours)。最低保存期間180日                             |

### Object Encryption

- サーバサイド暗号化
  
  - SSE-S3：AWSが管理する鍵で暗号化する。AES-256。headerにx-amz-server-side-encryption: AES256を指定する必要がある。新しいオブジェクト、バケットでデフォルトで有効化
  
  - SSE-KMS：AWS KMS管理の鍵を利用する。CloudTrailで鍵の利用状況を追跡できるがメリット。headerにx-amz-server-side-encryption: aws:kmsを指定する。ファイルアップロード時にAPIで鍵を呼ぶ必要があるが、呼べる頻度はサービスクォータがあるので、上限にひっかかる場合は上限を上げてもらう必要がある
  
  - SSE-C：カスタマーがAWSの外部で管理する鍵で暗号化する。HTTPS必須。HTTPヘッダーで鍵を指定する必要がある

- クライアントサイド暗号化
  
  - Amazon S3 Client-Side Encryption Livbraryのようにクライアントライブラリでファイルを暗号化してからアップロードする

- 通信中の暗号化
  
  - HTTPSを利用する。HTTPS推奨。SSE-Cの場合はHTTPS必須
  
  - 通信にHTTPSを強制するなら、aws:SecureTransport:falseをバケットポリシーで指定する

- バケットポリシーでx-amz-server-side-encryptionがヘッダーにない場合は拒否する設定を入れることで暗号化を強制できる

### CORS

- S3からファイルを取得する際に、他のwebサーバのコンテンツから要求されている場合に許可する

### MFA Delete

- オブジェクトのバージョンを削除したり、バージョニングを無効化する際に利用する

- ルートアカウントでMFAデバイスを設定済みである必要がある

### S3 Access Logs

- バケットに対するリクエストをロギングできる。ログは別のバケットに保存される。

- 監視するバケットとロギングバケットは分ける必要がある。（無限ループが発生する)

### S3 Access Point

- 特定のパスに対して読み書きの許可を与えたアクセスポイントを作成することで、管理を楽にする

- 読み取り専用などもできる

- 各アクセスポイントにDNS名を持たせることになる

- VPC Origin
  
  - VPC内部からVPCエンドポイントを経由してVPC Originに接続することで、インターネットに出ないでアクセスできる

- Multi Region Access Pointsを使うと、複数のリージョンに跨る複数のバケットに動的にルーティングする単一のアクセスポイントを作成できる。各リージョンのバケットは互いにレプリケーションされる。最もレイテンシーが低いバケットにルーティングされる。また、フェールオーバーもできる

- 



### S3 Event

- SQS, SNS, LambdaはS3 Eventで直結できる

- 他のサービスもいくつかはEventBridgeを経由することで連携できる

### Performance

- Multi-Part Upload：ファイルを分割してアップロード。5GB以上で必須

- S3 Transer Acceleration：ファイルを近くのエッジローケーションにアップロードしてから、S3にアップロードする

### S3 Inventory

- メタデータを毎週CSVやORCに出力できる

### Athena

- S3上のデータを分析する。QuickSightと組み合わせてレポートの作成などに利用される

- パフォーマンスの向上方法
  
  - スキャンするデータ量に対して課金されるので、Columnar dataを利用すると節約できる。Apache ParquetやORCなど
  
  - GlueでCSVをApache Parquetに変換するといい
  
  - S3のデータをPartitionで整理することで、検索が容易になる
  
  - 扱うファイルのサイズを大きくしてオーバーヘッドを減らす
  
  - 

- AthenaをLambdaに接続し、Lambdaがデータソースにアクセスすることで、さまざまなデータソースを利用できる

- 