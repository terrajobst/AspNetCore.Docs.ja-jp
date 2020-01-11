---
title: ASP.NET Core Blazor から web API を呼び出す
author: guardrex
description: クロスオリジンリソース共有 (CORS) 要求の作成など、JSON ヘルパーを使用して Blazor アプリから web API を呼び出す方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
uid: blazor/call-web-api
ms.openlocfilehash: f1929b48275a36552f061a64823267df0f3acabc
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2019
ms.locfileid: "74943915"
---
# <a name="call-a-web-api-from-aspnet-core-opno-locblazor"></a><span data-ttu-id="fc3f1-103">ASP.NET Core Blazor から web API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="fc3f1-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="fc3f1-104">[Luke Latham](https://github.com/guardrex)、 [Daniel Roth](https://github.com/danroth27)、[フアン De la クルス](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="fc3f1-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="fc3f1-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly)は、構成済みの `HttpClient` サービスを使用して web api を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="fc3f1-106">要求を作成します。これには、Blazor JSON ヘルパーまたは <xref:System.Net.Http.HttpRequestMessage>を使用した JavaScript [FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)オプションを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span>

<span data-ttu-id="fc3f1-107">[Blazor サーバー](xref:blazor/hosting-models#blazor-server)アプリは、通常 <xref:System.Net.Http.IHttpClientFactory>を使用して作成された <xref:System.Net.Http.HttpClient> インスタンスを使用して web api を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-107">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="fc3f1-108">詳細については、「<xref:fundamentals/http-requests>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-108">For more information, see <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="fc3f1-109">*BlazorWebAssemblySample*アプリを選択 &ndash; には、サンプルコード ([ダウンロード方法](xref:index#how-to-download-a-sample)) を[表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="fc3f1-110">*BlazorWebAssemblySample*サンプルアプリの次のコンポーネントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-110">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="fc3f1-111">Web API の呼び出し (*Pages/CallWebAPI*)</span><span class="sxs-lookup"><span data-stu-id="fc3f1-111">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="fc3f1-112">HTTP 要求テスター (*Components/HTTPRequestTester*)</span><span class="sxs-lookup"><span data-stu-id="fc3f1-112">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="fc3f1-113">パッケージ</span><span class="sxs-lookup"><span data-stu-id="fc3f1-113">Packages</span></span>

<span data-ttu-id="fc3f1-114">AspNetCore*を参照*します。 [プロジェクトファイル内の BlazorHttpClient](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.HttpClient/) NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-114">Reference the *experimental* [Microsoft.AspNetCore.Blazor.HttpClient](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.HttpClient/) NuGet package in the project file.</span></span> <span data-ttu-id="fc3f1-115">`Microsoft.AspNetCore.Blazor.HttpClient` は `HttpClient` と system.string に基づいて[います。](https://www.nuget.org/packages/System.Text.Json/)</span><span class="sxs-lookup"><span data-stu-id="fc3f1-115">`Microsoft.AspNetCore.Blazor.HttpClient` is based on `HttpClient` and [System.Text.Json](https://www.nuget.org/packages/System.Text.Json/).</span></span>

<span data-ttu-id="fc3f1-116">安定した API を使用するには、 [WebApi](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/)パッケージを使用します。このパッケージは[Json.NET/](https://www.newtonsoft.com/json/help/html/Introduction.htm)[を使用し](https://www.nuget.org/packages/Newtonsoft.Json/)ます。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-116">To use a stable API, use the [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) package, which uses [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)/[Json.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span> <span data-ttu-id="fc3f1-117">`Microsoft.AspNet.WebApi.Client` で安定した API を使用しても、このトピックで説明する JSON ヘルパーは提供されません。これは、実験的な `Microsoft.AspNetCore.Blazor.HttpClient` パッケージに固有のものです。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-117">Using the stable API in `Microsoft.AspNet.WebApi.Client` doesn't provide the JSON helpers described in this topic, which are unique to the experimental `Microsoft.AspNetCore.Blazor.HttpClient` package.</span></span>

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="fc3f1-118">HttpClient と JSON のヘルパー</span><span class="sxs-lookup"><span data-stu-id="fc3f1-118">HttpClient and JSON helpers</span></span>

<span data-ttu-id="fc3f1-119">Blazor WebAssembly では、 [Httpclient](xref:fundamentals/http-requests)は、要求を配信元サーバーに返すための事前に構成されたサービスとして利用できます。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-119">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="fc3f1-120">Blazor サーバーアプリには、既定では `HttpClient` サービスが含まれていません。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-120">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="fc3f1-121">[Httpclient ファクトリインフラストラクチャ](xref:fundamentals/http-requests)を使用して、アプリに `HttpClient` を提供します。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-121">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="fc3f1-122">`HttpClient` と JSON ヘルパーは、サードパーティの web API エンドポイントを呼び出すためにも使用されます。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-122">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="fc3f1-123">`HttpClient` は、ブラウザーの[FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)を使用して実装され、同じオリジンポリシーの適用などの制限が適用されます。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-123">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="fc3f1-124">クライアントのベースアドレスは、元のサーバーのアドレスに設定されます。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-124">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="fc3f1-125">`@inject` ディレクティブを使用して `HttpClient` インスタンスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-125">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="fc3f1-126">次の例では、Todo web API が作成、読み取り、更新、および削除 (CRUD) の各操作を処理します。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-126">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="fc3f1-127">この例は、を格納する `TodoItem` クラスに基づいています。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-127">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="fc3f1-128">ID (`Id`、`long`) &ndash; アイテムの一意の ID。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-128">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="fc3f1-129">項目の名前 (`Name`、`string`) &ndash; 名前。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-129">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="fc3f1-130">[状態] (`IsComplete`、`bool`) &ndash; Todo 項目が終了したかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-130">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="fc3f1-131">JSON ヘルパーメソッドは、要求を URI (次の例では web API) に送信し、応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-131">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="fc3f1-132">`GetJsonAsync` &ndash; は HTTP GET 要求を送信し、JSON 応答本文を解析してオブジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-132">`GetJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="fc3f1-133">次のコードでは、`_todoItems` がコンポーネントによって表示されます。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-133">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="fc3f1-134">`GetTodoItems` メソッドは、コンポーネントのレンダリングが終了したときにトリガーされます ([Oninitializer Edasync](xref:blazor/lifecycle#component-initialization-methods))。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-134">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="fc3f1-135">完全な例については、サンプルアプリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-135">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="fc3f1-136">`PostJsonAsync` &ndash; は、JSON でエンコードされたコンテンツを含む HTTP POST 要求を送信し、JSON 応答本文を解析してオブジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-136">`PostJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="fc3f1-137">次のコードでは、コンポーネントのバインドされた要素によって `_newItemName` が提供されています。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-137">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="fc3f1-138">`AddItem` メソッドは、`<button>` 要素を選択することによってトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-138">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="fc3f1-139">完全な例については、サンプルアプリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-139">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="_newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string _newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = _newItemName, IsComplete = false };
          await Http.PostJsonAsync("api/TodoItems", addItem);
      }
  }
  ```

* <span data-ttu-id="fc3f1-140">`PutJsonAsync` &ndash; は、JSON でエンコードされたコンテンツを含む HTTP PUT 要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-140">`PutJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="fc3f1-141">次のコードでは、`Name` と `IsCompleted` の `_editItem` 値が、コンポーネントのバインドされた要素によって提供されています。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-141">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="fc3f1-142">項目の `Id` は、UI の別の部分で項目が選択され、`EditItem` が呼び出されたときに設定されます。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-142">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="fc3f1-143">`SaveItem` メソッドは、Save `<button>` 要素を選択することによってトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-143">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="fc3f1-144">完全な例については、サンプルアプリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-144">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="_editItem.IsComplete" />
  <input @bind="_editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem _editItem = new TodoItem();

      private void EditItem(long id)
      {
          var editItem = _todoItems.Single(i => i.Id == id);
          _editItem = new TodoItem { Id = editItem.Id, Name = editItem.Name, 
              IsComplete = editItem.IsComplete };
      }

      private async Task SaveItem() =>
          await Http.PutJsonAsync($"api/TodoItems/{_editItem.Id}, _editItem);
  }
  ```

<span data-ttu-id="fc3f1-145"><xref:System.Net.Http> には、HTTP 要求を送信して HTTP 応答を受信するための拡張メソッドが追加されています。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-145"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="fc3f1-146">[Httpclient。 DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*)は、HTTP DELETE 要求を web API に送信するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-146">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="fc3f1-147">次のコードでは、Delete `<button>` 要素は `DeleteItem` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-147">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="fc3f1-148">バインドされた `<input>` 要素は、削除する項目の `id` を提供します。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-148">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="fc3f1-149">完全な例については、サンプルアプリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-149">See the sample app for a complete example.</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="_id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long _id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{_id}");
}
```

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="fc3f1-150">クロスオリジン リソース共有 (CORS)</span><span class="sxs-lookup"><span data-stu-id="fc3f1-150">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="fc3f1-151">ブラウザーのセキュリティでは、web ページが web ページを提供したものとは異なるドメインに要求を作成できないようにします。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-151">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="fc3f1-152">この制限は、*同一オリジン ポリシー*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-152">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="fc3f1-153">同一オリジン ポリシーは、悪意のあるサイトが別のサイトから機密データを読み取ることを防ぎます。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-153">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="fc3f1-154">ブラウザーから別のオリジンのエンドポイントへの要求を行うには、*エンドポイント*が[クロスオリジンリソース共有 (CORS)](https://www.w3.org/TR/cors/)を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-154">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="fc3f1-155">[Blazor WebAssembly サンプルアプリ ()](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)は、呼び出し Web API コンポーネント (*Pages/CALLWEBAPI*) で CORS を使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-155">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="fc3f1-156">他のサイトがアプリに対してクロスオリジンリソース共有 (CORS) 要求を行うことができるようにするには、「<xref:security/cors>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-156">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="fc3f1-157">HttpClient と HttpRequestMessage with Fetch API 要求オプション</span><span class="sxs-lookup"><span data-stu-id="fc3f1-157">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="fc3f1-158">Blazor webassembly で Webasで実行する場合は、 [Httpclient](xref:fundamentals/http-requests)と <xref:System.Net.Http.HttpRequestMessage> を使用して要求をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-158">When running on WebAssembly in a Blazor WebAssembly app, use [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> to customize requests.</span></span> <span data-ttu-id="fc3f1-159">たとえば、要求 URI、HTTP メソッド、および必要な要求ヘッダーを指定できます。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-159">For example, you can specify the request URI, HTTP method, and any desired request headers.</span></span>

```razor
@using System.Net.Http
@using System.Net.Http.Headers
@inject HttpClient Http

@code {
    private async Task PostRequest()
    {
        Http.DefaultRequestHeaders.Authorization =
            new AuthenticationHeaderValue("Bearer", "{OAUTH TOKEN}");

        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content = 
                new StringContent(
                    @"{""name"":""A New Todo Item"",""isComplete"":false}")
        };

        requestMessage.Content.Headers.ContentType = 
            new System.Net.Http.Headers.MediaTypeHeaderValue(
                "application/json");

        requestMessage.Content.Headers.TryAddWithoutValidation(
            "x-custom-header", "value");

        var response = await Http.SendAsync(requestMessage);
        var responseStatusCode = response.StatusCode;
        var responseBody = await response.Content.ReadAsStringAsync();
    }
}
```

<span data-ttu-id="fc3f1-160">Fetch API オプションの詳細については、 [MDN の web ドキュメント: WindowOrWorkerGlobalScope ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-160">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="fc3f1-161">CORS 要求で資格情報 (承認 cookie/ヘッダー) を送信する場合、CORS ポリシーで `Authorization` ヘッダーが許可されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-161">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="fc3f1-162">次のポリシーには、の構成が含まれています。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-162">The following policy includes configuration for:</span></span>

* <span data-ttu-id="fc3f1-163">要求オリジン (`http://localhost:5000`、`https://localhost:5001`)。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-163">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="fc3f1-164">任意のメソッド (動詞)。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-164">Any method (verb).</span></span>
* <span data-ttu-id="fc3f1-165">ヘッダーを `Content-Type` して `Authorization` します。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-165">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="fc3f1-166">カスタムヘッダー (`x-custom-header`など) を許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>を呼び出すときにヘッダーの一覧を表示します。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-166">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="fc3f1-167">クライアント側の JavaScript コードによって設定される資格情報 (`credentials` プロパティが `include`に設定されます)。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-167">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="fc3f1-168">詳細については、「<xref:security/cors>」およびサンプルアプリの HTTP 要求テスターコンポーネント (*Components/HTTPRequestTester*) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fc3f1-168">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fc3f1-169">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="fc3f1-169">Additional resources</span></span>

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="fc3f1-170">Kestrel HTTPS エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="fc3f1-170">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="fc3f1-171">W3C でのクロスオリジンリソース共有 (CORS)</span><span class="sxs-lookup"><span data-stu-id="fc3f1-171">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
