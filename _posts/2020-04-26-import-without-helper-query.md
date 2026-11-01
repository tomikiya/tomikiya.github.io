---
title: "【Power Query】ヘルパークエリに頼らないでフォルダーのデータを取り込む"
date: 2020-04-26
slug: import-without-helper-query
categories: [PowerPlatform]
---

ExcelやPower BIでデータを取得するときに、「フォルダーから」を選択するケースは多いと思います。
その時に作られるクエリ「ヘルパークエリ」の話です。
## ヘルパークエリとは

作った覚えのないクエリが生成されることがあります。
↓
![f:id:tomikiya:20200425204831p:plain](/images/20200425204831.png "f:id:tomikiya:20200425204831p:plain")
これがヘルパークエリです。
複数のフォルダーを取り込むとその数だけこのヘルパークエリが生成されたりするので、正直鬱陶しいと感じたことがある方も多いのではないでしょうか。
これはPower Queryがファイルの中身を解析するためのもので、ファイルに含まれるデータの列数や文字コード、見出し等を取得するために作られています。
解析した項目は、ファイルの中身を展開して結合するクエリで使われています。

## ヘルパークエリを生成するケース

ファイルを結合するクエリをPower Queryに作ってもらうと生成されます。
例えば、[データ]-[データの取得]-[ファイルから]-[フォルダーから]でフォルダーを選択し、「結合」を選ぶと生成されます。
![f:id:tomikiya:20200425212348p:plain](/images/20200425212348.png "f:id:tomikiya:20200425212348p:plain")
また、見出しのボタンで「ファイルの結合」をした場合にも作られたりします。
![f:id:tomikiya:20200425222511p:plain](/images/20200425222511.png "f:id:tomikiya:20200425222511p:plain")

## ヘルパークエリに頼らないで取り込むために

### フォルダーの情報を取り込む

まずはフォルダーの情報だけ取得します。ファイルの結合をしなければヘルパークエリは生成されません。
前述したクエリを生成するケースにおいて、「結合」を選ばず、「データの変換」を選ぶとヘルパークエリが生成されないことが分かります。
![f:id:tomikiya:20200425213157p:plain](/images/20200425213157.png "f:id:tomikiya:20200425213157p:plain")
結合の手前、ファイルの一覧まで取り込まれます。
![f:id:tomikiya:20200425213430p:plain](/images/20200425213430.png "f:id:tomikiya:20200425213430p:plain")

### Binaryを解析して中身を展開する

ヘルパークエリがやってくれていたBinaryの解析を自分で行います。
取り込みたいファイルの種類や文字コード等はほとんどの場合で取り込む前に把握しているはずですので、自分で指定できるはずです。
例えば「区切り位置がタブ」で「列数が3」、「文字コードがSHIFT-JIS」のCSVを取り込むなら以下のようにすれば解析結果の列を追加できます。

```
= Table.AddColumn(ソース, "CSV_Content", each Table.PromoteHeaders(Csv.Document([Content],[Delimiter="    ", Columns=3, Encoding=65001, QuoteStyle=QuoteStyle.None])))
```

（[Csv.Document](https://docs.microsoft.com/ja-jp/powerquery-m/csv-document)でCSVのコンテンツを取得し、[Table.PromoteHeaders](https://docs.microsoft.com/ja-jp/powerquery-m/table-promoteheaders)で1行目をヘッダとしています。）
↓
![f:id:tomikiya:20200425220734p:plain](/images/20200425220734.png "f:id:tomikiya:20200425220734p:plain")
あとは不要な列を削除してtableを展開すれば完了です。

#### よく使う文字コード

shift-JISは932で、utf-8は65001です。

#### 自分で式を作れない場合は

もし、文字コードが分からなかったり、どうやって解析すればよいか見当もつかない場合はヘルパークエリを敢えて作ってみてください。
「ファイルの変換」の関数クエリで手順を確認できます。
![f:id:tomikiya:20200425221739p:plain](/images/20200425221739.png "f:id:tomikiya:20200425221739p:plain")

## ということで

ヘルパークエリが煩わしくなってきたらPower Queryを理解してきている証拠だと個人的に思いますので、仕様を理解できてきたら挑戦してみてもよいかもしれません。
