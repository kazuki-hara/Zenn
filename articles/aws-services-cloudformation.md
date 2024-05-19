---
title: "AWS試験対策: CloudFormation"
emoji: "🐕"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: []
published: false
---

## 概要

## メモ

- CloudFormationスタック削除時にリソースの削除を防ぐ場合は、CloudFormationテンプレート内の対象リソースの削除ポリシーにRetain (保持)を指定する
- 存在しないプロパティを指定するとリソースを作成できない
- DependsON属性にリソースを指定すると、指定したリソースが作成された後に作成されるようになる
- 名前が一意でなければならないリソースが、既存リソースと名前重複するとOUTDATEDエラーになる