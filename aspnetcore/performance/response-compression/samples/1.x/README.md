# <a name="response-compression-sample-application-aspnet-core-1x"></a>応答の圧縮サンプル アプリケーション (ASP.NET Core 1.x)

このサンプルは、ASP.NET Core の使用を示しています。 1.x の HTTP 応答を圧縮する応答圧縮ミドルウェア。 サンプルでは、Gzip とテキストおよびイメージ応答の圧縮のカスタム プロバイダーについて説明し、圧縮する MIME の種類を追加する方法を示しています。 ASP.NET Core 2.x サンプルでは、次を参照してください。[応答の圧縮サンプル アプリケーション (ASP.NET Core 2.x)](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples/2.x)します。

## <a name="examples-in-this-sample"></a>このサンプルの例

* `GzipCompressionProvider`
  * `text/plain`
    * **/** -927 バイトに圧縮する 2,044 バイトで Lorem Ipsum のテキスト ファイルの応答
    * **/testfile1kb.txt** -47 バイトに圧縮する 1,033 バイトでテキスト ファイルの応答
    * **トリクル/** -1 秒間隔で 1 つの文字として発行された応答
  * `image/svg+xml`
    * **/banner.svg** -4,459 バイトに圧縮する 9,707 バイトで、スケーラブル ベクター グラフィックス (SVG) イメージの応答
* `CustomCompressionProvider`<br>ミドルウェアで使用するためのカスタム圧縮プロバイダーを実装する方法を示しています

要求が含まれる場合、`Accept-Encoding`ヘッダー、サンプルは、追加、`Vary: Accept-Encoding`ヘッダーを応答にします。 `Vary`キャッシュの代替値に基づいて、応答の複数のコピーを維持するように指示`Accept-Encoding`のため、圧縮を受け入れるかシステムのキャッシュに格納されます (gzip) の圧縮と圧縮されていないバージョンの両方、または圧縮されていない応答です。

## <a name="using-the-sample"></a>サンプルの使用

1. 使用して要求を行う[Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)、または[Postman](https://www.getpostman.com/)なしにアプリケーションに、`Accept-Encoding`ヘッダーと注応答ペイロードでは、応答のサイズと応答ヘッダー。
1. 追加、`Accept-Encoding: gzip`ヘッダーおよび応答ヘッダーと圧縮された応答のサイズに注意してください。 削除するには、応答のサイズを参照してください、`Content-Encoding: gzip`サンプル アプリで応答ヘッダーが含まれます。 Lorem Ipsum の応答本文での検索または**testfile1kb.txt**テキストは圧縮され読み取り不可能なことを確認する応答、します。
1. 追加、`Accept-Encoding: mycustomcompression`ヘッダーおよび応答ヘッダーに注意してください。 `CustomCompressionProvider`は実際には、応答を圧縮されず、空の実装に対してカスタム圧縮ストリーム ラッパーを作成することができますが、`CreateStream()`メソッド。
