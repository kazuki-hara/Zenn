---
title: "AWS試験対策: AMI"
emoji: "👌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["AWS", "AWS認定試験", "AMI"]
published: false
---

# 概要

# 勉強メモ

- AMI = Amazon Machine Image
- EC2をカスタムする。起動や設定時間の短縮につながる
- 特定のリージョンに作成し、リージョン間のコピーができる
- A Pablic AMI: AWS提供
- ユーザ自身が作成したAMI
- AWS MarketPlace AMI: 他の人が作ったAMI、MarketPlaceで売買可能

### プロセス

1. EC2の起動とカスタマイズ
2. EC2の停止とデータの整合性確認
3. AMIの作成、EBSスナップショットの作成
4. AMIからインスタンスを起動

### Options

- No Reboot Option
  - 通常はインスタンスを停止して、AMIを作成する前にAWSがファイルの整合性を確認する。ファイル整合性が確認できたらEBSスナップショットをとり、AMIを作成する
  - No Reboot Optionを利用すると、稼働中のEBSボリュームに対して直接スナップショットが作成される。ファイルの整合性は確認しないので、何をしているか自分で確認する必要がある

### アカウント間のAMIの共有

- AMIが暗号化されていない場合は、他アカウントとの共有や公開ができる。またユーザの鍵で暗号化もできる
- 他アカウントと暗号化されたAMIを共有するときは鍵も共有する必要がある
- 共有されているMAIをコピーすると、AMIの所有者が共有元から共有先に変わる。

### EC2 Image Builder

- VMやコンテナイメージの作成の自動化に利用される
- AMIからカスタマイズするためのBuildインスタンスを作成し、Buildインスタンスから新しいAMIや新しいDock erイメージを作成し、テストEC2インスタンスを作成する。テストEC2インスタンスで正常性確認やセキュリティチェックなどのテストが通ればAMIが配布される
- Image Builder自体は無料
- Build Componentsを選択すると、AMI作成時に自動で選択したライブラリをインストールしてくれる。独自のComponentsも作成可能
- テストも同様に用意されている
- 配布設定もできる。複数のリージョンに自動的にAMIを配布することが可能

### AMI in production

- AMIにtagをつけて、IAMポリシーで制御するこで、ユーザは承認された(Tag付された)AMIのみを利用d毛いるように制限できる
- AWS ConfigでEC2インスタンスを監視するとで、Tag付けされたAMIから起動されているものであるかどうかチェックすることができる