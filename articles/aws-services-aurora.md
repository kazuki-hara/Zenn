---
title: "AWS試験対策: Aurora"
emoji: "🦁"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: []
published: false
---

## 概要

Amazon Aurora (Aurora) は完全マネージド型のリレーショナルデータベースエンジンで、MySQL および PostgreSQL と互換性があります。MySQL と PostgreSQL が、ハイエンドの商用データベースのスピードおよび信頼性と、オープンソースデータベースのシンプルさとコスト効率を併せ持っていることは既にご存じでしょう。既存の MySQL および PostgreSQL データベースで現在使用しているコード、ツール、アプリケーションを Aurora でも使用できます。Aurora では、既存のアプリケーションのほとんどを変更せずに、ほんの少しのワークロードで MySQL のスループットの 5 倍、PostgreSQL のスループットの 3 倍を実現します。
- https://docs.aws.amazon.com/ja_jp/AmazonRDS/latest/AuroraUserGuide/CHAP_AuroraOverview.html

## メモ

- クラスタは1つのプライマリーインスタンス (読み書き可能)と最大15のリードレプリカインスタンス (読み取りのみ)で構成される
- Aurora Auto Scalingで負荷に応じてリードレプリカインスタンスを増減可能
- クラスターエンドポイントはプライマリDBインスタンスに接続するためのエンドポイント。リーダーエンドポイントはリードレプリカインスタンスに接続するためのエンドポイントで、リードレプリカ間で読み取りトラフィックを均等に分散させる
- 