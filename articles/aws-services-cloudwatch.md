---
title: "AWS試験対策: CloudWatch"
emoji: "👌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: []
published: false
---

## 概要

## メモ

- CloudWatch Logs Insightsを使用すると、CloudWatch Logsのログデータを検索し、分析できる
- サーバに統合CloudWatchエージェントをインストールすることで、ローカルログをサーバからCloudWatchへ送信可能 (オンプレサーバ・EC2両対応)
- 収集したメトリクスからエラーを検知し、SNS Topicへ通知を送信することで、SNSのサブスクライバへメール通知が可能
  - アラームを設定する対象リソースの指定をディメンションという。例としてEC2ならinstanceIDとか
  - InstanceTypeで指定するなど、カスタムディメンションも作成できる。CloudWatchエージェント構成ファイル内にappend_dimensionsフィールドを作成することで可能
- 