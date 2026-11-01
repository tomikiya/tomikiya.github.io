---
title: "【Excel VBA】プロキシ環境でHttprequestのSendでエラーが出たので対応した話"
date: 2020-06-14
slug: excel-httprequest-proxy
categories: [Excel]
---

ExcelからHttpリクエストをするケースが、特にWeb APIをExcelから叩くケースが少しずつ私の範囲で増えてきています。Power QueryがあればGETとPOSTをたたくことは容易ですが、それ以外のメソッドはどうしてもHttpリクエストでの実装に頼ることになります。

そんなん中、今回、私が出会ったエラーと対応を共有します。

## どんな現象が起こった？

既に実装して本番稼働していたExcelツールが「何もしないのに使えなくなった。」と問い合わせがありました。エラーのスクリーンショットを送ってもらうと、確かに私が用意した想定外の時に表示されるエラーメッセージが表示されていました。

このシステム、WebアプリのデータをWeb APIで取得して参照したり更新するものだったんですけど、認証のAPIで既にこけてる状態でした。

そこで、遠隔操作させてもらって実際のツールをデバックしたのですがHttpリクエストのSendメソッドのところでレスポンスが返ってこないような挙動をしていました。タイムアウトしているような間を確認しました。

```
Public Sub HttpRequestSample()
    Dim req As Object
    Set req = CreateObject("WinHttp.WinHttpRequest.5.1")
    req.Open "GET", "URL", False
    
    ' 細かいオプション(省略)

    req.Send  '←ここでエラー

    ' 以下、レスポンス処理(省略)
End Sub
```

## 原因を調査

「何もしてない。」は大概何かしているはずなので詳しく状況をヒアリングしました。そうすると「つい最近、事務所を引っ越した」という情報をGET。

多分それや。

で、IEのインターネットオプションを見るとプロキシが設定されていることを確認しました。どうやら引っ越し先の事務所はプロキシサーバを経由する環境のようでした。

うん、これっぽい。

そして、WinHttpのプロキシ設定をコマンドプロンプトで確認すると・・・。

```
> netsh winhttp reset proxy

現在の WinHTTP プロキシ設定:

    直接アクセス (プロキシ サーバーなし)。
```

あ・・・、原因を見つけました。

## SendメソッドはWinHttpのプロキシ設定を参照する

プロキシの設定はIEのインターネットオプションだけが設定されがちですが、WinHttpの設定も必要です。WinHttpはWindows Updateの時などにも参照されています。WinHttpにプロキシの設定がされていないとインターネットは参照できますがWindows Updateで失敗するはずです。

問合せ元に原因を説明し、プロキシ設定を変更する了承をいただき、コマンドプロンプトで以下を実行しました。

```
> netsh winhttp import proxy source=ie
```

インターネットオプションに設定してあるプロキシ設定をコピーする命令ですね。

この変更により、無事ツールがいつも通り使えるようになりました。

## プロキシを想定していなかった自分が悪い

非機能要件の取り決め段階でプロキシまで考慮していなかった自分が完全に悪いです。今回は急ぎで対応したのでWinHttpの設定を変えましたが、プロキシがあっても無くても対応できるようにVBA側で対応したほうが汎用性は高いでしょう。今後の開発ではそこらへんも忘れないようにしたいと思います。

ちなみにVBA側でプロキシの設定をする場合はこんな感じですね。

```
Public Sub HttpRequestSample()
    Dim req As Object
    Set req = CreateObject("WinHttp.WinHttpRequest.5.1")
    req.Open "GET", "URL", False
    
    ' 細かいオプション(省略)
    
    ' プロキシはここで設定
    req.setProxy 2, "proxyhost:port"
    req.setProxyCredentials "User", "Password"

    req.Send

    ' 以下、レスポンス処理(省略)
End Sub
```

## 補足：Power Queryはインタネットオプションのプロキシ設定を参照している模様

Power Queryで「Webから」を使用してWeb APIをたたいている場合、インターネットオプションのプロキシを見ている挙動でした。

何が言いたいかというと、インターネットオプションとWinHttpどちらにも忘れずにプロキシ設定をしましょう、です。
