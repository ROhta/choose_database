## 安易に立てるな

## データベース

#### ～そのデータどこで持つ？～

@snap[south]
太田
@snapend

---

よくあるwebアプリケーションの処理フロー

---

1. データベースから取ってくる
1. サーバサイドプログラムの変数に突っ込む
1. クライアントに渡す
1. クライアントサイドプログラムの変数に突っ込む
1. 表示

---

データを保持しなきゃいけないと思うと<br/>データベースに突っ込もうとしがち

+++
@transition[zoom]

### データはもっと<br/>いろんなところに置ける！

---

#### 基本戦略は

- なるべく取りに行かない
  - 取りに行くならなるべくフロント寄りの場所から
- なるべく保存しない
  - 保存をするならなるべくフロント寄りの場所に
  - ただしセキュリティを考える

---

### データを置ける場所

- サーバサイド |
  - データストア |
    - データベース |
    - ストレージ |
    - インメモリ |
  - プログラム内 |
    - グローバル変数 |
- クライアントサイド |
  - データストア |

---

## server side

---

### database

- RDB
- NoSQL
  - 列指向
  - グラフ型
  - KVS
  - DocumentDB

+++

#### RDB

- ORACLE
- SQL Server
- DB2
- MySQL
- MariaDB
- postgreSQL

+++

#### NoSQL

- 列指向
  - Cassandra, HBase, Redshift
- グラフ型
  - Neo4j, Neptune
- KVS
  - Redis, Riak, DynamoDB
- DocumentDB
  - MongoDB, CouchDB, Amazon DocumentDB

---

### storage

- s3
- サーバ上のローカルファイル

---

### In memory data store

---

- Redis
- Memcached

---

#### 使うならRedis

- memcachedでできてRedisで出来ないことはない |
- Redisでできてmemcahedで出来ないことはある |
  - データの半永続化 |
  - レプリケーション |
  - ソート |

---

### global variable

---

データの内容によっては、<br/>グローバル変数に大容量データを突っ込む実装もあり

---

#### 何回も取りに行く必要が無い情報

- マスター情報
  - 何らかのツリー構造
    - 組織図, 検索条件, etc
  - 受け付けるリクエストパラメータの種類
- 比較対象データ等
  - データAとデータBを照合させるような処理<br/>（Aはリクエスト毎に変わる、<br/>Bはリクエストに関わらず不変）
  - Bをグローバル変数に入れておく

---

ただし、気を配らないといけないポイントはある

---

- 上書き不可の定数として宣言する
- GCの設定を確認する |
- スレッドセーフか確認する |
- （可能であれば）ミドルウェア起動時のみ変数への代入処理が走る実装にする |

---

## client side

---

- グローバル変数に突っ込みまくるのはNG
  - 使用可能なメモリ容量はユーザの端末依存だから
  - 下手するとブラウザクラッシュ
- client sideでもデータストアは使える |

---

- cookie
- Web Storage
  - localStorage
  - sessionStorage
- webSQL
- Indexed DB

---

### cookie

- 文字列を保存する形式
- 保存容量4KB~~オワコン~~
- サーバにデータを自動送信
- 有効期限設定あり

---

### Web Storage

- KVS（文字列のみ格納可）
- 保存容量5MB
- データ送信しない
- 有効期限設定なし

---

### local Storage vs session Storage

|| localStorage | sessionStorage |
| --- | --- | --- |
| データの<br/>有効期限 | jsで消さない限り無限 | セッション内 |
| データの<br/>有効範囲 | 同一ドメイン内 | タブ内 |

---

### webSQL

- ブラウザ上でSQLiteが使える
- 廃止されたからIndexedDBを使え<br/>と[googleが言っている](https://developers.google.com/web/tools/lighthouse/audits/web-sql?hl=ja)

---

### Indexed DB

- テーブル1つのみのRDB、というイメージ
  - 結合や外部キー制約はなし
  - 文字列以外も保存可能
  - key以外で検索可能
  - トランザクションがある＝非同期処理もいける
- 保存容量めっちゃいっぱい（up to quata）
  - 端末の空き容量による

---

### 使いどき

- cookieとwebSQLは無い |
- webStorage |
  - 手軽に使いたい（保存容量少ない）とき |
  - 同期処理で問題ないとき |
  - データのタブ間共有をしたくないときのみsessionStorage |
- Indexed DB |
  - ガチな（保存容量・種類が多い）とき |
  - 非同期処理（トランザクション）が必要なとき |
  - 検索したいとき |
  - 文字列以外も保存したいとき |

+++

時間があれば実演

---

## まとめ

- 初期設計は大事
- 実装前によく考えましょう

+++

@quote[最高の設計と実装は、<br/>お互いの尊重する雰囲気の下で<br/>プロダクションとプロダクトに<br/>対する関心が<br/>1つになったときに生まれるもので、<br/>それこそがSREが約束するものです。]

@snap[south]
SRE本 第31章より
@snapend
