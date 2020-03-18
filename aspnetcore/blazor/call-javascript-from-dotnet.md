---
title: ASP.NET Core Blazor で .NET メソッドから JavaScript 関数を呼び出す
author: guardrex
description: Blazor アプリで .NET メソッドから JavaScript 関数を呼び出す方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/19/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 7a27b6f1be2ef296d5b2b2a4f566e0cdedbe6480
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78647522"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-opno-locblazor"></a>ASP.NET Core Blazor で .NET メソッドから JavaScript 関数を呼び出す

作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor アプリでは、.NET メソッドから JavaScript 関数を呼び出すことも、JavaScript 関数から .NET メソッドを呼び出すこともできます。 これらのシナリオは、"*JavaScript 相互運用*" ("*JS 相互運用*") と呼ばれます。

この記事では、.NET から JavaScript 関数を呼び出す方法について説明します。 JavaScript から .NET メソッドを呼び出す方法については、「<xref:blazor/call-dotnet-from-javascript>」を参照してください。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

.NET から JavaScript を呼び出すには、`IJSRuntime` 抽象化を使用します。 JS 相互運用呼び出しを発行するには、コンポーネントに `IJSRuntime` 抽象化を挿入します。 `InvokeAsync<T>` メソッドでは、JSON シリアル化可能な任意の数の引数と共に呼び出す JavaScript 関数の識別子を受け取ります。 関数の識別子は、グローバル スコープ (`window`) に関連しています。 `window.someScope.someFunction` を呼び出す場合、識別子は `someScope.someFunction` です。 関数は、呼び出す前に登録する必要はありません。 また、戻り値の型 `T` も JSON シリアル化可能である必要があります。 `T` は、返される JSON 型に最適にマップされる .NET 型と一致する必要があります。

Blazor サーバー アプリでプリレンダリングが有効になっている場合、最初のプリレンダリング中に JavaScript を呼び出すことはできません。 JavaScript 相互運用呼び出しは、ブラウザーとの接続が確立されるまで遅延させる必要があります。 詳細については、「[Blazor サーバー アプリがプリレンダリングされていることを検出する](#detect-when-a-blazor-server-app-is-prerendering)」セクションを参照してください。

次の例は、JavaScript ベースのデコーダーである [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder) に基づいています。 この例では、C# メソッドから JavaScript 関数を呼び出す方法を示します。 JavaScript 関数は、C# メソッドからバイト配列を受け取り、配列をデコードし、テキストをコンポーネントに返して表示できるようにします。

*wwwroot/index.html* (Blazor WebAssembly) または *Pages/_Host.cshtml* (Blazor サーバー) の `<head>` 要素内で、`TextDecoder` を使用して、渡された配列をデコードし、デコードした値を返す JavaScript 関数を提供します。

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

JavaScript コードでは、前の例で示したコードのように、スクリプト ファイルへの参照を使用して JavaScript ファイル ( *.js*) から読み込むこともできます。

```html
<script src="exampleJsInterop.js"></script>
```

次のコンポーネント:

* コンポーネント ボタン (**配列の変換**) が選択された場合、`JSRuntime` を使用して `convertArray` JavaScript 関数を呼び出します。
* JavaScript 関数が呼び出されると、渡された配列が文字列に変換されます。 文字列は、表示できるようにコンポーネントに返されます。

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a>IJSRuntime

`IJSRuntime` 抽象化を使用するには、次のいずれかの方法を採用します。

* Razor コンポーネント ( *.razor*) に `IJSRuntime` 抽象化を挿入します。

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  *wwwroot/index.html* (Blazor WebAssembly) または *Pages/_Host.cshtml* (Blazor サーバー) の `<head>` 要素内で、`handleTickerChanged` JavaScript 関数を提供します。 関数は `IJSRuntime.InvokeVoidAsync` を指定して呼び出され、値を返しません。

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* `IJSRuntime` 抽象化をクラス ( *.cs*) に挿入します。

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  *wwwroot/index.html* (Blazor WebAssembly) または *Pages/_Host.cshtml* (Blazor サーバー) の `<head>` 要素内で、`handleTickerChanged` JavaScript 関数を提供します。 関数は `JSRuntime.InvokeAsync` を指定して呼び出され、値を返します。

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic) でコンテンツを動的に生成するには、`[Inject]` 属性を使用します。

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

このトピックに添付されているクライアント側のサンプル アプリでは、ユーザー入力を受け取り、ウェルカム メッセージを表示するために、DOM とやりとりする 2 つの JavaScript 関数をアプリで使用できます。

* `showPrompt` &ndash; ユーザー入力 (ユーザーの名前) を受け入れるプロンプトを生成し、名前を呼び出し元に返します。
* `displayWelcome` &ndash; `welcome` の `id` を持つ DOM オブジェクトに、呼び出し元からのウェルカム メッセージを割り当てます。

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

JavaScript ファイルを参照する `<script>` タグを *wwwroot/index.html* ファイル (Blazor WebAssembly) または *Pages/_Host.cshtml* ファイル (Blazor サーバー) に配置します。

*wwwroot/index.html* (Blazor WebAssembly):

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

*Pages/_Host cshtml* (Blazor サーバー):

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

`<script>` タグを動的に更新できないため、`<script>` タグをコンポーネント ファイル内に配置しないでください。

.NET メソッドは、`IJSRuntime.InvokeAsync<T>` を呼び出して、*exampleJsInterop* ファイル内の JavaScript 関数と相互運用します。

`IJSRuntime` 抽象化は、Blazor サーバーのシナリオを可能にするために非同期です。 アプリが Blazor WebAssembly アプリであり、JavaScript 関数を同期的に呼び出す必要がある場合は、`IJSInProcessRuntime` にダウンキャストし、代わりに `Invoke<T>` を呼び出します。 ほとんどの JS 相互運用ライブラリでは、確実にすべてのシナリオでライブラリを使用できるように、非同期 API を使用することをお勧めします。

サンプル アプリには、JS 相互運用を示すコンポーネントが含まれています。 コンポーネント:

* JavaScript プロンプトを介してユーザー入力を受け取ります。
* テキストをコンポーネントに返して処理します。
* DOM とやりとりしてウェルカム メッセージを表示する 2 番目の JavaScript 関数を呼び出します。

*Pages/JSInterop.razor*:

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
        var name = await JSRuntime.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JSRuntime.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

1. コンポーネントの **[Trigger JavaScript Prompt]** ボタンを選択して `TriggerJsPrompt` を実行すると、*wwwroot/exampleJsInterop.js* ファイル内に指定した JavaScript `showPrompt` 関数が呼び出されます。
1. `showPrompt` 関数は、ユーザー入力 (ユーザーの名前) を受け取ります。これは、HTML エンコードされ、コンポーネントに返されます。 コンポーネントにより、ユーザーの名前がローカル変数 `name` に格納されます。
1. `name` に格納された文字列は、ウェルカム メッセージに組み込まれます。このメッセージが JavaScript 関数 `displayWelcome` に渡され、ウェルカム メッセージが見出しタグにレンダリングされます。

## <a name="call-a-void-javascript-function"></a>void JavaScript 関数を呼び出す

[void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) または [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) を返す JavaScript 関数は、`IJSRuntime.InvokeVoidAsync` を指定して呼び出します。

## <a name="detect-when-a-opno-locblazor-server-app-is-prerendering"></a>Blazor サーバー アプリがプリレンダリングされていることを検出する
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>要素への参照をキャプチャする

一部の JS 相互運用シナリオでは、HTML 要素への参照が必要です。 たとえば、UI ライブラリで初期化のための要素参照が必要な場合、`focus` や `play` などの要素でコマンドのような API の呼び出し必要になる可能性があります。

次の方法を使用して、コンポーネント内の HTML 要素への参照をキャプチャします。

* `@ref` 属性を HTML 要素に追加します。
* 名前が `@ref` 属性の値に一致する `ElementReference` 型のフィールドを定義します。

次の例は、`username` `<input>` 要素への参照をキャプチャする方法を示しています。

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> Blazor とやりとりしない空の要素のコンテンツを変化させるには、要素参照のみを使用します。 このシナリオは、サードパーティの API が要素にコンテンツを提供する場合に便利です。 Blazor は要素とやりとりしないため、Blazor の要素表現と DOM との間に競合が発生する可能性がありません。
>
> 次の例では、Blazor が DOM とやりとりしてこの要素のリスト項目 (`<li>`) を設定するため、順序なしリスト (`ul`) のコンテンツを変化させるのは "*危険*" です。
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
> JS 相互運用により要素 `MyList` のコンテンツが変更され、Blazor でその要素に差分を適用しようとした場合、差分は DOM と一致しません。

.NET コードに関しては、`ElementReference` は不透明なハンドルです。 `ElementReference` を使用して実行できるのは、JS 相互運用を介して JavaScript コードに渡すこと "*のみ*" です。 これを行うと、JavaScript 側のコードが `HTMLElement` インスタンスを受け取り、通常の DOM API で使用できます。

たとえば、次のコードでは、要素にフォーカスを設定できるようにする .NET 拡張メソッドを定義しています。

*exampleJsInterop.js*:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

値を返さない JavaScript 関数を呼び出すには、`IJSRuntime.InvokeVoidAsync` を使用します。 次のコードは、キャプチャされた `ElementReference` で前述の JavaScript 関数を呼び出して、ユーザー名入力にフォーカスを設定します。

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

拡張メソッドを使用するには、`IJSRuntime` インスタンスを受け取る静的拡張メソッドを作成します。

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

`Focus` メソッドは、オブジェクトで直接呼び出されます。 次の例では、`Focus` メソッドが `JsInteropClasses` 名前空間から使用できることを前提としています。

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> `username` 変数は、コンポーネントがレンダリングされた後にのみ設定されます。 未入力の `ElementReference` が JavaScript コードに渡された場合、JavaScript コードは `null` の値を受け取ります。 コンポーネントのレンダリングが完了した後に要素参照を操作する (要素に初期フォーカスを設定する) には、[OnAfterRenderAsync または OnAfterRender コンポーネント ライフサイクル メソッド](xref:blazor/lifecycle#after-component-render)を使用します。

ジェネリック型を操作して値を返す場合は、[ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1) を使用します。

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

`GenericMethod` は、型を持つオブジェクトで直接呼び出されます。 次の例では、`GenericMethod` が `JsInteropClasses` 名前空間から使用できることを前提としています。

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a>コンポーネント間で要素を参照する

`ElementReference` は、コンポーネントの `OnAfterRender` メソッドでのみ有効であることが保証されます (要素参照は `struct` です)。そのため、コンポーネント間で要素参照を渡すことはできません。

親コンポーネントが要素参照を他のコンポーネントで使用できるようにするために、親コンポーネントは次のことを実行できます。

* 子コンポーネントがコールバックを登録できるようにします。
* 渡された要素参照を使用して、登録されたコールバックを`OnAfterRender` イベント中に呼び出します。 間接的には、この方法により、子コンポーネントが親の要素参照とやりとりできるようになります。

次の Blazor WebAssembly の例は、この方法を示しています。

*wwwroot/index.html* の `<head>` 内:

```html
<style>
    .red { color: red }
</style>
```

*wwwroot/index.html* の `<body>` 内:

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

*Pages/Index.razor.cs*:

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

*Shared/SurveyPrompt.razor* (子コンポーネント):

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

*Shared/SurveyPrompt.razor.cs*:

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

## <a name="harden-js-interop-calls"></a>JS 相互運用呼び出しの強化

JS 相互運用は、ネットワーク エラーにより失敗する可能性があるため、信頼性の低いものとして扱う必要があります。 既定では、Blazor サーバー アプリでは、サーバー上の JS 相互運用の呼び出しは 1 分後にタイムアウトします。 10 秒など、アプリでより積極的なタイムアウトが許容される場合は、次のいずれかの方法を使用してタイムアウトを設定します。

* `Startup.ConfigureServices` でグローバルに、タイムアウトを指定します。

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* コンポーネント コードでの呼び出しごとに、1 回の呼び出しでタイムアウトを指定できます。

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

リソース枯渇の詳細については、「<xref:security/blazor/server>」を参照してください。

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="additional-resources"></a>その他の技術情報

* <xref:blazor/call-dotnet-from-javascript>
* [InteropComponent.razor の例 (dotnet/AspNetCore GitHub リポジトリ、3.1 リリース ブランチ)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Blazor サーバー アプリで大規模なデータ転送を実行する](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
