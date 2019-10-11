---
title: ASP.NET Core Blazor から web API を呼び出す
author: guardrex
description: クロスオリジンリソース共有 (CORS) 要求の作成など、JSON ヘルパーを使用して Blazor アプリから web API を呼び出す方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/05/2019
uid: blazor/call-web-api
ms.openlocfilehash: 3d70af2226eb29870458a5fd3c2bbbc3ee5c14ce
ms.sourcegitcommit: 73a451e9a58ac7102f90b608d661d8c23dd9bbaf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72037443"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="39da5-103">ASP.NET Core Blazor から web API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="39da5-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="39da5-104">[Luke Latham](https://github.com/guardrex)、 [Daniel Roth](https://github.com/danroth27)、[フアン De la クルス](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="39da5-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="39da5-105">Blazor WebAssembly は、構成済みの @no__t 0 サービスを使用して web Api を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="39da5-105">Blazor WebAssembly apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="39da5-106">Blazor JSON ヘルパーまたは <xref:System.Net.Http.HttpRequestMessage> を使用して、JavaScript [FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)オプションを含めることができるように要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="39da5-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span>

<span data-ttu-id="39da5-107">Blazor サーバーアプリは、通常 <xref:System.Net.Http.IHttpClientFactory> を使用して作成された @no__t 0 のインスタンスを使用して web Api を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="39da5-107">Blazor Server apps call web APIs using <xref:System.Net.Http.HttpClient> instances typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="39da5-108">詳細については、「 <xref:fundamentals/http-requests> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="39da5-108">For more information, see <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="39da5-109">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="39da5-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="39da5-110">Blazor WebAssembly 例については、サンプルアプリの次のコンポーネントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="39da5-110">For Blazor WebAssembly examples, see the following components in the sample app:</span></span>

* <span data-ttu-id="39da5-111">Web API の呼び出し (*Pages/CallWebAPI*)</span><span class="sxs-lookup"><span data-stu-id="39da5-111">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="39da5-112">HTTP 要求テスター (*Components/HTTPRequestTester*)</span><span class="sxs-lookup"><span data-stu-id="39da5-112">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="39da5-113">HttpClient と JSON のヘルパー</span><span class="sxs-lookup"><span data-stu-id="39da5-113">HttpClient and JSON helpers</span></span>

<span data-ttu-id="39da5-114">Blazor WebAssembly では、 [Httpclient](xref:fundamentals/http-requests)は、要求を配信元サーバーに返すための事前に構成されたサービスとして利用できます。</span><span class="sxs-lookup"><span data-stu-id="39da5-114">In Blazor WebAssembly apps, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span> <span data-ttu-id="39da5-115">@No__t-0 の JSON ヘルパーを使用するには、`Microsoft.AspNetCore.Blazor.HttpClient` にパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="39da5-115">To use `HttpClient` JSON helpers, add a package reference to `Microsoft.AspNetCore.Blazor.HttpClient`.</span></span> <span data-ttu-id="39da5-116">`HttpClient` および JSON ヘルパーは、サードパーティの web API エンドポイントを呼び出すためにも使用されます。</span><span class="sxs-lookup"><span data-stu-id="39da5-116">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="39da5-117">`HttpClient` は、ブラウザーの[FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)を使用して実装され、同じオリジンポリシーの適用などの制限が適用されます。</span><span class="sxs-lookup"><span data-stu-id="39da5-117">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="39da5-118">クライアントのベースアドレスは、元のサーバーのアドレスに設定されます。</span><span class="sxs-lookup"><span data-stu-id="39da5-118">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="39da5-119">@No__t-1 ディレクティブを使用して @no__t 0 のインスタンスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="39da5-119">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```cshtml
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="39da5-120">次の例では、Todo web API が作成、読み取り、更新、および削除 (CRUD) の各操作を処理します。</span><span class="sxs-lookup"><span data-stu-id="39da5-120">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="39da5-121">これらの例は、を格納する @no__t 0 のクラスに基づいています。</span><span class="sxs-lookup"><span data-stu-id="39da5-121">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="39da5-122">ID (`Id`、`long`) &ndash; アイテムの一意の ID。</span><span class="sxs-lookup"><span data-stu-id="39da5-122">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="39da5-123">名前 (`Name`、`string`) &ndash; 項目の名前。</span><span class="sxs-lookup"><span data-stu-id="39da5-123">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="39da5-124">状態 (`IsComplete`、`bool`) &ndash; Todo 項目が終了したかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="39da5-124">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="39da5-125">JSON ヘルパーメソッドは、要求を URI (次の例では web API) に送信し、応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="39da5-125">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="39da5-126">`GetJsonAsync` &ndash; は HTTP GET 要求を送信し、JSON 応答本文を解析してオブジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="39da5-126">`GetJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="39da5-127">次のコードでは、`_todoItems` がコンポーネントによって表示されます。</span><span class="sxs-lookup"><span data-stu-id="39da5-127">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="39da5-128">@No__t-0 メソッドは、コンポーネントのレンダリングが終了したときにトリガーされます ([On初期化 Edasync](xref:blazor/components#lifecycle-methods))。</span><span class="sxs-lookup"><span data-stu-id="39da5-128">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/components#lifecycle-methods)).</span></span> <span data-ttu-id="39da5-129">完全な例については、サンプルアプリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="39da5-129">See the sample app for a complete example.</span></span>

  ```cshtml
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="39da5-130">`PostJsonAsync` &ndash; は、JSON でエンコードされたコンテンツを含む HTTP POST 要求を送信し、JSON 応答本文を解析してオブジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="39da5-130">`PostJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="39da5-131">次のコードでは、コンポーネントのバインドされた要素によって `_newItemName` が提供されています。</span><span class="sxs-lookup"><span data-stu-id="39da5-131">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="39da5-132">@No__t-0 メソッドは、`<button>` 要素を選択することによってトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="39da5-132">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="39da5-133">完全な例については、サンプルアプリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="39da5-133">See the sample app for a complete example.</span></span>

  ```cshtml
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

* <span data-ttu-id="39da5-134">`PutJsonAsync` &ndash; は、JSON でエンコードされたコンテンツを含む HTTP PUT 要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="39da5-134">`PutJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="39da5-135">次のコードでは、`Name` と `IsCompleted` の `_editItem` の値が、コンポーネントのバインドされた要素によって提供されています。</span><span class="sxs-lookup"><span data-stu-id="39da5-135">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="39da5-136">項目の `Id` は、UI の別の部分で項目が選択され、`EditItem` が呼び出されたときに設定されます。</span><span class="sxs-lookup"><span data-stu-id="39da5-136">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="39da5-137">@No__t-0 メソッドは、Save `<button>` 要素を選択することによってトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="39da5-137">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="39da5-138">完全な例については、サンプルアプリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="39da5-138">See the sample app for a complete example.</span></span>

  ```cshtml
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

<span data-ttu-id="39da5-139"><xref:System.Net.Http> には、HTTP 要求を送信して HTTP 応答を受信するための拡張メソッドが追加されています。</span><span class="sxs-lookup"><span data-stu-id="39da5-139"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="39da5-140">[Httpclient。 DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*)は、HTTP DELETE 要求を web API に送信するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="39da5-140">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="39da5-141">次のコードでは、Delete `<button>` 要素は `DeleteItem` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="39da5-141">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="39da5-142">バインドされた `<input>` 要素は、削除する項目の `id` を提供します。</span><span class="sxs-lookup"><span data-stu-id="39da5-142">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="39da5-143">完全な例については、サンプルアプリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="39da5-143">See the sample app for a complete example.</span></span>

```cshtml
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

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="39da5-144">クロスオリジンリソース共有 (CORS)</span><span class="sxs-lookup"><span data-stu-id="39da5-144">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="39da5-145">ブラウザーのセキュリティでは、web ページが web ページを提供したものとは異なるドメインに要求を作成できないようにします。</span><span class="sxs-lookup"><span data-stu-id="39da5-145">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="39da5-146">この制限は、*同一オリジン ポリシー*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="39da5-146">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="39da5-147">同一オリジン ポリシーは、悪意のあるサイトが別のサイトから機密データを読み取ることを防ぎます。</span><span class="sxs-lookup"><span data-stu-id="39da5-147">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="39da5-148">ブラウザーから別のオリジンのエンドポイントへの要求を行うには、*エンドポイント*が[クロスオリジンリソース共有 (CORS)](https://www.w3.org/TR/cors/)を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="39da5-148">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="39da5-149">このサンプルアプリでは、呼び出し Web API コンポーネント (*Pages/CallWebAPI*) で CORS を使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="39da5-149">The sample app demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="39da5-150">他のサイトがアプリに対してクロスオリジンリソース共有 (CORS) 要求を行うことができるようにするには、「<xref:security/cors>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="39da5-150">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="39da5-151">HttpClient と HttpRequestMessage with Fetch API 要求オプション</span><span class="sxs-lookup"><span data-stu-id="39da5-151">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="39da5-152">Blazor webassembly で WebAssembly 実行する場合は、 [Httpclient](xref:fundamentals/http-requests)と <xref:System.Net.Http.HttpRequestMessage> を使用して要求をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="39da5-152">When running on WebAssembly in a Blazor WebAssembly app, use [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> to customize requests.</span></span> <span data-ttu-id="39da5-153">たとえば、要求 URI、HTTP メソッド、および必要な要求ヘッダーを指定できます。</span><span class="sxs-lookup"><span data-stu-id="39da5-153">For example, you can specify the request URI, HTTP method, and any desired request headers.</span></span>

<span data-ttu-id="39da5-154">要求の `WebAssemblyHttpMessageHandler.FetchArgs` プロパティを使用して、基になる JavaScript [FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)に要求オプションを指定します。</span><span class="sxs-lookup"><span data-stu-id="39da5-154">Supply request options to the underlying JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) using the `WebAssemblyHttpMessageHandler.FetchArgs` property on the request.</span></span> <span data-ttu-id="39da5-155">次の例に示すように、`credentials` プロパティは、次のいずれかの値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="39da5-155">As shown in the following example, the `credentials` property is set to any of the following values:</span></span>

* <span data-ttu-id="39da5-156">`FetchCredentialsOption.Include` ("include") &ndash; は、クロスオリジン要求の場合でも、ブラウザーが資格情報 (cookie や HTTP 認証ヘッダーなど) を送信するように通知します。</span><span class="sxs-lookup"><span data-stu-id="39da5-156">`FetchCredentialsOption.Include` ("include") &ndash; Advises the browser to send credentials (such as cookies or HTTP authentication headers) even for cross-origin requests.</span></span> <span data-ttu-id="39da5-157">CORS ポリシーが資格情報を許可するように構成されている場合にのみ許可されます。</span><span class="sxs-lookup"><span data-stu-id="39da5-157">Only allowed when the CORS policy is configured to allow credentials.</span></span>
* <span data-ttu-id="39da5-158">`FetchCredentialsOption.Omit` ("省略") @no__t、ブラウザーが資格情報を送信しないことを通知します (cookie や HTTP 認証ヘッダーなど)。</span><span class="sxs-lookup"><span data-stu-id="39da5-158">`FetchCredentialsOption.Omit` ("omit") &ndash; Advises the browser never to send credentials (such as cookies or HTTP auth headers).</span></span>
* <span data-ttu-id="39da5-159">`FetchCredentialsOption.SameOrigin` ("同じオリジン") &ndash; は、ターゲット URL が呼び出し元アプリケーションと同じオリジンにある場合にのみ、ブラウザーが資格情報 (cookie や HTTP auth ヘッダーなど) を送信するように通知します。</span><span class="sxs-lookup"><span data-stu-id="39da5-159">`FetchCredentialsOption.SameOrigin` ("same-origin") &ndash; Advises the browser to send credentials (such as cookies or HTTP auth headers) only if the target URL is on the same origin as the calling application.</span></span>

```cshtml
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
        
        requestMessage.Properties[WebAssemblyHttpMessageHandler.FetchArgs] = new
        { 
            credentials = FetchCredentialsOption.Include
        };

        var response = await Http.SendAsync(requestMessage);
        var responseStatusCode = response.StatusCode;
        var responseBody = await response.Content.ReadAsStringAsync();
    }
}
```

<span data-ttu-id="39da5-160">Fetch API オプションの詳細については、次を参照してください [MDN web docs:WindowOrWorkerGlobalScope ():P arameters @ no__t-0。</span><span class="sxs-lookup"><span data-stu-id="39da5-160">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="39da5-161">CORS 要求で資格情報 (承認 cookie/ヘッダー) を送信する場合、CORS ポリシーによって `Authorization` ヘッダーが許可されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="39da5-161">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="39da5-162">次のポリシーには、の構成が含まれています。</span><span class="sxs-lookup"><span data-stu-id="39da5-162">The following policy includes configuration for:</span></span>

* <span data-ttu-id="39da5-163">要求オリジン (`http://localhost:5000`、`https://localhost:5001`)。</span><span class="sxs-lookup"><span data-stu-id="39da5-163">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="39da5-164">任意のメソッド (動詞)。</span><span class="sxs-lookup"><span data-stu-id="39da5-164">Any method (verb).</span></span>
* <span data-ttu-id="39da5-165">`Content-Type` および `Authorization` ヘッダーです。</span><span class="sxs-lookup"><span data-stu-id="39da5-165">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="39da5-166">カスタムヘッダー (たとえば `x-custom-header`) を許可するには <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> を呼び出すときにヘッダーを一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="39da5-166">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="39da5-167">クライアント側の JavaScript コードによって設定された資格情報 (`credentials` プロパティが `include` に設定されている)。</span><span class="sxs-lookup"><span data-stu-id="39da5-167">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="39da5-168">詳細については、「<xref:security/cors>」とサンプルアプリの HTTP 要求テスターコンポーネント (*Components/HTTPRequestTester*) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="39da5-168">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="39da5-169">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="39da5-169">Additional resources</span></span>

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="39da5-170">Kestrel HTTPS エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="39da5-170">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="39da5-171">W3C でのクロスオリジンリソース共有 (CORS)</span><span class="sxs-lookup"><span data-stu-id="39da5-171">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
