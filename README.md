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

##### 実行環境

* `serviceA`と`serviceB`の2つのサービスがある
* 2つのサービスは`apps.local`の名前空間に存在する
* `serviceA`はHTTPプロトコルで`serviceB`と通信する
* `serviceB`はversion1とし、version2の`serviceB`を`serviceBv2`としてデプロイ済
 * 要は`serviceA`と`serviceB`と`serviceBv2`の3サービス存在する

##### やること

* `serviceA`→`serviceB`の通信を、`App Mesh`を利用して`serviceA`→`serviceBv2`に切り替える
* 具体的には
    * `serviceA`→`serviceB`の通信量と`serviceA`→`serviceBv2`の通信量を`3:1`の状態で`serviceBv2`の動作を確認
    * `serviceBv2`の動作確認ができたところ、`App Mesh`の通信量重み付けの設定で全ての通信を`serviceBv2`に向ける
    * **アプリをいじらなくても、App Meshの設定だけでサービス間の通信を制御できることを確認する**

##### Meshと仮想サービス作成

* Mesh名`apps`
* 仮想サービス名`serviceb.apps.local`(`servicea.apps.local`はあとで)
* あとはデフォルト通り

##### 仮想ノード作成

* 仮想ノード名`serviceB`
* サービス検出方法`DNS`
* DNSホスト名`serviceb.apps.local`
* **リスナー設定** プロトコルは`http2`でポート`80`

##### 仮想ルーター作成

* 仮想ルーター名`serviceB`
* **リスナー設定** プロトコルは`http2`でポート`80`
* ルート名`serviceB`
* ルート設定は`http2`
* ターゲットの仮想ノードを`serviceB`重みは`100`
* プレフィックスの一致`/`

##### リソース追加

* `apps`Mesh選択
* 仮想ノード`serviceBv2`追加
    * 仮想ノード名`serviceBv2`
    * サービス検出方法`DNS`
    * DNSホスト名`servicebv2.apps.local`
    * **リスナー設定** プロトコルは`http2`でポート`80`
* 仮想ノード`serviceA`追加
    * 仮想ノード名`serviceA`
    * サービス検出方法`DNS`
    * DNSホスト名`servicea.apps.local`
    * **リスナー設定** プロトコルは`http2`でポート`80`
*  仮想ルーター`serviceB`のターゲットを`serviceB`を`75'、`serviceBv2`を`25`にする
* 仮想サービス`servicea.apps.local`作成
    * 仮想ノード`serviceA`

##### ECS各タスクにApp Meshを有効化する

* タスク定義内に**App Mesh有効化** のチェックボックスがあるのでチェック入れて仮想ノードとマッピングさせると適用

#### CLI + CloudFormationで構築

* AWSワークショップ及びサンプルを元に構築
   * **実行環境を直接見てもらおうと思ったけど、稼働し続けてたら料金発生してしまったので止めます**

* https://github.com/harunobukameda/AWS-App-Mesh-AWS-Cloud-Map
* https://github.com/aws/aws-app-mesh-examples/tree/master/examples

## 参考

* https://www.oreilly.co.jp/books/9784873119311/
