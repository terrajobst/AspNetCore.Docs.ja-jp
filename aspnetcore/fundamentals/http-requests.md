---
title: ASP.NET Core で IHttpClientFactory を使用して HTTP 要求を行う
author: stevejgordon
description: IHttpClientFactory インターフェイスを使用して、ASP.NET Core の論理 HttpClient インスタンスを管理する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
uid: fundamentals/http-requests
ms.openlocfilehash: 482f8e28c23c621cecaf9ce111d89e9166ea6d85
ms.sourcegitcommit: da2fb2d78ce70accdba903ccbfdcfffdd0112123
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2020
ms.locfileid: "75722727"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="c01aa-103">ASP.NET Core で IHttpClientFactory を使用して HTTP 要求を行う</span><span class="sxs-lookup"><span data-stu-id="c01aa-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c01aa-104">寄稿者: [Glenn Condron](https://github.com/glennc)、[Ryan Nowak](https://github.com/rynowak)、[Steve Gordon](https://github.com/stevejgordon)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="c01aa-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="c01aa-105">アプリ内で <xref:System.Net.Http.HttpClient> インスタンスを構成して作成するために、<xref:System.Net.Http.IHttpClientFactory> を登録して使用できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="c01aa-106">`IHttpClientFactory` には次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="c01aa-107">論理 `HttpClient` インスタンスの名前付けと構成を一元化します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="c01aa-108">たとえば、[GitHub](https://github.com/) にアクセスするために、*github* という名前のクライアントを登録して構成できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="c01aa-109">一般的なアクセスのために、既定のクライアントを登録できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="c01aa-110">`HttpClient` でのハンドラーのデリゲートにより、送信ミドルウェアの概念が体系化されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="c01aa-111">`HttpClient` でのハンドラーのデリゲートを利用するために、Polly ベースのミドルウェアに対する拡張機能が提供されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="c01aa-112">基になっている `HttpClientMessageHandler` インスタンスのプールと有効期間が管理されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="c01aa-113">自動管理により、`HttpClient` の有効期間を手動で管理するときの一般的な DNS (ドメイン ネーム システム) の問題が発生しなくなります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="c01aa-114">ファクトリによって作成されたクライアントから送信されるすべての要求に対し、(`ILogger` によって) 構成可能なログ エクスペリエンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="c01aa-115">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="c01aa-115">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="c01aa-116">このトピックのバージョンのサンプル コードでは、HTTP 応答で返された JSON コンテンツを、<xref:System.Text.Json> を使用して逆シリアル化します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="c01aa-117">`Json.NET` および `ReadAsAsync<T>` を使用するサンプルについては、バージョン セレクターを使用して、このトピックの 2.x バージョンを選択してください。</span><span class="sxs-lookup"><span data-stu-id="c01aa-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="c01aa-118">利用パターン</span><span class="sxs-lookup"><span data-stu-id="c01aa-118">Consumption patterns</span></span>

<span data-ttu-id="c01aa-119">アプリで `IHttpClientFactory` を使用するには複数の方法があります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="c01aa-120">基本的な使用方法</span><span class="sxs-lookup"><span data-stu-id="c01aa-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="c01aa-121">名前付きクライアント</span><span class="sxs-lookup"><span data-stu-id="c01aa-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="c01aa-122">型指定されたクライアント</span><span class="sxs-lookup"><span data-stu-id="c01aa-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="c01aa-123">生成されたクライアント</span><span class="sxs-lookup"><span data-stu-id="c01aa-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="c01aa-124">どの方法が最善かは、アプリの要件によって異なります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="c01aa-125">基本的な使用方法</span><span class="sxs-lookup"><span data-stu-id="c01aa-125">Basic usage</span></span>

<span data-ttu-id="c01aa-126">`IHttpClientFactory` は、`AddHttpClient` を呼び出すことによって登録できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="c01aa-127">`IHttpClientFactory` は、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) を使用して要求できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c01aa-128">次のコードでは、`IHttpClientFactory` を使用して `HttpClient` インスタンスを作成しています。</span><span class="sxs-lookup"><span data-stu-id="c01aa-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="c01aa-129">前の例のように `IHttpClientFactory` を使用するのは、既存のアプリをリファクタリングするのに適した方法です。</span><span class="sxs-lookup"><span data-stu-id="c01aa-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="c01aa-130">`HttpClient` の使用方法に対する影響はありません。</span><span class="sxs-lookup"><span data-stu-id="c01aa-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="c01aa-131">既存のアプリで `HttpClient` のインスタンスが作成されている場所を、<xref:System.Net.Http.IHttpClientFactory.CreateClient*> の呼び出しに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="c01aa-132">名前付きクライアント</span><span class="sxs-lookup"><span data-stu-id="c01aa-132">Named clients</span></span>

<span data-ttu-id="c01aa-133">名前付きクライアントは、次の場合に適しています。</span><span class="sxs-lookup"><span data-stu-id="c01aa-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="c01aa-134">アプリで、多くの異なる `HttpClient` を使用する必要がある。</span><span class="sxs-lookup"><span data-stu-id="c01aa-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="c01aa-135">多くの `HttpClient` の構成が異なる。</span><span class="sxs-lookup"><span data-stu-id="c01aa-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="c01aa-136">名前付き `HttpClient` の構成は、`Startup.ConfigureServices` での登録時に指定できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="c01aa-137">上記のコードでは、クライアントに次のものが構成されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="c01aa-138">ベース アドレス `https://api.github.com/`。</span><span class="sxs-lookup"><span data-stu-id="c01aa-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="c01aa-139">GitHub API を使用するために必要な 2 つのヘッダー。</span><span class="sxs-lookup"><span data-stu-id="c01aa-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="c01aa-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="c01aa-140">CreateClient</span></span>

<span data-ttu-id="c01aa-141"><xref:System.Net.Http.IHttpClientFactory.CreateClient*> が呼び出されるたびに、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="c01aa-142">`HttpClient` の新しいインスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="c01aa-143">構成アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-143">The configuration action is called.</span></span>

<span data-ttu-id="c01aa-144">名前付きクライアントを作成するには、その名前を `CreateClient` に渡します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="c01aa-145">上記のコードでは、要求でホスト名を指定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="c01aa-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="c01aa-146">クライアントに構成されているベース アドレスが使用されるため、コードではパスを渡すだけで済みます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="c01aa-147">型指定されたクライアント</span><span class="sxs-lookup"><span data-stu-id="c01aa-147">Typed clients</span></span>

<span data-ttu-id="c01aa-148">型指定されたクライアント:</span><span class="sxs-lookup"><span data-stu-id="c01aa-148">Typed clients:</span></span>

* <span data-ttu-id="c01aa-149">キーとして文字列を使用する必要なしに、名前付きクライアントと同じ機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="c01aa-150">クライアントを使用するときに、IntelliSense とコンパイラのヘルプが提供されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="c01aa-151">特定の `HttpClient` を構成してそれと対話する 1 つの場所を提供します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="c01aa-152">たとえば、単一の型指定されたクライアントは、次のために使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="c01aa-153">単一のバックエンド エンドポイント用。</span><span class="sxs-lookup"><span data-stu-id="c01aa-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="c01aa-154">エンドポイントを処理するすべてのロジックをカプセル化するため。</span><span class="sxs-lookup"><span data-stu-id="c01aa-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="c01aa-155">DI に対応しており、アプリ内の必要な場所に挿入できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="c01aa-156">型指定されたクライアントは、そのコンストラクターで `HttpClient` パラメーターを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="c01aa-157">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-157">In the preceding code:</span></span>

* <span data-ttu-id="c01aa-158">構成が型指定されたクライアントに移動されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="c01aa-159">`HttpClient` オブジェクトは、パブリック プロパティとして公開されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="c01aa-160">`HttpClient` の機能を公開する API 固有のメソッドを作成できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="c01aa-161">たとえば、`GetAspNetDocsIssues` メソッドでは、未解決の問題を取得するためのコードがカプセル化されています。</span><span class="sxs-lookup"><span data-stu-id="c01aa-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="c01aa-162">次のコードでは、`Startup.ConfigureServices` 内で <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> を呼び出して、型指定されたクライアント クラスを登録しています。</span><span class="sxs-lookup"><span data-stu-id="c01aa-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="c01aa-163">型指定されたクライアントは、DI で一時的として登録されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="c01aa-164">型指定されたクライアントは、直接挿入して使用できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-164">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="c01aa-165">型指定されたクライアントのコンストラクターではなく、`Startup.ConfigureServices` での登録時に型指定されたクライアントの構成を指定できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-165">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="c01aa-166">`HttpClient` は、型指定されたクライアント内にカプセル化できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-166">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="c01aa-167">プロパティとして公開するのではなく、`HttpClient` インスタンスを内部的に呼び出すパブリック メソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-167">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="c01aa-168">上記のコードでは、`HttpClient` はプライベート フィールドに格納されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-168">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="c01aa-169">`HttpClient` へのアクセスは、パブリック `GetRepos` メソッドによって行われます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-169">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="c01aa-170">生成されたクライアント</span><span class="sxs-lookup"><span data-stu-id="c01aa-170">Generated clients</span></span>

<span data-ttu-id="c01aa-171">`IHttpClientFactory` は、[Refit](https://github.com/paulcbetts/refit) などのサードパーティ製ライブラリと組み合わせて使用できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-171">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="c01aa-172">Refit は、.NET 用の REST ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="c01aa-172">Refit is a REST library for .NET.</span></span> <span data-ttu-id="c01aa-173">REST API をライブ インターフェイスに変換します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-173">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="c01aa-174">インターフェイスの実装は `RestService` によって動的に生成され、`HttpClient` を使用して外部 HTTP の呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="c01aa-174">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="c01aa-175">インターフェイスと応答は、外部の API とその応答を表すように定義されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-175">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="c01aa-176">型指定されたクライアントを追加し、Refit を使用して実装を生成できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-176">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddControllers();
}
```

<span data-ttu-id="c01aa-177">定義されたインターフェイスは、DI および Refit によって提供される実装で必要に応じて使用できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-177">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="c01aa-178">送信要求ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="c01aa-178">Outgoing request middleware</span></span>

<span data-ttu-id="c01aa-179">`HttpClient` は、送信 HTTP 要求用にリンクできるハンドラーのデリゲートの概念を備えています。</span><span class="sxs-lookup"><span data-stu-id="c01aa-179">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="c01aa-180">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="c01aa-180">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="c01aa-181">各名前付きクライアントに適用するハンドラーの定義が簡単になります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-181">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="c01aa-182">送信要求ミドルウェア パイプラインを構築するための複数のハンドラーの登録とチェーン化がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="c01aa-182">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="c01aa-183">各ハンドラーは、送信要求の前と後に処理を実行できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-183">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="c01aa-184">このパターンは次のようなものです。</span><span class="sxs-lookup"><span data-stu-id="c01aa-184">This pattern:</span></span>

  * <span data-ttu-id="c01aa-185">ASP.NET Core での受信ミドルウェア パイプラインに似ています。</span><span class="sxs-lookup"><span data-stu-id="c01aa-185">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="c01aa-186">次のような HTTP 要求に関する横断的な問題を管理するためのメカニズムが提供されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-186">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="c01aa-187">キャッシュ</span><span class="sxs-lookup"><span data-stu-id="c01aa-187">caching</span></span>
    * <span data-ttu-id="c01aa-188">エラー処理</span><span class="sxs-lookup"><span data-stu-id="c01aa-188">error handling</span></span>
    * <span data-ttu-id="c01aa-189">シリアル化</span><span class="sxs-lookup"><span data-stu-id="c01aa-189">serialization</span></span>
    * <span data-ttu-id="c01aa-190">ログ</span><span class="sxs-lookup"><span data-stu-id="c01aa-190">logging</span></span>

<span data-ttu-id="c01aa-191">デリゲート ハンドラーを作成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="c01aa-191">To create a delegating handler:</span></span>

* <span data-ttu-id="c01aa-192"><xref:System.Net.Http.DelegatingHandler> から派生します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-192">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="c01aa-193"><xref:System.Net.Http.DelegatingHandler.SendAsync*> をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="c01aa-193">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="c01aa-194">パイプライン内の次のハンドラーに要求を渡す前に、コードを実行します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-194">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="c01aa-195">上記のコードでは、`X-API-KEY` ヘッダーが要求に含まれているかどうかが確認されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-195">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="c01aa-196">`X-API-KEY` がない場合は、<xref:System.Net.HttpStatusCode.BadRequest> が返されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-196">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="c01aa-197"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName> を使用して `HttpClient` の構成に複数のハンドラーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-197">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="c01aa-198">上記のコードでは、`ValidateHeaderHandler` が DI で登録されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-198">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="c01aa-199">`IHttpClientFactory` では、ハンドラーごとに個別の DI スコープが作成されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-199">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="c01aa-200">ハンドラーは、任意のスコープのサービスに依存することができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-200">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="c01aa-201">ハンドラーが依存するサービスは、ハンドラーが破棄されるときに破棄されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-201">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="c01aa-202">登録が済むと、<xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> を呼び出してハンドラーの型を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-202">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="c01aa-203">実行する順序で、複数のハンドラーを登録することができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-203">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="c01aa-204">最後の `HttpClientHandler` が要求を実行するまで、各ハンドラーは次のハンドラーをラップします。</span><span class="sxs-lookup"><span data-stu-id="c01aa-204">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="c01aa-205">次の方法のいずれかを使って、要求ごとの状態をメッセージ ハンドラーと共有します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-205">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="c01aa-206">[HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties) を使用して、ハンドラーにデータを渡します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-206">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="c01aa-207"><xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> を使って現在の要求にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="c01aa-207">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="c01aa-208">データを渡すカスタムの <xref:System.Threading.AsyncLocal`1> ストレージ オブジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-208">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="c01aa-209">Polly ベースのハンドラーを使用する</span><span class="sxs-lookup"><span data-stu-id="c01aa-209">Use Polly-based handlers</span></span>

<span data-ttu-id="c01aa-210">`IHttpClientFactory` は、サードパーティのライブラリ [Polly](https://github.com/App-vNext/Polly) と統合します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-210">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="c01aa-211">Polly は、.NET 用の包括的な回復力および一時的エラー処理ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="c01aa-211">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="c01aa-212">開発者は、自然でスレッド セーフな方法を使用して、Retry、Circuit Breaker、Timeout、Bulkhead Isolation、Fallback などのポリシーを表現できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-212">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="c01aa-213">構成されている `HttpClient` インスタンスで Polly ポリシーを使用できるようにするための、拡張メソッドが提供されています。</span><span class="sxs-lookup"><span data-stu-id="c01aa-213">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="c01aa-214">Polly の拡張機能では、クライアントへの Polly ベースのハンドラーの追加がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="c01aa-214">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="c01aa-215">Polly では、[Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="c01aa-215">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="c01aa-216">一時的な障害を処理する</span><span class="sxs-lookup"><span data-stu-id="c01aa-216">Handle transient faults</span></span>

<span data-ttu-id="c01aa-217">通常、外部 HTTP を呼び出すときに発生する障害は、一時的なものです。</span><span class="sxs-lookup"><span data-stu-id="c01aa-217">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="c01aa-218"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> では、一時的なエラーを処理するためのポリシーを定義できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-218"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="c01aa-219">`AddTransientHttpErrorPolicy` で構成されたポリシーでは、次の応答が処理されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-219">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="c01aa-220">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="c01aa-220">HTTP 5xx</span></span>
* <span data-ttu-id="c01aa-221">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="c01aa-221">HTTP 408</span></span>

<span data-ttu-id="c01aa-222">`AddTransientHttpErrorPolicy` では、可能性のある一時的障害を表すエラーを処理するために構成された `PolicyBuilder` オブジェクトへのアクセスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-222">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="c01aa-223">上記のコードでは、`WaitAndRetryAsync` ポリシーが定義されています。</span><span class="sxs-lookup"><span data-stu-id="c01aa-223">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="c01aa-224">失敗した要求は最大 3 回再試行され、再試行の間には 600 ミリ秒の遅延が設けられます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-224">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="c01aa-225">ポリシーを動的に選択する</span><span class="sxs-lookup"><span data-stu-id="c01aa-225">Dynamically select policies</span></span>

<span data-ttu-id="c01aa-226">Polly ベースのハンドラーを追加するための拡張メソッドが提供されています (<xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*> など)。</span><span class="sxs-lookup"><span data-stu-id="c01aa-226">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="c01aa-227">次の `AddPolicyHandler` のオーバーロードでは、要求が検査されて、適用するポリシーが決定されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-227">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="c01aa-228">上記のコードでは、送信要求が HTTP GET の場合は、10 秒のタイムアウトが適用されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-228">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="c01aa-229">他の HTTP メソッドの場合は、30 秒のタイムアウトが使用されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-229">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="c01aa-230">複数の Polly ハンドラーを追加する</span><span class="sxs-lookup"><span data-stu-id="c01aa-230">Add multiple Polly handlers</span></span>

<span data-ttu-id="c01aa-231">Polly のポリシーは入れ子にするのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="c01aa-231">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="c01aa-232">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="c01aa-232">In the preceding example:</span></span>

* <span data-ttu-id="c01aa-233">2 つのハンドラーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-233">Two handlers are added.</span></span>
* <span data-ttu-id="c01aa-234">1 つ目のハンドラーでは、<xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> を使用して再試行ポリシーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-234">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="c01aa-235">失敗した要求は 3 回まで再試行されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-235">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="c01aa-236">2 つ目の `AddTransientHttpErrorPolicy` の呼び出しでは、サーキット ブレーカー ポリシーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-236">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="c01aa-237">試行が連続して 5 回失敗した場合、それ以上の外部要求は 30 秒間ブロックされます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-237">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="c01aa-238">サーキット ブレーカー ポリシーはステートフルです。</span><span class="sxs-lookup"><span data-stu-id="c01aa-238">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="c01aa-239">このクライアントからのすべての呼び出しは、同じサーキット状態を共有します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-239">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="c01aa-240">Polly レジストリからポリシーを追加する</span><span class="sxs-lookup"><span data-stu-id="c01aa-240">Add policies from the Polly registry</span></span>

<span data-ttu-id="c01aa-241">定期的に使用されるポリシーを管理するには、ポリシーを 1 回定義した後、`PolicyRegistry` でポリシーを登録します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-241">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="c01aa-242">次のコードの内容は以下のとおりです。</span><span class="sxs-lookup"><span data-stu-id="c01aa-242">In the following code:</span></span>

* <span data-ttu-id="c01aa-243">"regular" ポリシーと "long" ポリシーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-243">The "regular" and "long" polices are added.</span></span>
* <span data-ttu-id="c01aa-244"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> では、レジストリから "regular" ポリシーと "long" ポリシーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-244"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>  adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="c01aa-245">`IHttpClientFactory` と Polly の統合の詳細については、[Polly に関する wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c01aa-245">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="c01aa-246">HttpClient と有効期間の管理</span><span class="sxs-lookup"><span data-stu-id="c01aa-246">HttpClient and lifetime management</span></span>

<span data-ttu-id="c01aa-247">`IHttpClientFactory` で `CreateClient` を呼び出すたびに、`HttpClient` の新しいインスタンスが返されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-247">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="c01aa-248"><xref:System.Net.Http.HttpMessageHandler> は、名前付きクライアントごとに作成されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-248">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="c01aa-249">ファクトリによって `HttpMessageHandler` インスタンスの有効期間が管理されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-249">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="c01aa-250">`IHttpClientFactory` は、リソースの消費量を減らすため、ファクトリによって作成された `HttpMessageHandler` のインスタンスをプールします。</span><span class="sxs-lookup"><span data-stu-id="c01aa-250">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="c01aa-251">新しい `HttpClient` インスタンスを作成するときに、プールの `HttpMessageHandler` インスタンスの有効期間が切れていない場合はそれを再利用する場合があります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-251">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="c01aa-252">通常、各ハンドラーでは基になる HTTP 接続が独自に管理されるため、ハンドラーはプールすることが望まれます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-252">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="c01aa-253">必要以上のハンドラーを作成すると、接続に遅延が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-253">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="c01aa-254">また、一部のハンドラーでは接続が無期限に開かれており、DNS (ドメイン ネーム システム) の変更にハンドラーが対応できないことがあります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-254">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="c01aa-255">ハンドラーの既定の有効期間は 2 分です。</span><span class="sxs-lookup"><span data-stu-id="c01aa-255">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="c01aa-256">名前付きクライアントごとに、既定値をオーバーライドすることができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-256">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="c01aa-257">通常、`HttpClient` インスタンスは、破棄する必要の**ない** .NET オブジェクトとして扱うことができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-257">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="c01aa-258">破棄すると送信要求がキャンセルされ、<xref:System.IDisposable.Dispose*> の呼び出し後には指定された `HttpClient` インスタンスを使用できなくなります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-258">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="c01aa-259">`IHttpClientFactory` は、`HttpClient` インスタンスによって使用されたリソースの追跡と破棄を行います。</span><span class="sxs-lookup"><span data-stu-id="c01aa-259">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="c01aa-260">`IHttpClientFactory` の登場以前は、1 つの `HttpClient` インスタンスを長い期間使い続けるパターンが一般的に使用されていました。</span><span class="sxs-lookup"><span data-stu-id="c01aa-260">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="c01aa-261">`IHttpClientFactory` に移行した後は、このパターンは不要になります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-261">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="c01aa-262">IHttpClientFactory の代替手段</span><span class="sxs-lookup"><span data-stu-id="c01aa-262">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="c01aa-263">DI 対応のアプリ内で `IHttpClientFactory` を使用すれば、次のことを回避できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-263">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="c01aa-264">`HttpMessageHandler` インスタンスをプールすることによるリソース枯渇の問題。</span><span class="sxs-lookup"><span data-stu-id="c01aa-264">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="c01aa-265">一定の間隔で `HttpMessageHandler` インスタンスを循環させることによって発生する古くなった DNS の問題。</span><span class="sxs-lookup"><span data-stu-id="c01aa-265">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="c01aa-266">有効期間の長い <xref:System.Net.Http.SocketsHttpHandler> インスタンスを使用して、上記の問題を解決する別の方法があります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-266">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="c01aa-267">アプリの起動時に `SocketsHttpHandler` インスタンスを作成し、アプリの有効期間中、それを使用します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-267">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="c01aa-268">DNS の更新時間に基づいて、<xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> を適切な値に構成します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-268">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="c01aa-269">必要に応じて `new HttpClient(handler, disposeHandler: false)` を使用して `HttpClient` インスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-269">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="c01aa-270">上記の方法を使用すると、`IHttpClientFactory` が同様の方法で解決するリソース管理の問題を解決できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-270">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="c01aa-271">`SocketsHttpHandler` を使用すると、`HttpClient` インスタンス間で接続を共有できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-271">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="c01aa-272">この共有によってソケットの枯渇が防止されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-272">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="c01aa-273">`SocketsHttpHandler` では、古くなった DNS の問題を回避するために `PooledConnectionLifetime` に従って接続を循環されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-273">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="c01aa-274">クッキー</span><span class="sxs-lookup"><span data-stu-id="c01aa-274">Cookies</span></span>

<span data-ttu-id="c01aa-275">`HttpMessageHandler` インスタンスをプールすると、`CookieContainer` オブジェクトが共有されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-275">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="c01aa-276">予期せぬ `CookieContainer` オブジェクト共有があると、多くの場合、コードは不適切なものとなります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-276">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="c01aa-277">Cookie を必要とするアプリの場合は、次のいずれかを検討してください。</span><span class="sxs-lookup"><span data-stu-id="c01aa-277">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="c01aa-278">自動的な Cookie 処理の無効化</span><span class="sxs-lookup"><span data-stu-id="c01aa-278">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="c01aa-279">`IHttpClientFactory` の回避</span><span class="sxs-lookup"><span data-stu-id="c01aa-279">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="c01aa-280"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> を呼び出して、自動的な Cookie 処理を無効にします。</span><span class="sxs-lookup"><span data-stu-id="c01aa-280">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="c01aa-281">ログの記録</span><span class="sxs-lookup"><span data-stu-id="c01aa-281">Logging</span></span>

<span data-ttu-id="c01aa-282">`IHttpClientFactory` によって作成されたクライアントは、すべての要求のログ メッセージを記録します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-282">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="c01aa-283">既定のログ メッセージを見るには、ログの構成で適切な情報レベルを有効にします。</span><span class="sxs-lookup"><span data-stu-id="c01aa-283">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="c01aa-284">要求ヘッダーのログなどの追加ログは、トレース レベルでのみ含まれます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-284">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="c01aa-285">各クライアントに使用されるログのカテゴリには、クライアントの名前が含まれます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-285">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="c01aa-286">たとえば、*MyNamedClient* という名前のクライアントでは、"System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler" というカテゴリでメッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-286">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="c01aa-287">*LogicalHandler* というサフィックスが付いたメッセージが、要求ハンドラーのパイプラインの外部で発生します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-287">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="c01aa-288">要求では、パイプライン内の他のハンドラーが要求を処理する前に、メッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-288">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="c01aa-289">応答では、他のパイプライン ハンドラーが応答を受け取った後で、メッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-289">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="c01aa-290">ログ記録は、要求ハンドラーのパイプラインの内部でも行われます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-290">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="c01aa-291">*MyNamedClient* の例では、これらのメッセージはログ カテゴリ "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler" でログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-291">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="c01aa-292">要求では、他のすべてのハンドラーが実行された後、要求が送信される直前に、これが行われます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-292">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="c01aa-293">応答では、このログ記録には、ハンドラー パイプラインを通じ戻される前の応答の状態が含まれます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-293">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="c01aa-294">パイプラインの外部と内部でログを有効にすると、他のパイプライン ハンドラーによる変更を検査できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-294">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="c01aa-295">これには、要求ヘッダーや応答状態コードへの変更を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-295">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="c01aa-296">ログのカテゴリにクライアントの名前を含めると、特定の名前付きクライアントでログをフィルター処理できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-296">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="c01aa-297">HttpMessageHandler を構成する</span><span class="sxs-lookup"><span data-stu-id="c01aa-297">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="c01aa-298">クライアントによって使用される内部 `HttpMessageHandler` の構成を制御することが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-298">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="c01aa-299">名前付きクライアントまたは型指定されたクライアントを追加すると、`IHttpClientBuilder` が返されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-299">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="c01aa-300"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 拡張メソッドを使用して、デリゲートを定義することができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-300">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="c01aa-301">デリゲートは、そのクライアントによって使用されるプライマリ `HttpMessageHandler` の作成と構成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-301">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="c01aa-302">コンソール アプリで IHttpClientFactory を使用する</span><span class="sxs-lookup"><span data-stu-id="c01aa-302">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="c01aa-303">コンソール アプリで、次のパッケージ参照をプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-303">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="c01aa-304">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="c01aa-304">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="c01aa-305">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="c01aa-305">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="c01aa-306">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-306">In the following example:</span></span>

* <span data-ttu-id="c01aa-307"><xref:System.Net.Http.IHttpClientFactory> は[汎用ホスト](xref:fundamentals/host/generic-host)のサービス コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-307"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="c01aa-308">`MyService` により、クライアント ファクトリ インスタンスがサービスから作成され、それが `HttpClient` の作成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-308">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="c01aa-309">`HttpClient` は Web ページを取得する目的で使用されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-309">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="c01aa-310">`Main` により、サービスの `GetPage` メソッドを実行し、Web ページ コンテンツの最初の 500 文字をコンソールに書き込むためのスコープが作成されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-310">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="c01aa-311">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="c01aa-311">Additional resources</span></span>

* [<span data-ttu-id="c01aa-312">HttpClientFactory を使用して回復力の高い HTTP 要求を実装する</span><span class="sxs-lookup"><span data-stu-id="c01aa-312">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="c01aa-313">HttpClientFactory ポリシーと Polly ポリシーで指数バックオフを含む HTTP 呼び出しの再試行を実装する</span><span class="sxs-lookup"><span data-stu-id="c01aa-313">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="c01aa-314">サーキット ブレーカー パターンを実装する</span><span class="sxs-lookup"><span data-stu-id="c01aa-314">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="c01aa-315">.NET で JSON のシリアル化と逆シリアル化を行う方法</span><span class="sxs-lookup"><span data-stu-id="c01aa-315">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="c01aa-316">寄稿者: [Glenn Condron](https://github.com/glennc)、[Ryan Nowak](https://github.com/rynowak)、[Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="c01aa-316">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="c01aa-317">アプリ内で <xref:System.Net.Http.HttpClient> インスタンスを構成して作成するために、<xref:System.Net.Http.IHttpClientFactory> を登録して使用できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-317">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="c01aa-318">次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-318">It offers the following benefits:</span></span>

* <span data-ttu-id="c01aa-319">論理 `HttpClient` インスタンスの名前付けと構成を一元化します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-319">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="c01aa-320">たとえば、*github* クライアントを登録して、[GitHub](https://github.com/) にアクセスするように構成できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-320">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="c01aa-321">既定のクライアントは、他の目的に登録できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-321">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="c01aa-322">`HttpClient` でのハンドラーのデリゲートにより送信ミドルウェアの概念を体系化し、Polly ベースのミドルウェアでそれを利用するための拡張機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-322">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="c01aa-323">基になっている `HttpClientMessageHandler` インスタンスのプールと有効期間を管理し、`HttpClient` の有効期間を手動で管理するときに発生する一般的な DNS の問題を防ぎます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-323">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="c01aa-324">ファクトリによって作成されたクライアントから送信されるすべての要求に対し、(`ILogger` によって) 構成可能なログ エクスペリエンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-324">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="c01aa-325">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="c01aa-325">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="c01aa-326">利用パターン</span><span class="sxs-lookup"><span data-stu-id="c01aa-326">Consumption patterns</span></span>

<span data-ttu-id="c01aa-327">アプリで `IHttpClientFactory` を使用するには複数の方法があります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-327">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="c01aa-328">基本的な使用方法</span><span class="sxs-lookup"><span data-stu-id="c01aa-328">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="c01aa-329">名前付きクライアント</span><span class="sxs-lookup"><span data-stu-id="c01aa-329">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="c01aa-330">型指定されたクライアント</span><span class="sxs-lookup"><span data-stu-id="c01aa-330">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="c01aa-331">生成されたクライアント</span><span class="sxs-lookup"><span data-stu-id="c01aa-331">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="c01aa-332">これらの間に厳密な優劣はありません。</span><span class="sxs-lookup"><span data-stu-id="c01aa-332">None of them are strictly superior to another.</span></span> <span data-ttu-id="c01aa-333">どの方法が最善かは、アプリの制約に依存します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-333">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="c01aa-334">基本的な使用方法</span><span class="sxs-lookup"><span data-stu-id="c01aa-334">Basic usage</span></span>

<span data-ttu-id="c01aa-335">`IHttpClientFactory` は、`Startup.ConfigureServices` メソッドの内部で `IServiceCollection` の `AddHttpClient` 拡張メソッドを呼び出すことによって登録できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-335">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="c01aa-336">登録が済むと、コードは、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) を使用してサービスを挿入できる任意の場所で、`IHttpClientFactory` を受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-336">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c01aa-337">`IHttpClientFactory` を使用して、`HttpClient` インスタンスを作成できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-337">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="c01aa-338">このように `IHttpClientFactory` を使用するのは、既存のアプリをリファクタリングする優れた方法です。</span><span class="sxs-lookup"><span data-stu-id="c01aa-338">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="c01aa-339">`HttpClient` の使用方法に影響はありません。</span><span class="sxs-lookup"><span data-stu-id="c01aa-339">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="c01aa-340">現在 `HttpClient` インスタンスが作成されている場所で、それを <xref:System.Net.Http.IHttpClientFactory.CreateClient*> の呼び出しに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-340">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="c01aa-341">名前付きクライアント</span><span class="sxs-lookup"><span data-stu-id="c01aa-341">Named clients</span></span>

<span data-ttu-id="c01aa-342">それぞれ構成が異なる多数の `HttpClient` をアプリで個別に使用する必要がある場合は、**名前付きクライアント**を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-342">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="c01aa-343">名前付き `HttpClient` の構成は、`Startup.ConfigureServices` での登録時に指定できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-343">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="c01aa-344">上記のコードでは、*github* という名前を指定して `AddHttpClient` を呼び出しています。</span><span class="sxs-lookup"><span data-stu-id="c01aa-344">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="c01aa-345">このクライアントには既定の構成がいくつか適用されています。つまり、GitHub API を使用するために必要なベース アドレスと 2 つのヘッダーです。</span><span class="sxs-lookup"><span data-stu-id="c01aa-345">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="c01aa-346">`CreateClient` を呼び出すたびに、`HttpClient` の新しいインスタンスが作成されて、構成アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-346">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="c01aa-347">名前付きクライアントを使用するには、文字列パラメーターを `CreateClient` に渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-347">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="c01aa-348">作成するクライアントの名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-348">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="c01aa-349">上記のコードでは、要求でホスト名を指定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="c01aa-349">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="c01aa-350">クライアントに構成されているベース アドレスが使用されるため、パスだけを渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-350">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="c01aa-351">型指定されたクライアント</span><span class="sxs-lookup"><span data-stu-id="c01aa-351">Typed clients</span></span>

<span data-ttu-id="c01aa-352">型指定されたクライアント:</span><span class="sxs-lookup"><span data-stu-id="c01aa-352">Typed clients:</span></span>

* <span data-ttu-id="c01aa-353">キーとして文字列を使用する必要なしに、名前付きクライアントと同じ機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-353">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="c01aa-354">クライアントを使用するときに、IntelliSense とコンパイラのヘルプが提供されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-354">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="c01aa-355">特定の `HttpClient` を構成してそれと対話する 1 つの場所を提供します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-355">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="c01aa-356">たとえば、単一の型指定されたクライアントを単一のバックエンド エンドポイントに対して使用し、そのエンドポイントを操作するすべてのロジックをカプセル化できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-356">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="c01aa-357">DI に対応しており、アプリ内の必要な場所に挿入できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-357">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="c01aa-358">型指定されたクライアントは、そのコンストラクターで `HttpClient` パラメーターを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-358">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="c01aa-359">上記のコードでは、構成が型指定されたクライアントに移動されています。</span><span class="sxs-lookup"><span data-stu-id="c01aa-359">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="c01aa-360">`HttpClient` オブジェクトは、パブリック プロパティとして公開されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-360">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="c01aa-361">`HttpClient` 機能を公開する API 固有のメソッドを定義することができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-361">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="c01aa-362">`GetAspNetDocsIssues` メソッドは、GitHub リポジトリで最新の未解決の問題をクエリして解析するために必要なコードをカプセル化します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-362">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="c01aa-363">型指定されたクライアントを登録するには、ジェネリック <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 拡張メソッドを `Startup.ConfigureServices` 内で使用して、型指定されたクライアントのクラスを指定します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-363">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="c01aa-364">型指定されたクライアントは、DI で一時的として登録されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-364">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="c01aa-365">型指定されたクライアントは、直接挿入して使用できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-365">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="c01aa-366">好みに応じて、型指定されたクライアントのコンストラクターではなく、`Startup.ConfigureServices` での登録時に型指定されたクライアントの構成を指定できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-366">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="c01aa-367">型指定されたクライアントの内部に `HttpClient` を完全にカプセル化することができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-367">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="c01aa-368">プロパティとして公開するのではなく、`HttpClient` インスタンスを内部的に呼び出すパブリック メソッドとして提供することができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-368">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="c01aa-369">上記のコードでは、`HttpClient` はプライベート フィールドとして格納されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-369">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="c01aa-370">外部呼び出しを行うすべてのアクセスは、`GetRepos` メソッドを通して行われます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-370">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="c01aa-371">生成されたクライアント</span><span class="sxs-lookup"><span data-stu-id="c01aa-371">Generated clients</span></span>

<span data-ttu-id="c01aa-372">`IHttpClientFactory` は、[Refit](https://github.com/paulcbetts/refit) などの他のサードパーティ製ライブラリと組み合わせて使用できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-372">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="c01aa-373">Refit は、.NET 用の REST ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="c01aa-373">Refit is a REST library for .NET.</span></span> <span data-ttu-id="c01aa-374">REST API をライブ インターフェイスに変換します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-374">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="c01aa-375">インターフェイスの実装は `RestService` によって動的に生成され、`HttpClient` を使用して外部 HTTP の呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="c01aa-375">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="c01aa-376">インターフェイスと応答は、外部の API とその応答を表すように定義されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-376">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="c01aa-377">型指定されたクライアントを追加し、Refit を使用して実装を生成できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-377">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("https://localhost:5001");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="c01aa-378">定義されたインターフェイスは、DI および Refit によって提供される実装で必要に応じて使用できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-378">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="c01aa-379">送信要求ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="c01aa-379">Outgoing request middleware</span></span>

<span data-ttu-id="c01aa-380">`HttpClient` は既に、送信 HTTP 要求用にリンクできるハンドラーのデリゲートの概念を備えています。</span><span class="sxs-lookup"><span data-stu-id="c01aa-380">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="c01aa-381">`IHttpClientFactory` を使用すると、各名前付きクライアントに適用するハンドラーを簡単に定義できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-381">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="c01aa-382">複数のハンドラーを登録してチェーン化し、送信要求ミドルウェア パイプラインを構築できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-382">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="c01aa-383">各ハンドラーは、送信要求の前と後に処理を実行できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-383">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="c01aa-384">このパターンは、ASP.NET Core での受信ミドルウェア パイプラインに似ています。</span><span class="sxs-lookup"><span data-stu-id="c01aa-384">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="c01aa-385">このパターンは、キャッシュ、エラー処理、シリアル化、ログ記録など、HTTP 要求に関する横断的関心事を管理するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-385">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="c01aa-386">ハンドラーを作成するには、<xref:System.Net.Http.DelegatingHandler> の派生クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-386">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="c01aa-387">パイプライン内の次のハンドラーに要求を渡す前にコードを実行するように、`SendAsync` メソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="c01aa-387">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="c01aa-388">上記のコードでは、基本的なハンドラーが定義されています。</span><span class="sxs-lookup"><span data-stu-id="c01aa-388">The preceding code defines a basic handler.</span></span> <span data-ttu-id="c01aa-389">このコードは、`X-API-KEY` ヘッダーが要求に含まれていたかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-389">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="c01aa-390">ヘッダーがない場合、HTTP 呼び出しを行わずに適切な応答を返すことができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-390">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="c01aa-391">登録時には、1 つまたは複数のハンドラーを `HttpClient` の構成に追加することができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-391">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="c01aa-392">このタスクは、<xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> の拡張メソッドを使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-392">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="c01aa-393">上記のコードでは、`ValidateHeaderHandler` が DI で登録されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-393">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="c01aa-394">`IHttpClientFactory` では、ハンドラーごとに個別の DI スコープが作成されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-394">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="c01aa-395">ハンドラーは、任意のスコープのサービスに自由に依存することができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-395">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="c01aa-396">ハンドラーが依存するサービスは、ハンドラーが破棄されるときに破棄されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-396">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="c01aa-397">登録が済むと、<xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> を呼び出してハンドラーの型を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-397">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="c01aa-398">実行する順序で、複数のハンドラーを登録することができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-398">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="c01aa-399">最後の `HttpClientHandler` が要求を実行するまで、各ハンドラーは次のハンドラーをラップします。</span><span class="sxs-lookup"><span data-stu-id="c01aa-399">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="c01aa-400">次の方法のいずれかを使って、要求ごとの状態をメッセージ ハンドラーと共有します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-400">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="c01aa-401">`HttpRequestMessage.Properties` を使ってデータをハンドラーに渡します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-401">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="c01aa-402">`IHttpContextAccessor` を使って現在の要求にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="c01aa-402">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="c01aa-403">データを渡すカスタムの `AsyncLocal` ストレージ オブジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-403">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="c01aa-404">Polly ベースのハンドラーを使用する</span><span class="sxs-lookup"><span data-stu-id="c01aa-404">Use Polly-based handlers</span></span>

<span data-ttu-id="c01aa-405">`IHttpClientFactory` は、[Polly](https://github.com/App-vNext/Polly) という名前の人気のあるサードパーティ製ライブラリと統合します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-405">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="c01aa-406">Polly は、.NET 用の包括的な回復力および一時的エラー処理ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="c01aa-406">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="c01aa-407">開発者は、自然でスレッド セーフな方法を使用して、Retry、Circuit Breaker、Timeout、Bulkhead Isolation、Fallback などのポリシーを表現できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-407">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="c01aa-408">構成されている `HttpClient` インスタンスで Polly ポリシーを使用できるようにするための、拡張メソッドが提供されています。</span><span class="sxs-lookup"><span data-stu-id="c01aa-408">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="c01aa-409">Polly の拡張機能は、次のようになっています。</span><span class="sxs-lookup"><span data-stu-id="c01aa-409">The Polly extensions:</span></span>

* <span data-ttu-id="c01aa-410">クライアントへの Polly ベースのハンドラーの追加がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-410">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="c01aa-411">[Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet パッケージのインストール後に使用できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-411">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="c01aa-412">このパッケージは、ASP.NET Core 共有フレームワークに含まれていません。</span><span class="sxs-lookup"><span data-stu-id="c01aa-412">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="c01aa-413">一時的な障害を処理する</span><span class="sxs-lookup"><span data-stu-id="c01aa-413">Handle transient faults</span></span>

<span data-ttu-id="c01aa-414">外部 HTTP を呼び出す際に発生する一般的な障害のほとんどは、一時的なものです。</span><span class="sxs-lookup"><span data-stu-id="c01aa-414">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="c01aa-415">`AddTransientHttpErrorPolicy` という便利な拡張メソッドが含まれており、一時的なエラーを処理するためのポリシーを定義できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-415">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="c01aa-416">この拡張メソッドで構成されたポリシーは、`HttpRequestException`、HTTP 5xx 応答、および HTTP 408 応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-416">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="c01aa-417">`AddTransientHttpErrorPolicy` 拡張メソッドは、`Startup.ConfigureServices` 内で使用できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-417">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c01aa-418">この拡張メソッドは、可能性のある一時的障害を表すエラーを処理するように構成された `PolicyBuilder` オブジェクトへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-418">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="c01aa-419">上記のコードでは、`WaitAndRetryAsync` ポリシーが定義されています。</span><span class="sxs-lookup"><span data-stu-id="c01aa-419">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="c01aa-420">失敗した要求は最大 3 回再試行され、再試行の間には 600 ミリ秒の遅延が設けられます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-420">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="c01aa-421">ポリシーを動的に選択する</span><span class="sxs-lookup"><span data-stu-id="c01aa-421">Dynamically select policies</span></span>

<span data-ttu-id="c01aa-422">Polly ベースのハンドラーを追加するために使用できる追加の拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-422">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="c01aa-423">そのような拡張メソッドの 1 つは `AddPolicyHandler` であり、複数のオーバーロードを持ちます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-423">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="c01aa-424">1 つのオーバーロードでは、適用するポリシーを定義するときに要求を検査できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-424">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="c01aa-425">上記のコードでは、送信要求が HTTP GET の場合は、10 秒のタイムアウトが適用されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-425">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="c01aa-426">他の HTTP メソッドの場合は、30 秒のタイムアウトが使用されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-426">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="c01aa-427">複数の Polly ハンドラーを追加する</span><span class="sxs-lookup"><span data-stu-id="c01aa-427">Add multiple Polly handlers</span></span>

<span data-ttu-id="c01aa-428">Polly ポリシーを入れ子にして拡張機能を提供するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="c01aa-428">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="c01aa-429">前の例では、2 つのハンドラーが追加されています。</span><span class="sxs-lookup"><span data-stu-id="c01aa-429">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="c01aa-430">最初のハンドラーは、`AddTransientHttpErrorPolicy` 拡張メソッドを使用して再試行ポリシーを追加します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-430">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="c01aa-431">失敗した要求は 3 回まで再試行されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-431">Failed requests are retried up to three times.</span></span> <span data-ttu-id="c01aa-432">`AddTransientHttpErrorPolicy` の 2 番目の呼び出しでは、サーキット ブレーカー ポリシーが追加されています。</span><span class="sxs-lookup"><span data-stu-id="c01aa-432">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="c01aa-433">試行が連続して 5 回失敗した場合、それ以上の外部要求は 30 秒間ブロックされます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-433">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="c01aa-434">サーキット ブレーカー ポリシーはステートフルです。</span><span class="sxs-lookup"><span data-stu-id="c01aa-434">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="c01aa-435">このクライアントからのすべての呼び出しは、同じサーキット状態を共有します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-435">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="c01aa-436">Polly レジストリからポリシーを追加する</span><span class="sxs-lookup"><span data-stu-id="c01aa-436">Add policies from the Polly registry</span></span>

<span data-ttu-id="c01aa-437">定期的に使用されるポリシーを管理するには、ポリシーを 1 回定義した後、`PolicyRegistry` でポリシーを登録します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-437">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="c01aa-438">提供されている拡張メソッドを使用することで、レジストリからポリシーを使用してハンドラーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-438">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="c01aa-439">上記のコードでは、`PolicyRegistry` が `ServiceCollection` に追加されるときに 2 つのポリシーが登録されています。</span><span class="sxs-lookup"><span data-stu-id="c01aa-439">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="c01aa-440">レジストリからポリシーを使用するには、適用するポリシーの名前を渡して `AddPolicyHandlerFromRegistry` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-440">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="c01aa-441">`IHttpClientFactory` および Polly の統合について詳しくは、[Polly の Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="c01aa-441">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="c01aa-442">HttpClient と有効期間の管理</span><span class="sxs-lookup"><span data-stu-id="c01aa-442">HttpClient and lifetime management</span></span>

<span data-ttu-id="c01aa-443">`IHttpClientFactory` で `CreateClient` を呼び出すたびに、`HttpClient` の新しいインスタンスが返されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-443">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="c01aa-444">名前付きクライアントごとに <xref:System.Net.Http.HttpMessageHandler> が存在します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-444">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="c01aa-445">ファクトリによって `HttpMessageHandler` インスタンスの有効期間が管理されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-445">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="c01aa-446">`IHttpClientFactory` は、リソースの消費量を減らすため、ファクトリによって作成された `HttpMessageHandler` のインスタンスをプールします。</span><span class="sxs-lookup"><span data-stu-id="c01aa-446">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="c01aa-447">新しい `HttpClient` インスタンスを作成するときに、プールの `HttpMessageHandler` インスタンスの有効期間が切れていない場合はそれを再利用する場合があります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-447">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="c01aa-448">通常、各ハンドラーでは基になる HTTP 接続が独自に管理されるため、ハンドラーはプールすることが望まれます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-448">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="c01aa-449">必要以上のハンドラーを作成すると、接続に遅延が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-449">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="c01aa-450">また、一部のハンドラーは接続を無期限に開いており、DNS の変更にハンドラーが対応できないことがあります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-450">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="c01aa-451">ハンドラーの既定の有効期間は 2 分です。</span><span class="sxs-lookup"><span data-stu-id="c01aa-451">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="c01aa-452">名前付きクライアントごとに、既定値をオーバーライドすることができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-452">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="c01aa-453">オーバーライドするには、クライアント作成時に返された `IHttpClientBuilder` で <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-453">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="c01aa-454">クライアントを破棄する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="c01aa-454">Disposal of the client isn't required.</span></span> <span data-ttu-id="c01aa-455">破棄すると送信要求がキャンセルされ、<xref:System.IDisposable.Dispose*> の呼び出し後には指定された `HttpClient` インスタンスを使用できなくなります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-455">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="c01aa-456">`IHttpClientFactory` は、`HttpClient` インスタンスによって使用されたリソースの追跡と破棄を行います。</span><span class="sxs-lookup"><span data-stu-id="c01aa-456">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="c01aa-457">通常、`HttpClient` インスタンスは、破棄を必要としない .NET オブジェクトとして扱うことができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-457">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="c01aa-458">`IHttpClientFactory` の登場以前は、1 つの `HttpClient` インスタンスを長い期間使い続けるパターンが一般的に使用されていました。</span><span class="sxs-lookup"><span data-stu-id="c01aa-458">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="c01aa-459">`IHttpClientFactory` に移行した後は、このパターンは不要になります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-459">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="c01aa-460">IHttpClientFactory の代替手段</span><span class="sxs-lookup"><span data-stu-id="c01aa-460">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="c01aa-461">DI 対応のアプリ内で `IHttpClientFactory` を使用すれば、次のことを回避できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-461">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="c01aa-462">`HttpMessageHandler` インスタンスをプールすることによるリソース枯渇の問題。</span><span class="sxs-lookup"><span data-stu-id="c01aa-462">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="c01aa-463">一定の間隔で `HttpMessageHandler` インスタンスを循環させることによって発生する古くなった DNS の問題。</span><span class="sxs-lookup"><span data-stu-id="c01aa-463">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="c01aa-464">有効期間の長い <xref:System.Net.Http.SocketsHttpHandler> インスタンスを使用して、上記の問題を解決する別の方法があります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-464">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="c01aa-465">アプリの起動時に `SocketsHttpHandler` インスタンスを作成し、アプリの有効期間中、それを使用します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-465">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="c01aa-466">DNS の更新時間に基づいて、<xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> を適切な値に構成します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-466">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="c01aa-467">必要に応じて `new HttpClient(handler, disposeHandler: false)` を使用して `HttpClient` インスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-467">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="c01aa-468">上記の方法を使用すると、`IHttpClientFactory` が同様の方法で解決するリソース管理の問題を解決できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-468">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="c01aa-469">`SocketsHttpHandler` を使用すると、`HttpClient` インスタンス間で接続を共有できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-469">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="c01aa-470">この共有によってソケットの枯渇が防止されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-470">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="c01aa-471">`SocketsHttpHandler` では、古くなった DNS の問題を回避するために `PooledConnectionLifetime` に従って接続を循環されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-471">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="c01aa-472">クッキー</span><span class="sxs-lookup"><span data-stu-id="c01aa-472">Cookies</span></span>

<span data-ttu-id="c01aa-473">`HttpMessageHandler` インスタンスをプールすると、`CookieContainer` オブジェクトが共有されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-473">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="c01aa-474">予期せぬ `CookieContainer` オブジェクト共有があると、多くの場合、コードは不適切なものとなります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-474">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="c01aa-475">Cookie を必要とするアプリの場合は、次のいずれかを検討してください。</span><span class="sxs-lookup"><span data-stu-id="c01aa-475">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="c01aa-476">自動的な Cookie 処理の無効化</span><span class="sxs-lookup"><span data-stu-id="c01aa-476">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="c01aa-477">`IHttpClientFactory` の回避</span><span class="sxs-lookup"><span data-stu-id="c01aa-477">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="c01aa-478"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> を呼び出して、自動的な Cookie 処理を無効にします。</span><span class="sxs-lookup"><span data-stu-id="c01aa-478">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="c01aa-479">ログの記録</span><span class="sxs-lookup"><span data-stu-id="c01aa-479">Logging</span></span>

<span data-ttu-id="c01aa-480">`IHttpClientFactory` によって作成されたクライアントは、すべての要求のログ メッセージを記録します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-480">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="c01aa-481">既定のログ メッセージを見るには、ログの構成で適切な情報レベルを有効にします。</span><span class="sxs-lookup"><span data-stu-id="c01aa-481">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="c01aa-482">要求ヘッダーのログなどの追加ログは、トレース レベルでのみ含まれます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-482">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="c01aa-483">各クライアントに使用されるログのカテゴリには、クライアントの名前が含まれます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-483">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="c01aa-484">たとえば、*MyNamedClient* という名前のクライアントは、カテゴリ `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` でメッセージをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-484">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="c01aa-485">*LogicalHandler* というサフィックスが付いたメッセージが、要求ハンドラーのパイプラインの外部で発生します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-485">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="c01aa-486">要求では、パイプライン内の他のハンドラーが要求を処理する前に、メッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-486">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="c01aa-487">応答では、他のパイプライン ハンドラーが応答を受け取った後で、メッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-487">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="c01aa-488">ログ記録は、要求ハンドラーのパイプラインの内部でも行われます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-488">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="c01aa-489">*MyNamedClient* の例では、それらのメッセージはログ カテゴリ `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` に対して記録されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-489">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="c01aa-490">要求では、他のすべてのハンドラーが実行した後、要求がネットワークに送信される直前に、これが行われます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-490">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="c01aa-491">応答では、このログ記録には、ハンドラー パイプラインを通じ戻される前の応答の状態が含まれます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-491">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="c01aa-492">パイプラインの外部と内部でログを有効にすると、他のパイプライン ハンドラーによる変更を検査できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-492">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="c01aa-493">これには、たとえば、要求ヘッダーや応答状態コードへの変更を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-493">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="c01aa-494">ログのカテゴリにクライアントの名前を含めると、必要に応じて、特定の名前付きクライアントでログをフィルター処理できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-494">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="c01aa-495">HttpMessageHandler を構成する</span><span class="sxs-lookup"><span data-stu-id="c01aa-495">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="c01aa-496">クライアントによって使用される内部 `HttpMessageHandler` の構成を制御することが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-496">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="c01aa-497">名前付きクライアントまたは型指定されたクライアントを追加すると、`IHttpClientBuilder` が返されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-497">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="c01aa-498"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 拡張メソッドを使用して、デリゲートを定義することができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-498">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="c01aa-499">デリゲートは、そのクライアントによって使用されるプライマリ `HttpMessageHandler` の作成と構成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-499">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="c01aa-500">コンソール アプリで IHttpClientFactory を使用する</span><span class="sxs-lookup"><span data-stu-id="c01aa-500">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="c01aa-501">コンソール アプリで、次のパッケージ参照をプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-501">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="c01aa-502">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="c01aa-502">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="c01aa-503">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="c01aa-503">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="c01aa-504">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-504">In the following example:</span></span>

* <span data-ttu-id="c01aa-505"><xref:System.Net.Http.IHttpClientFactory> は[汎用ホスト](xref:fundamentals/host/generic-host)のサービス コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-505"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="c01aa-506">`MyService` により、クライアント ファクトリ インスタンスがサービスから作成され、それが `HttpClient` の作成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-506">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="c01aa-507">`HttpClient` は Web ページを取得する目的で使用されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-507">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="c01aa-508">`Main` により、サービスの `GetPage` メソッドを実行し、Web ページ コンテンツの最初の 500 文字をコンソールに書き込むためのスコープが作成されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-508">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="c01aa-509">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="c01aa-509">Additional resources</span></span>

* [<span data-ttu-id="c01aa-510">HttpClientFactory を使用して回復力の高い HTTP 要求を実装する</span><span class="sxs-lookup"><span data-stu-id="c01aa-510">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="c01aa-511">HttpClientFactory ポリシーと Polly ポリシーで指数バックオフを含む HTTP 呼び出しの再試行を実装する</span><span class="sxs-lookup"><span data-stu-id="c01aa-511">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="c01aa-512">サーキット ブレーカー パターンを実装する</span><span class="sxs-lookup"><span data-stu-id="c01aa-512">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="c01aa-513">寄稿者: [Glenn Condron](https://github.com/glennc)、[Ryan Nowak](https://github.com/rynowak)、[Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="c01aa-513">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="c01aa-514">アプリ内で <xref:System.Net.Http.HttpClient> インスタンスを構成して作成するために、<xref:System.Net.Http.IHttpClientFactory> を登録して使用できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-514">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="c01aa-515">次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-515">It offers the following benefits:</span></span>

* <span data-ttu-id="c01aa-516">論理 `HttpClient` インスタンスの名前付けと構成を一元化します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-516">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="c01aa-517">たとえば、*github* クライアントを登録して、[GitHub](https://github.com/) にアクセスするように構成できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-517">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="c01aa-518">既定のクライアントは、他の目的に登録できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-518">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="c01aa-519">`HttpClient` でのハンドラーのデリゲートにより送信ミドルウェアの概念を体系化し、Polly ベースのミドルウェアでそれを利用するための拡張機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-519">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="c01aa-520">基になっている `HttpClientMessageHandler` インスタンスのプールと有効期間を管理し、`HttpClient` の有効期間を手動で管理するときに発生する一般的な DNS の問題を防ぎます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-520">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="c01aa-521">ファクトリによって作成されたクライアントから送信されるすべての要求に対し、(`ILogger` によって) 構成可能なログ エクスペリエンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-521">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="c01aa-522">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="c01aa-522">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c01aa-523">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="c01aa-523">Prerequisites</span></span>

<span data-ttu-id="c01aa-524">.NET Framework をターゲットとするプロジェクトでは、[Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet パッケージのインストールが必要です。</span><span class="sxs-lookup"><span data-stu-id="c01aa-524">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="c01aa-525">.NET Core をターゲットとし、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を参照するプロジェクトには、既に `Microsoft.Extensions.Http` パッケージが含まれています。</span><span class="sxs-lookup"><span data-stu-id="c01aa-525">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="c01aa-526">利用パターン</span><span class="sxs-lookup"><span data-stu-id="c01aa-526">Consumption patterns</span></span>

<span data-ttu-id="c01aa-527">アプリで `IHttpClientFactory` を使用するには複数の方法があります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-527">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="c01aa-528">基本的な使用方法</span><span class="sxs-lookup"><span data-stu-id="c01aa-528">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="c01aa-529">名前付きクライアント</span><span class="sxs-lookup"><span data-stu-id="c01aa-529">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="c01aa-530">型指定されたクライアント</span><span class="sxs-lookup"><span data-stu-id="c01aa-530">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="c01aa-531">生成されたクライアント</span><span class="sxs-lookup"><span data-stu-id="c01aa-531">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="c01aa-532">これらの間に厳密な優劣はありません。</span><span class="sxs-lookup"><span data-stu-id="c01aa-532">None of them are strictly superior to another.</span></span> <span data-ttu-id="c01aa-533">どの方法が最善かは、アプリの制約に依存します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-533">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="c01aa-534">基本的な使用方法</span><span class="sxs-lookup"><span data-stu-id="c01aa-534">Basic usage</span></span>

<span data-ttu-id="c01aa-535">`IHttpClientFactory` は、`Startup.ConfigureServices` メソッドの内部で `IServiceCollection` の `AddHttpClient` 拡張メソッドを呼び出すことによって登録できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-535">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="c01aa-536">登録が済むと、コードは、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) を使用してサービスを挿入できる任意の場所で、`IHttpClientFactory` を受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-536">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c01aa-537">`IHttpClientFactory` を使用して、`HttpClient` インスタンスを作成できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-537">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="c01aa-538">このように `IHttpClientFactory` を使用するのは、既存のアプリをリファクタリングする優れた方法です。</span><span class="sxs-lookup"><span data-stu-id="c01aa-538">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="c01aa-539">`HttpClient` の使用方法に影響はありません。</span><span class="sxs-lookup"><span data-stu-id="c01aa-539">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="c01aa-540">現在 `HttpClient` インスタンスが作成されている場所で、それを <xref:System.Net.Http.IHttpClientFactory.CreateClient*> の呼び出しに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-540">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="c01aa-541">名前付きクライアント</span><span class="sxs-lookup"><span data-stu-id="c01aa-541">Named clients</span></span>

<span data-ttu-id="c01aa-542">それぞれ構成が異なる多数の `HttpClient` をアプリで個別に使用する必要がある場合は、**名前付きクライアント**を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-542">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="c01aa-543">名前付き `HttpClient` の構成は、`Startup.ConfigureServices` での登録時に指定できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-543">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="c01aa-544">上記のコードでは、*github* という名前を指定して `AddHttpClient` を呼び出しています。</span><span class="sxs-lookup"><span data-stu-id="c01aa-544">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="c01aa-545">このクライアントには既定の構成がいくつか適用されています。つまり、GitHub API を使用するために必要なベース アドレスと 2 つのヘッダーです。</span><span class="sxs-lookup"><span data-stu-id="c01aa-545">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="c01aa-546">`CreateClient` を呼び出すたびに、`HttpClient` の新しいインスタンスが作成されて、構成アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-546">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="c01aa-547">名前付きクライアントを使用するには、文字列パラメーターを `CreateClient` に渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-547">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="c01aa-548">作成するクライアントの名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-548">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="c01aa-549">上記のコードでは、要求でホスト名を指定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="c01aa-549">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="c01aa-550">クライアントに構成されているベース アドレスが使用されるため、パスだけを渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-550">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="c01aa-551">型指定されたクライアント</span><span class="sxs-lookup"><span data-stu-id="c01aa-551">Typed clients</span></span>

<span data-ttu-id="c01aa-552">型指定されたクライアント:</span><span class="sxs-lookup"><span data-stu-id="c01aa-552">Typed clients:</span></span>

* <span data-ttu-id="c01aa-553">キーとして文字列を使用する必要なしに、名前付きクライアントと同じ機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-553">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="c01aa-554">クライアントを使用するときに、IntelliSense とコンパイラのヘルプが提供されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-554">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="c01aa-555">特定の `HttpClient` を構成してそれと対話する 1 つの場所を提供します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-555">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="c01aa-556">たとえば、単一の型指定されたクライアントを単一のバックエンド エンドポイントに対して使用し、そのエンドポイントを操作するすべてのロジックをカプセル化できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-556">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="c01aa-557">DI に対応しており、アプリ内の必要な場所に挿入できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-557">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="c01aa-558">型指定されたクライアントは、そのコンストラクターで `HttpClient` パラメーターを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-558">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="c01aa-559">上記のコードでは、構成が型指定されたクライアントに移動されています。</span><span class="sxs-lookup"><span data-stu-id="c01aa-559">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="c01aa-560">`HttpClient` オブジェクトは、パブリック プロパティとして公開されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-560">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="c01aa-561">`HttpClient` 機能を公開する API 固有のメソッドを定義することができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-561">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="c01aa-562">`GetAspNetDocsIssues` メソッドは、GitHub リポジトリで最新の未解決の問題をクエリして解析するために必要なコードをカプセル化します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-562">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="c01aa-563">型指定されたクライアントを登録するには、ジェネリック <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 拡張メソッドを `Startup.ConfigureServices` 内で使用して、型指定されたクライアントのクラスを指定します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-563">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="c01aa-564">型指定されたクライアントは、DI で一時的として登録されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-564">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="c01aa-565">型指定されたクライアントは、直接挿入して使用できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-565">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="c01aa-566">好みに応じて、型指定されたクライアントのコンストラクターではなく、`Startup.ConfigureServices` での登録時に型指定されたクライアントの構成を指定できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-566">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="c01aa-567">型指定されたクライアントの内部に `HttpClient` を完全にカプセル化することができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-567">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="c01aa-568">プロパティとして公開するのではなく、`HttpClient` インスタンスを内部的に呼び出すパブリック メソッドとして提供することができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-568">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="c01aa-569">上記のコードでは、`HttpClient` はプライベート フィールドとして格納されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-569">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="c01aa-570">外部呼び出しを行うすべてのアクセスは、`GetRepos` メソッドを通して行われます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-570">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="c01aa-571">生成されたクライアント</span><span class="sxs-lookup"><span data-stu-id="c01aa-571">Generated clients</span></span>

<span data-ttu-id="c01aa-572">`IHttpClientFactory` は、[Refit](https://github.com/paulcbetts/refit) などの他のサードパーティ製ライブラリと組み合わせて使用できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-572">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="c01aa-573">Refit は、.NET 用の REST ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="c01aa-573">Refit is a REST library for .NET.</span></span> <span data-ttu-id="c01aa-574">REST API をライブ インターフェイスに変換します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-574">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="c01aa-575">インターフェイスの実装は `RestService` によって動的に生成され、`HttpClient` を使用して外部 HTTP の呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="c01aa-575">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="c01aa-576">インターフェイスと応答は、外部の API とその応答を表すように定義されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-576">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="c01aa-577">型指定されたクライアントを追加し、Refit を使用して実装を生成できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-577">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="c01aa-578">定義されたインターフェイスは、DI および Refit によって提供される実装で必要に応じて使用できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-578">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="c01aa-579">送信要求ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="c01aa-579">Outgoing request middleware</span></span>

<span data-ttu-id="c01aa-580">`HttpClient` は既に、送信 HTTP 要求用にリンクできるハンドラーのデリゲートの概念を備えています。</span><span class="sxs-lookup"><span data-stu-id="c01aa-580">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="c01aa-581">`IHttpClientFactory` を使用すると、各名前付きクライアントに適用するハンドラーを簡単に定義できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-581">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="c01aa-582">複数のハンドラーを登録してチェーン化し、送信要求ミドルウェア パイプラインを構築できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-582">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="c01aa-583">各ハンドラーは、送信要求の前と後に処理を実行できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-583">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="c01aa-584">このパターンは、ASP.NET Core での受信ミドルウェア パイプラインに似ています。</span><span class="sxs-lookup"><span data-stu-id="c01aa-584">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="c01aa-585">このパターンは、キャッシュ、エラー処理、シリアル化、ログ記録など、HTTP 要求に関する横断的関心事を管理するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-585">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="c01aa-586">ハンドラーを作成するには、<xref:System.Net.Http.DelegatingHandler> の派生クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-586">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="c01aa-587">パイプライン内の次のハンドラーに要求を渡す前にコードを実行するように、`SendAsync` メソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="c01aa-587">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="c01aa-588">上記のコードでは、基本的なハンドラーが定義されています。</span><span class="sxs-lookup"><span data-stu-id="c01aa-588">The preceding code defines a basic handler.</span></span> <span data-ttu-id="c01aa-589">このコードは、`X-API-KEY` ヘッダーが要求に含まれていたかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-589">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="c01aa-590">ヘッダーがない場合、HTTP 呼び出しを行わずに適切な応答を返すことができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-590">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="c01aa-591">登録時には、1 つまたは複数のハンドラーを `HttpClient` の構成に追加することができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-591">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="c01aa-592">このタスクは、<xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> の拡張メソッドを使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-592">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="c01aa-593">上記のコードでは、`ValidateHeaderHandler` が DI で登録されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-593">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="c01aa-594">ハンドラーは、スコープではなく、一時的なサービスとして DI で登録する**必要があります**。</span><span class="sxs-lookup"><span data-stu-id="c01aa-594">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="c01aa-595">ハンドラーがスコープ付きサービスとして登録されており、ハンドラーが依存するサービスを破棄できる場合:</span><span class="sxs-lookup"><span data-stu-id="c01aa-595">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="c01aa-596">ハンドラーのサービスは、ハンドラーがスコープ外に出る前に破棄されることがあります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-596">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="c01aa-597">破棄されたハンドラー サービスが原因でハンドラーが失敗します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-597">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="c01aa-598">登録が済むと、<xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> を呼び出してハンドラーの型を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-598">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="c01aa-599">実行する順序で、複数のハンドラーを登録することができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-599">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="c01aa-600">最後の `HttpClientHandler` が要求を実行するまで、各ハンドラーは次のハンドラーをラップします。</span><span class="sxs-lookup"><span data-stu-id="c01aa-600">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="c01aa-601">次の方法のいずれかを使って、要求ごとの状態をメッセージ ハンドラーと共有します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-601">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="c01aa-602">`HttpRequestMessage.Properties` を使ってデータをハンドラーに渡します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-602">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="c01aa-603">`IHttpContextAccessor` を使って現在の要求にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="c01aa-603">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="c01aa-604">データを渡すカスタムの `AsyncLocal` ストレージ オブジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-604">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="c01aa-605">Polly ベースのハンドラーを使用する</span><span class="sxs-lookup"><span data-stu-id="c01aa-605">Use Polly-based handlers</span></span>

<span data-ttu-id="c01aa-606">`IHttpClientFactory` は、[Polly](https://github.com/App-vNext/Polly) という名前の人気のあるサードパーティ製ライブラリと統合します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-606">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="c01aa-607">Polly は、.NET 用の包括的な回復力および一時的エラー処理ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="c01aa-607">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="c01aa-608">開発者は、自然でスレッド セーフな方法を使用して、Retry、Circuit Breaker、Timeout、Bulkhead Isolation、Fallback などのポリシーを表現できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-608">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="c01aa-609">構成されている `HttpClient` インスタンスで Polly ポリシーを使用できるようにするための、拡張メソッドが提供されています。</span><span class="sxs-lookup"><span data-stu-id="c01aa-609">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="c01aa-610">Polly の拡張機能は、次のようになっています。</span><span class="sxs-lookup"><span data-stu-id="c01aa-610">The Polly extensions:</span></span>

* <span data-ttu-id="c01aa-611">クライアントへの Polly ベースのハンドラーの追加がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-611">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="c01aa-612">[Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet パッケージのインストール後に使用できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-612">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="c01aa-613">このパッケージは、ASP.NET Core 共有フレームワークに含まれていません。</span><span class="sxs-lookup"><span data-stu-id="c01aa-613">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="c01aa-614">一時的な障害を処理する</span><span class="sxs-lookup"><span data-stu-id="c01aa-614">Handle transient faults</span></span>

<span data-ttu-id="c01aa-615">外部 HTTP を呼び出す際に発生する一般的な障害のほとんどは、一時的なものです。</span><span class="sxs-lookup"><span data-stu-id="c01aa-615">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="c01aa-616">`AddTransientHttpErrorPolicy` という便利な拡張メソッドが含まれており、一時的なエラーを処理するためのポリシーを定義できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-616">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="c01aa-617">この拡張メソッドで構成されたポリシーは、`HttpRequestException`、HTTP 5xx 応答、および HTTP 408 応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-617">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="c01aa-618">`AddTransientHttpErrorPolicy` 拡張メソッドは、`Startup.ConfigureServices` 内で使用できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-618">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c01aa-619">この拡張メソッドは、可能性のある一時的障害を表すエラーを処理するように構成された `PolicyBuilder` オブジェクトへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-619">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="c01aa-620">上記のコードでは、`WaitAndRetryAsync` ポリシーが定義されています。</span><span class="sxs-lookup"><span data-stu-id="c01aa-620">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="c01aa-621">失敗した要求は最大 3 回再試行され、再試行の間には 600 ミリ秒の遅延が設けられます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-621">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="c01aa-622">ポリシーを動的に選択する</span><span class="sxs-lookup"><span data-stu-id="c01aa-622">Dynamically select policies</span></span>

<span data-ttu-id="c01aa-623">Polly ベースのハンドラーを追加するために使用できる追加の拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-623">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="c01aa-624">そのような拡張メソッドの 1 つは `AddPolicyHandler` であり、複数のオーバーロードを持ちます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-624">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="c01aa-625">1 つのオーバーロードでは、適用するポリシーを定義するときに要求を検査できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-625">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="c01aa-626">上記のコードでは、送信要求が HTTP GET の場合は、10 秒のタイムアウトが適用されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-626">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="c01aa-627">他の HTTP メソッドの場合は、30 秒のタイムアウトが使用されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-627">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="c01aa-628">複数の Polly ハンドラーを追加する</span><span class="sxs-lookup"><span data-stu-id="c01aa-628">Add multiple Polly handlers</span></span>

<span data-ttu-id="c01aa-629">Polly ポリシーを入れ子にして拡張機能を提供するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="c01aa-629">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="c01aa-630">前の例では、2 つのハンドラーが追加されています。</span><span class="sxs-lookup"><span data-stu-id="c01aa-630">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="c01aa-631">最初のハンドラーは、`AddTransientHttpErrorPolicy` 拡張メソッドを使用して再試行ポリシーを追加します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-631">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="c01aa-632">失敗した要求は 3 回まで再試行されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-632">Failed requests are retried up to three times.</span></span> <span data-ttu-id="c01aa-633">`AddTransientHttpErrorPolicy` の 2 番目の呼び出しでは、サーキット ブレーカー ポリシーが追加されています。</span><span class="sxs-lookup"><span data-stu-id="c01aa-633">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="c01aa-634">試行が連続して 5 回失敗した場合、それ以上の外部要求は 30 秒間ブロックされます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-634">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="c01aa-635">サーキット ブレーカー ポリシーはステートフルです。</span><span class="sxs-lookup"><span data-stu-id="c01aa-635">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="c01aa-636">このクライアントからのすべての呼び出しは、同じサーキット状態を共有します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-636">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="c01aa-637">Polly レジストリからポリシーを追加する</span><span class="sxs-lookup"><span data-stu-id="c01aa-637">Add policies from the Polly registry</span></span>

<span data-ttu-id="c01aa-638">定期的に使用されるポリシーを管理するには、ポリシーを 1 回定義した後、`PolicyRegistry` でポリシーを登録します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-638">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="c01aa-639">提供されている拡張メソッドを使用することで、レジストリからポリシーを使用してハンドラーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-639">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="c01aa-640">上記のコードでは、`PolicyRegistry` が `ServiceCollection` に追加されるときに 2 つのポリシーが登録されています。</span><span class="sxs-lookup"><span data-stu-id="c01aa-640">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="c01aa-641">レジストリからポリシーを使用するには、適用するポリシーの名前を渡して `AddPolicyHandlerFromRegistry` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-641">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="c01aa-642">`IHttpClientFactory` および Polly の統合について詳しくは、[Polly の Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="c01aa-642">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="c01aa-643">HttpClient と有効期間の管理</span><span class="sxs-lookup"><span data-stu-id="c01aa-643">HttpClient and lifetime management</span></span>

<span data-ttu-id="c01aa-644">`IHttpClientFactory` で `CreateClient` を呼び出すたびに、`HttpClient` の新しいインスタンスが返されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-644">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="c01aa-645">名前付きクライアントごとに <xref:System.Net.Http.HttpMessageHandler> が存在します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-645">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="c01aa-646">ファクトリによって `HttpMessageHandler` インスタンスの有効期間が管理されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-646">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="c01aa-647">`IHttpClientFactory` は、リソースの消費量を減らすため、ファクトリによって作成された `HttpMessageHandler` のインスタンスをプールします。</span><span class="sxs-lookup"><span data-stu-id="c01aa-647">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="c01aa-648">新しい `HttpClient` インスタンスを作成するときに、プールの `HttpMessageHandler` インスタンスの有効期間が切れていない場合はそれを再利用する場合があります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-648">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="c01aa-649">通常、各ハンドラーでは基になる HTTP 接続が独自に管理されるため、ハンドラーはプールすることが望まれます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-649">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="c01aa-650">必要以上のハンドラーを作成すると、接続に遅延が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-650">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="c01aa-651">また、一部のハンドラーは接続を無期限に開いており、DNS の変更にハンドラーが対応できないことがあります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-651">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="c01aa-652">ハンドラーの既定の有効期間は 2 分です。</span><span class="sxs-lookup"><span data-stu-id="c01aa-652">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="c01aa-653">名前付きクライアントごとに、既定値をオーバーライドすることができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-653">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="c01aa-654">オーバーライドするには、クライアント作成時に返された `IHttpClientBuilder` で <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-654">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="c01aa-655">クライアントを破棄する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="c01aa-655">Disposal of the client isn't required.</span></span> <span data-ttu-id="c01aa-656">破棄すると送信要求がキャンセルされ、<xref:System.IDisposable.Dispose*> の呼び出し後には指定された `HttpClient` インスタンスを使用できなくなります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-656">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="c01aa-657">`IHttpClientFactory` は、`HttpClient` インスタンスによって使用されたリソースの追跡と破棄を行います。</span><span class="sxs-lookup"><span data-stu-id="c01aa-657">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="c01aa-658">通常、`HttpClient` インスタンスは、破棄を必要としない .NET オブジェクトとして扱うことができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-658">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="c01aa-659">`IHttpClientFactory` の登場以前は、1 つの `HttpClient` インスタンスを長い期間使い続けるパターンが一般的に使用されていました。</span><span class="sxs-lookup"><span data-stu-id="c01aa-659">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="c01aa-660">`IHttpClientFactory` に移行した後は、このパターンは不要になります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-660">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="c01aa-661">IHttpClientFactory の代替手段</span><span class="sxs-lookup"><span data-stu-id="c01aa-661">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="c01aa-662">DI 対応のアプリ内で `IHttpClientFactory` を使用すれば、次のことを回避できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-662">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="c01aa-663">`HttpMessageHandler` インスタンスをプールすることによるリソース枯渇の問題。</span><span class="sxs-lookup"><span data-stu-id="c01aa-663">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="c01aa-664">一定の間隔で `HttpMessageHandler` インスタンスを循環させることによって発生する古くなった DNS の問題。</span><span class="sxs-lookup"><span data-stu-id="c01aa-664">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="c01aa-665">有効期間の長い <xref:System.Net.Http.SocketsHttpHandler> インスタンスを使用して、上記の問題を解決する別の方法があります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-665">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="c01aa-666">アプリの起動時に `SocketsHttpHandler` インスタンスを作成し、アプリの有効期間中、それを使用します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-666">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="c01aa-667">DNS の更新時間に基づいて、<xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> を適切な値に構成します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-667">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="c01aa-668">必要に応じて `new HttpClient(handler, disposeHandler: false)` を使用して `HttpClient` インスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-668">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="c01aa-669">上記の方法を使用すると、`IHttpClientFactory` が同様の方法で解決するリソース管理の問題を解決できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-669">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="c01aa-670">`SocketsHttpHandler` を使用すると、`HttpClient` インスタンス間で接続を共有できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-670">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="c01aa-671">この共有によってソケットの枯渇が防止されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-671">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="c01aa-672">`SocketsHttpHandler` では、古くなった DNS の問題を回避するために `PooledConnectionLifetime` に従って接続を循環されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-672">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="c01aa-673">クッキー</span><span class="sxs-lookup"><span data-stu-id="c01aa-673">Cookies</span></span>

<span data-ttu-id="c01aa-674">`HttpMessageHandler` インスタンスをプールすると、`CookieContainer` オブジェクトが共有されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-674">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="c01aa-675">予期せぬ `CookieContainer` オブジェクト共有があると、多くの場合、コードは不適切なものとなります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-675">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="c01aa-676">Cookie を必要とするアプリの場合は、次のいずれかを検討してください。</span><span class="sxs-lookup"><span data-stu-id="c01aa-676">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="c01aa-677">自動的な Cookie 処理の無効化</span><span class="sxs-lookup"><span data-stu-id="c01aa-677">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="c01aa-678">`IHttpClientFactory` の回避</span><span class="sxs-lookup"><span data-stu-id="c01aa-678">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="c01aa-679"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> を呼び出して、自動的な Cookie 処理を無効にします。</span><span class="sxs-lookup"><span data-stu-id="c01aa-679">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="c01aa-680">ログの記録</span><span class="sxs-lookup"><span data-stu-id="c01aa-680">Logging</span></span>

<span data-ttu-id="c01aa-681">`IHttpClientFactory` によって作成されたクライアントは、すべての要求のログ メッセージを記録します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-681">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="c01aa-682">既定のログ メッセージを見るには、ログの構成で適切な情報レベルを有効にします。</span><span class="sxs-lookup"><span data-stu-id="c01aa-682">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="c01aa-683">要求ヘッダーのログなどの追加ログは、トレース レベルでのみ含まれます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-683">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="c01aa-684">各クライアントに使用されるログのカテゴリには、クライアントの名前が含まれます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-684">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="c01aa-685">たとえば、*MyNamedClient* という名前のクライアントは、カテゴリ `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` でメッセージをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-685">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="c01aa-686">*LogicalHandler* というサフィックスが付いたメッセージが、要求ハンドラーのパイプラインの外部で発生します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-686">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="c01aa-687">要求では、パイプライン内の他のハンドラーが要求を処理する前に、メッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-687">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="c01aa-688">応答では、他のパイプライン ハンドラーが応答を受け取った後で、メッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-688">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="c01aa-689">ログ記録は、要求ハンドラーのパイプラインの内部でも行われます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-689">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="c01aa-690">*MyNamedClient* の例では、それらのメッセージはログ カテゴリ `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` に対して記録されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-690">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="c01aa-691">要求では、他のすべてのハンドラーが実行した後、要求がネットワークに送信される直前に、これが行われます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-691">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="c01aa-692">応答では、このログ記録には、ハンドラー パイプラインを通じ戻される前の応答の状態が含まれます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-692">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="c01aa-693">パイプラインの外部と内部でログを有効にすると、他のパイプライン ハンドラーによる変更を検査できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-693">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="c01aa-694">これには、たとえば、要求ヘッダーや応答状態コードへの変更を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-694">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="c01aa-695">ログのカテゴリにクライアントの名前を含めると、必要に応じて、特定の名前付きクライアントでログをフィルター処理できます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-695">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="c01aa-696">HttpMessageHandler を構成する</span><span class="sxs-lookup"><span data-stu-id="c01aa-696">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="c01aa-697">クライアントによって使用される内部 `HttpMessageHandler` の構成を制御することが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="c01aa-697">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="c01aa-698">名前付きクライアントまたは型指定されたクライアントを追加すると、`IHttpClientBuilder` が返されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-698">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="c01aa-699"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 拡張メソッドを使用して、デリゲートを定義することができます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-699">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="c01aa-700">デリゲートは、そのクライアントによって使用されるプライマリ `HttpMessageHandler` の作成と構成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-700">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="c01aa-701">コンソール アプリで IHttpClientFactory を使用する</span><span class="sxs-lookup"><span data-stu-id="c01aa-701">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="c01aa-702">コンソール アプリで、次のパッケージ参照をプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-702">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="c01aa-703">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="c01aa-703">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="c01aa-704">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="c01aa-704">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="c01aa-705">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="c01aa-705">In the following example:</span></span>

* <span data-ttu-id="c01aa-706"><xref:System.Net.Http.IHttpClientFactory> は[汎用ホスト](xref:fundamentals/host/generic-host)のサービス コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-706"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="c01aa-707">`MyService` により、クライアント ファクトリ インスタンスがサービスから作成され、それが `HttpClient` の作成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-707">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="c01aa-708">`HttpClient` は Web ページを取得する目的で使用されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-708">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="c01aa-709">`Main` により、サービスの `GetPage` メソッドを実行し、Web ページ コンテンツの最初の 500 文字をコンソールに書き込むためのスコープが作成されます。</span><span class="sxs-lookup"><span data-stu-id="c01aa-709">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="c01aa-710">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="c01aa-710">Additional resources</span></span>

* [<span data-ttu-id="c01aa-711">HttpClientFactory を使用して回復力の高い HTTP 要求を実装する</span><span class="sxs-lookup"><span data-stu-id="c01aa-711">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="c01aa-712">HttpClientFactory ポリシーと Polly ポリシーで指数バックオフを含む HTTP 呼び出しの再試行を実装する</span><span class="sxs-lookup"><span data-stu-id="c01aa-712">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="c01aa-713">サーキット ブレーカー パターンを実装する</span><span class="sxs-lookup"><span data-stu-id="c01aa-713">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
