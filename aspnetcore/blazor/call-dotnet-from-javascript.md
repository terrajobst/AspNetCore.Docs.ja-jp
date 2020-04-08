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
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-opno-locblazor"></a><span data-ttu-id="f41e0-103">ASP.NET Core Blazor で JavaScript 関数から .NET メソッドを呼び出す</span><span class="sxs-lookup"><span data-stu-id="f41e0-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="f41e0-104">作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Daniel Roth](https://github.com/danroth27)、[Shashikant Rudrawadi](http://wisne.co)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f41e0-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="f41e0-105">Blazor アプリでは、.NET メソッドから JavaScript 関数を呼び出すことも、JavaScript 関数から .NET メソッドを呼び出すこともできます。</span><span class="sxs-lookup"><span data-stu-id="f41e0-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="f41e0-106">これらのシナリオは、"*JavaScript 相互運用*" ("*JS 相互運用*") と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="f41e0-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="f41e0-107">この記事では、JavaScript から .NET メソッドを呼び出す方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="f41e0-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="f41e0-108">.NET から JavaScript 関数を呼び出す方法については、「<xref:blazor/call-javascript-from-dotnet>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f41e0-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="f41e0-109">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="f41e0-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="f41e0-110">静的 .NET メソッドの呼び出し</span><span class="sxs-lookup"><span data-stu-id="f41e0-110">Static .NET method call</span></span>

<span data-ttu-id="f41e0-111">JavaScript から静的 .NET メソッドを呼び出すには、`DotNet.invokeMethod` 関数または `DotNet.invokeMethodAsync` 関数を使用します。</span><span class="sxs-lookup"><span data-stu-id="f41e0-111">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="f41e0-112">呼び出す静的メソッドの識別子、関数を含むアセンブリの名前、任意の引数を渡します。</span><span class="sxs-lookup"><span data-stu-id="f41e0-112">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="f41e0-113">Blazor サーバーのシナリオをサポートするには、非同期バージョンを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="f41e0-113">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="f41e0-114">.NET メソッドはパブリックかつ静的であり、`[JSInvokable]` 属性を持つ必要があります。</span><span class="sxs-lookup"><span data-stu-id="f41e0-114">The .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="f41e0-115">オープン ジェネリック メソッドを呼び出すことは、現在サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="f41e0-115">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="f41e0-116">サンプル アプリには、`int` 配列を返す C# メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="f41e0-116">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="f41e0-117">`JSInvokable` 属性がメソッドに適用されます。</span><span class="sxs-lookup"><span data-stu-id="f41e0-117">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="f41e0-118">*Pages/JsInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="f41e0-118">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="f41e0-119">クライアントに提供される JavaScript は、C# .NET メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f41e0-119">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="f41e0-120">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="f41e0-120">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="f41e0-121">**[Trigger .NET static method ReturnArrayAsync]** ボタンが選択されている場合は、ブラウザーの Web 開発者ツールでコンソール出力を確認します。</span><span class="sxs-lookup"><span data-stu-id="f41e0-121">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="f41e0-122">コンソール出力は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="f41e0-122">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="f41e0-123">4 番目の配列値は、`data.push(4);` によって返される配列 (`ReturnArrayAsync`) にプッシュされます。</span><span class="sxs-lookup"><span data-stu-id="f41e0-123">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="f41e0-124">既定では、メソッド識別子はメソッド名ですが、`JSInvokableAttribute` コンストラクターを使用して別の識別子を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="f41e0-124">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="f41e0-125">クライアント側の JavaScript ファイル</span><span class="sxs-lookup"><span data-stu-id="f41e0-125">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

## <a name="instance-method-call"></a><span data-ttu-id="f41e0-126">インスタンス メソッドの呼び出し</span><span class="sxs-lookup"><span data-stu-id="f41e0-126">Instance method call</span></span>

<span data-ttu-id="f41e0-127">JavaScript から .NET インスタンス メソッドを呼び出すこともできます。</span><span class="sxs-lookup"><span data-stu-id="f41e0-127">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="f41e0-128">JavaScript から .NET インスタンス メソッドを呼び出すには</span><span class="sxs-lookup"><span data-stu-id="f41e0-128">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="f41e0-129">参照渡しで .NET インスタンスを JavaScript に渡します。</span><span class="sxs-lookup"><span data-stu-id="f41e0-129">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="f41e0-130">静的呼び出しを `DotNetObjectReference.Create` にします。</span><span class="sxs-lookup"><span data-stu-id="f41e0-130">Make a static call to `DotNetObjectReference.Create`.</span></span>
  * <span data-ttu-id="f41e0-131">インスタンスを `DotNetObjectReference` インスタンスにラップし、`Create` インスタンスで `DotNetObjectReference` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f41e0-131">Wrap the instance in a `DotNetObjectReference` instance and call `Create` on the `DotNetObjectReference` instance.</span></span> <span data-ttu-id="f41e0-132">`DotNetObjectReference` オブジェクトを破棄します (このセクションの後半で例を示します)。</span><span class="sxs-lookup"><span data-stu-id="f41e0-132">Dispose of `DotNetObjectReference` objects (an example appears later in this section).</span></span>
* <span data-ttu-id="f41e0-133">`invokeMethod` 関数または `invokeMethodAsync` 関数を使用して、インスタンスで .NET インスタンス メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f41e0-133">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="f41e0-134">.NET インスタンスは、JavaScript から他の .NET メソッドを呼び出すときに引数として渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="f41e0-134">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="f41e0-135">サンプル アプリでは、メッセージがクライアント側のコンソールにログ出力されます。</span><span class="sxs-lookup"><span data-stu-id="f41e0-135">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="f41e0-136">サンプル アプリで示される以下の例については、ブラウザーの開発者ツールでブラウザーのコンソール出力を確認してください。</span><span class="sxs-lookup"><span data-stu-id="f41e0-136">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="f41e0-137">**[Trigger .NET instance method HelloHelper.SayHello]** ボタンが選択されている場合、`ExampleJsInterop.CallHelloHelperSayHello` が呼び出され、メソッドに名前 `Blazor` を渡します。</span><span class="sxs-lookup"><span data-stu-id="f41e0-137">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="f41e0-138">*Pages/JsInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="f41e0-138">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="f41e0-139">`CallHelloHelperSayHello` では、`sayHello` の新しいインスタンスを使用して JavaScript 関数 `HelloHelper` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f41e0-139">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="f41e0-140">*JsInteropClasses/ExampleJsInterop.cs*:</span><span class="sxs-lookup"><span data-stu-id="f41e0-140">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="f41e0-141">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="f41e0-141">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="f41e0-142">名前は `HelloHelper` のコンストラクターに渡されます。これにより、`HelloHelper.Name` プロパティが設定されます。</span><span class="sxs-lookup"><span data-stu-id="f41e0-142">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="f41e0-143">JavaScript 関数 `sayHello` が実行されると、`HelloHelper.SayHello` によって `Hello, {Name}!` メッセージが返されます。これは、JavaScript 関数によってコンソールに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="f41e0-143">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="f41e0-144">*JsInteropClasses/HelloHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="f41e0-144">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="f41e0-145">ブラウザーの Web 開発者ツールでのコンソール出力</span><span class="sxs-lookup"><span data-stu-id="f41e0-145">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="f41e0-146">メモリ リークを回避し、`DotNetObjectReference` を作成するコンポーネントでガベージ コレクションを許可するには、次のいずれかの方法を採用します。</span><span class="sxs-lookup"><span data-stu-id="f41e0-146">To avoid a memory leak and allow garbage collection on a component that creates a `DotNetObjectReference`, adopt one of the following approaches:</span></span>

* <span data-ttu-id="f41e0-147">`DotNetObjectReference` インスタンスを作成したクラスのオブジェクトを破棄します。</span><span class="sxs-lookup"><span data-stu-id="f41e0-147">Dispose of the object in the class that created the `DotNetObjectReference` instance:</span></span>

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

  <span data-ttu-id="f41e0-148">`ExampleJsInterop` クラスに示されている上記のパターンは、コンポーネントに実装することもできます。</span><span class="sxs-lookup"><span data-stu-id="f41e0-148">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

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

* <span data-ttu-id="f41e0-149">コンポーネントまたはクラスによって `DotNetObjectReference` が破棄されない場合は、`.dispose()` を呼び出すことによって、クライアント上のオブジェクトを破棄します。</span><span class="sxs-lookup"><span data-stu-id="f41e0-149">When the component or class doesn't dispose of the `DotNetObjectReference`, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="f41e0-150">コンポーネント インスタンス メソッドの呼び出し</span><span class="sxs-lookup"><span data-stu-id="f41e0-150">Component instance method call</span></span>

<span data-ttu-id="f41e0-151">コンポーネントの .NET メソッドを呼び出すには、次の手順を行います。</span><span class="sxs-lookup"><span data-stu-id="f41e0-151">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="f41e0-152">コンポーネントに対して静的メソッド呼び出しを行うには、`invokeMethod` または `invokeMethodAsync` 関数を使用します。</span><span class="sxs-lookup"><span data-stu-id="f41e0-152">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="f41e0-153">コンポーネントの静的メソッドにより、そのインスタンス メソッドへの呼び出しが、呼び出された `Action` としてラップされます。</span><span class="sxs-lookup"><span data-stu-id="f41e0-153">The component's static method wraps the call to its instance method as an invoked `Action`.</span></span>

<span data-ttu-id="f41e0-154">クライアント側の JavaScript:</span><span class="sxs-lookup"><span data-stu-id="f41e0-154">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethod('BlazorSample', 'UpdateMessageCaller');
}
```

<span data-ttu-id="f41e0-155">*Pages/JSInteropComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="f41e0-155">*Pages/JSInteropComponent.razor*:</span></span>

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

<span data-ttu-id="f41e0-156">複数のコンポーネントがあり、それぞれに呼び出すインスタンス メソッドがある場合は、ヘルパー クラスを使用して、各コンポーネントのインスタンス メソッドを (`Action` として) 呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f41e0-156">When there are several components, each with instance methods to call, use a helper class to invoke the instance methods (as `Action`s) of each component.</span></span>

<span data-ttu-id="f41e0-157">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="f41e0-157">In the following example:</span></span>

* <span data-ttu-id="f41e0-158">`JSInterop` コンポーネントには、複数の `ListItem` コンポーネントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="f41e0-158">The `JSInterop` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="f41e0-159">各 `ListItem` コンポーネントは、メッセージとボタンで構成されます。</span><span class="sxs-lookup"><span data-stu-id="f41e0-159">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="f41e0-160">`ListItem` コンポーネント ボタンが選択されると、その `ListItem` の `UpdateMessage` メソッドによってリスト項目のテキストが変更され、ボタンが非表示になります。</span><span class="sxs-lookup"><span data-stu-id="f41e0-160">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="f41e0-161">*MessageUpdateInvokeHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="f41e0-161">*MessageUpdateInvokeHelper.cs*:</span></span>

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

<span data-ttu-id="f41e0-162">クライアント側の JavaScript:</span><span class="sxs-lookup"><span data-stu-id="f41e0-162">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="f41e0-163">*Shared/ListItem.razor*:</span><span class="sxs-lookup"><span data-stu-id="f41e0-163">*Shared/ListItem.razor*:</span></span>

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

<span data-ttu-id="f41e0-164">*Pages/JSInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="f41e0-164">*Pages/JSInterop.razor*:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="f41e0-165">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="f41e0-165">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="f41e0-166">InteropComponent.razor の例 (dotnet/AspNetCore GitHub リポジトリ、3.1 リリース ブランチ)</span><span class="sxs-lookup"><span data-stu-id="f41e0-166">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="f41e0-167">[Blazor サーバー アプリで大規模なデータ転送を実行する](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="f41e0-167">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
