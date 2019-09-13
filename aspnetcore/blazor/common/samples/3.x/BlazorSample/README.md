# <a name="blazor-webassembly-sample-app"></a>Blazor WebAssembly サンプルアプリ

このサンプルでは、Blazor のドキュメントで説明されている Blazor シナリオの使用方法を示します。

## <a name="call-web-api-example"></a>呼び出し web API の例

この web api の例では、 <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">チュートリアル用のサンプルアプリに基づく実行中の web api が必要です。ASP.NET Core MVC</a>のトピックを使用して web API を作成します。 サンプルアプリでは、web API への要求`https://localhost:10000/api/todo`をで行います。 別の web API アドレスが使用されている`ServiceEndpoint`場合は、Razor コンポーネントの`@functions`ブロックの定数値を更新します。</p>

このサンプルアプリで`http://localhost:5000`は`https://localhost:5001` 、web API との間で<a href="https://docs.microsoft.com/aspnet/core/security/cors">クロスオリジンリソース共有 (CORS)</a>要求を行います。 資格情報 (承認 cookie/ヘッダー) が許可されます。 を呼び出す`Startup.Configure` `UseMvc`前に、次の CORS ミドルウェア構成を web API のメソッドに追加します。</p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

Blazor アプリの必要に応じ`WithOrigins`て、のドメインとポートを調整します。

Web API は CORS 用に構成されており、承認 cookie/ヘッダーおよびクライアントコードからの要求を許可していますが、このチュートリアルで作成された web API は、実際には要求を承認しません。 実装ガイダンスについては、 <a href="https://docs.microsoft.com/aspnet/core/security/">ASP.NET Core のセキュリティと id</a>に関する記事をご覧ください。
