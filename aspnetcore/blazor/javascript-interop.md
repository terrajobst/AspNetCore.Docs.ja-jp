---
title: ASP.NET Core Blazor JavaScript 相互運用機能
author: guardrex
description: Blazor アプリで JavaScript から .NET および .NET メソッドから JavaScript 関数を呼び出す方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
uid: blazor/javascript-interop
ms.openlocfilehash: 05225b86701b7a5d5c84dd43afbef70dd1ece228
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2019
ms.locfileid: "74944071"
---
# <a name="aspnet-core-opno-locblazor-javascript-interop"></a><span data-ttu-id="be390-103">ASP.NET Core Blazor JavaScript 相互運用機能</span><span class="sxs-lookup"><span data-stu-id="be390-103">ASP.NET Core Blazor JavaScript interop</span></span>

<span data-ttu-id="be390-104">[Javier Calvarro jeannine](https://github.com/javiercn)、 [Daniel Roth](https://github.com/danroth27)、 [Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="be390-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="be390-105">Blazor アプリは、JavaScript コードから .NET および .NET メソッドから JavaScript 関数を呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="be390-105">A Blazor app can invoke JavaScript functions from .NET and .NET methods from JavaScript code.</span></span>

<span data-ttu-id="be390-106">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="be390-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="invoke-javascript-functions-from-net-methods"></a><span data-ttu-id="be390-107">.NET メソッドから JavaScript 関数を呼び出す</span><span class="sxs-lookup"><span data-stu-id="be390-107">Invoke JavaScript functions from .NET methods</span></span>

<span data-ttu-id="be390-108">JavaScript 関数を呼び出すために .NET コードが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="be390-108">There are times when .NET code is required to call a JavaScript function.</span></span> <span data-ttu-id="be390-109">たとえば、JavaScript 呼び出しは、ブラウザーの機能や機能を JavaScript ライブラリからアプリに公開できます。</span><span class="sxs-lookup"><span data-stu-id="be390-109">For example, a JavaScript call can expose browser capabilities or functionality from a JavaScript library to the app.</span></span> <span data-ttu-id="be390-110">このシナリオは、 *JavaScript 相互運用性*(*JS 相互運用*) と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="be390-110">This scenario is called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="be390-111">.NET から JavaScript を呼び出すには、`IJSRuntime` 抽象化を使用します。</span><span class="sxs-lookup"><span data-stu-id="be390-111">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="be390-112">`InvokeAsync<T>` メソッドは、任意の数の JSON シリアル化可能な引数と共に呼び出す JavaScript 関数の識別子を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="be390-112">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="be390-113">関数識別子は、グローバルスコープ (`window`) に対して相対的です。</span><span class="sxs-lookup"><span data-stu-id="be390-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="be390-114">`window.someScope.someFunction`を呼び出す場合は、識別子が `someScope.someFunction`ます。</span><span class="sxs-lookup"><span data-stu-id="be390-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="be390-115">呼び出される前に、関数を登録する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="be390-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="be390-116">戻り値の型 `T` も JSON シリアル化可能である必要があります。</span><span class="sxs-lookup"><span data-stu-id="be390-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="be390-117">`T` は、返される JSON 型に最適にマップされる .NET 型と一致している必要があります。</span><span class="sxs-lookup"><span data-stu-id="be390-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="be390-118">Blazor サーバーアプリの場合:</span><span class="sxs-lookup"><span data-stu-id="be390-118">For Blazor Server apps:</span></span>

* <span data-ttu-id="be390-119">複数のユーザー要求が Blazor サーバーアプリによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="be390-119">Multiple user requests are processed by the Blazor Server app.</span></span> <span data-ttu-id="be390-120">JavaScript 関数を呼び出すために、コンポーネント内で `JSRuntime.Current` を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="be390-120">Don't call `JSRuntime.Current` in a component to invoke JavaScript functions.</span></span>
* <span data-ttu-id="be390-121">`IJSRuntime` の抽象化を挿入し、挿入されたオブジェクトを使用して、JS 相互運用機能の呼び出しを発行します。</span><span class="sxs-lookup"><span data-stu-id="be390-121">Inject the `IJSRuntime` abstraction and use the injected object to issue JS interop calls.</span></span>
* <span data-ttu-id="be390-122">Blazor アプリのプリレンダリング中に、ブラウザーとの接続が確立されていないため、JavaScript を呼び出すことはできません。</span><span class="sxs-lookup"><span data-stu-id="be390-122">While a Blazor app is prerendering, calling into JavaScript isn't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="be390-123">詳細については、「 [Blazor アプリのプリレンダリングを検出する](#detect-when-a-blazor-app-is-prerendering)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="be390-123">For more information, see the [Detect when a Blazor app is prerendering](#detect-when-a-blazor-app-is-prerendering) section.</span></span>

<span data-ttu-id="be390-124">次の例は、JavaScript ベースの試験的なデコーダーである[Textdecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder)に基づいています。</span><span class="sxs-lookup"><span data-stu-id="be390-124">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), an experimental JavaScript-based decoder.</span></span> <span data-ttu-id="be390-125">この例では、 C#メソッドから JavaScript 関数を呼び出す方法を示します。</span><span class="sxs-lookup"><span data-stu-id="be390-125">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="be390-126">JavaScript 関数は、 C#メソッドからバイト配列を受け取り、配列をデコードし、テキストをコンポーネントに返して表示できるようにします。</span><span class="sxs-lookup"><span data-stu-id="be390-126">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="be390-127">*Wwwroot/index.html* (Blazor WebAssembly または*Pages/_Host* (Blazor Server) の `<head>` 要素内では、`TextDecoder` を使用して渡された配列をデコードし、デコードされた値を返す JavaScript 関数を提供します。</span><span class="sxs-lookup"><span data-stu-id="be390-127">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](javascript-interop/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="be390-128">前の例で示したコードなどの JavaScript コードは、スクリプトファイルへの参照を使用して JavaScript ファイル ( *.js*) から読み込むこともできます。</span><span class="sxs-lookup"><span data-stu-id="be390-128">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="be390-129">次のコンポーネント:</span><span class="sxs-lookup"><span data-stu-id="be390-129">The following component:</span></span>

* <span data-ttu-id="be390-130">コンポーネントボタン (**配列の変換**) が選択されている場合に `JSRuntime` を使用して `convertArray` JavaScript 関数を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="be390-130">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="be390-131">JavaScript 関数が呼び出されると、渡された配列が文字列に変換されます。</span><span class="sxs-lookup"><span data-stu-id="be390-131">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="be390-132">文字列は、表示のためにコンポーネントに返されます。</span><span class="sxs-lookup"><span data-stu-id="be390-132">The string is returned to the component for display.</span></span>

[!code-razor[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="use-of-ijsruntime"></a><span data-ttu-id="be390-133">IJSRuntime の使用</span><span class="sxs-lookup"><span data-stu-id="be390-133">Use of IJSRuntime</span></span>

<span data-ttu-id="be390-134">`IJSRuntime` の抽象化を使用するには、次のいずれかの方法を採用します。</span><span class="sxs-lookup"><span data-stu-id="be390-134">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="be390-135">Razor コンポーネント (*razor*) に `IJSRuntime` の抽象化を挿入します。</span><span class="sxs-lookup"><span data-stu-id="be390-135">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-razor[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="be390-136">*Wwwroot/index.html* (Blazor WebAssembly または*Pages/_Host* (Blazor Server) の `<head>` 要素内で、`handleTickerChanged` JavaScript 関数を提供します。</span><span class="sxs-lookup"><span data-stu-id="be390-136">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="be390-137">関数は `IJSRuntime.InvokeVoidAsync` と共に呼び出され、値を返しません。</span><span class="sxs-lookup"><span data-stu-id="be390-137">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="be390-138">`IJSRuntime` の抽象化をクラス ( *.cs*) に挿入します。</span><span class="sxs-lookup"><span data-stu-id="be390-138">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="be390-139">*Wwwroot/index.html* (Blazor WebAssembly または*Pages/_Host* (Blazor Server) の `<head>` 要素内で、`handleTickerChanged` JavaScript 関数を提供します。</span><span class="sxs-lookup"><span data-stu-id="be390-139">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="be390-140">関数は `JSRuntime.InvokeAsync` を指定して呼び出され、値を返します。</span><span class="sxs-lookup"><span data-stu-id="be390-140">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="be390-141">[BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic)を使用した動的なコンテンツ生成の場合は、`[Inject]` 属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="be390-141">For dynamic content generation with [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="be390-142">このトピックに付属しているクライアント側のサンプルアプリでは、ユーザー入力を受け取り、ウェルカムメッセージを表示するために、DOM と対話する2つの JavaScript 関数をアプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="be390-142">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="be390-143">&ndash; を `showPrompt` と、ユーザー入力 (ユーザーの名前) を受け取り、呼び出し元に名前を返すように求めるプロンプトが生成されます。</span><span class="sxs-lookup"><span data-stu-id="be390-143">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="be390-144">`displayWelcome` &ndash; は、`welcome`の `id` を使用して、呼び出し元からのウェルカムメッセージを DOM オブジェクトに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="be390-144">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="be390-145">*wwwroot/exampleJsInterop*:</span><span class="sxs-lookup"><span data-stu-id="be390-145">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="be390-146">JavaScript ファイルを参照する `<script>` タグを、 *wwwroot/index.html*ファイル (Blazor WebAssembly または*Pages/_Host* (Blazor Server) ファイルに配置します。</span><span class="sxs-lookup"><span data-stu-id="be390-146">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server).</span></span>

<span data-ttu-id="be390-147">*wwwroot/index.html* (Blazor webas):</span><span class="sxs-lookup"><span data-stu-id="be390-147">*wwwroot/index.html* (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=15)]

<span data-ttu-id="be390-148">*Pages/_Host* (Blazor サーバー):</span><span class="sxs-lookup"><span data-stu-id="be390-148">*Pages/_Host.cshtml* (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=21)]

<span data-ttu-id="be390-149">`<script>` タグを動的に更新できないため、コンポーネントファイルに `<script>` タグを配置しないでください。</span><span class="sxs-lookup"><span data-stu-id="be390-149">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="be390-150">.NET メソッドは、`IJSRuntime.InvokeAsync<T>`を呼び出すことによって、 *exampleJsInterop*ファイル内の JavaScript 関数と相互運用します。</span><span class="sxs-lookup"><span data-stu-id="be390-150">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="be390-151">`IJSRuntime` の抽象化は、Blazor サーバーのシナリオを可能にする非同期です。</span><span class="sxs-lookup"><span data-stu-id="be390-151">The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="be390-152">アプリが Blazor Webasのアプリで、JavaScript 関数を同期的に呼び出す必要がある場合は、`IJSInProcessRuntime` にダウンキャストし、代わりに `Invoke<T>` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="be390-152">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="be390-153">ほとんどの JS 相互運用機能ライブラリでは、非同期 Api を使用して、すべてのシナリオでライブラリを使用できるようにすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="be390-153">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="be390-154">サンプルアプリには、JS 相互運用機能を示すためのコンポーネントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="be390-154">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="be390-155">コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="be390-155">The component:</span></span>

* <span data-ttu-id="be390-156">JavaScript プロンプトを使用してユーザー入力を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="be390-156">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="be390-157">処理するテキストをコンポーネントに返します。</span><span class="sxs-lookup"><span data-stu-id="be390-157">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="be390-158">DOM と対話してウェルカムメッセージを表示する2番目の JavaScript 関数を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="be390-158">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="be390-159">*Pages/JSInterop*:</span><span class="sxs-lookup"><span data-stu-id="be390-159">*Pages/JSInterop.razor*:</span></span>

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

1. <span data-ttu-id="be390-160">コンポーネントの **[トリガー JavaScript プロンプト]** ボタンを選択して `TriggerJsPrompt` を実行すると、 *wwwroot/exampleJsInterop*ファイルで提供されている javascript `showPrompt` 関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="be390-160">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="be390-161">`showPrompt` 関数は、HTML エンコードされ、コンポーネントに返されるユーザー入力 (ユーザーの名前) を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="be390-161">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="be390-162">コンポーネントは、ユーザーの名前をローカル変数 `name`に格納します。</span><span class="sxs-lookup"><span data-stu-id="be390-162">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="be390-163">`name` に格納されている文字列は、ウェルカムメッセージに組み込まれます。このメッセージは、JavaScript 関数に渡されます。 `displayWelcome`は、ウェルカムメッセージを見出しタグにレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="be390-163">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="be390-164">Void JavaScript 関数を呼び出す</span><span class="sxs-lookup"><span data-stu-id="be390-164">Call a void JavaScript function</span></span>

<span data-ttu-id="be390-165">[Void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void)または[Undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined)を返す JavaScript 関数は、`IJSRuntime.InvokeVoidAsync`で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="be390-165">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeVoidAsync`.</span></span>

## <a name="detect-when-a-opno-locblazor-app-is-prerendering"></a><span data-ttu-id="be390-166">Blazor アプリがプリレンダリングされるタイミングを検出する</span><span class="sxs-lookup"><span data-stu-id="be390-166">Detect when a Blazor app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="be390-167">要素への参照をキャプチャする</span><span class="sxs-lookup"><span data-stu-id="be390-167">Capture references to elements</span></span>

<span data-ttu-id="be390-168">一部の JS 相互運用シナリオでは、HTML 要素への参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="be390-168">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="be390-169">たとえば、UI ライブラリに初期化のための要素参照が必要な場合や、`focus` や `play`などの要素に対してコマンドに似た Api を呼び出す必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="be390-169">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="be390-170">次の方法を使用して、コンポーネント内の HTML 要素への参照をキャプチャします。</span><span class="sxs-lookup"><span data-stu-id="be390-170">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="be390-171">`@ref` 属性を HTML 要素に追加します。</span><span class="sxs-lookup"><span data-stu-id="be390-171">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="be390-172">`@ref` 属性の値に一致する名前を持つ `ElementReference` 型のフィールドを定義します。</span><span class="sxs-lookup"><span data-stu-id="be390-172">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="be390-173">次の例は、`username` `<input>` 要素への参照をキャプチャする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="be390-173">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="be390-174">Blazorと対話しない空の要素の内容を変化させるには、要素参照のみを使用します。</span><span class="sxs-lookup"><span data-stu-id="be390-174">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="be390-175">このシナリオは、サードパーティの API が要素にコンテンツを提供する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="be390-175">This scenario is useful when a 3rd party API supplies content to the element.</span></span> <span data-ttu-id="be390-176">Blazor は要素と対話しないため、Blazorが要素と DOM を表現している間に競合が発生する可能性はありません。</span><span class="sxs-lookup"><span data-stu-id="be390-176">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="be390-177">次の例では、Blazor が DOM とやり取りしてこの要素のリスト項目 (`<li>`) を設定するため、順序なしのリスト (`ul`) の内容を変化させるのは*危険*です。</span><span class="sxs-lookup"><span data-stu-id="be390-177">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="be390-178">JS 相互運用機能によって要素の内容が `MyList` され、その要素に差分を適用しようと Blazor 試みた場合、相違は DOM と一致しません。</span><span class="sxs-lookup"><span data-stu-id="be390-178">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="be390-179">.NET コードに関しては、`ElementReference` は不透明なハンドルです。</span><span class="sxs-lookup"><span data-stu-id="be390-179">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="be390-180">`ElementReference` で行うことができるのは、JS 相互運用機能を使用して JavaScript コードに渡すこと*だけ*です。</span><span class="sxs-lookup"><span data-stu-id="be390-180">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="be390-181">これを行うと、JavaScript 側のコードは `HTMLElement` インスタンスを受け取ります。このインスタンスは、通常の DOM Api と共に使用できます。</span><span class="sxs-lookup"><span data-stu-id="be390-181">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="be390-182">たとえば、次のコードでは、要素にフォーカスを設定できるようにする .NET 拡張メソッドを定義しています。</span><span class="sxs-lookup"><span data-stu-id="be390-182">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="be390-183">*exampleJsInterop*:</span><span class="sxs-lookup"><span data-stu-id="be390-183">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="be390-184">値を返さない JavaScript 関数を呼び出すには、`IJSRuntime.InvokeVoidAsync`を使用します。</span><span class="sxs-lookup"><span data-stu-id="be390-184">To call a JavaScript function that doesn't return a value, use `IJSRuntime.InvokeVoidAsync`.</span></span> <span data-ttu-id="be390-185">次のコードは、キャプチャされた `ElementReference`で前の JavaScript 関数を呼び出すことによって、ユーザー名入力にフォーカスを設定します。</span><span class="sxs-lookup"><span data-stu-id="be390-185">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured `ElementReference`:</span></span>

[!code-razor[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="be390-186">拡張メソッドを使用するには、`IJSRuntime` インスタンスを受け取る静的拡張メソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="be390-186">To use an extension method, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="be390-187">`Focus` メソッドは、オブジェクトに対して直接呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="be390-187">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="be390-188">次の例では、`Focus` メソッドが `JsInteropClasses` 名前空間から使用できることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="be390-188">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](javascript-interop/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="be390-189">`username` 変数は、コンポーネントがレンダリングされた後にのみ設定されます。</span><span class="sxs-lookup"><span data-stu-id="be390-189">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="be390-190">JavaScript コードにいない `ElementReference` が渡されると、JavaScript コードは `null`の値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="be390-190">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="be390-191">コンポーネントのレンダリングが完了した後に要素参照を操作するには (要素に初期フォーカスを設定するには)、 [OnAfterRenderAsync または OnAfterRender コンポーネントライフサイクルメソッド](xref:blazor/lifecycle#after-component-render)を使用します。</span><span class="sxs-lookup"><span data-stu-id="be390-191">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [OnAfterRenderAsync or OnAfterRender component lifecycle methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="be390-192">ジェネリック型を操作して値を返す場合は、 [Valuetask\<t >](xref:System.Threading.Tasks.ValueTask`1)を使用します。</span><span class="sxs-lookup"><span data-stu-id="be390-192">When working with generic types and returning a value, use [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="be390-193">`GenericMethod` は、型を使用してオブジェクトに対して直接呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="be390-193">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="be390-194">次の例では、`GenericMethod` が `JsInteropClasses` 名前空間から使用できることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="be390-194">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](javascript-interop/samples_snapshot/component3.razor?highlight=17)]

## <a name="invoke-net-methods-from-javascript-functions"></a><span data-ttu-id="be390-195">JavaScript 関数からの .NET メソッドの呼び出し</span><span class="sxs-lookup"><span data-stu-id="be390-195">Invoke .NET methods from JavaScript functions</span></span>

### <a name="static-net-method-call"></a><span data-ttu-id="be390-196">静的 .NET メソッド呼び出し</span><span class="sxs-lookup"><span data-stu-id="be390-196">Static .NET method call</span></span>

<span data-ttu-id="be390-197">JavaScript から静的 .NET メソッドを呼び出すには、`DotNet.invokeMethod` または `DotNet.invokeMethodAsync` 関数を使用します。</span><span class="sxs-lookup"><span data-stu-id="be390-197">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="be390-198">呼び出す静的メソッドの識別子、関数を含むアセンブリの名前、および任意の引数を渡します。</span><span class="sxs-lookup"><span data-stu-id="be390-198">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="be390-199">Blazor サーバーのシナリオをサポートするには、非同期バージョンを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="be390-199">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="be390-200">.Net メソッドを JavaScript から呼び出すには、.NET メソッドが public、static、および `[JSInvokable]` 属性を持っている必要があります。</span><span class="sxs-lookup"><span data-stu-id="be390-200">To invoke a .NET method from JavaScript, the .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="be390-201">既定では、メソッド識別子はメソッド名ですが、`JSInvokableAttribute` コンストラクターを使用して別の識別子を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="be390-201">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor.</span></span> <span data-ttu-id="be390-202">オープンジェネリックメソッドを呼び出すことは現在サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="be390-202">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="be390-203">このサンプルアプリにはC# `int`s の配列を返すメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="be390-203">The sample app includes a C# method to return an array of `int`s.</span></span> <span data-ttu-id="be390-204">`JSInvokable` 属性がメソッドに適用されます。</span><span class="sxs-lookup"><span data-stu-id="be390-204">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="be390-205">*Pages/JsInterop*:</span><span class="sxs-lookup"><span data-stu-id="be390-205">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="be390-206">クライアントに提供される JavaScript はC# 、.net メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="be390-206">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="be390-207">*wwwroot/exampleJsInterop*:</span><span class="sxs-lookup"><span data-stu-id="be390-207">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="be390-208">**[.Net 静的メソッド ReturnArrayAsync のトリガー]** ボタンが選択されている場合は、ブラウザーの web developer ツールでコンソール出力を確認します。</span><span class="sxs-lookup"><span data-stu-id="be390-208">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="be390-209">コンソールの出力は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="be390-209">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="be390-210">4番目の配列値は、`ReturnArrayAsync`によって返される配列 (`data.push(4);`) にプッシュされます。</span><span class="sxs-lookup"><span data-stu-id="be390-210">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

### <a name="instance-method-call"></a><span data-ttu-id="be390-211">インスタンスメソッドの呼び出し</span><span class="sxs-lookup"><span data-stu-id="be390-211">Instance method call</span></span>

<span data-ttu-id="be390-212">JavaScript から .NET インスタンスメソッドを呼び出すこともできます。</span><span class="sxs-lookup"><span data-stu-id="be390-212">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="be390-213">JavaScript から .NET インスタンスメソッドを呼び出すには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="be390-213">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="be390-214">.NET インスタンスを `DotNetObjectReference` インスタンスにラップして、JavaScript に渡します。</span><span class="sxs-lookup"><span data-stu-id="be390-214">Pass the .NET instance to JavaScript by wrapping it in a `DotNetObjectReference` instance.</span></span> <span data-ttu-id="be390-215">.NET インスタンスは、JavaScript への参照によって渡されます。</span><span class="sxs-lookup"><span data-stu-id="be390-215">The .NET instance is passed by reference to JavaScript.</span></span>
* <span data-ttu-id="be390-216">`invokeMethod` または `invokeMethodAsync` 関数を使用して、インスタンスで .NET インスタンスメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="be390-216">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="be390-217">.NET インスタンスは、JavaScript から他の .NET メソッドを呼び出すときに引数として渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="be390-217">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="be390-218">サンプルアプリは、メッセージをクライアント側コンソールに記録します。</span><span class="sxs-lookup"><span data-stu-id="be390-218">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="be390-219">サンプルアプリで示されている次の例については、ブラウザーの開発者ツールでブラウザーのコンソール出力を確認してください。</span><span class="sxs-lookup"><span data-stu-id="be390-219">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="be390-220">**[.Net インスタンスメソッドをトリガーする HelloHelper]** ボタンを選択すると、`ExampleJsInterop.CallHelloHelperSayHello` が呼び出され、`Blazor`の名前がメソッドに渡されます。</span><span class="sxs-lookup"><span data-stu-id="be390-220">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="be390-221">*Pages/JsInterop*:</span><span class="sxs-lookup"><span data-stu-id="be390-221">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="be390-222">`CallHelloHelperSayHello` は、`HelloHelper`の新しいインスタンスを使用して JavaScript 関数 `sayHello` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="be390-222">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="be390-223">*JsInteropClasses/ExampleJsInterop*:</span><span class="sxs-lookup"><span data-stu-id="be390-223">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

<span data-ttu-id="be390-224">*wwwroot/exampleJsInterop*:</span><span class="sxs-lookup"><span data-stu-id="be390-224">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="be390-225">名前は `HelloHelper`のコンストラクターに渡され、`HelloHelper.Name` プロパティが設定されます。</span><span class="sxs-lookup"><span data-stu-id="be390-225">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="be390-226">JavaScript 関数 `sayHello` が実行されると、`HelloHelper.SayHello` は `Hello, {Name}!` メッセージを返します。このメッセージは、JavaScript 関数によってコンソールに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="be390-226">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="be390-227">*JsInteropClasses/HelloHelper*:</span><span class="sxs-lookup"><span data-stu-id="be390-227">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="be390-228">ブラウザーの web 開発者ツールのコンソール出力:</span><span class="sxs-lookup"><span data-stu-id="be390-228">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-class-library"></a><span data-ttu-id="be390-229">クラスライブラリで相互運用コードを共有する</span><span class="sxs-lookup"><span data-stu-id="be390-229">Share interop code in a class library</span></span>

<span data-ttu-id="be390-230">JS 相互運用コードは、NuGet パッケージ内のコードを共有できるクラスライブラリに含めることができます。</span><span class="sxs-lookup"><span data-stu-id="be390-230">JS interop code can be included in a class library, which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="be390-231">クラスライブラリは、ビルドされたアセンブリに JavaScript リソースの埋め込みを処理します。</span><span class="sxs-lookup"><span data-stu-id="be390-231">The class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="be390-232">JavaScript ファイルは*wwwroot*フォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="be390-232">The JavaScript files are placed in the *wwwroot* folder.</span></span> <span data-ttu-id="be390-233">ツールは、ライブラリの構築時にリソースの埋め込みを行います。</span><span class="sxs-lookup"><span data-stu-id="be390-233">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="be390-234">ビルド済みの NuGet パッケージは、NuGet パッケージを参照するのと同じ方法で、アプリのプロジェクトファイルで参照されます。</span><span class="sxs-lookup"><span data-stu-id="be390-234">The built NuGet package is referenced in the app's project file the same way that any NuGet package is referenced.</span></span> <span data-ttu-id="be390-235">パッケージが復元された後、アプリコードはと同じように JavaScript C#を呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="be390-235">After the package is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="be390-236">詳細については、「<xref:blazor/class-libraries>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="be390-236">For more information, see <xref:blazor/class-libraries>.</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="be390-237">JS 相互運用呼び出しの強化</span><span class="sxs-lookup"><span data-stu-id="be390-237">Harden JS interop calls</span></span>

<span data-ttu-id="be390-238">JS 相互運用機能は、ネットワークエラーのために失敗する可能性があり、信頼性の低いものとして扱う必要があります。</span><span class="sxs-lookup"><span data-stu-id="be390-238">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="be390-239">既定では、Blazor サーバーアプリは、1分後に JS 相互運用機能の呼び出しをサーバー上でタイムアウトします。</span><span class="sxs-lookup"><span data-stu-id="be390-239">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="be390-240">10秒など、アプリでより積極的なタイムアウトが許容される場合は、次のいずれかの方法を使用してタイムアウトを設定します。</span><span class="sxs-lookup"><span data-stu-id="be390-240">If an app can tolerate a more aggressive timeout, such as 10 seconds, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="be390-241">`Startup.ConfigureServices`でグローバルに、タイムアウトを指定します。</span><span class="sxs-lookup"><span data-stu-id="be390-241">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="be390-242">コンポーネントコードでの呼び出しごとに、1回の呼び出しでタイムアウトを指定できます。</span><span class="sxs-lookup"><span data-stu-id="be390-242">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="be390-243">リソース枯渇の詳細については、「<xref:security/blazor/server>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="be390-243">For more information on resource exhaustion, see <xref:security/blazor/server>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="be390-244">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="be390-244">Additional resources</span></span>

* [<span data-ttu-id="be390-245">InteropComponent の例 (aspnet/AspNetCore GitHub リポジトリ、3.0 リリースブランチ)</span><span class="sxs-lookup"><span data-stu-id="be390-245">InteropComponent.razor example (aspnet/AspNetCore GitHub repository, 3.0 release branch)</span></span>](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
