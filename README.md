# マイクロサービス学習用プロジェクト

## 目的

* 概念や思想をちゃんと理解する
* 不向きな部分、デメリットも抑える
* Spring Framework、AWSの構築方法を習得する

## マイクロサービス

### 1. マイクロサービスとは

* **ビジネスドメインに基づいてモデル化** された、 **独立してデプロイ可能** なサービス
* サービス同士はネットワークを介して相互に通信
* サービス指向アーキテクチャ（SOA）の一種
* **新しい技術を使う必要はない。** どんな言語で、どんなプラットフォームでも良い

#### 1.1. 独立デプロイ可能性

* 他のサービスに影響を与えることなくデプロイできる
* サービス同士が疎結合

#### 1.2. ビジネスドメインに基づくモデル化

##### モノリシック（従来は）

* 3層アーキテクチャ（Web、App、DB）に対して、層ごと開発チームを形成していた
    * 機能ではなく技術のまとまりでチームを形成
    * DBは機能やチーム間で共有することが多いため、変更の影響が大きい

##### マイクロサービス

* 技術ではなく機能のまとまりでチームを形成する
* サービスのカプセル化が可能
    * サービス内に小さな3層アーキテクチャができる   
    * 他サービスへの影響を減らし、変更が容易になる
    * **DBの共有は、独立デプロイ可能性を実現するうえで一番の弊害**

#### 1.3. マイクロサービスの利点

* 各サービスの作業を並行して行える
* 1機能=1サービスなので、担当サービスの理解が容易
* サービスごと異なる言語、DB、開発手法の選択が可能

#### 1.4. マイクロサービスの課題

* サービス間のやりとりがネットワーク通信になるため、レイテンシーやネットワーク障害をより考慮する必要がある

#### 1.5. マイクロサービスのポイント

* 段階的にマイクロサービス化する。一気に大量のマイクロサービスを作らない
* 1サービスは小規模で
* 技術単位ではなく、機能単位の組織づくり
    * プロダクトオーナーや運用チームは、各々サービスチームごと配置
    * それらサービスを束ねる技術リーダーは別途設ける

### [2. モノリス（モノリシック）とは](doc/2.monolithic.md)

### [3. 結合と凝集](doc/3.coupling_cohesion.md)

### [4. 移行計画](doc/4.migration_plan.md)

### [5. モノリスの分割](doc/5.monolithic_division.md)

### [6. データベースの分割](doc/6.database_division.md)

### [7. その他](doc/7.others.md)

## 構築

### Sock Shop

* マイクロサービスのデモサイト
* https://microservices-demo.github.io/
    * [Github](https://github.com/microservices-demo)
* Java、Go、Python、C#などいろんな言語のマイクロサービスがある
* `docker-compose`すると各自環境で起動できる

### AWS

* [AWS App Mesh](https://aws.amazon.com/jp/app-mesh/)
* マイクロサービスで必要なサービス間の通信を管理するためのサービス
   * 大規模なマイクロサービスだと必須
* 以下 **構築するためにはAWSアカウントおよび環境が必要**
* https://www.youtube.com/watch?v=Nmgl2LhezTc

#### AWSコンソールで構築

* https://docs.aws.amazon.com/app-mesh/latest/userguide/getting-started-ecs.html
* https://docs.aws.amazon.com/ja_jp/AmazonECS/latest/developerguide/getting-started-fargate.html

##### ざっくりやること

1. 事前にECSなど実行コンテナを通常通り構築（EC2でもおｋ）
2. App Meshでマイクロサービス全体を **Mesh** とした環境を構築
3. Mesh内に、仮想サービス、仮想ノード、仮想ルーターを用意
    * 何れも仮想ってついてる通り、まぁインターフェースみたいなもの
        * 仮想ノードは、事前に作ったECSのタスクやECSなどを紐づけるインターフェース
        * 仮想ルーターは、仮想ノードを切り替える際に利用するもの
        * 仮想サービスは、仮想ノードや仮想ルーターをまとめたサービス全体をまとめたもの
4. 1.で作ったコンテナ側でApp Meshを有効化する
    * ECSだとTask定義内で、App Mesh有効化のチェックボックスを入れる

#### CLI + CloudFormationで構築

* AWSワークショップ及びサンプルを元に構築
   * **実行環境を直接見てもらおうと思ったけど、稼働し続けてたら料金発生してしまったので止めます**

* https://github.com/harunobukameda/AWS-App-Mesh-AWS-Cloud-Map
* https://github.com/aws/aws-app-mesh-examples/tree/master/examples

## 参考

* https://www.oreilly.co.jp/books/9784873119311/
