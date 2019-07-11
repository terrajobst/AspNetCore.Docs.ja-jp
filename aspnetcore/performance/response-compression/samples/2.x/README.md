# <a name="response-compression-sample-application-aspnet-core-2x"></a>応答の圧縮サンプル アプリケーション (ASP.NET Core 2.x)

このサンプルは、ASP.NET Core の使用を示しています。 2.x を HTTP 応答を圧縮する応答圧縮ミドルウェア。 サンプルでは、Gzip、Brotli、テキストおよびイメージ応答の圧縮のカスタム プロバイダーを説明し、圧縮する MIME の種類を追加する方法を示しています。 ASP.NET Core 1.x サンプルでは、次を参照してください。[応答の圧縮サンプル アプリケーション (ASP.NET Core 1.x)](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples/1.x)します。

## <a name="examples-in-this-sample"></a>このサンプルの例

* `BrotliCompressionProvider`
  * `text/plain`
    * **/** -Lorem Ipsum テキスト ファイルで応答 2,044 バイト ~ 979 バイトに圧縮します。
    * **/testfile1kb.txt** -~ 36 バイトには、圧縮 1,033 バイトでテキスト ファイル応答します。
    * **トリクル/** -応答の 1 秒間隔で 1 つの文字として発行します。
* `GzipCompressionProvider`
  * `text/plain`
    * **/** -Lorem Ipsum テキスト ファイルで応答 2,044 バイト ~ 927 バイトに圧縮します。
    * **/testfile1kb.txt** -~ 47 バイトには、圧縮 1,033 バイトでテキスト ファイル応答します。
    * **トリクル/** -応答の 1 秒間隔で 1 つの文字として発行します。
  * `image/svg+xml`
    * **/banner.svg** -~ 4,459 バイトには、圧縮 9,707 バイトで、スケーラブル ベクター グラフィックス (SVG) イメージ応答します。
* `CustomCompressionProvider`<br>ミドルウェアで使用するためのカスタム圧縮プロバイダーを実装する方法を示します。

要求が含まれる場合、`Accept-Encoding`ヘッダーと応答の圧縮が成功したら、ミドルウェアが自動的に追加、`Vary: Accept-Encoding`ヘッダーを応答にします。 `Vary`キャッシュの代替値に基づいて、応答の複数のコピーを維持するように指示`Accept-Encoding`のために両方を圧縮された Gzip (Brotli) かシステムを受け入れるため、圧縮されていないバージョンがキャッシュに格納されます、圧縮または圧縮されていない応答します。

## <a name="use-the-sample"></a>サンプルを使用します。

1. 使用して要求を行う[Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)、または[Postman](https://www.getpostman.com/)なしにアプリケーションに、`Accept-Encoding`ヘッダーと注応答ペイロードでは、応答のサイズと応答ヘッダー。
1. 追加、`Accept-Encoding: br`または`Accept-Encoding: gzip`ヘッダーおよび応答ヘッダーと圧縮された応答のサイズに注意してください。 応答のサイズになると、および`Content-Encoding`か Gzip で圧縮することを示すミドルウェアによって応答ヘッダーを追加または Brotli が発生しました。 Lorem Ipsum の応答本文での検索または**testfile1kb.txt**テキストは圧縮され読み取り不可能なことを確認する応答、します。
1. 追加、`Accept-Encoding: mycustomcompression`ヘッダーおよび応答ヘッダーに注意してください。 `CustomCompressionProvider`は実際には、応答を圧縮されず、空の実装に対してカスタム圧縮ストリーム ラッパーを作成することができますが、`CreateStream()`メソッド。
