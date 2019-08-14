# <a name="response-compression-sample-application-aspnet-core-3x"></a>応答の圧縮サンプルアプリケーション (ASP.NET Core 3.x)

このサンプルでは、HTTP 応答を圧縮するための ASP.NET Core 3.x 応答圧縮ミドルウェアの使用方法を示します。 このサンプルでは、テキストとイメージの応答に使用する Gzip、Brotli、カスタムの圧縮プロバイダーを示し、圧縮のために MIME の種類を追加する方法を示します。 ASP.NET Core 2.x サンプルについては、「[応答圧縮のサンプルアプリケーション (ASP.NET Core 2.x)](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples/2.x)」を参照してください。

## <a name="examples-in-this-sample"></a>このサンプルの例

* `BrotliCompressionProvider`
  * `text/plain`
    * **/** -Lorem Ipsum テキストファイルの応答は、2044バイトで ~ 979 バイトに圧縮されます。
    * **/testfile1kb.txt** -テキストファイルの応答が1033バイトで、最大36バイトに圧縮されます。
    * **/trickle** -1 秒間隔で1つの文字として発行された応答。
* `GzipCompressionProvider`
  * `text/plain`
    * **/** -Lorem Ipsum テキストファイルの応答は、2044バイトで ~ 927 バイトに圧縮されます。
    * **/testfile1kb.txt** -テキストファイルの応答が1033バイトで、最大47バイトに圧縮されます。
    * **/trickle** -1 秒間隔で1つの文字として発行された応答。
  * `image/svg+xml`
    * **/banner.svg** -9707 バイトのスケーラブルベクターグラフィックス (svg) イメージ応答。最大4459バイトに圧縮されます。
* `CustomCompressionProvider`<br>ミドルウェアで使用するカスタム圧縮プロバイダーを実装する方法について説明します。

要求に`Accept-Encoding`ヘッダーと応答の圧縮が正常に含まれる場合、ミドルウェアは自動的`Vary: Accept-Encoding`にヘッダーを応答に追加します。 ヘッダー `Vary`は、の`Accept-Encoding`代替値に基づいて応答の複数のコピーを保持するようにキャッシュに指示します。したがって、圧縮された (Gzip または Brotli) と圧縮されていないバージョンは、圧縮された応答または圧縮されていない応答。

## <a name="use-the-sample"></a>サンプルを使用する

1. `Accept-Encoding`ヘッダーを使用せずに、 [Fiddler](https://www.telerik.com/fiddler)、[消火バグ](https://getfirebug.com/)、または[Postman](https://www.getpostman.com/)を使用して要求をアプリケーションに作成し、応答ペイロード、応答サイズ、応答ヘッダーをメモします。
1. `Accept-Encoding: br`または`Accept-Encoding: gzip`ヘッダーを追加し、圧縮された応答サイズと応答ヘッダーをメモします。 応答サイズは削除され、 `Content-Encoding`応答ヘッダーは Gzip または Brotli を使用した圧縮が発生したことを示すミドルウェアによって含まれます。 Lorem Ipsum または**testfile1kb**応答の応答本文を確認すると、テキストが圧縮されて読みにくくなっていることがわかります。
1. `Accept-Encoding: mycustomcompression`ヘッダーを追加し、応答ヘッダーをメモします。 は`CustomCompressionProvider` 、実際には応答を圧縮しない空の実装ですが、 `CreateStream()`メソッドのカスタム圧縮ストリームラッパーを作成することができます。
