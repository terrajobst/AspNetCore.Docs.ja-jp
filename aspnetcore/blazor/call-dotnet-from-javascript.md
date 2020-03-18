---
title: ASP.NET Core Blazor で JavaScript 関数から .NET メソッドを呼び出す
author: guardrex
description: Blazor アプリで JavaScript 関数から .NET メソッドを呼び出す方法について学習します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/19/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: f4964341e261c65269eedafbbd6e676c1054f427
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78647408"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-opno-locblazor"></a><span data-ttu-id="07f50-103">ASP.NET Core Blazor で JavaScript 関数から .NET メソッドを呼び出す</span><span class="sxs-lookup"><span data-stu-id="07f50-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="07f50-104">作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="07f50-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="07f50-105">Blazor アプリでは、.NET メソッドから JavaScript 関数を呼び出すことも、JavaScript 関数から .NET メソッドを呼び出すこともできます。</span><span class="sxs-lookup"><span data-stu-id="07f50-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="07f50-106">これらのシナリオは、"*JavaScript 相互運用*" ("*JS 相互運用*") と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="07f50-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="07f50-107">この記事では、JavaScript から .NET メソッドを呼び出す方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="07f50-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="07f50-108">.NET から JavaScript 関数を呼び出す方法については、「<xref:blazor/call-javascript-from-dotnet>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="07f50-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="07f50-109">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="07f50-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="07f50-110">静的 .NET メソッドの呼び出し</span><span class="sxs-lookup"><span data-stu-id="07f50-110">Static .NET method call</span></span>

<span data-ttu-id="07f50-111">JavaScript から静的 .NET メソッドを呼び出すには、`DotNet.invokeMethod` 関数または `DotNet.invokeMethodAsync` 関数を使用します。</span><span class="sxs-lookup"><span data-stu-id="07f50-111">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="07f50-112">呼び出す静的メソッドの識別子、関数を含むアセンブリの名前、任意の引数を渡します。</span><span class="sxs-lookup"><span data-stu-id="07f50-112">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="07f50-113">Blazor サーバーのシナリオをサポートするには、非同期バージョンを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="07f50-113">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="07f50-114">.NET メソッドはパブリックかつ静的であり、`[JSInvokable]` 属性を持つ必要があります。</span><span class="sxs-lookup"><span data-stu-id="07f50-114">The .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="07f50-115">オープン ジェネリック メソッドを呼び出すことは、現在サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="07f50-115">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="07f50-116">サンプル アプリには、`int` 配列を返す C# メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="07f50-116">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="07f50-117">`JSInvokable` 属性がメソッドに適用されます。</span><span class="sxs-lookup"><span data-stu-id="07f50-117">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="07f50-118">*Pages/JsInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="07f50-118">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="07f50-119">クライアントに提供される JavaScript は、C# .NET メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="07f50-119">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="07f50-120">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="07f50-120">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="07f50-121">**[Trigger .NET static method ReturnArrayAsync]** ボタンが選択されている場合は、ブラウザーの Web 開発者ツールでコンソール出力を確認します。</span><span class="sxs-lookup"><span data-stu-id="07f50-121">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="07f50-122">コンソール出力は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="07f50-122">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="07f50-123">4 番目の配列値は、`ReturnArrayAsync` によって返される配列 (`data.push(4);`) にプッシュされます。</span><span class="sxs-lookup"><span data-stu-id="07f50-123">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="07f50-124">既定では、メソッド識別子はメソッド名ですが、`JSInvokableAttribute` コンストラクターを使用して別の識別子を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="07f50-124">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="07f50-125">クライアント側の JavaScript ファイル</span><span class="sxs-lookup"><span data-stu-id="07f50-125">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

## <a name="instance-method-call"></a><span data-ttu-id="07f50-126">インスタンス メソッドの呼び出し</span><span class="sxs-lookup"><span data-stu-id="07f50-126">Instance method call</span></span>

<span data-ttu-id="07f50-127">JavaScript から .NET インスタンス メソッドを呼び出すこともできます。</span><span class="sxs-lookup"><span data-stu-id="07f50-127">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="07f50-128">JavaScript から .NET インスタンス メソッドを呼び出すには</span><span class="sxs-lookup"><span data-stu-id="07f50-128">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="07f50-129">参照渡しで .NET インスタンスを JavaScript に渡します。</span><span class="sxs-lookup"><span data-stu-id="07f50-129">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="07f50-130">静的呼び出しを `DotNetObjectReference.Create` にします。</span><span class="sxs-lookup"><span data-stu-id="07f50-130">Make a static call to `DotNetObjectReference.Create`.</span></span>
  * <span data-ttu-id="07f50-131">インスタンスを `DotNetObjectReference` インスタンスにラップし、`DotNetObjectReference` インスタンスで `Create` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="07f50-131">Wrap the instance in a `DotNetObjectReference` instance and call `Create` on the `DotNetObjectReference` instance.</span></span> <span data-ttu-id="07f50-132">`DotNetObjectReference` オブジェクトを破棄します (このセクションの後半で例を示します)。</span><span class="sxs-lookup"><span data-stu-id="07f50-132">Dispose of `DotNetObjectReference` objects (an example appears later in this section).</span></span>
* <span data-ttu-id="07f50-133">`invokeMethod` 関数または `invokeMethodAsync` 関数を使用して、インスタンスで .NET インスタンス メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="07f50-133">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="07f50-134">.NET インスタンスは、JavaScript から他の .NET メソッドを呼び出すときに引数として渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="07f50-134">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="07f50-135">サンプル アプリでは、メッセージがクライアント側のコンソールにログ出力されます。</span><span class="sxs-lookup"><span data-stu-id="07f50-135">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="07f50-136">サンプル アプリで示される以下の例については、ブラウザーの開発者ツールでブラウザーのコンソール出力を確認してください。</span><span class="sxs-lookup"><span data-stu-id="07f50-136">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="07f50-137">**[Trigger .NET instance method HelloHelper.SayHello]** ボタンが選択されている場合、`ExampleJsInterop.CallHelloHelperSayHello` が呼び出され、メソッドに名前 `Blazor` を渡します。</span><span class="sxs-lookup"><span data-stu-id="07f50-137">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="07f50-138">*Pages/JsInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="07f50-138">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="07f50-139">`CallHelloHelperSayHello` では、`HelloHelper` の新しいインスタンスを使用して JavaScript 関数 `sayHello` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="07f50-139">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="07f50-140">*JsInteropClasses/ExampleJsInterop.cs*:</span><span class="sxs-lookup"><span data-stu-id="07f50-140">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

<span data-ttu-id="07f50-141">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="07f50-141">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="07f50-142">名前は `HelloHelper` のコンストラクターに渡されます。これにより、`HelloHelper.Name` プロパティが設定されます。</span><span class="sxs-lookup"><span data-stu-id="07f50-142">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="07f50-143">JavaScript 関数 `sayHello` が実行されると、`HelloHelper.SayHello` によって `Hello, {Name}!` メッセージが返されます。これは、JavaScript 関数によってコンソールに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="07f50-143">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="07f50-144">*JsInteropClasses/HelloHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="07f50-144">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="07f50-145">ブラウザーの Web 開発者ツールでのコンソール出力</span><span class="sxs-lookup"><span data-stu-id="07f50-145">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="07f50-146">メモリ リークを回避し、`DotNetObjectReference` を作成するコンポーネントでガベージ コレクションを許可するには、`DotNetObjectReference` インスタンスを作成したクラスでオブジェクトを破棄します。</span><span class="sxs-lookup"><span data-stu-id="07f50-146">To avoid a memory leak and allow garbage collection on a component that creates a `DotNetObjectReference`, dispose of the object in the class that created the `DotNetObjectReference` instance:</span></span>

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
  
<span data-ttu-id="07f50-147">`ExampleJsInterop` クラスに示されている上記のパターンは、コンポーネントに実装することもできます。</span><span class="sxs-lookup"><span data-stu-id="07f50-147">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>
  
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

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="additional-resources"></a><span data-ttu-id="07f50-148">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="07f50-148">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="07f50-149">InteropComponent.razor の例 (dotnet/AspNetCore GitHub リポジトリ、3.1 リリース ブランチ)</span><span class="sxs-lookup"><span data-stu-id="07f50-149">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="07f50-150">[Blazor サーバー アプリで大規模なデータ転送を実行する](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="07f50-150">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
