---
title: "AWS試験対策: CloudFormation"
emoji: "🐕"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: []
published: false
---

## 概要

コードでインフラを構築する。コードのバージョン管理も可能。作成したリソースはタグづけされるのでコスト管理もしやすい。

テンプレートを更新したいときは、既存のテンプレートを変更するのではなく、新しいテンプレートをアップロードする必要がある

## メモ

### UserData

- UserDataはBase64関数の中に書く。するとインスタンス作成時にUserDataに書いたスクリプトが実行される

### Policy

- DeletionPolicy
  
  - DeletionPolicy=Deleteとすると、スタック削除するときに明確にリソースを削除する。S3の場合は、中身が空ではないときにバケットの削除に失敗する
  
  - DeletionPolicy=Retainの場合は、スタック削除時に削除されなくなる
  
  - DeletionPolicy=Snapshotとすると、DBやキャッシュ系のサービスではインスタンスは削除されるが、スナップショットを最後に取得する

- StackPolicy
  
  - jsonで指定する。
  
  - Cloudformationで作成したリソースに対してどのような更新を許可するかを指定できる。
  
  - 意図しない更新からスタックを保護できる

- TerminationProtection
  
  - スタックの誤削除を防ぐ
  
  - 

### cfn

- cfn-init
  
  - MetaDataに記載した情報で設定を行う。UserDataに記載したスクリプトを外出しして構造化できるイメージ？

- cfn-signal
  
  - cfn-initの後に実行する。
  
  - WaitConditionを利用sルウ
  
  - cfn-initの結果を取得し、CloudFormatioに返すことができる

- CloudFormationはリソースの作成に失敗したら削除してしまうので、デバッグするためにはロールバックを無効にする必要がある

### カスタムリソース

- オンプレやサードパーティ、削除時のラムダの実行などを定義できる

### ネストされたスタック

- スタックの中に作成するスタック

- Cross Stackは異なるタイミングで作成され、前のスタックで作成したリソースのIDなどを参照できる

- Nested Stackは部品として再利用される。

- Nested Stackを利用するためには、テンプレート内のTypeで指定して、部品として利用するスタックのテンプレートのURLを指定する

### DependsOn

- リソースを作る順番を指定できる。

- DependsOn属性で指定したリソースが作成された後に作成されるようになる

### StackSets

- 複数のアカウントとリージョンにまたがるスタックを一括で操作できる

- 管理者アカウントでスタックを更新すると、ターゲットアカウントはその更新を受け取れる

- 管理者アカウントは、ターゲットアカウントにスタックをデプロイするIAMロールを持つ必要がある。Organizationを利用している場合はIAMロールが自動で作成されるが、利用していない場合はIAMロールを自分で作成する必要がある

- Organizationを利用すれば、アカウント作成と同時にスタックのデプロイが可能になる。