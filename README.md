# マイクロサービス学習用プロジェクト

## 目的

* 概念や思想をちゃんと理解する
* 不向きな部分、デメリットも抑える
* Spring Framework、AWSの構築方法を習得する

## マイクロサービス

### 1. マイクロサービスとは

* ビジネスドメインに基づいてモデル化された、独立してデプロイ可能なサービス
* サービス同士はネットワークを介して相互に通信
* サービス指向アーキテクチャ（SOA）の一種
* **新しい技術** を使う必要はない。どんな言語で、どんなプラットフォームでも良い

#### 1.1. 独立デプロイ可能性

* 他のサービスに影響を与えることなくデプロイできる
* サービス同士が疎結合

#### 1.2. ビジネスモデルに基づくモデル化

* 従来のモノリシックかつ3層アーキテクチャ（Web、App、DB）は、層ごと組織やチームを形成していた
    * DBはサービス間で共有することが多いため、変更の影響が大きい
    * 機能ではなく技術のまとまりでチームを形成
* 技術ではなく機能のまとまりでチームを形成することで、サービスのカプセル化が可能
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
    * プロダクトオーナーや運用チームは、各々サービスごと配置
    * それらサービスを束ねる技術リーダーは別途設ける

### 2. モノリス（モノリシック）とは

* すべてのコードが単一のプロセスとしてデプロイされているシステム

#### 2.1. モノリスの課題

* 複数人で同じコードを変更することが増える
   * 異なる開発者が同じコードを変更したい、異なるチームが異なる時間帯に機能追加したくなる
   * システムや機能の境界線が混乱する

#### 2.2. モノリスの利点

* シンプル
* コードの再利用がしやすい
    * マイクロサービスの場合は、丸コピか共通化する必要がある
* 監視やE2Eテストがしやすい
    * マイクロサービスは複雑になりがち
* モノリス ≠ レガシー
    * モノリスも選択肢の1つ 

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

### ccc

3.1. aaaa
3.2. aaaa
3.3. aaaa
3.4. aaaa
3.5. aaaa
3.6. aaaa
3.7. aaaa
3.8. aaaa
3.9. aaaa
3.10. aaaa

### ddd

4.1. aaaa
4.2. aaaa
4.3. aaaa
4.4. aaaa
4.5. aaaa
4.6. aaaa
4.7. aaaa
4.8. aaaa
4.9. aaaa
4.10. aaaa
4.11. aaaa
4.12. aaaa
4.13. aaaa
4.14. aaaa
4.15. aaaa
4.16. aaaa
4.17. aaaa

### eee

5.1. aaaa
5.2. aaaa
5.3. aaaa
5.4. aaaa
5.5. aaaa
5.6. aaaa
5.7. aaaa
5.8. aaaa
5.9. aaaa
5.10. aaaa
5.11. aaaa
5.12. aaaa

### fff

## 構築

### Spring Framework

### AWS
