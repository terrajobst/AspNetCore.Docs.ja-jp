# <a name="aspnet-core-url-rewriting-sample"></a>ASP.NET Core の URL リライト サンプル

このサンプルでは、ASP.NET Core URL リライト ミドルウェアの使用法を示します。 このアプリでは、URL リダイレクトと URL リライト オプションを示します。

サンプルを実行すると、いずれかのルールが要求 URL に適用されるときに書き換えられる URL またはリダイレクトされる URL が、非ファイル応答で返されます。 XML とテキスト ファイルの例では、ミドルウェアによって要求の URL が書き換えられた後、静的ファイル ミドルウェアによってファイルが提供されます。

## <a name="examples-in-this-sample"></a>このサンプルの例

* `AddRedirect("redirect-rule/(.*)", "redirected/$1")`
  - 成功状態コード: 302 (検出)
  - 例 (リダイレクト): **/redirect-rule/{capture_group}** から **/redirected/{capture_group}** へ
* `AddRewrite(@"^rewrite-rule/(\d+)/(\d+)", "rewritten?var1=$1&var2=$2", skipRemainingRules: true)`
  - 成功状態コード: 200 (OK)
  - 例 (リライト): **/rewrite-rule/{capture_group_1}/{capture_group_2}** から **/rewritten?var1={capture_group_1}&var2={capture_group_2}** へ
* `AddApacheModRewrite(env.ContentRootFileProvider, "ApacheModRewrite.txt")`
  - 成功状態コード: 302 (検出)
  - 例 (リダイレクト): **/apache-mod-rules-redirect/{capture_group}** から **/redirected?id={capture_group}** へ
* `AddIISUrlRewrite(env.ContentRootFileProvider, "IISUrlRewrite.xml")`
  - 成功状態コード: 200 (OK)
  - 例 (リライト): **/iis-rules-rewrite/{capture_group}** から **/rewritten?id={capture_group}** へ
* `Add(RedirectXmlFileRequests)`
  - 成功状態コード: 301 (永続的に移動)
  - 例 (リダイレクト): **/file.xml** から **/xmlfiles/file.xml** へ
* `Add(RewriteTextFileRequests)`
  - 成功状態コード: 200 (OK)
  - 例 (書き換え): **/some_file.txt** から **/file.txt** へ
* `Add(new RedirectImageRequests(".png", "/png-images")))`<br>`Add(new RedirectImageRequests(".jpg", "/jpg-images")))`
  - 成功状態コード: 301 (永続的に移動)
  - 例 (リダイレクト): **/image.png** から **/png-images/image.png** へ
  - 例 (リダイレクト): **/image.jpg** から **/jpg-images/image.jpg** へ

## <a name="use-a-physicalfileprovider"></a>PhysicalFileProvider を使用する

`AddApacheModRewrite()` メソッドと `AddIISUrlRewrite()` メソッドに渡す `PhysicalFileProvider` を作成して、`IFileProvider` を取得することもできます。

```csharp
using Microsoft.Extensions.FileProviders;
PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
```

## <a name="secure-redirection-extensions"></a>セキュリティで保護されたリダイレクトの拡張機能

このサンプルには、URL (`https://localhost:5001`、`https://localhost`) とテスト証明書 (*testCert.pfx*) を使用してこれらのセキュリティ保護されたリダイレクト方法を調べるための `WebHostBuilder` 構成が含まれています。 サーバーでポート 443 が既に割り当て済みまたは使用中の場合、`https://localhost` の例は機能しません。*Program.cs* ファイルの `CreateWebHostBuilder` メソッドでポート 443 に対する `ListenOptions` を削除するか、またはサーバーでポート 443 をバインド解除して、Kestrel がポートを使用できるようにしてください。

| メソッド                           | 状態コード |    ポート    |
| -------------------------------- | :---------: | :--------: |
| `.AddRedirectToHttpsPermanent()` |     301     | null (465) |
| `.AddRedirectToHttps()`          |     302     | null (465) |
| `.AddRedirectToHttps(301)`       |     301     | null (465) |
| `.AddRedirectToHttps(301, 5001)` |     301     |    5001    |
