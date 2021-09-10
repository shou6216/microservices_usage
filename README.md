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

#### 1.2. ビジネスモデルに基づくモデル化

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

### 3. 結合と凝集

* **結合** あるものに対する変更がどれくらい別のものの変更を必要とするか
* **凝集** 関連するコードをどのようにグループ化するか
* 「構造は、 **凝集度** が高く、 **結合度** が低い場合に安定する」
* マイクロサービスの変更量を減らす = 同じ機能を1つのサービスにまとめる = 凝集を高める

#### 3.1. 実装結合

* 実装結合はよろしくない
   * データベースを共有した実装
       * 例）Aサービスのデータベースを、Bサービスが使う
       * Aサービスのデータベース変更に追従できない（自分で直さないといけない）
       * こういう場合は、実装を隠すような形でAサービスにAPIを定義する
* APIは **アウトサイドイン思考** で考えると良い
   * アウトサイドイン =  サービス利用者の視点からデザインする手法
   * インサイドアウト = 内部データや実装からデザインする手法。これはダメ

#### 3.2. 一時的結合

* Aサービス ->(同期的HTTP) Bサービス ->(同期的HTTP) Cサービス  
のように、 **時間的に** に複数サービスがつながった状態。
    * キャッシュを使ったり、非同期通信にすることで結合度を下げる

#### 3.3. デプロイ度結合

* 複数モジュールからなる単一プロセスにおいて、任意のモジュールを変更した場合
その変更をデプロイするためには、変更していないモジュールも含めて全体をデプロイしないといけない
     * 変更の必要があるものだけデプロイする仕組みや構成が望ましい
     * またリリースの規模（変更量の規模）が小さいほどリスクを減らせる
     * 継続的デリバリーが良い

#### 3.4. ドメイン結合

* サービス間のデータ受け渡しにおいて、ドメイン（用途）を明確にして
過不足のないようにすることが望ましい
    * **過** サービスで利用しない情報は送らない、受け取らない
    * **不足** 受け渡し後に、サービス内で別途データ取得処理をしないように、最初の受け渡しで必要分を用意する
* サービスをモデル化する上で、ドメイン駆動設計は重要

### 4. 移行計画

#### 4.1. マイクロサービスを選択する理由

* 市場投入までの時間を減らす
* 高負荷のサービスだけスケールアップし、逆に必要のないサービスはスケールダウンできる
* 堅牢性の改善（機能のある領域に影響を与えても、システム全体をダウンさせる必要はない）
* 新しい技術を受け入れやすい

#### 4.2. マイクロサービスが向かないケース

* ドメインが不明瞭
    *  サービス境界の判断が難しい
* スタートアップ
    * ドメインが不明瞭と同じ
* 顧客の環境にインストールして管理するもの
* 一度モノリスで作り、サービス境界及びドメインを明確にしてからとマイクロサービス化するのが適切

#### 4.3. モノリスからマイクロへ移行の方針

* 少しずつ、段階的に分解しながら進める
* アジャイルの思想に近い。小さなイテレーションを繰り返す
* 小さく分解することで、その分解によって生まれる様々な問題やコストも小さくすることが狙い
* 分解には、 **ドメイン駆動設計**

#### 4.4. ドメイン駆動設計

* サービスの境界を定義
* サービス間の関連を可視化する
* **イベントストーミング** の実施
* 関連（依存）が多いサービスは、関連元の変更も多くなるため、移行コストが大きい
* 一方で、関連（依存）が少ないまたは全くないサービスは、移行コストが低い

#### 4.5. 分解の優先順位

* **分解のしやすさ** と **分解による利益** の2軸で考える
* どちらも高いものから順々に進める

#### 4.6. チームの編成

* アーキテクチャと同様に、チームのサイロ化も変える必要がある
    * 設計、製造、テスト、運用など工程ごとチーム分けることで、単純な変更でも変更量や作業時間が多くなりがち
* 1つのチーム内で、上記メンバを構成することで、変更量や時間を減らし自律的な活動ができる
    * マイクロサービスやDevOpsの思想
* 運用状況のAS-ISとTO-BEを整理する 

### 5. モノリスの分割

#### 5.1. モノリスの変更可能性

* 既存システムが、何かしらの理由で変更できないケースの対処法
    * ソースコードがない
    * （古すぎて）スキルがない
    * 機能が複雑怪奇
* （ソースコードがある場合） **モノリスに残しつつ** マイクロサービスに移行する（コピー）
    * 移行後問題がなければモノリス側を削除することで、いつでもロールバックできるようにしておく
* 古いシステムは、ドメインモデリング（ドメインの境界線定義）が難しいことが多い（MVCなどを意識しない作りが多いため）
    * マイクロサービス化の前に、（モノリス内で） **モジュール化** するところから始める
    * モジュール化してからマイクロサービス化すると進めやすい
    * このモジュール化も、小さく、容易なところから進める

#### 5.2. ストラングラーアプリケーション

##### 概要

* モノリスからマイクロサービスへ **段階的** 、 **中断可能** 、 **元に戻せる** 移行の進め方 
* 呼び出し方法を変えずに、呼び出し先の機能を丸ごとマイクロサービスに移行する
* 元から機能が独立していると適用しやすい

##### 手順

1. 既存の呼び出し方法と移行対象を特定する
2. 既存の機能そのままに、コピーする形でマイクロサービスに移行する
3. 既存の呼び出しをマイクロサービスに切り替える
    * 既存のモノリス側の機能はそのまま

##### 使いどころ

* 既存システムに変更を加えることなく移行可能
* 他の作業者がモノリスに手を加えている場合に作業しやすい
* サードパーティやSaaSなどブラックボックスシステムに有効
* 移行対象の呼び出し元がモノリス内にある場合、呼び出しを切り替えるにはモノリス内をいじる必要があるため不向き
* HTTPプロトコルは移行しやすい。逆にRPCは移行しにくい
    * リバースプロキシを使うことで、呼び出しの切り替えが簡単にできるため（元に戻すのも簡単）
* モノリスとマイクロサービスが同じデータに対して登録・更新を行う場合は、別のアプローチが必要
* （メモ）デプロイとリリースは区別する
    * デプロイは、本番環境への配置（ユーザーの利用状況は気にしない）
    * リリースは、デプロイ後にユーザーが利用した状態
* 移行しながら機能改修を行う場合、切り戻しが困難になるため、極力移行が完了するまで機能改修は我慢する

##### プロトコルを変える

* プロキシを利用することで、プロトコルを変えることができる
    * SOAP → gRPC
* プロキシ側にプロトコルを変更する仕組みを入れてしまうと、プロキシ側の作業も増えてしまうので注意
    * 切り替え自体はシステム内に含めるべき
    * **サービスメッシュ** という手法がある

##### サービスメッシュ

* マイクロサービスごと個別のプロキシ（=ローカルプロキシ）を持つ
   *  ローカルプロキシ越しにサービス間の呼び出しをルーティングする
* ローカルプロキシは、コントロールプレーンという親のプロキシで管理

#### 5.3. UI

* ユーザーインターフェースにも同様にマイクロサービスを適用できる
* 画面をドメインごと領域ごと分けて、レンダリングする（=あとの考えは基本同じ）

#### 5.4. 抽象化によるブランチ

##### 概要

* ストラングラーアプリケーションは、元から機能が疎結合だと適用できる
   * 逆を言えば、密結合だと呼び出し元に影響が大きく適用しにくい
* プログラミングでいえば、インターフェースと実装クラスの関係のような仕組み
   * インターフェースを用意して、呼び出し先との繋がりを抽象化
   * そのインターフェースに対して、既存の機能を適用させつつ、マイクロサービス版の実装クラスを用意する

##### 手順

1. 置き換える機能の抽象を作る
2. 作成した抽象を使用するように既存機能のクライアントを変更する
3. 機能を改良した抽象の実装を新たに作る
4. 新しい実装を使用するように抽象を切り替える
5. 抽象を後始末し、古い実装を削除する

##### 使いどころ

* ストラングラーアプリケーションがすぐ適用できず、既存システムに手を加えることができる

#### 5.5. デコレーティングコラボレーター

* 既存のモノリスに対して何かしらの理由で **変更できない** 場合の手法
* モノリスのリクエスト/レスポンスに対して、機能を提供するためのプロキシを通過させて実現する
    * 追加したい機能に対して、既存のリクエスト/レスポンスに含まれる情報で実現できるか検証する必要がある
    * 仮にない場合は、モノリスに対して別途必要な情報を取得するAPIを用意する必要がある

#### 5.6. 変更データキャプチャ

* これまでの手法は、モノリスの呼び出し部分に対するアプローチ
* この手法は、データストアに対するアプローチ
* 既存モノリスの提供するAPIに対して、処理の細かいトリガーが不明、レスポンスの情報不足の場合に適用する
* ざっくり言うと、  **データベースの変更を検知して** マイクロサービスを実行する
    *  データベーストリガー
    *  トランザクションログの監視
* 変更量が多いのでリスクがでかい

### 6. データベースの分割

* これまではシステムの話
* データも同じように分解してマイクロサービスに含める必要がある
* 1つのデータベースを複数のサービスが共有する構成が多い
    * あるデータに対して、どのサービスが利用して、どのサービスが所有者なのか判断が難しい
* 仮に分解ができなくとも、既存システムが利用するデータベースと切り分けるだけでも重要
    *  この観点で以下手法をまとめる

#### 6.1. データベースビュー

* 名前の通り、ビューを使う
* 物理的なデータ構造への依存度を減らすことができる

#### 6.2. データベースラッパー

* サービスが直接データベースにアクセスしないようにする
* データベースをラップするサービスを用意し、そのサービスを呼び出すように変更する
* データベースの分解が難しい時に適用できる
    * データベースのテーブル数を増やすことを防げる

#### 6.3. データベースの同期

* データベースの分割（=同期）方法
* 移行先データベース用意 → 移行元先でデータ同期をしながら一定期間利用
    * データベース間でデータベーストリガーやトランザクションログを利用して同期を取る
    * トラブルがあれば元に戻すため
* 問題がなくなったタイミングで、呼び出し元システムの切り替え含めて移行元データベースを削除
* 同期方法やタイミングによって、データの不整合が起きることを考慮

##### トレーサー書き込み

* （データベース間の同期処理ではなく）移行元システムから移行前のデータを更新しつつ、移行先のマイクロサービスも実行して同期を取る方法
    * データの移行が完全に終わったら、呼び出しを移行先のマイクロサービスに切り替える

#### 6.4. データベースとコードの分割順序

##### データベースから分割

* パフォーマンスやトランザクションの整合性に関する問題を早期に（サービスを分割する前に）発見できる
    * **このあたりが懸念としてある場合は、データベース分割から**
* データベース分割後にサービス（コード）を分割することに対して、
    * doma2、HibernateなどORM系ライブラリは、どのデータベースやテーブルを利用するかを明確に区別して
管理することができるのでコードの分割が容易になる

##### コードから分割

* だいたいこっちが多い
    * **データベース層の懸念が特になければ、コード分割から** 
* サービス層を分割することで、そのサービスで必要なデータが明確にしやすい
* データベースの分割に対して、サービスの分割はコストが少ない（短期的に着手できる）
* モノリス内でだけでAPIを分割するだけでも、データベースの隠蔽ができるのでこの後のマイクロサービスへの移行がしやすい

##### データベースとコード一緒に分割

* 段階的な検証や評価が難しいので非推奨。

#### 6.5. テーブルの分割

* カラム（データ）の所有者を明確にして、複数のサービスが１つのカラムを更新しないような作りにする
   * サービスAとサービスBがカラムCを直接更新ではなく
   * カラムCがサービスAの所有者だとすると
   * サービスBは、サービスAを介してカラムCを更新する

#### 6.6. 外部キー

* もともと外部キー参照しているテーブルを、別々のサービスに分解することは難しい（いろいろ考慮が必要
    * 一度のSQLで同時に取得できないので、レイテンシーが増える
    * データの一貫性も保証されない（削除できてしまう）ので事前チェックや削除処理制御が別途必要
* 考え方として、外部キー参照しているものは2つに分けないほうが良い可能性がある（ドメイン境界を見直す）
    
#### 6.7. 静的データ

* データ量かつ変更量が少ないデータは、そもそもデータベースで保持せず、サービスのEnumで定義する

#### 6.8. トランザクション

* データベースが分割される際、通常のデータベース側の原子性とは別に考慮が必要
   * 各サービスの別々のトランザクションを制御する必要がある
   * **2フェーズコミット**
       *  ただ行ロックが必要だし、あまり良い方法ではない気がする
* 原子性が必要なデータはそもそも分けないことが良い

#### 6.9. サーガ

* 2フェーズコミットなど分散トランザクションの代替案
* 1サービスの処理内で複数サブサービス（データベース）利用する際、サブサービスごとトランザクションを張る仕組み
   * 長時間の行ロックをしなくてすむ 
* （ただし）個々のトランザクションでエラーが発生したら、個々にロールバックもしくは再度更新処理を実行して対処する必要がある
   * 処理全体を一括でロールバックはできない
* サブサービスA→B→C→Dがあり、Cでエラーが発生した場合は、別途A、Bに対する復旧処理を用意する必要がある
* マイクロサービスではこれが一般的

### 6.10. 破壊的変更

#### 概要

* マイクロサービスの利用側に影響を与えないようにする必要がある
* 影響ないと思ってがっつり変えて本番環境にデプロイしたら、多機能に影響出たとか最悪
    *  もともとパラメータ2つだったけど、3つにした
    *  返すデータ構造を変えた
* マイクロサービスやチームの数が増えれば増えるほど、この問題は顕在化する

#### 対策

* テストを頻繁に行うことが最良の検出方法
* 変更を加えたら（検出したら）関係者に素早く周知すること
* そもそも破壊的変更で環なく、拡張する形に変更ができないか検討
* 変更前後のマイクロサービスを同時稼働させる
   * 変更後のデプロイ環境追加、変更前後の利用データ互換性など意識すべきことが増えて結構負担
* 1つのマイクロサービス内で互換性を持たせる
   * マイクロサービス内に変更前後のAPIを両方持つ

### 6.11. 監視

* ログや監視は、モノリスに比べて見るべきプロセスが増える
* どちらにせよ、1つのサービスに集約することが望ましい（ログ収集マイクロサービスなど）
   * 各マイクロサービスを利用する際にゲートウェイ的なものを通せば、収集が可能

### 6.12. 開発方法

#### 概要

* マイクロサービスいっぱいあると、開発対象のサービスを作るためにいくつかのマイクロサービスが必要
* 所有する開発PCで全部起動できなくなる

#### 対策

* スタブ化する
* 別サーバで立てたサービスを利用する
* **Telepresence** ってのがうまい具合にマイクロサービスを管理してくれるっぽい
* 本番環境では、 **Kubertetes** など使って管理するのが良い

5.8. aaaa TODO
5.9. aaaa
5.10. aaaa
5.11. aaaa
5.12. aaaa

### fff

## 構築

### Spring Framework

### AWS

## 参考
