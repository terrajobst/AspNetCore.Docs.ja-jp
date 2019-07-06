# <a name="aspnet-core-response-caching-sample"></a>ASP.NET Core の応答キャッシュのサンプル

このサンプルは、ASP.NET Core の使用方法を示します[応答キャッシュ ミドルウェア](https://docs.microsoft.com/aspnet/core/performance/caching/middleware)します。

そのインデックス ページのアプリの応答を含む、`Cache-Control`ヘッダー キャッシュの動作を構成します。 また、アプリ、設定、`Vary`場合にのみ応答を処理するためにキャッシュを構成するヘッダー、`Accept-Encoding`を指定する元の要求からの後続の要求のヘッダーと一致します。

サンプルを実行するときに、インデックス ページは保存され、最大で 10 秒間キャッシュにキャッシュから提供されます。

キャッシュ動作をテストするには。

* キャッシュ動作をテストするのにブラウザーを使用しないでください。 ブラウザーは、多くの場合、ミドルウェアがキャッシュされたページを提供していることを妨げている再読み込みのキャッシュ制御ヘッダーを追加します。 たとえば、`Cache-Control`ヘッダーの値を`max-age=0`)、ブラウザーで追加する場合があります。
* 要求ヘッダーを明示的に設定できるようにする開発者ツールを使用してなど<a href="https://www.telerik.com/fiddler">Fiddler</a>または<a href="https://www.getpostman.com/">Postman</a>します。
