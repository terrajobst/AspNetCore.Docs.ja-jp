# <a name="blazor-webassembly-sample-app"></a>Blazor WebAssembly サンプルアプリ

このサンプルでは、Blazor のドキュメントで説明されている Blazor シナリオの使用方法を示します。

## <a name="call-web-api-example"></a>呼び出し web API の例

この web api の例では、ASP.NET Core トピックを使用した<a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">WEB api の作成</a>に関するトピックのサンプルアプリに基づく実行中の web api が必要です。このトピックでは、Blazor サンプルアプリと同じ HTTPS ポート (5001) を既定で使用します。 同じコンピューターで両方のアプリを同時に使用するには、web API のポートを変更します (たとえば、ポート1万を使用します)。 サンプルアプリは `https://localhost:10000/api/TodoItems` で web API に要求を行います。 別の web API アドレスが使用されている場合は、Razor コンポーネントの `@code` ブロックの `ServiceEndpoint` 定数値を更新します。</p>

このサンプルアプリでは、`http://localhost:5000` または `https://localhost:5001` から web API への<a href="https://docs.microsoft.com/aspnet/core/security/cors">クロスオリジンリソース共有 (CORS)</a>要求を行います。 資格情報 (承認 cookie/ヘッダー) が許可されます。 次の CORS ミドルウェア構成を web API の `Startup.Configure` メソッドに追加します。</p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

Blazor アプリに必要な `WithOrigins` のドメインとポートを調整します。

Web API は CORS 用に構成されており、承認 cookie/ヘッダーおよびクライアントコードからの要求を許可していますが、このチュートリアルで作成された web API は、実際には要求を承認しません。 実装ガイダンスについては、 <a href="https://docs.microsoft.com/aspnet/core/security/">ASP.NET Core のセキュリティと id</a>に関する記事をご覧ください。
