# <a name="blazor-webassembly-sample-app"></a><span data-ttu-id="feb72-101">Blazor WebAssembly サンプルアプリ</span><span class="sxs-lookup"><span data-stu-id="feb72-101">Blazor WebAssembly Sample App</span></span>

<span data-ttu-id="feb72-102">このサンプルでは、Blazor のドキュメントで説明されている Blazor シナリオの使用方法を示します。</span><span class="sxs-lookup"><span data-stu-id="feb72-102">This sample illustrates the use of Blazor scenarios described in the Blazor documentation.</span></span>

## <a name="call-web-api-example"></a><span data-ttu-id="feb72-103">呼び出し web API の例</span><span class="sxs-lookup"><span data-stu-id="feb72-103">Call web API example</span></span>

<span data-ttu-id="feb72-104">この web api の例では、 <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">チュートリアル用のサンプルアプリに基づく実行中の web api が必要です。ASP.NET Core MVC</a>のトピックを使用して web API を作成します。</span><span class="sxs-lookup"><span data-stu-id="feb72-104">The web API example requires a running web API based on the sample app for the <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">Tutorial: Create a web API with ASP.NET Core MVC</a> topic.</span></span> <span data-ttu-id="feb72-105">サンプルアプリでは、web API への要求`https://localhost:10000/api/todo`をで行います。</span><span class="sxs-lookup"><span data-stu-id="feb72-105">The sample app makes requests to the web API at `https://localhost:10000/api/todo`.</span></span> <span data-ttu-id="feb72-106">別の web API アドレスが使用されている`ServiceEndpoint`場合は、Razor コンポーネントの`@functions`ブロックの定数値を更新します。</span><span class="sxs-lookup"><span data-stu-id="feb72-106">If a different web API address is used, update the `ServiceEndpoint` constant value in the Razor component's `@functions` block.</span></span></p>

<span data-ttu-id="feb72-107">このサンプルアプリで`http://localhost:5000`は`https://localhost:5001` 、web API との間で<a href="https://docs.microsoft.com/aspnet/core/security/cors">クロスオリジンリソース共有 (CORS)</a>要求を行います。</span><span class="sxs-lookup"><span data-stu-id="feb72-107">The sample app makes a <a href="https://docs.microsoft.com/aspnet/core/security/cors">cross-origin resource sharing (CORS)</a> request from `http://localhost:5000` or `https://localhost:5001` to the web API.</span></span> <span data-ttu-id="feb72-108">資格情報 (承認 cookie/ヘッダー) が許可されます。</span><span class="sxs-lookup"><span data-stu-id="feb72-108">Credentials (authorization cookies/headers) are permitted.</span></span> <span data-ttu-id="feb72-109">を呼び出す`Startup.Configure` `UseMvc`前に、次の CORS ミドルウェア構成を web API のメソッドに追加します。</span><span class="sxs-lookup"><span data-stu-id="feb72-109">Add the following CORS middleware configuration to the web API's `Startup.Configure` method before it calls `UseMvc`:</span></span></p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

<span data-ttu-id="feb72-110">Blazor アプリの必要に応じ`WithOrigins`て、のドメインとポートを調整します。</span><span class="sxs-lookup"><span data-stu-id="feb72-110">Adjust the domains and ports of `WithOrigins` as needed for the Blazor app.</span></span>

<span data-ttu-id="feb72-111">Web API は CORS 用に構成されており、承認 cookie/ヘッダーおよびクライアントコードからの要求を許可していますが、このチュートリアルで作成された web API は、実際には要求を承認しません。</span><span class="sxs-lookup"><span data-stu-id="feb72-111">The web API is configured for CORS to permit authorization cookies/headers and requests from client code, but the web API as created by the tutorial doesn't actually authorize requests.</span></span> <span data-ttu-id="feb72-112">実装ガイダンスについては、 <a href="https://docs.microsoft.com/aspnet/core/security/">ASP.NET Core のセキュリティと id</a>に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="feb72-112">See the <a href="https://docs.microsoft.com/aspnet/core/security/">ASP.NET Core Security and Identity articles</a> for implementation guidance.</span></span>
