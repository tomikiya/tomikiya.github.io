---
title: "【Excel】データが欲しいのに表をもらったのでノンプログラミングでデータに変える"
date: 2020-04-14
slug: excel-convert-table-to-data
categories: [Excel]
---

データを集計してほしいと言われてもらったものが表だった時にExcelでデータに変換する方法になります。  
これまでVBAでゴリゴリ頑張っていた部分がプログラミング無しで出来るのは非常にありがたいです。

## 方法

PowerQueryを使います。PowerQueryについては詳しく書いている方がいらっしゃったので省略します。  
[ExcelでPowerQueryを使ってデータ収集分析](https://qiita.com/mosugi/items/71df310b35db81722d1e)

## やりたいこと

以下のような表をもらったときに、

![f:id:tomikiya:20200411203906p:plain](/images/20200411203906.png "f:id:tomikiya:20200411203906p:plain")

このようなデータを作りたいです。

![f:id:tomikiya:20200411203913p:plain](/images/20200411203913.png "f:id:tomikiya:20200411203913p:plain")

## やり方

表を選択し、「データ」タブの「テーブルまたは範囲から」を選択します。

![f:id:tomikiya:20200411203930p:plain](/images/20200411203930.png "f:id:tomikiya:20200411203930p:plain")

「テーブルの作成」のダイアログが出てくるので、データ範囲が間違っていないか確認しOKを選択します。

![f:id:tomikiya:20200411234331p:plain](/images/20200411234331.png "f:id:tomikiya:20200411234331p:plain")

するとPowerQueryのクエリエディターが立ち上がります。

![f:id:tomikiya:20200411204134p:plain](/images/20200411204134.png "f:id:tomikiya:20200411204134p:plain")

新潟、富山、石川の列を選択して「変換」タブの「列のピボット解除」を選択します。  
月の列を選択して「その他の列のピボット解除」でもOKです。

![f:id:tomikiya:20200411204155p:plain](/images/20200411204155.png "f:id:tomikiya:20200411204155p:plain")

一瞬でほしいデータに代わりました。

![f:id:tomikiya:20200411232422p:plain](/images/20200411232422.png "f:id:tomikiya:20200411232422p:plain")

見出しをダブルクリックすると名称を変更できます。

![f:id:tomikiya:20200411232437p:plain](/images/20200411232437.png "f:id:tomikiya:20200411232437p:plain")

「ファイル」タブから「閉じて読み込む」を選択してクエリエディタを終了させます。

![f:id:tomikiya:20200411232514p:plain](/images/20200411232514.png "f:id:tomikiya:20200411232514p:plain")

これで出来上がりです。

![f:id:tomikiya:20200411232528p:plain](/images/20200411232528.png "f:id:tomikiya:20200411232528p:plain")

## 結合セルがあったらどうするの？

### 1.縦に結合したセルがあった場合

![f:id:tomikiya:20200411232543p:plain](/images/20200411232543.png "f:id:tomikiya:20200411232543p:plain")

前述したようにまずはPowerQueryに取り込みます。

![f:id:tomikiya:20200411232603p:plain](/images/20200411232603.png "f:id:tomikiya:20200411232603p:plain")

すると今度はこんな形になります。結合セルにnullが入っています。

![f:id:tomikiya:20200411232620p:plain](/images/20200411232620.png "f:id:tomikiya:20200411232620p:plain")

「変換」タブに「フィル」があるので下方向にフィルを行います。

![f:id:tomikiya:20200411232643p:plain](/images/20200411232643.png "f:id:tomikiya:20200411232643p:plain")

するとnullが埋まるので、あとの操作は同じです。  
新潟、富山、石川の列を選択した状態でピボットテーブルの解除を行い、見出しを整えてPowerQueryを終了させます。

![f:id:tomikiya:20200411232715p:plain](/images/20200411232715.png "f:id:tomikiya:20200411232715p:plain")

出来上がりです。

![f:id:tomikiya:20200411232735p:plain](/images/20200411232735.png "f:id:tomikiya:20200411232735p:plain")

このあたりの操作はもっと詳しく書かれている方がいますので参考にされてみるとよいと思います。

[[Excel 取得と変換] Power Query でクロス集計表・マトリックス表を表形式・テーブル形式に変換する | Road to Cloud Office](https://road2cloudoffice.blogspot.jp/2016/05/power-query.html)

### 2.横に結合したセルがあった場合

![f:id:tomikiya:20200411232830p:plain](/images/20200411232830.png "f:id:tomikiya:20200411232830p:plain")

「先頭行をテーブルの見出しとして使用する」のチェックを外して取り込ます。

![f:id:tomikiya:20200411232841p:plain](/images/20200411232841.png "f:id:tomikiya:20200411232841p:plain")

↓

![f:id:tomikiya:20200411232908p:plain](/images/20200411232908.png "f:id:tomikiya:20200411232908p:plain")

「横にフィル」というものがないので一旦表の行列を入れ替えます。  
「変換」タブの「入れ替え」を選択します。

![f:id:tomikiya:20200411232938p:plain](/images/20200411232938.png "f:id:tomikiya:20200411232938p:plain")

↓

![f:id:tomikiya:20200411232945p:plain](/images/20200411232945.png "f:id:tomikiya:20200411232945p:plain")

1列目で「下にフィル」を行います。

![f:id:tomikiya:20200411232956p:plain](/images/20200411232956.png "f:id:tomikiya:20200411232956p:plain")

1月2月3月がレコードとして扱われてしまっていますので「ホーム」タブの「1行目をヘッダーとして使用」を選択します。

![f:id:tomikiya:20200411233008p:plain](/images/20200411233008.png "f:id:tomikiya:20200411233008p:plain")

↓

![f:id:tomikiya:20200411233106p:plain](/images/20200411233106.png "f:id:tomikiya:20200411233106p:plain")

見出しを整えてピボット解除して出来上がりです。

![f:id:tomikiya:20200411233132p:plain](/images/20200411233132.png "f:id:tomikiya:20200411233132p:plain")

↓

![f:id:tomikiya:20200411233144p:plain](/images/20200411233144.png "f:id:tomikiya:20200411233144p:plain")

### 3.縦と横どちらにも結合したセルがあった場合

![f:id:tomikiya:20200411233202p:plain](/images/20200411233202.png "f:id:tomikiya:20200411233202p:plain")

これまでのやり方＋αでできます。  
まずは取り込んで1列目を下にフィルします。

![f:id:tomikiya:20200411233222p:plain](/images/20200411233222.png "f:id:tomikiya:20200411233222p:plain")

↓

![f:id:tomikiya:20200411233229p:plain](/images/20200411233229.png "f:id:tomikiya:20200411233229p:plain")

ここで少し工夫します。列1と列2を結合しておきます。そうすると後で行列を入れ替えたときに年と月の列を年月として見出しにできるようになります。

「変換」タブの「列のマージ」を選択します。

![f:id:tomikiya:20200411233336p:plain](/images/20200411233336.png "f:id:tomikiya:20200411233336p:plain")

↓

![f:id:tomikiya:20200411233348p:plain](/images/20200411233348.png "f:id:tomikiya:20200411233348p:plain")

区切り記号は何でもよいです。とりあえず大概のデータに被らなそうな「★」が個人的にはお気に入りです。

![f:id:tomikiya:20200411233409p:plain](/images/20200411233409.png "f:id:tomikiya:20200411233409p:plain")

↓

![f:id:tomikiya:20200411233426p:plain](/images/20200411233426.png "f:id:tomikiya:20200411233426p:plain")

この状態で行列を入れ替えます。  
![34.png](https://qiita-image-store.s3.amazonaws.com/0/97601/a7e61b54-6657-4eb3-3c2f-7e5014cc554c.png)

1列目を下にフィルします。また1行目を見出しとして使用します。さらに1列名2列目の見出しを適宜変更します。

![f:id:tomikiya:20200411234858p:plain](/images/20200411234858.png "f:id:tomikiya:20200411234858p:plain")

あともう少しです。「2017★1」から「2018★3」を選択してピボット解除します。

![f:id:tomikiya:20200411233528p:plain](/images/20200411233528.png "f:id:tomikiya:20200411233528p:plain")

属性（＝年月）の列を選択して「変換」タブの「列の分割」を行い年と月に分割します。

![f:id:tomikiya:20200411233550p:plain](/images/20200411233550.png "f:id:tomikiya:20200411233550p:plain")

↓

![f:id:tomikiya:20200411233557p:plain](/images/20200411233557.png "f:id:tomikiya:20200411233557p:plain")

見出しを整えて出来上がりです。お疲れ様でした。

![f:id:tomikiya:20200411233608p:plain](/images/20200411233608.png "f:id:tomikiya:20200411233608p:plain")

### おまけ

こんな表をもらったら。

![f:id:tomikiya:20200411233629p:plain](/images/20200411233629.png "f:id:tomikiya:20200411233629p:plain")

PowerQueryに取り込む範囲を分けます。まずはA列からD列を選択して取り込みます。

![f:id:tomikiya:20200411233652p:plain](/images/20200411233652.png "f:id:tomikiya:20200411233652p:plain")

そのあとは何もしないで「ファイル」タブの「閉じて次に読み込む」を選択します。

![f:id:tomikiya:20200411233719p:plain](/images/20200411233719.png "f:id:tomikiya:20200411233719p:plain")

今はExcel側に出力したくないので「接続の作成のみ」を選択します。

![f:id:tomikiya:20200411233730p:plain](/images/20200411233730.png "f:id:tomikiya:20200411233730p:plain")

この作業をE列からH列、I列からL列にも行って3つのテーブルをPowerQueryに取り込みます。  
左ペインを開くと3つのテーブルが取り込まれているのが分かります。

![f:id:tomikiya:20200411233903p:plain](/images/20200411233903.png "f:id:tomikiya:20200411233903p:plain")

「ホーム」タブの「クエリの追加」-「クエリを新規クエリとして追加」を選択して3つのテーブルを結合します。

![f:id:tomikiya:20200411233922p:plain](/images/20200411233922.png "f:id:tomikiya:20200411233922p:plain")

↓

![f:id:tomikiya:20200411233944p:plain](/images/20200411233944.png "f:id:tomikiya:20200411233944p:plain")

↓

![f:id:tomikiya:20200411233954p:plain](/images/20200411233954.png "f:id:tomikiya:20200411233954p:plain")

ピボットを解除して出来上がりです。

## ということで

変なデータを送られてきても、まあ何とかしてやろうかと思えるようになりますね。Power Queryを覚えると作業効率が格段に上がりますので是非活用してましょう。
