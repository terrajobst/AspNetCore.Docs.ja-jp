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
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-opno-locblazor"></a>ASP.NET Core Blazor で JavaScript 関数から .NET メソッドを呼び出す

作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)

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

4 番目の配列値は、`ReturnArrayAsync` によって返される配列 (`data.push(4);`) にプッシュされます。

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
  * インスタンスを `DotNetObjectReference` インスタンスにラップし、`DotNetObjectReference` インスタンスで `Create` を呼び出します。 `DotNetObjectReference` オブジェクトを破棄します (このセクションの後半で例を示します)。
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

`CallHelloHelperSayHello` では、`HelloHelper` の新しいインスタンスを使用して JavaScript 関数 `sayHello` を呼び出します。

*JsInteropClasses/ExampleJsInterop.cs*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

名前は `HelloHelper` のコンストラクターに渡されます。これにより、`HelloHelper.Name` プロパティが設定されます。 JavaScript 関数 `sayHello` が実行されると、`HelloHelper.SayHello` によって `Hello, {Name}!` メッセージが返されます。これは、JavaScript 関数によってコンソールに書き込まれます。

*JsInteropClasses/HelloHelper.cs*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

ブラウザーの Web 開発者ツールでのコンソール出力

```console
Hello, Blazor!
```

メモリ リークを回避し、`DotNetObjectReference` を作成するコンポーネントでガベージ コレクションを許可するには、`DotNetObjectReference` インスタンスを作成したクラスでオブジェクトを破棄します。

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

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="additional-resources"></a>その他の技術情報

* <xref:blazor/call-javascript-from-dotnet>
* [InteropComponent.razor の例 (dotnet/AspNetCore GitHub リポジトリ、3.1 リリース ブランチ)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Blazor サーバー アプリで大規模なデータ転送を実行する](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
