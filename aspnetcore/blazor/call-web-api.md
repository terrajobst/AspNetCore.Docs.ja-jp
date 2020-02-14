---
title: ASP.NET Core Blazor から web API を呼び出す
author: guardrex
description: クロスオリジンリソース共有 (CORS) 要求の作成など、JSON ヘルパーを使用して Blazor アプリから web API を呼び出す方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/22/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: 345fb6962e3376c22551eb7914c70c89cb7100d5
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2020
ms.locfileid: "77213276"
---
# <a name="call-a-web-api-from-aspnet-core-opno-locblazor"></a>ASP.NET Core Blazor から web API を呼び出す

[Luke Latham](https://github.com/guardrex)、 [Daniel Roth](https://github.com/danroth27)、[フアン De la クルス](https://github.com/juandelacruz23)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly)は、構成済みの `HttpClient` サービスを使用して web api を呼び出します。 要求を作成します。これには、Blazor JSON ヘルパーまたは <xref:System.Net.Http.HttpRequestMessage>を使用した JavaScript [FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)オプションを含めることができます。 Blazor Webasのアプリの `HttpClient` サービスは、配信元のサーバーに要求を戻すことに重点を置いています。 このトピックのガイダンスは Blazor Webasapps アプリにのみ関連しています。

[Blazor サーバー](xref:blazor/hosting-models#blazor-server)アプリは <xref:System.Net.Http.HttpClient> インスタンスを使用して web api を呼び出します。通常は <xref:System.Net.Http.IHttpClientFactory>を使用して作成されます。 このトピックのガイダンスは Blazor サーバーアプリには関係しません。 Blazor サーバーアプリを開発するときは、<xref:fundamentals/http-requests>のガイダンスに従ってください。

*BlazorWebAssemblySample*アプリを選択 &ndash; には、サンプルコード ([ダウンロード方法](xref:index#how-to-download-a-sample)) を[表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します。

*BlazorWebAssemblySample*サンプルアプリの次のコンポーネントを参照してください。

* Web API の呼び出し (*Pages/CallWebAPI*)
* HTTP 要求テスター (*Components/HTTPRequestTester*)

## <a name="packages"></a>パッケージ

AspNetCore*を参照*します。 [プロジェクトファイル内の BlazorHttpClient](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.HttpClient/) NuGet パッケージ。 `Microsoft.AspNetCore.Blazor.HttpClient` は `HttpClient` と system.string に基づいて[います。](https://www.nuget.org/packages/System.Text.Json/)

安定した API を使用するには、 [WebApi](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/)パッケージを使用します。このパッケージは[Json.NET/](https://www.newtonsoft.com/json/help/html/Introduction.htm)[を使用し](https://www.nuget.org/packages/Newtonsoft.Json/)ます。 `Microsoft.AspNet.WebApi.Client` で安定した API を使用しても、このトピックで説明する JSON ヘルパーは提供されません。これは、実験的な `Microsoft.AspNetCore.Blazor.HttpClient` パッケージに固有のものです。

## <a name="httpclient-and-json-helpers"></a>HttpClient と JSON のヘルパー

Blazor WebAssembly では、 [Httpclient](xref:fundamentals/http-requests)は、要求を配信元サーバーに返すための事前に構成されたサービスとして利用できます。

Blazor サーバーアプリには、既定では `HttpClient` サービスが含まれていません。 [Httpclient ファクトリインフラストラクチャ](xref:fundamentals/http-requests)を使用して、アプリに `HttpClient` を提供します。

`HttpClient` と JSON ヘルパーは、サードパーティの web API エンドポイントを呼び出すためにも使用されます。 `HttpClient` は、ブラウザーの[FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)を使用して実装され、同じオリジンポリシーの適用などの制限が適用されます。

クライアントのベースアドレスは、元のサーバーのアドレスに設定されます。 `@inject` ディレクティブを使用して `HttpClient` インスタンスを挿入します。

```razor
@using System.Net.Http
@inject HttpClient Http
```

次の例では、Todo web API が作成、読み取り、更新、および削除 (CRUD) の各操作を処理します。 この例は、を格納する `TodoItem` クラスに基づいています。

* ID (`Id`、`long`) &ndash; アイテムの一意の ID。
* 項目の名前 (`Name`、`string`) &ndash; 名前。
* [状態] (`IsComplete`、`bool`) &ndash; Todo 項目が終了したかどうかを示します。

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

JSON ヘルパーメソッドは、要求を URI (次の例では web API) に送信し、応答を処理します。

* `GetJsonAsync` &ndash; は HTTP GET 要求を送信し、JSON 応答本文を解析してオブジェクトを作成します。

  次のコードでは、`_todoItems` がコンポーネントによって表示されます。 `GetTodoItems` メソッドは、コンポーネントのレンダリングが終了したときにトリガーされます ([Oninitializer Edasync](xref:blazor/lifecycle#component-initialization-methods))。 完全な例については、サンプルアプリを参照してください。

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* `PostJsonAsync` &ndash; は、JSON でエンコードされたコンテンツを含む HTTP POST 要求を送信し、JSON 応答本文を解析してオブジェクトを作成します。

  次のコードでは、コンポーネントのバインドされた要素によって `_newItemName` が提供されています。 `AddItem` メソッドは、`<button>` 要素を選択することによってトリガーされます。 完全な例については、サンプルアプリを参照してください。

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

* `PutJsonAsync` &ndash; は、JSON でエンコードされたコンテンツを含む HTTP PUT 要求を送信します。

  次のコードでは、`Name` と `IsCompleted` の `_editItem` 値が、コンポーネントのバインドされた要素によって提供されています。 項目の `Id` は、UI の別の部分で項目が選択され、`EditItem` が呼び出されたときに設定されます。 `SaveItem` メソッドは、Save `<button>` 要素を選択することによってトリガーされます。 完全な例については、サンプルアプリを参照してください。

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

<xref:System.Net.Http> には、HTTP 要求を送信して HTTP 応答を受信するための拡張メソッドが追加されています。 [Httpclient。 DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*)は、HTTP DELETE 要求を web API に送信するために使用されます。

次のコードでは、Delete `<button>` 要素は `DeleteItem` メソッドを呼び出します。 バインドされた `<input>` 要素は、削除する項目の `id` を提供します。 完全な例については、サンプルアプリを参照してください。

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

## <a name="cross-origin-resource-sharing-cors"></a>クロスオリジン リソース共有 (CORS)

ブラウザーのセキュリティでは、web ページが web ページを提供したものとは異なるドメインに要求を作成できないようにします。 この制限は、*同一オリジン ポリシー*と呼ばれます。 同一オリジン ポリシーは、悪意のあるサイトが別のサイトから機密データを読み取ることを防ぎます。 ブラウザーから別のオリジンのエンドポイントへの要求を行うには、*エンドポイント*が[クロスオリジンリソース共有 (CORS)](https://www.w3.org/TR/cors/)を有効にする必要があります。

[Blazor WebAssembly サンプルアプリ ()](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)は、呼び出し Web API コンポーネント (*Pages/CALLWEBAPI*) で CORS を使用する方法を示しています。

他のサイトがアプリに対してクロスオリジンリソース共有 (CORS) 要求を行うことができるようにするには、「<xref:security/cors>」を参照してください。

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>HttpClient と HttpRequestMessage with Fetch API 要求オプション

Blazor webassembly で Webasで実行する場合は、 [Httpclient](xref:fundamentals/http-requests)と <xref:System.Net.Http.HttpRequestMessage> を使用して要求をカスタマイズします。 たとえば、要求 URI、HTTP メソッド、および必要な要求ヘッダーを指定できます。

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

Fetch API オプションの詳細については、[MDN の web ドキュメントを参照してください。WindowOrWorkerGlobalScope ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)。

CORS 要求で資格情報 (承認 cookie/ヘッダー) を送信する場合、CORS ポリシーで `Authorization` ヘッダーが許可されている必要があります。

次のポリシーには、の構成が含まれています。

* 要求オリジン (`http://localhost:5000`、`https://localhost:5001`)。
* 任意のメソッド (動詞)。
* ヘッダーを `Content-Type` して `Authorization` します。 カスタムヘッダー (`x-custom-header`など) を許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>を呼び出すときにヘッダーの一覧を表示します。
* クライアント側の JavaScript コードによって設定される資格情報 (`credentials` プロパティが `include`に設定されます)。

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

詳細については、「<xref:security/cors>」およびサンプルアプリの HTTP 要求テスターコンポーネント (*Components/HTTPRequestTester*) を参照してください。

## <a name="additional-resources"></a>その他の技術情報

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Kestrel HTTPS エンドポイントの構成](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [W3C でのクロスオリジンリソース共有 (CORS)](https://www.w3.org/TR/cors/)
