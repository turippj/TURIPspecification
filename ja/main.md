# Tiny Universal (Tottori University) Robot Interface Protocol (TURIP) 仕様

## TURIPの概要

TURIPは、電子モジュールとの通信を簡単化するためのインターフェイス仕様です。
開発者の学習コストを最小限に抑え、組み込み機器開発をより簡便にすることを目的としています。
TURIPは複数の既存プロトコルからの同一のメモリ領域、変数へのアクセス方法を定義します。
TURIPは、センサの数値データなどといった小容量のデータのやりとりに適しています。
また、TURIPは複数のプロトコル間のブリッジとなることができます。
TURIPは非常に軽量なため、対応した電子デバイス(TURIPデバイス)は、安価なマイクロコントローラ等で実装できます。

## バージョン

### 本仕様のバージョン

0.3-draft

### バージョニングの定義

バージョンの標記は以下のように示されます。

[メジャーバージョン].[通算バージョン]

##### メジャーバージョン

TURIPのメジャーバージョンです。0.x系を除く同じメジャーバージョン間では、後方互換性が維持されます。

##### 累積バージョン

TURIPの累積バージョンです。`PORT_TURIP_VERSION`に対応します。
奇数は開発版、偶数はリリース版です。

## TURIPが対応するデータ形式

TURIPでは、予め決められたデータ形式（データ型）を扱うことが可能です。
データ型の種類は以下に示す通りです。

| 名称       | 仕様                      |
|------------|---------------------------|
| bool       | ブール型                  |
| uint8      | 符号無し8bit整数型        |
| int8       | 符号付き8bit整数型        |
| uint16     | 符号無し16bit整数型       |
| int16      | 符号付き16bit整数型       |
| uint32     | 符号無し32bit整数型       |
| int32(int) | 符号付き32bit整数型       |
| uint64     | 符号無し64bit整数型       |
| int64      | 符号付き64bit整数型       |
| float      | 単精度(32bit)浮動小数点型 |
| double     | 倍精度(64bit)浮動小数点型 |
| string     | テキスト                  |
| bin        | バイナリ                  |

## ポート

TURIPはTURIPデバイス内部に構築された「ポート」にアクセスすることでデータ交換を実現しています。
ポートにはそれぞれにデータが格納されています。
ポートの範囲は0から127までです。
詳細は「TURIPのポートマップ(PortMapping.md)」を参照してください。

## 対応プロトコル

TURIPは複数の既存プロトコルに対応しています。
それぞれのプロトコルにおける仕様は、別紙で定義します。

プロトコル | ドキュメントタイトル
-----------|---------------------------------
SPI        | TURIPonSPI仕様 (TURIPonSPI.md)
UART       | TURIPonUART仕様 (TURIPonUART.md)
HTTP       | TURIPonHTTP仕様 (TURIPonHTTP.md)

### バイナリインターフェイス/テキストインターフェイス

TURIPには「バイナリインターフェイス」と「テキストインターフェイス」が存在します。
バイナリインターフェイスは軽量性を重視しており、マイコン-センサデバイス間等の通信に有利です。
テキストインターフェイスは柔軟性を重視しており、コンピュータ間の通信、中・長距離の通信に有利です。
それぞれでインターフェイスで用いるプロトコルは決まっています。

- バイナリインターフェイス
  - SPI
- テキストインターフェイス
  - UART
  - HTTP

## TURIPID

TURIPデバイスは、それぞれ固有のIDを有します。
IDは型番とシリアル番号を組み合わせた64bitの整数です。
型番は、申請毎にTURIP管理団体が発行します。
ただし、0x0001-0x0fffの範囲は、管理団体に登録せずに自由に利用可能です。
シリアル番号はベンダーが管理します。
シリアル番号を定義しない場合は、0x0とします。

### IDの省略表示

テキストモードでは、IDを省略表記することが可能です。

`[型番]-[シリアル番号]`

それぞれ、上位の'0'は省略できます。
たとえば、型番が`0x00001234`、シリアル番号が`0x0000abcd`の場合、省略表記は
`1234-abcd`
となります。

## マニフェスト

TURIPモジュールは、マニフェストと呼ばれるドキュメントでその仕様を表現することが可能です。
マニフェストは型番毎に定義され、マニフェストリポジトリ(manifest.turip.org)に登録されています。
マニフェストの取得はHTTP GETメソッドによって、
`http(s)://manifest.turip.org/[型番]`にアクセスします。
型番は16進数表記です。
詳細は「TURIPManifest仕様(Manifest.md)」を参照してください。
