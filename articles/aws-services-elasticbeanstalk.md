---
title: ""
emoji: "👋"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: []
published: false
---

# 概要

- Beanstalkは　AWS上でアプリケーションをデプロイするために必要なサービスをまとめて1つのインターフェースとして扱えるマネージドサービス

- 様々な言語をサポートしているので、大体デプロイできる

- Web environmentではELBにASGのEC2を直接接続する。Worker environmentでは、ELBからEC2の間にSQSを利用する。

- 他にもEC2とRDSのシングル構成や高可用な構成など
