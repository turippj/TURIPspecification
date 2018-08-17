# TURIPonUART仕様

## 概要

TURIPonUARTは、UART上でTURIPを動作させるための仕様です。
テキストベースかつ対話式な仕様なため、人が直接TURIPモジュールとやりとりすることが可能なのが特徴です。

## 物理仕様

TURIPonUARTは明確な物理仕様を有しません。
ただし、参考として標準的な物理仕様を示します。

### 標準的な物理仕様

電源電圧およびロジックレベルは3.3Vです。
ピンアサインは以下の通りとなります。
4番ピンは、マイコンのリセットピンなどと接続し、セルフプログラミング等に用いることが可能です。
必要に応じてコネクタは自由に決めることができます。

ピン番号 | ピン名称
---------|-------------
1        | PIN_UART_TX
2        | PIN_UART_RX
3        | PIN_UART_3V3
4        | -
5        | PIN_UART_GND

## ロジック仕様

ボーレートは`PORT_UART_BAUD_RATE`で設定されています。
8ビット長で、パリティービットはありません。

## コマンド体系

TURIPonUARTコマンドは、HTTPライクなものです。
コマンドは`<LF>`または`<CR><LF>`で終端されます。
また、TURIPデバイスからの応答はJSON形式となります。

### ポートへの読み書き

#### 読み出し

TURIPデバイスからの読み込みは[ポート番号 + 改行コード]です。
具体的な書式は以下の通りです。

`[port]<LF>`

#### 書き込み

TURIPデバイスへの書き込はポート番号の後にデータを送ります。
ポート番号とデータ間にはスペースが入ります。
具体的な書式は以下の通りです。

`[port] [データ]<LF>`

### TURIPモジュールの応答

TURIPモジュールからの応答はJSON形式となります。
JSONオブジェクトの中身は次の通りです。
また、p、t、dはリスト形式になる場合があります。
その場合はステータスコードに'4'が加算されます。

Key | Value type         | 概要
----|--------------------|------------------------------------
s   | int                | TURIPデバイスからのステータスコード
p   | int                | アクセスしたポート番号
t   | string             | ポートに格納されているデータの型
d   | (ポート内容に依存) | ポートに格納されているデータ本体


#### ステータスコード

ステータスコードはバイナリモード時と同様です。
また、ステータスコードは足し合わせることができます。

code | 説明
-----|-----------------------------------------
0  | 正常な通信を行った
1  | エラーが発生した
2  | 複数データが存在する
4  | プッシュ送信データ

#### 応答例

標準的な応答を以下に示します。

```
{
  "s":0,
  "p":1,
  "t":"int16",
  "d":1234
}
```

#### 応答例(複数データを含む場合)

複数データを有する場合の標準的な応答を以下に示します。

```
{
  "s":6,
  [
  {"p":1,"t":"int16","d":1234},
  {"p":2,"t":"float","d":123.4}
  ]
}
```

## ブリッジ機能

TURIPonUARTは、子モジュールへのカスケード接続をサポートするブリッジ機能を有しています。
読み込み時は以下の通りとなります。

`[id]/[port]<LF>`

また、書き込みの書式は以下の通りです。

`[id]/[port] [データ]<LF>`

多段接続を行う場合は以下の通りとなります。

`[id1]/[id2]/[id3]/[port]`