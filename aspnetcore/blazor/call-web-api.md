---
title: ASP.NET Core Blazor から web API を呼び出す
author: guardrex
description: クロスオリジンリソース共有 (CORS) 要求の作成など、JSON ヘルパーを使用して Blazor アプリから web API を呼び出す方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/call-web-api
ms.openlocfilehash: 152a2d5ac9a4325592ca414e9ea5e70c947d079f
ms.sourcegitcommit: 092061c4f6ef46ed2165fa84de6273d3786fb97e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70963704"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a>ASP.NET Core Blazor から web API を呼び出す

[Luke Latham](https://github.com/guardrex)および[Daniel Roth](https://github.com/danroth27)

Blazor webassembly は、事前に構成済み`HttpClient`のサービスを使用して web api を呼び出します。 作成要求<xref:System.Net.Http.HttpRequestMessage>。 Blazor JSON ヘルパーまたはを使用して、JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)オプションを含めることができます。

Blazor サーバーアプリは、通常、 <xref:System.Net.Http.HttpClient>を使用<xref:System.Net.Http.IHttpClientFactory>して作成されたインスタンスを使用して web api を呼び出します。 詳細については、「 <xref:fundamentals/http-requests> 」を参照してください。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

Blazor WebAssembly 例については、サンプルアプリの次のコンポーネントを参照してください。

* Web API の呼び出し (*Pages/CallWebAPI*)
* HTTP 要求テスター (*Components/HTTPRequestTester*)

## <a name="httpclient-and-json-helpers"></a>HttpClient と JSON のヘルパー

Blazor WebAssembly では、 [Httpclient](xref:fundamentals/http-requests)は、要求を配信元サーバーに返すための事前に構成されたサービスとして利用できます。 JSON ヘルパー `HttpClient`を使用するには、へ`Microsoft.AspNetCore.Blazor.HttpClient`のパッケージ参照を追加します。 `HttpClient`また、JSON ヘルパーは、サードパーティの web API エンドポイントを呼び出すためにも使用されます。 `HttpClient`は、ブラウザー [FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)を使用して実装され、同じオリジンポリシーの適用などの制限が適用されます。

クライアントのベースアドレスは、元のサーバーのアドレスに設定されます。 ディレクティブを使用してインスタンスを挿入します。`HttpClient` `@inject`

```cshtml
@using System.Net.Http
@inject HttpClient Http
```

次の例では、Todo web API が作成、読み取り、更新、および削除 (CRUD) の各操作を処理します。 この例は、を格納`TodoItem`するクラスに基づいています。

* Id (`Id`、 `long`) &ndash;項目の一意の id。
* 名前 (`Name`, `string`) &ndash;項目の名前。
* Status (`IsComplete`, `bool`) &ndash;は、Todo 項目が終了したかどうかを示します。

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

JSON ヘルパーメソッドは、要求を URI (次の例では web API) に送信し、応答を処理します。

* `GetJsonAsync`&ndash; HTTP GET 要求を送信し、JSON 応答本文を解析してオブジェクトを作成します。

  次のコード`_todoItems`では、がコンポーネントによって表示されます。 メソッドは、コンポーネントのレンダリングが終了したときにトリガーされます ([oninitializer edasync)。](xref:blazor/components#lifecycle-methods) `GetTodoItems` 完全な例については、サンプルアプリを参照してください。

  ```cshtml
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetJsonAsync<TodoItem[]>("api/todo");
  }
  ```

* `PostJsonAsync`&ndash; Json でエンコードされたコンテンツを含む HTTP POST 要求を送信し、json 応答本文を解析してオブジェクトを作成します。

  次のコードでは`_newItemName` 、はコンポーネントのバインド要素によって提供されています。 メソッド`AddItem`は、要素を`<button>`選択することによってトリガーされます。 完全な例については、サンプルアプリを参照してください。

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
          await Http.PostJsonAsync("api/todo", addItem);
      }
  }
  ```

* `PutJsonAsync`&ndash; JSON でエンコードされたコンテンツを含む HTTP PUT 要求を送信します。

  次のコード`_editItem`では、と`Name` `IsCompleted`の値は、コンポーネントのバインドされた要素によって提供されます。 項目の`Id`は、 `EditItem` UI の別の部分で項目が選択され、が呼び出されたときに設定されます。 メソッドは、Save `<button>`要素を選択することによってトリガーされます。 `SaveItem` 完全な例については、サンプルアプリを参照してください。

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
          await Http.PutJsonAsync($"api/todo/{_editItem.Id}, _editItem);
  }
  ```

<xref:System.Net.Http>HTTP 要求を送信し、HTTP 応答を受信するための拡張メソッドが追加されています。 [Httpclient。 DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*)は、HTTP DELETE 要求を web API に送信するために使用されます。

次のコードでは、Delete `<button>`要素はメソッド`DeleteItem`を呼び出します。 バインド`<input>`された要素`id`は、削除する項目のを提供します。 完全な例については、サンプルアプリを参照してください。

```cshtml
@using System.Net.Http
@inject HttpClient Http

<input @bind="_id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long _id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/todo/{_id}");
}
```

## <a name="cross-origin-resource-sharing-cors"></a>クロスオリジンリソース共有 (CORS)

ブラウザーのセキュリティでは、web ページが web ページを提供したものとは異なるドメインに要求を作成できないようにします。 この制限は、*同一オリジン ポリシー*と呼ばれます。 同一オリジン ポリシーは、悪意のあるサイトが別のサイトから機密データを読み取ることを防ぎます。 ブラウザーから別のオリジンのエンドポイントへの要求を行うには、*エンドポイント*が[クロスオリジンリソース共有 (CORS)](https://www.w3.org/TR/cors/)を有効にする必要があります。

このサンプルアプリでは、呼び出し Web API コンポーネント (*Pages/CallWebAPI*) で CORS を使用する方法を示しています。

他のサイトがアプリに対してクロスオリジンリソース共有 (CORS) 要求を行うことが<xref:security/cors>できるようにするには、「」を参照してください。

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>HttpClient と HttpRequestMessage with Fetch API 要求オプション

Blazor webassembly で webassembly 実行する場合は、 [httpclient](xref:fundamentals/http-requests)とを使用<xref:System.Net.Http.HttpRequestMessage>して要求をカスタマイズします。 たとえば、要求 URI、HTTP メソッド、および必要な要求ヘッダーを指定できます。

要求の`WebAssemblyHttpMessageHandler.FetchArgs`プロパティを使用して、基になる JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)に要求オプションを指定します。 次の例`credentials`に示すように、プロパティは次のいずれかの値に設定されます。

* `FetchCredentialsOption.Include`("include")&ndash;クロスオリジン要求の場合でも、ブラウザーに対して資格情報 (cookie や HTTP 認証ヘッダーなど) を送信するように通知します。 CORS ポリシーが資格情報を許可するように構成されている場合にのみ許可されます。
* `FetchCredentialsOption.Omit`("省略")&ndash;ブラウザーが資格情報を送信しないことを通知します (cookie や HTTP auth ヘッダーなど)。
* `FetchCredentialsOption.SameOrigin`("同じオリジン")&ndash;ターゲット URL が呼び出し元アプリケーションと同じオリジンにある場合にのみ、ブラウザーに資格情報 (cookie や HTTP auth ヘッダーなど) を送信するように通知します。

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
            RequestUri = new Uri("https://localhost:10000/api/todo"),
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

Fetch API オプションの詳細については[、MDN の web ドキュメントを参照してください。WindowOrWorkerGlobalScope ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)。

Cors 要求で資格情報 (承認 cookie/ヘッダー) を送信する`Authorization`場合、cors ポリシーによってヘッダーが許可されている必要があります。

次のポリシーには、の構成が含まれています。

* 要求オリジン (`http://localhost:5000`, `https://localhost:5001`)。
* 任意のメソッド (動詞)。
* `Content-Type`と`Authorization`のヘッダー。 カスタムヘッダー ( `x-custom-header`など) を許可するには、を呼び出す<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>ときにヘッダーを一覧表示します。
* クライアント側の JavaScript コードによって設定`credentials`される資格`include`情報 (プロパティがに設定されます)。

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

詳細については<xref:security/cors> 、「」およびサンプルアプリの HTTP 要求テスターコンポーネント (*Components/HTTPRequestTester*) を参照してください。

## <a name="additional-resources"></a>その他の技術情報

* <xref:fundamentals/http-requests>
* [W3C でのクロスオリジンリソース共有 (CORS)](https://www.w3.org/TR/cors/)
