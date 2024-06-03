---
title: ""
emoji: "🐡"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: []
published: false
---

# 概要

- CDNサービス

- 異なるエッジロケーションにコンテンツをキャッシュすることで遅延が減る。

- エッジロケーションは現在216拠点

- 世界中にキャッシュすることでDDoS攻撃の対策にもなる

## 主な設定項目と仕様

### Origins

- S3 bucket
  
  - CloudFrontのみがS3にアクセスできるようにOAIを利用する

- Custom Origin
  
  - ALB、EC2、S3website、任意のHTTPバックエンド
  
  - EC2をオリジンにする場合は、EC2をパブリックインスタンスにする必要がある
  
  - ALBをオリジンにする場合は、EC2はプライベートインスタンスでいいが、ALBをパブリックにする必要がある
  
  - EC2やALBのセキュリティグループでは、エッジロケーションのすべてのIPアドレスからのインバウンドアクセスを許可する必要がある

- エッジロケーションはアクセスがあるとコンテンツのキャッシュがあるか確認し、キャッシュがない場合はオリジンにコンテンツを取得しにいく

- エッジロケーションとオリジンの間はAWS内でプライベート接続される

### CloudFrontとS3 Cross Region Replicationの違い

- CloudFront
  
  - Global Edge network
  
  - ファイルはTTLの期間キャッシュされる
  
  - 静的コンテンツを世界中どこからでもアクセスできるようにする

- S3 Cross Region Replication
  
  - レプリケーションするリージョンごとに設定が必要なので、世界中のすべてのリージョンに対応できるわけではない
  
  - ファイルはリアルタイムで更新されるのでキャッシュは無い
  
  - Read Only
  
  - 動的コンテンツを複数のリージョンで低遅延で配信する場合に最適

### Geo Restriction

- 配信にアクセスする国に応じて、承認国や禁止国のリストを作成したり、アクセスする人を制限できる

- ユーザのIPアドレスとそのアドレスが属する国を照合している

- 著作権の保護などが目的

### Access Log

- CloudFrontはオリジンへのアクセスのログをS3に保存する

- ログパケットはオリジンパケットとは分けるべき

- 複数のCloudFrontディストリビューションのログを同じバケットに集めることができる

### トラブルシューティング

- 403は存在しないオブジェクトにアクセスしようとしているとき、5xxはゲートウェイの問題

### キャッシュについて

- Headers、Session Cookies、Query String Parametersなどのパラメータの組み合わせに基づいてキャッシュされる

- Cache Control HeaderとExpires headerを利用してTTLをコントロールする
  
  - オリジンからCloudFrontへの通信でCache-Control: max-ageヘッダを利用すると、アプリケーションからTTLを制御できる
  
  - 最小TTL、最長TTL、デフォルトTTLを設定できる。Cache-Controlヘッダがない場合は、デフォルトTTLが適用される

- CreateInvaidationAPIを利用してキャッシュの一部を無効化することもできる

- キャッシュヒット率を最大化する方法
  
  - 静的ファイル：headerとsessionのキャッシングルールを作成しないことで、キャッシュヒット率を最大化する。
  
  - 動的ファイル：headerやcookieに基づくキャッシュを利用して、ALB+EC2やAPI Gateway+Lambdaに転送する。

- CloudWatchでCacheHitRateをモニタリングすると良い

- CloudFrontの背後にALBを配置して、stickyセッションを利用する場合は、必要なcookieをCloufFrontのcookienのwhitelistに追加する必要がある