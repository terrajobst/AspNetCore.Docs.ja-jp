---
title: ASP.NET Core Blazor で JavaScript 関数から .NET メソッドを呼び出す
author: guardrex
description: Blazor アプリで JavaScript 関数から .NET メソッドを呼び出す方法について学習します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/24/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: dbf44fe7923998c65119e42d97c304890fa95523
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218792"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-opno-locblazor"></a>ASP.NET Core Blazor で JavaScript 関数から .NET メソッドを呼び出す

作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Daniel Roth](https://github.com/danroth27)、[Shashikant Rudrawadi](http://wisne.co)、[Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor アプリでは、.NET メソッドから JavaScript 関数を呼び出すことも、JavaScript 関数から .NET メソッドを呼び出すこともできます。 これらのシナリオは、"*JavaScript 相互運用*" ("*JS 相互運用*") と呼ばれます。

この記事では、JavaScript から .NET メソッドを呼び出す方法について説明します。 .NET から JavaScript 関数を呼び出す方法については、「<xref:blazor/call-javascript-from-dotnet>」を参照してください。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="static-net-method-call"></a>静的 .NET メソッドの呼び出し

JavaScript から静的 .NET メソッドを呼び出すには、`DotNet.invokeMethod` 関数または `DotNet.invokeMethodAsync` 関数を使用します。 呼び出す静的メソッドの識別子、関数を含むアセンブリの名前、任意の引数を渡します。 Blazor サーバーのシナリオをサポートするには、非同期バージョンを使用することをお勧めします。 .NET メソッドはパブリックかつ静的であり、`[JSInvokable]` 属性を持つ必要があります。 オープン ジェネリック メソッドを呼び出すことは、現在サポートされていません。

サンプル アプリには、`int` 配列を返す C# メソッドが含まれています。 `JSInvokable` 属性がメソッドに適用されます。

*Pages/JsInterop.razor*:

```razor
<button type="button" class="btn btn-primary"
        onclick="exampleJsFunctions.returnArrayAsyncJs()">
    Trigger .NET static method ReturnArrayAsync
</button>

@code {
    [JSInvokable]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

クライアントに提供される JavaScript は、C# .NET メソッドを呼び出します。

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

**[Trigger .NET static method ReturnArrayAsync]** ボタンが選択されている場合は、ブラウザーの Web 開発者ツールでコンソール出力を確認します。

コンソール出力は、次のようになります。

```console
Array(4) [ 1, 2, 3, 4 ]
```

4 番目の配列値は、`data.push(4);` によって返される配列 (`ReturnArrayAsync`) にプッシュされます。

既定では、メソッド識別子はメソッド名ですが、`JSInvokableAttribute` コンストラクターを使用して別の識別子を指定することもできます。

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

クライアント側の JavaScript ファイル

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

## <a name="instance-method-call"></a>インスタンス メソッドの呼び出し

JavaScript から .NET インスタンス メソッドを呼び出すこともできます。 JavaScript から .NET インスタンス メソッドを呼び出すには

* 参照渡しで .NET インスタンスを JavaScript に渡します。
  * 静的呼び出しを `DotNetObjectReference.Create` にします。
  * インスタンスを `DotNetObjectReference` インスタンスにラップし、`Create` インスタンスで `DotNetObjectReference` を呼び出します。 `DotNetObjectReference` オブジェクトを破棄します (このセクションの後半で例を示します)。
* `invokeMethod` 関数または `invokeMethodAsync` 関数を使用して、インスタンスで .NET インスタンス メソッドを呼び出します。 .NET インスタンスは、JavaScript から他の .NET メソッドを呼び出すときに引数として渡すこともできます。

> [!NOTE]
> サンプル アプリでは、メッセージがクライアント側のコンソールにログ出力されます。 サンプル アプリで示される以下の例については、ブラウザーの開発者ツールでブラウザーのコンソール出力を確認してください。

**[Trigger .NET instance method HelloHelper.SayHello]** ボタンが選択されている場合、`ExampleJsInterop.CallHelloHelperSayHello` が呼び出され、メソッドに名前 `Blazor` を渡します。

*Pages/JsInterop.razor*:

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JSRuntime);
        await exampleJsInterop.CallHelloHelperSayHello("Blazor");
    }
}
```

`CallHelloHelperSayHello` では、`sayHello` の新しいインスタンスを使用して JavaScript 関数 `HelloHelper` を呼び出します。

*JsInteropClasses/ExampleJsInterop.cs*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

名前は `HelloHelper` のコンストラクターに渡されます。これにより、`HelloHelper.Name` プロパティが設定されます。 JavaScript 関数 `sayHello` が実行されると、`HelloHelper.SayHello` によって `Hello, {Name}!` メッセージが返されます。これは、JavaScript 関数によってコンソールに書き込まれます。

*JsInteropClasses/HelloHelper.cs*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

ブラウザーの Web 開発者ツールでのコンソール出力

```console
Hello, Blazor!
```

メモリ リークを回避し、`DotNetObjectReference` を作成するコンポーネントでガベージ コレクションを許可するには、次のいずれかの方法を採用します。

* `DotNetObjectReference` インスタンスを作成したクラスのオブジェクトを破棄します。

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime _jsRuntime;
      private DotNetObjectReference<HelloHelper> _objRef;

      public ExampleJsInterop(IJSRuntime jsRuntime)
      {
          _jsRuntime = jsRuntime;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          _objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return _jsRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              _objRef);
      }

      public void Dispose()
      {
          _objRef?.Dispose();
      }
  }
  ```

  `ExampleJsInterop` クラスに示されている上記のパターンは、コンポーネントに実装することもできます。

  ```razor
  @page "/JSInteropComponent"
  @using BlazorSample.JsInteropClasses
  @implements IDisposable
  @inject IJSRuntime JSRuntime

  <h1>JavaScript Interop</h1>

  <button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
      Trigger .NET instance method HelloHelper.SayHello
  </button>

  @code {
      private DotNetObjectReference<HelloHelper> _objRef;

      public async Task TriggerNetInstanceMethod()
      {
          _objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

          await JSRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              _objRef);
      }

      public void Dispose()
      {
          _objRef?.Dispose();
      }
  }
  ```

* コンポーネントまたはクラスによって `DotNetObjectReference` が破棄されない場合は、`.dispose()` を呼び出すことによって、クライアント上のオブジェクトを破棄します。

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a>コンポーネント インスタンス メソッドの呼び出し

コンポーネントの .NET メソッドを呼び出すには、次の手順を行います。

* コンポーネントに対して静的メソッド呼び出しを行うには、`invokeMethod` または `invokeMethodAsync` 関数を使用します。
* コンポーネントの静的メソッドにより、そのインスタンス メソッドへの呼び出しが、呼び出された `Action` としてラップされます。

クライアント側の JavaScript:

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethod('BlazorSample', 'UpdateMessageCaller');
}
```

*Pages/JSInteropComponent.razor*:

```razor
@page "/JSInteropComponent"

<p>
    Message: @_message
</p>

<p>
    <button onclick="updateMessageCallerJS()">Call JS Method</button>
</p>

@code {
    private static Action _action;
    private string _message = "Select the button.";

    protected override void OnInitialized()
    {
        _action = UpdateMessage;
    }

    private void UpdateMessage()
    {
        _message = "UpdateMessage Called!";
        StateHasChanged();
    }

    [JSInvokable]
    public static void UpdateMessageCaller()
    {
        _action.Invoke();
    }
}
```

複数のコンポーネントがあり、それぞれに呼び出すインスタンス メソッドがある場合は、ヘルパー クラスを使用して、各コンポーネントのインスタンス メソッドを (`Action` として) 呼び出します。

次に例を示します。

* `JSInterop` コンポーネントには、複数の `ListItem` コンポーネントが含まれています。
* 各 `ListItem` コンポーネントは、メッセージとボタンで構成されます。
* `ListItem` コンポーネント ボタンが選択されると、その `ListItem` の `UpdateMessage` メソッドによってリスト項目のテキストが変更され、ボタンが非表示になります。

*MessageUpdateInvokeHelper.cs*:

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action _action;

    public MessageUpdateInvokeHelper(Action action)
    {
        _action = action;
    }

    [JSInvokable("BlazorSample")]
    public void UpdateMessageCaller()
    {
        _action.Invoke();
    }
}
```

クライアント側の JavaScript:

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

*Shared/ListItem.razor*:

```razor
@inject IJSRuntime JsRuntime

<li>
    @_message
    <button @onclick="InteropCall" style="display:@_display">InteropCall</button>
</li>

@code {
    private string _message = "Select one of these list item buttons.";
    private string _display = "inline-block";
    private MessageUpdateInvokeHelper _messageUpdateInvokeHelper;

    protected override void OnInitialized()
    {
        _messageUpdateInvokeHelper = new MessageUpdateInvokeHelper(UpdateMessage);
    }

    protected async Task InteropCall()
    {
        await JsRuntime.InvokeVoidAsync("updateMessageCallerJS",
            DotNetObjectReference.Create(_messageUpdateInvokeHelper));
    }

    private void UpdateMessage()
    {
        _message = "UpdateMessage Called!";
        _display = "none";
        StateHasChanged();
    }
}
```

*Pages/JSInterop.razor*:

```razor
@page "/JSInterop"

<h1>List of components</h1>

<ul>
    <ListItem />
    <ListItem />
    <ListItem />
    <ListItem />
</ul>
```

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="additional-resources"></a>その他の技術情報

* <xref:blazor/call-javascript-from-dotnet>
* [InteropComponent.razor の例 (dotnet/AspNetCore GitHub リポジトリ、3.1 リリース ブランチ)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Blazor サーバー アプリで大規模なデータ転送を実行する](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
