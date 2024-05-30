---
title: "AWS試験対策: Lambda"
emoji: "👌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: []
published: false
---

## 概要

- 実行時間に制限がある (15分)

- オンデマンドに実行される

- 自動でスケーリングされる

## 主な設定項目や仕様

### 主な仕様

- 100万件のリクエッストと400000GBsまで無料

- RAMは最大10GBまで拡張可能。RAMを拡張するとCPUの性能も向上する

- 様々な言語に対応。非対応な場合は、Custom RUntime APIを利用できる

- Lambda Container Imageを利用可能。ただし、コンテナがLambda Runtime APIに対応している必要がある

- 一緒に利用されるサービス
  
  - API Gateway
  
  - Kinesis
  
  - DynamoDB
  
  - S3
    
    - S3のイベント通知をトリガーにできる。イベント通知は、オブジェクトの作成や削除、リストアなど
    
    - S3→SNS→SQS、S3→SQS→Lambda、S3→Lambdaなどの繋げ方がある。
    
    - LambdaのデットレターキューをSQSに繋げたり、メタデータをDynamoDBに挿入することもできる
  
  - CloudFront
  
  - CloudWatch Events / EventBridge
    
    - cronでLambdaの定期実行やCodePipelineでstateが変わったことをトリガーにするなど
  
  - CloudWatch Logs
    
    - LambdaにCloudWatch Logsにログを書き込むIAMロールが必要
  
  - SNS
  
  - SQS
  
  - Cognito
  
  - X-Ray
    
    - Lambdaで有効化してX-Ray SDKを実行する
    
    - AWSXRayDaemonWriteAccessのIAMロールが必要

### IAM policy

- ベストプラクティス；１つのLambda関数につき1つのロールを作成する

- 他アカウントや他リソースから呼び出すには、リソースベースポリシーで許可する必要がある

- SQSをトリガーにする場合は、Lambda関数がSQSを読み込みにいくので、LambdaにIAMロールを割り当てる必要がある。

### Configuration

- RAM
  
  - 128MBから10GBまで増やせる
  
  - RAMを増やすとvCPUが増える

- デフォルトのタイムアウト時間は3秒、最大15分

- Execution Context
  
  - 実行後しばらくの間維持される
  
  - Lambda再実行時は再利用される。データベース接続の高速化などに有効
  
  - /tmpディレクトリは実行を跨いで利用可能。10GBの容量がある。データの永続化が必要ならS3に保存する。/tmpでの暗号化機能はないので、暗号化したかったらKMSを利用して自分で暗号化する必要がある。
  
  - ベストプラクティス：実行時間が長い前処理はハンドラー関数の外に出して再利用できるようにしておく

### 並列処理とスロットリング

- 最大で1000個まで同時実行可能

- 同時実行数を制限するために、予約同時実行を関数ごとに設定するのが推奨

- 制限を超えて同時実行されると同期呼び出しの場合は429エラーを返す。非同期呼び出しの場合は自動的にリトライしてDLQに送られる

- 非同期呼び出し時の自動リトライ時間は指数関数的に増加して1秒に1回から5分に1回まで増える

### Cold StartとProvisioned Concurrency

- 新しくインスタンスが作成される。ハンドラー関数の外のコードが実行されるので、処理に時間がかかる場合がある

- Provisioned Concurrencyは、Concurrencyをあらかじめ割り当てることで、Cold Startが起こらないようにする

- Concurrencyの最大数はアカウント全体で1000