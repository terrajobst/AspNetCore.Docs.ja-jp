---
title: ASP.NET Core Blazor JavaScript 相互運用機能
author: guardrex
description: Blazor アプリで JavaScript から .NET および .NET メソッドから JavaScript 関数を呼び出す方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/javascript-interop
ms.openlocfilehash: d681eea5a5e876912bd614fba8ea45a464844496
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447166"
---
# <a name="aspnet-core-opno-locblazor-javascript-interop"></a>ASP.NET Core Blazor JavaScript 相互運用機能

[Javier Calvarro jeannine](https://github.com/javiercn)、 [Daniel Roth](https://github.com/danroth27)、 [Luke latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor アプリは、JavaScript コードから .NET および .NET メソッドから JavaScript 関数を呼び出すことができます。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="invoke-javascript-functions-from-net-methods"></a>.NET メソッドから JavaScript 関数を呼び出す

JavaScript 関数を呼び出すために .NET コードが必要になる場合があります。 たとえば、JavaScript 呼び出しは、ブラウザーの機能や機能を JavaScript ライブラリからアプリに公開できます。 このシナリオは、 *JavaScript 相互運用性*(*JS 相互運用*) と呼ばれます。

.NET から JavaScript を呼び出すには、`IJSRuntime` 抽象化を使用します。 JS 相互運用呼び出しを発行するには、コンポーネントに `IJSRuntime` の抽象化を挿入します。 `InvokeAsync<T>` メソッドは、任意の数の JSON シリアル化可能な引数と共に呼び出す JavaScript 関数の識別子を受け取ります。 関数識別子は、グローバルスコープ (`window`) に対して相対的です。 `window.someScope.someFunction`を呼び出す場合は、識別子が `someScope.someFunction`ます。 呼び出される前に、関数を登録する必要はありません。 戻り値の型 `T` も JSON シリアル化可能である必要があります。 `T` は、返される JSON 型に最適にマップされる .NET 型と一致している必要があります。

プリレンダリングが有効になっている Blazor サーバーアプリの場合、初期のプリレンダリング中に JavaScript を呼び出すことはできません。 JavaScript の相互運用呼び出しは、ブラウザーとの接続が確立されるまで遅延させる必要があります。 詳細については、「 [Blazor アプリのプリレンダリングを検出する](#detect-when-a-blazor-app-is-prerendering)」セクションを参照してください。

次の例は、JavaScript ベースの試験的なデコーダーである[Textdecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder)に基づいています。 この例では、 C#メソッドから JavaScript 関数を呼び出す方法を示します。 JavaScript 関数は、 C#メソッドからバイト配列を受け取り、配列をデコードし、テキストをコンポーネントに返して表示できるようにします。

*Wwwroot/index.html* (Blazor WebAssembly または*Pages/_Host* (Blazor Server) の `<head>` 要素内では、`TextDecoder` を使用して渡された配列をデコードし、デコードされた値を返す JavaScript 関数を提供します。

[!code-html[](javascript-interop/samples_snapshot/index-script-convertarray.html)]

前の例で示したコードなどの JavaScript コードは、スクリプトファイルへの参照を使用して JavaScript ファイル ( *.js*) から読み込むこともできます。

```html
<script src="exampleJsInterop.js"></script>
```

次のコンポーネント:

* コンポーネントボタン (**配列の変換**) が選択されている場合に `JSRuntime` を使用して `convertArray` JavaScript 関数を呼び出します。
* JavaScript 関数が呼び出されると、渡された配列が文字列に変換されます。 文字列は、表示のためにコンポーネントに返されます。

[!code-razor[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="use-of-ijsruntime"></a>IJSRuntime の使用

`IJSRuntime` の抽象化を使用するには、次のいずれかの方法を採用します。

* Razor コンポーネント (*razor*) に `IJSRuntime` の抽象化を挿入します。

  [!code-razor[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

  *Wwwroot/index.html* (Blazor WebAssembly または*Pages/_Host* (Blazor Server) の `<head>` 要素内で、`handleTickerChanged` JavaScript 関数を提供します。 関数は `IJSRuntime.InvokeVoidAsync` と共に呼び出され、値を返しません。

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged1.html)]

* `IJSRuntime` の抽象化をクラス ( *.cs*) に挿入します。

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  *Wwwroot/index.html* (Blazor WebAssembly または*Pages/_Host* (Blazor Server) の `<head>` 要素内で、`handleTickerChanged` JavaScript 関数を提供します。 関数は `JSRuntime.InvokeAsync` を指定して呼び出され、値を返します。

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged2.html)]

* [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)を使用した動的なコンテンツ生成の場合は、`[Inject]` 属性を使用します。

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

このトピックに付属しているクライアント側のサンプルアプリでは、ユーザー入力を受け取り、ウェルカムメッセージを表示するために、DOM と対話する2つの JavaScript 関数をアプリで使用できます。

* &ndash; を `showPrompt` と、ユーザー入力 (ユーザーの名前) を受け取り、呼び出し元に名前を返すように求めるプロンプトが生成されます。
* `displayWelcome` &ndash; は、`welcome`の `id` を使用して、呼び出し元からのウェルカムメッセージを DOM オブジェクトに割り当てます。

*wwwroot/exampleJsInterop*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

JavaScript ファイルを参照する `<script>` タグを、 *wwwroot/index.html*ファイル (Blazor WebAssembly または*Pages/_Host* (Blazor Server) ファイルに配置します。

*wwwroot/index.html* (Blazor webas):

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

*Pages/_Host* (Blazor サーバー):

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

`<script>` タグを動的に更新できないため、コンポーネントファイルに `<script>` タグを配置しないでください。

.NET メソッドは、`IJSRuntime.InvokeAsync<T>`を呼び出すことによって、 *exampleJsInterop*ファイル内の JavaScript 関数と相互運用します。

`IJSRuntime` の抽象化は、Blazor サーバーのシナリオを可能にする非同期です。 アプリが Blazor Webasのアプリで、JavaScript 関数を同期的に呼び出す必要がある場合は、`IJSInProcessRuntime` にダウンキャストし、代わりに `Invoke<T>` を呼び出します。 ほとんどの JS 相互運用機能ライブラリでは、非同期 Api を使用して、すべてのシナリオでライブラリを使用できるようにすることをお勧めします。

サンプルアプリには、JS 相互運用機能を示すためのコンポーネントが含まれています。 コンポーネント:

* JavaScript プロンプトを使用してユーザー入力を受け取ります。
* 処理するテキストをコンポーネントに返します。
* DOM と対話してウェルカムメッセージを表示する2番目の JavaScript 関数を呼び出します。

*Pages/JSInterop*:

```razor
@page "/JSInterop"
@using BlazorSample.JsInteropClasses
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        // showPrompt is implemented in wwwroot/exampleJsInterop.js
        var name = await JSRuntime.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");
        // displayWelcome is implemented in wwwroot/exampleJsInterop.js
        await JSRuntime.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

1. コンポーネントの **[トリガー JavaScript プロンプト]** ボタンを選択して `TriggerJsPrompt` を実行すると、 *wwwroot/exampleJsInterop*ファイルで提供されている javascript `showPrompt` 関数が呼び出されます。
1. `showPrompt` 関数は、HTML エンコードされ、コンポーネントに返されるユーザー入力 (ユーザーの名前) を受け取ります。 コンポーネントは、ユーザーの名前をローカル変数 `name`に格納します。
1. `name` に格納されている文字列は、ウェルカムメッセージに組み込まれます。このメッセージは、JavaScript 関数に渡されます。 `displayWelcome`は、ウェルカムメッセージを見出しタグにレンダリングします。

## <a name="call-a-void-javascript-function"></a>Void JavaScript 関数を呼び出す

[Void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void)または[Undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined)を返す JavaScript 関数は、`IJSRuntime.InvokeVoidAsync`で呼び出されます。

## <a name="detect-when-a-opno-locblazor-app-is-prerendering"></a>Blazor アプリがプリレンダリングされるタイミングを検出する
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>要素への参照をキャプチャする

一部の JS 相互運用シナリオでは、HTML 要素への参照が必要です。 たとえば、UI ライブラリに初期化のための要素参照が必要な場合や、`focus` や `play`などの要素に対してコマンドに似た Api を呼び出す必要がある場合があります。

次の方法を使用して、コンポーネント内の HTML 要素への参照をキャプチャします。

* `@ref` 属性を HTML 要素に追加します。
* `@ref` 属性の値に一致する名前を持つ `ElementReference` 型のフィールドを定義します。

次の例は、`username` `<input>` 要素への参照をキャプチャする方法を示しています。

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> Blazorと対話しない空の要素の内容を変化させるには、要素参照のみを使用します。 このシナリオは、サードパーティの API が要素にコンテンツを提供する場合に便利です。 Blazor は要素と対話しないため、Blazorが要素と DOM を表現している間に競合が発生する可能性はありません。
>
> 次の例では、Blazor が DOM とやり取りしてこの要素のリスト項目 (`<li>`) を設定するため、順序なしのリスト (`ul`) の内容を変化させるのは*危険*です。
>
> ```razor
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> JS 相互運用機能によって要素の内容が `MyList` され、その要素に差分を適用しようと Blazor 試みた場合、相違は DOM と一致しません。

.NET コードに関しては、`ElementReference` は不透明なハンドルです。 `ElementReference` で行うことができるのは、JS 相互運用機能を使用して JavaScript コードに渡すこと*だけ*です。 これを行うと、JavaScript 側のコードは `HTMLElement` インスタンスを受け取ります。このインスタンスは、通常の DOM Api と共に使用できます。

たとえば、次のコードでは、要素にフォーカスを設定できるようにする .NET 拡張メソッドを定義しています。

*exampleJsInterop*:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

値を返さない JavaScript 関数を呼び出すには、`IJSRuntime.InvokeVoidAsync`を使用します。 次のコードは、キャプチャされた `ElementReference`で前の JavaScript 関数を呼び出すことによって、ユーザー名入力にフォーカスを設定します。

[!code-razor[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,11-12)]

拡張メソッドを使用するには、`IJSRuntime` インスタンスを受け取る静的拡張メソッドを作成します。

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

`Focus` メソッドは、オブジェクトに対して直接呼び出されます。 次の例では、`Focus` メソッドが `JsInteropClasses` 名前空間から使用できることを前提としています。

[!code-razor[](javascript-interop/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> `username` 変数は、コンポーネントがレンダリングされた後にのみ設定されます。 JavaScript コードにいない `ElementReference` が渡されると、JavaScript コードは `null`の値を受け取ります。 コンポーネントのレンダリングが完了した後に要素参照を操作するには (要素に初期フォーカスを設定するには)、 [OnAfterRenderAsync または OnAfterRender コンポーネントライフサイクルメソッド](xref:blazor/lifecycle#after-component-render)を使用します。

ジェネリック型を操作して値を返す場合は、 [Valuetask\<t >](xref:System.Threading.Tasks.ValueTask`1)を使用します。

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

`GenericMethod` は、型を使用してオブジェクトに対して直接呼び出されます。 次の例では、`GenericMethod` が `JsInteropClasses` 名前空間から使用できることを前提としています。

[!code-razor[](javascript-interop/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a>コンポーネント間での参照要素

`ElementReference` は、コンポーネントの `OnAfterRender` メソッドでは有効であることが保証されます (および要素参照は `struct`)。そのため、コンポーネント間で要素参照を渡すことはできません。

親コンポーネントが要素参照を他のコンポーネントで使用できるようにするために、親コンポーネントは次のことを実行できます。

* 子コンポーネントがコールバックを登録できるようにします。
* 渡された要素参照を使用して、`OnAfterRender` イベント中に登録されたコールバックを呼び出します。 間接的には、この方法により、子コンポーネントが親要素の参照と対話できるようになります。

次の Blazor の例は、この方法を示しています。

*Wwwroot/index.html*の `<head>`:

```html
<style>
    .red { color: red }
</style>
```

*Wwwroot/index.html*の `<body>`:

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

*Pages/Index. razor* (親コンポーネント):

```razor
@page "/"

<h1 @ref="_title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

*Pages/Index. razor. .cs*:

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool _disposing;
        private IList<IObserver<ElementReference>> _subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference _title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in _subscriptions)
            {
                try
                {
                    subscription.OnNext(_title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            _disposing = true;

            foreach (var subscription in _subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            _subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (_disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            _subscriptions.Add(observer);

            return new Subscription(observer, this);
        }

        private class Subscription : IDisposable
        {
            public Subscription(IObserver<ElementReference> observer, Index self)
            {
                Observer = observer;
                Self = self;
            }

            public IObserver<ElementReference> Observer { get; }
            public Index Self { get; }

            public void Dispose()
            {
                Self._subscriptions.Remove(Observer);
            }
        }
    }
}
```

*Shared/SurveyPrompt* (子コンポーネント):

```razor
@inject IJSRuntime JS

<div class="alert alert-secondary mt-4" role="alert">
    <span class="oi oi-pencil mr-2" aria-hidden="true"></span>
    <strong>@Title</strong>

    <span class="text-nowrap">
        Please take our
        <a target="_blank" class="font-weight-bold" 
            href="https://go.microsoft.com/fwlink/?linkid=2109206">brief survey</a>
    </span>
    and tell us what you think.
</div>

@code {
    [Parameter]
    public string Title { get; set; }
}
```

*Shared/SurveyPrompt*:

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable _subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (_subscription != null)
            {
                _subscription.Dispose();
            }

            _subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            _subscription = null;
        }

        public void OnError(Exception error)
        {
            _subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            _subscription?.Dispose();
        }
    }
}
```

## <a name="invoke-net-methods-from-javascript-functions"></a>JavaScript 関数からの .NET メソッドの呼び出し

### <a name="static-net-method-call"></a>静的 .NET メソッド呼び出し

JavaScript から静的 .NET メソッドを呼び出すには、`DotNet.invokeMethod` または `DotNet.invokeMethodAsync` 関数を使用します。 呼び出す静的メソッドの識別子、関数を含むアセンブリの名前、および任意の引数を渡します。 Blazor サーバーのシナリオをサポートするには、非同期バージョンを使用することをお勧めします。 .NET メソッドは、public、static、および `[JSInvokable]` 属性を持っている必要があります。 オープンジェネリックメソッドを呼び出すことは現在サポートされていません。

このサンプルアプリにはC# `int`s の配列を返すメソッドが含まれています。 `JSInvokable` 属性がメソッドに適用されます。

*Pages/JsInterop*:

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

クライアントに提供される JavaScript はC# 、.net メソッドを呼び出します。

*wwwroot/exampleJsInterop*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

**[.Net 静的メソッド ReturnArrayAsync のトリガー]** ボタンが選択されている場合は、ブラウザーの web developer ツールでコンソール出力を確認します。

コンソールの出力は次のとおりです。

```console
Array(4) [ 1, 2, 3, 4 ]
```

4番目の配列値は、`ReturnArrayAsync`によって返される配列 (`data.push(4);`) にプッシュされます。

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

クライアント側の JavaScript ファイルで、次のようにします。

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

### <a name="instance-method-call"></a>インスタンスメソッドの呼び出し

JavaScript から .NET インスタンスメソッドを呼び出すこともできます。 JavaScript から .NET インスタンスメソッドを呼び出すには、次の手順を実行します。

* .NET インスタンスを JavaScript への参照によって渡します。
  * `DotNetObjectReference.Create`の静的な呼び出しを行います。
  * インスタンスを `DotNetObjectReference` インスタンスにラップし、`DotNetObjectReference` インスタンスで `Create` を呼び出します。 `DotNetObjectReference` オブジェクトを破棄します (このセクションの後半で例を示します)。
* `invokeMethod` または `invokeMethodAsync` 関数を使用して、インスタンスで .NET インスタンスメソッドを呼び出します。 .NET インスタンスは、JavaScript から他の .NET メソッドを呼び出すときに引数として渡すこともできます。

> [!NOTE]
> サンプルアプリは、メッセージをクライアント側コンソールに記録します。 サンプルアプリで示されている次の例については、ブラウザーの開発者ツールでブラウザーのコンソール出力を確認してください。

**[.Net インスタンスメソッドをトリガーする HelloHelper]** ボタンを選択すると、`ExampleJsInterop.CallHelloHelperSayHello` が呼び出され、`Blazor`の名前がメソッドに渡されます。

*Pages/JsInterop*:

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

`CallHelloHelperSayHello` は、`HelloHelper`の新しいインスタンスを使用して JavaScript 関数 `sayHello` を呼び出します。

*JsInteropClasses/ExampleJsInterop*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

*wwwroot/exampleJsInterop*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

名前は `HelloHelper`のコンストラクターに渡され、`HelloHelper.Name` プロパティが設定されます。 JavaScript 関数 `sayHello` が実行されると、`HelloHelper.SayHello` は `Hello, {Name}!` メッセージを返します。このメッセージは、JavaScript 関数によってコンソールに書き込まれます。

*JsInteropClasses/HelloHelper*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

ブラウザーの web 開発者ツールのコンソール出力:

```console
Hello, Blazor!
```

メモリリークを回避し、`DotNetObjectReference`を作成するコンポーネントでガベージコレクションを許可するには、`DotNetObjectReference` インスタンスを作成したクラスでオブジェクトを破棄します。

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

## <a name="share-interop-code-in-a-class-library"></a>クラスライブラリで相互運用コードを共有する

JS 相互運用コードは、NuGet パッケージ内のコードを共有できるクラスライブラリに含めることができます。

クラスライブラリは、ビルドされたアセンブリに JavaScript リソースの埋め込みを処理します。 JavaScript ファイルは*wwwroot*フォルダーに配置されます。 ツールは、ライブラリの構築時にリソースの埋め込みを行います。

ビルド済みの NuGet パッケージは、NuGet パッケージを参照するのと同じ方法で、アプリのプロジェクトファイルで参照されます。 パッケージが復元された後、アプリコードはと同じように JavaScript C#を呼び出すことができます。

詳細については、「<xref:blazor/class-libraries>」を参照してください。

## <a name="harden-js-interop-calls"></a>JS 相互運用呼び出しの強化

JS 相互運用機能は、ネットワークエラーのために失敗する可能性があり、信頼性の低いものとして扱う必要があります。 既定では、Blazor サーバーアプリは、1分後に JS 相互運用機能の呼び出しをサーバー上でタイムアウトします。 10秒など、アプリでより積極的なタイムアウトが許容される場合は、次のいずれかの方法を使用してタイムアウトを設定します。

* `Startup.ConfigureServices`でグローバルに、タイムアウトを指定します。

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* コンポーネントコードでの呼び出しごとに、1回の呼び出しでタイムアウトを指定できます。

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

リソース枯渇の詳細については、「<xref:security/blazor/server>」を参照してください。

## <a name="perform-large-data-transfers-in-opno-locblazor-server-apps"></a>Blazor Server アプリで大規模なデータ転送を実行する

場合によっては、JavaScript と Blazorの間で大量のデータを転送する必要があります。 通常、大規模なデータ転送は次の場合に発生します。

* ブラウザーファイルシステム Api は、ファイルをアップロードまたはダウンロードするために使用されます。
* サードパーティのライブラリとの相互運用が必要です。

Blazor Server では、パフォーマンスの問題につながる可能性のある1つの大きなメッセージを渡すことができないように制限されています。

JavaScript と Blazor間でデータを転送するコードを開発するときは、次のガイダンスを考慮してください。

* データをより小さな部分に分割し、すべてのデータがサーバーによって受信されるまでデータセグメントを順番に送信します。
* JavaScript およびC#コードでラージオブジェクトを割り当てないでください。
* データを送受信するときに、メイン UI スレッドを長時間ブロックしないでください。
* プロセスの完了時またはキャンセル時に消費されるメモリを解放します。
* セキュリティ上の理由から、次の追加要件を適用します。
  * 渡すことのできるファイルまたはデータの最大サイズを宣言します。
  * クライアントからサーバーへの最小アップロードレートを宣言します。
* データがサーバーによって受信された後、データは次のようになります。
  * すべてのセグメントが収集されるまで、一時的にメモリバッファーに格納されます。
  * 直ちに消費されます。 たとえば、データは、データベースに直ちに格納することも、各セグメントを受信するたびにディスクに書き込むこともできます。

次の file アップローダークラスは、クライアントとの JS 相互運用機能を処理します。 アップローダークラスは、JS 相互運用機能を使用して次のことを行います。

* データセグメントを送信するクライアントをポーリングします。
* ポーリングがタイムアウトした場合は、トランザクションを中止します。

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime _jsRuntime;
    private readonly int _segmentSize = 6144;
    private readonly int _maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> _thisReference;
    private List<IMemoryOwner<byte>> _uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        _jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await _jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)_segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await _jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < _maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(_segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, _segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          _uploadedSegments.Add(current);
        }

        var segments = _uploadedSegments;
        _uploadedSegments = null;

        return new SegmentedStream(segments, _segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (_uploadedSegments != null)
        {
            foreach (var segment in _uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

前の例の場合:

* `_maxBase64SegmentSize` は `8192`に設定されます。これは `_maxBase64SegmentSize = _segmentSize * 4 / 3`から計算されます。
* 低レベルの .NET Core メモリ管理 Api は、`_uploadedSegments`のサーバーにメモリセグメントを格納するために使用されます。
* `ReceiveFile` メソッドは、JS 相互運用機能によるアップロードを処理するために使用されます。
  * ファイルサイズは、`_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`との JS 相互運用によってバイト単位で決定されます。
  * 受信するセグメントの数が計算され、`numberOfSegments`に格納されます。
  * セグメントは、`_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`との JS 相互運用を通じて `for` ループで要求されます。 デコードする前に、すべてのセグメントが8192バイトである必要があります。 クライアントは、効率的な方法でデータを送信することを強制されます。
  * 受信した各セグメントに対して、<xref:System.Convert.TryFromBase64String*>でデコードする前にチェックが実行されます。
  * アップロードが完了すると、データを含むストリームが新しい <xref:System.IO.Stream> (`SegmentedStream`) として返されます。

セグメント化されたストリームクラスは、セグメントの一覧を読み取り専用ではない <xref:System.IO.Stream>として公開します。

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> _sequence;
    private long _currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            _sequence = new ReadOnlySequence<byte>(
                segments[0].Memory.Slice(0, lastSegmentSize));
            return;
        }

        var sequenceSegment = new BufferSegment<byte>(
            segments[0].Memory.Slice(0, segmentSize));
        var lastSegment = sequenceSegment;

        for (int i = 1; i < segments.Count; i++)
        {
            var isLastSegment = i + 1 == segments.Count;
            lastSegment = lastSegment.Append(segments[i].Memory.Slice(
                0, isLastSegment ? lastSegmentSize : segmentSize));
        }

        _sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(_currentPosition + count < _sequence.Length ? 
            count : _sequence.Length - _currentPosition);
        var data = _sequence.Slice(_currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        _currentPosition += bytesToWrite;

        return bytesToWrite;
    }

    private class BufferSegment<T> : ReadOnlySequenceSegment<T>
    {
        public BufferSegment(ReadOnlyMemory<T> memory)
        {
            Memory = memory;
        }

        public BufferSegment<T> Append(ReadOnlyMemory<T> memory)
        {
            var segment = new BufferSegment<T>(memory)
            {
                RunningIndex = RunningIndex + Memory.Length
            };

            Next = segment;

            return segment;
        }
    }

    public override bool CanRead => true;

    public override bool CanSeek => false;

    public override bool CanWrite => false;

    public override long Length => throw new NotImplementedException();

    public override void Flush() => throw new NotImplementedException();

    public override long Seek(long offset, SeekOrigin origin) => 
        throw new NotImplementedException();

    public override void SetLength(long value) => 
        throw new NotImplementedException();

    public override void Write(byte[] buffer, int offset, int count) => 
        throw new NotImplementedException();
}
```

次のコードは、データを受け取る JavaScript 関数を実装しています。

```javascript
function getFileSize(selector) {
  const file = getFile(selector);
  return file.size;
}

async function receiveSegment(segmentNumber, selector) {
  const file = getFile(selector);
  var segments = getFileSegments(file);
  var index = segmentNumber * 6144;
  return await getNextChunk(file, index);
}

function getFile(selector) {
  const element = document.querySelector(selector);
  if (!element) {
    throw new Error('Invalid selector');
  }
  const files = element.files;
  if (!files || files.length === 0) {
    throw new Error(`Element ${elementId} doesn't contain any files.`);
  }
  const file = files[0];
  return file;
}

function getFileSegments(file) {
  const segments = Math.floor(size % 6144 === 0 ? size / 6144 : 1 + size / 6144);
  return segments;
}

async function getNextChunk(file, index) {
  const length = file.size - index <= 6144 ? file.size - index : 6144;
  const chunk = file.slice(index, index + length);
  index += length;
  const base64Chunk = await this.base64EncodeAsync(chunk);
  return { base64Chunk, index };
}

async function base64EncodeAsync(chunk) {
  const reader = new FileReader();
  const result = new Promise((resolve, reject) => {
    reader.addEventListener('load',
      () => {
        const base64Chunk = reader.result;
        const cleanChunk = 
          base64Chunk.replace('data:application/octet-stream;base64,', '');
        resolve(cleanChunk);
      },
      false);
    reader.addEventListener('error', reject);
  });
  reader.readAsDataURL(chunk);
  return result;
}
```

## <a name="additional-resources"></a>その他のリソース

* [InteropComponent の例 (dotnet/AspNetCore GitHub リポジトリ、3.1 リリースブランチ)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
