# <a name="blazor-webassembly-sample-app"></a>Blazor WebAssembly サンプル アプリ

このサンプルは、Blazor のドキュメントで説明されている Blazor シナリオの使用方法を示しています。

## <a name="call-web-api-example"></a>Web API の例を呼び出す

Web API の例では、「<a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">ASP.NET Core で Web API を作成する</a>」 トピックのサンプル アプリに基づく Web API が実行されている必要があります。この Web API では、既定で Blazor サンプル アプリと同じ HTTPS ポート (5001) が使用されています。 同じコンピューターで両方のアプリを同時に使用するには、Web API のポートを変更します (ポート10000 を使用するなど)。 サンプル アプリは `https://localhost:10000/api/TodoItems` にある Web API に対して要求を実行します。 別の Web API アドレスが使用されている場合は、`ServiceEndpoint`Razor コンポーネントのブロックの定数値`@code`を更新します。</p>

このサンプル アプリでは、`http://localhost:5000` または `https://localhost:5001` から Web API への<a href="https://docs.microsoft.com/aspnet/core/security/cors">クロス オリジン リソース共有 (CORS)</a> 要求を実行 します。 資格情報 (承認 cookie/ヘッダー) が許可されています。 次の CORS ミドルウェア構成を Web API の `Startup.Configure` メソッドに追加します。</p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

Blazor アプリに合わせて `WithOrigins` のドメインとポートを必要に応じて調整します。

Web API は CORS で承認 cookie/ヘッダーおよびクライアント コードからの要求が許可されるように構成されていますが、このチュートリアルで作成された Web API は、実際には要求を承認しません。 実装ガイダンスについては、<a href="https://docs.microsoft.com/aspnet/core/security/">ASP.NET Core のセキュリティと ID に関する記事</a>をご覧ください。
