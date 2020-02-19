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
# <a name="aspnet-core-opno-locblazor-javascript-interop"></a><span data-ttu-id="cf441-103">ASP.NET Core Blazor JavaScript 相互運用機能</span><span class="sxs-lookup"><span data-stu-id="cf441-103">ASP.NET Core Blazor JavaScript interop</span></span>

<span data-ttu-id="cf441-104">[Javier Calvarro jeannine](https://github.com/javiercn)、 [Daniel Roth](https://github.com/danroth27)、 [Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="cf441-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="cf441-105">Blazor アプリは、JavaScript コードから .NET および .NET メソッドから JavaScript 関数を呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="cf441-105">A Blazor app can invoke JavaScript functions from .NET and .NET methods from JavaScript code.</span></span>

<span data-ttu-id="cf441-106">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="cf441-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="invoke-javascript-functions-from-net-methods"></a><span data-ttu-id="cf441-107">.NET メソッドから JavaScript 関数を呼び出す</span><span class="sxs-lookup"><span data-stu-id="cf441-107">Invoke JavaScript functions from .NET methods</span></span>

<span data-ttu-id="cf441-108">JavaScript 関数を呼び出すために .NET コードが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="cf441-108">There are times when .NET code is required to call a JavaScript function.</span></span> <span data-ttu-id="cf441-109">たとえば、JavaScript 呼び出しは、ブラウザーの機能や機能を JavaScript ライブラリからアプリに公開できます。</span><span class="sxs-lookup"><span data-stu-id="cf441-109">For example, a JavaScript call can expose browser capabilities or functionality from a JavaScript library to the app.</span></span> <span data-ttu-id="cf441-110">このシナリオは、 *JavaScript 相互運用性*(*JS 相互運用*) と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="cf441-110">This scenario is called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="cf441-111">.NET から JavaScript を呼び出すには、`IJSRuntime` 抽象化を使用します。</span><span class="sxs-lookup"><span data-stu-id="cf441-111">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="cf441-112">JS 相互運用呼び出しを発行するには、コンポーネントに `IJSRuntime` の抽象化を挿入します。</span><span class="sxs-lookup"><span data-stu-id="cf441-112">To issue JS interop calls, inject the `IJSRuntime` abstraction in your component.</span></span> <span data-ttu-id="cf441-113">`InvokeAsync<T>` メソッドは、任意の数の JSON シリアル化可能な引数と共に呼び出す JavaScript 関数の識別子を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="cf441-113">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="cf441-114">関数識別子は、グローバルスコープ (`window`) に対して相対的です。</span><span class="sxs-lookup"><span data-stu-id="cf441-114">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="cf441-115">`window.someScope.someFunction`を呼び出す場合は、識別子が `someScope.someFunction`ます。</span><span class="sxs-lookup"><span data-stu-id="cf441-115">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="cf441-116">呼び出される前に、関数を登録する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="cf441-116">There's no need to register the function before it's called.</span></span> <span data-ttu-id="cf441-117">戻り値の型 `T` も JSON シリアル化可能である必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf441-117">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="cf441-118">`T` は、返される JSON 型に最適にマップされる .NET 型と一致している必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf441-118">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="cf441-119">プリレンダリングが有効になっている Blazor サーバーアプリの場合、初期のプリレンダリング中に JavaScript を呼び出すことはできません。</span><span class="sxs-lookup"><span data-stu-id="cf441-119">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="cf441-120">JavaScript の相互運用呼び出しは、ブラウザーとの接続が確立されるまで遅延させる必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf441-120">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="cf441-121">詳細については、「 [Blazor アプリのプリレンダリングを検出する](#detect-when-a-blazor-app-is-prerendering)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="cf441-121">For more information, see the [Detect when a Blazor app is prerendering](#detect-when-a-blazor-app-is-prerendering) section.</span></span>

<span data-ttu-id="cf441-122">次の例は、JavaScript ベースの試験的なデコーダーである[Textdecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder)に基づいています。</span><span class="sxs-lookup"><span data-stu-id="cf441-122">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), an experimental JavaScript-based decoder.</span></span> <span data-ttu-id="cf441-123">この例では、 C#メソッドから JavaScript 関数を呼び出す方法を示します。</span><span class="sxs-lookup"><span data-stu-id="cf441-123">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="cf441-124">JavaScript 関数は、 C#メソッドからバイト配列を受け取り、配列をデコードし、テキストをコンポーネントに返して表示できるようにします。</span><span class="sxs-lookup"><span data-stu-id="cf441-124">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="cf441-125">*Wwwroot/index.html* (Blazor WebAssembly または*Pages/_Host* (Blazor Server) の `<head>` 要素内では、`TextDecoder` を使用して渡された配列をデコードし、デコードされた値を返す JavaScript 関数を提供します。</span><span class="sxs-lookup"><span data-stu-id="cf441-125">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](javascript-interop/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="cf441-126">前の例で示したコードなどの JavaScript コードは、スクリプトファイルへの参照を使用して JavaScript ファイル ( *.js*) から読み込むこともできます。</span><span class="sxs-lookup"><span data-stu-id="cf441-126">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="cf441-127">次のコンポーネント:</span><span class="sxs-lookup"><span data-stu-id="cf441-127">The following component:</span></span>

* <span data-ttu-id="cf441-128">コンポーネントボタン (**配列の変換**) が選択されている場合に `JSRuntime` を使用して `convertArray` JavaScript 関数を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="cf441-128">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="cf441-129">JavaScript 関数が呼び出されると、渡された配列が文字列に変換されます。</span><span class="sxs-lookup"><span data-stu-id="cf441-129">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="cf441-130">文字列は、表示のためにコンポーネントに返されます。</span><span class="sxs-lookup"><span data-stu-id="cf441-130">The string is returned to the component for display.</span></span>

[!code-razor[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="use-of-ijsruntime"></a><span data-ttu-id="cf441-131">IJSRuntime の使用</span><span class="sxs-lookup"><span data-stu-id="cf441-131">Use of IJSRuntime</span></span>

<span data-ttu-id="cf441-132">`IJSRuntime` の抽象化を使用するには、次のいずれかの方法を採用します。</span><span class="sxs-lookup"><span data-stu-id="cf441-132">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="cf441-133">Razor コンポーネント (*razor*) に `IJSRuntime` の抽象化を挿入します。</span><span class="sxs-lookup"><span data-stu-id="cf441-133">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-razor[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="cf441-134">*Wwwroot/index.html* (Blazor WebAssembly または*Pages/_Host* (Blazor Server) の `<head>` 要素内で、`handleTickerChanged` JavaScript 関数を提供します。</span><span class="sxs-lookup"><span data-stu-id="cf441-134">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="cf441-135">関数は `IJSRuntime.InvokeVoidAsync` と共に呼び出され、値を返しません。</span><span class="sxs-lookup"><span data-stu-id="cf441-135">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="cf441-136">`IJSRuntime` の抽象化をクラス ( *.cs*) に挿入します。</span><span class="sxs-lookup"><span data-stu-id="cf441-136">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="cf441-137">*Wwwroot/index.html* (Blazor WebAssembly または*Pages/_Host* (Blazor Server) の `<head>` 要素内で、`handleTickerChanged` JavaScript 関数を提供します。</span><span class="sxs-lookup"><span data-stu-id="cf441-137">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="cf441-138">関数は `JSRuntime.InvokeAsync` を指定して呼び出され、値を返します。</span><span class="sxs-lookup"><span data-stu-id="cf441-138">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="cf441-139">[BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)を使用した動的なコンテンツ生成の場合は、`[Inject]` 属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="cf441-139">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="cf441-140">このトピックに付属しているクライアント側のサンプルアプリでは、ユーザー入力を受け取り、ウェルカムメッセージを表示するために、DOM と対話する2つの JavaScript 関数をアプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="cf441-140">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="cf441-141">&ndash; を `showPrompt` と、ユーザー入力 (ユーザーの名前) を受け取り、呼び出し元に名前を返すように求めるプロンプトが生成されます。</span><span class="sxs-lookup"><span data-stu-id="cf441-141">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="cf441-142">`displayWelcome` &ndash; は、`welcome`の `id` を使用して、呼び出し元からのウェルカムメッセージを DOM オブジェクトに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="cf441-142">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="cf441-143">*wwwroot/exampleJsInterop*:</span><span class="sxs-lookup"><span data-stu-id="cf441-143">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="cf441-144">JavaScript ファイルを参照する `<script>` タグを、 *wwwroot/index.html*ファイル (Blazor WebAssembly または*Pages/_Host* (Blazor Server) ファイルに配置します。</span><span class="sxs-lookup"><span data-stu-id="cf441-144">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server).</span></span>

<span data-ttu-id="cf441-145">*wwwroot/index.html* (Blazor webas):</span><span class="sxs-lookup"><span data-stu-id="cf441-145">*wwwroot/index.html* (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="cf441-146">*Pages/_Host* (Blazor サーバー):</span><span class="sxs-lookup"><span data-stu-id="cf441-146">*Pages/_Host.cshtml* (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="cf441-147">`<script>` タグを動的に更新できないため、コンポーネントファイルに `<script>` タグを配置しないでください。</span><span class="sxs-lookup"><span data-stu-id="cf441-147">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="cf441-148">.NET メソッドは、`IJSRuntime.InvokeAsync<T>`を呼び出すことによって、 *exampleJsInterop*ファイル内の JavaScript 関数と相互運用します。</span><span class="sxs-lookup"><span data-stu-id="cf441-148">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="cf441-149">`IJSRuntime` の抽象化は、Blazor サーバーのシナリオを可能にする非同期です。</span><span class="sxs-lookup"><span data-stu-id="cf441-149">The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="cf441-150">アプリが Blazor Webasのアプリで、JavaScript 関数を同期的に呼び出す必要がある場合は、`IJSInProcessRuntime` にダウンキャストし、代わりに `Invoke<T>` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="cf441-150">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="cf441-151">ほとんどの JS 相互運用機能ライブラリでは、非同期 Api を使用して、すべてのシナリオでライブラリを使用できるようにすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="cf441-151">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="cf441-152">サンプルアプリには、JS 相互運用機能を示すためのコンポーネントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="cf441-152">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="cf441-153">コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="cf441-153">The component:</span></span>

* <span data-ttu-id="cf441-154">JavaScript プロンプトを使用してユーザー入力を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="cf441-154">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="cf441-155">処理するテキストをコンポーネントに返します。</span><span class="sxs-lookup"><span data-stu-id="cf441-155">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="cf441-156">DOM と対話してウェルカムメッセージを表示する2番目の JavaScript 関数を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="cf441-156">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="cf441-157">*Pages/JSInterop*:</span><span class="sxs-lookup"><span data-stu-id="cf441-157">*Pages/JSInterop.razor*:</span></span>

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

1. <span data-ttu-id="cf441-158">コンポーネントの **[トリガー JavaScript プロンプト]** ボタンを選択して `TriggerJsPrompt` を実行すると、 *wwwroot/exampleJsInterop*ファイルで提供されている javascript `showPrompt` 関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="cf441-158">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="cf441-159">`showPrompt` 関数は、HTML エンコードされ、コンポーネントに返されるユーザー入力 (ユーザーの名前) を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="cf441-159">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="cf441-160">コンポーネントは、ユーザーの名前をローカル変数 `name`に格納します。</span><span class="sxs-lookup"><span data-stu-id="cf441-160">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="cf441-161">`name` に格納されている文字列は、ウェルカムメッセージに組み込まれます。このメッセージは、JavaScript 関数に渡されます。 `displayWelcome`は、ウェルカムメッセージを見出しタグにレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="cf441-161">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="cf441-162">Void JavaScript 関数を呼び出す</span><span class="sxs-lookup"><span data-stu-id="cf441-162">Call a void JavaScript function</span></span>

<span data-ttu-id="cf441-163">[Void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void)または[Undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined)を返す JavaScript 関数は、`IJSRuntime.InvokeVoidAsync`で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="cf441-163">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeVoidAsync`.</span></span>

## <a name="detect-when-a-opno-locblazor-app-is-prerendering"></a><span data-ttu-id="cf441-164">Blazor アプリがプリレンダリングされるタイミングを検出する</span><span class="sxs-lookup"><span data-stu-id="cf441-164">Detect when a Blazor app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="cf441-165">要素への参照をキャプチャする</span><span class="sxs-lookup"><span data-stu-id="cf441-165">Capture references to elements</span></span>

<span data-ttu-id="cf441-166">一部の JS 相互運用シナリオでは、HTML 要素への参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="cf441-166">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="cf441-167">たとえば、UI ライブラリに初期化のための要素参照が必要な場合や、`focus` や `play`などの要素に対してコマンドに似た Api を呼び出す必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="cf441-167">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="cf441-168">次の方法を使用して、コンポーネント内の HTML 要素への参照をキャプチャします。</span><span class="sxs-lookup"><span data-stu-id="cf441-168">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="cf441-169">`@ref` 属性を HTML 要素に追加します。</span><span class="sxs-lookup"><span data-stu-id="cf441-169">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="cf441-170">`@ref` 属性の値に一致する名前を持つ `ElementReference` 型のフィールドを定義します。</span><span class="sxs-lookup"><span data-stu-id="cf441-170">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="cf441-171">次の例は、`username` `<input>` 要素への参照をキャプチャする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="cf441-171">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="cf441-172">Blazorと対話しない空の要素の内容を変化させるには、要素参照のみを使用します。</span><span class="sxs-lookup"><span data-stu-id="cf441-172">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="cf441-173">このシナリオは、サードパーティの API が要素にコンテンツを提供する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="cf441-173">This scenario is useful when a 3rd party API supplies content to the element.</span></span> <span data-ttu-id="cf441-174">Blazor は要素と対話しないため、Blazorが要素と DOM を表現している間に競合が発生する可能性はありません。</span><span class="sxs-lookup"><span data-stu-id="cf441-174">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="cf441-175">次の例では、Blazor が DOM とやり取りしてこの要素のリスト項目 (`<li>`) を設定するため、順序なしのリスト (`ul`) の内容を変化させるのは*危険*です。</span><span class="sxs-lookup"><span data-stu-id="cf441-175">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="cf441-176">JS 相互運用機能によって要素の内容が `MyList` され、その要素に差分を適用しようと Blazor 試みた場合、相違は DOM と一致しません。</span><span class="sxs-lookup"><span data-stu-id="cf441-176">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="cf441-177">.NET コードに関しては、`ElementReference` は不透明なハンドルです。</span><span class="sxs-lookup"><span data-stu-id="cf441-177">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="cf441-178">`ElementReference` で行うことができるのは、JS 相互運用機能を使用して JavaScript コードに渡すこと*だけ*です。</span><span class="sxs-lookup"><span data-stu-id="cf441-178">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="cf441-179">これを行うと、JavaScript 側のコードは `HTMLElement` インスタンスを受け取ります。このインスタンスは、通常の DOM Api と共に使用できます。</span><span class="sxs-lookup"><span data-stu-id="cf441-179">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="cf441-180">たとえば、次のコードでは、要素にフォーカスを設定できるようにする .NET 拡張メソッドを定義しています。</span><span class="sxs-lookup"><span data-stu-id="cf441-180">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="cf441-181">*exampleJsInterop*:</span><span class="sxs-lookup"><span data-stu-id="cf441-181">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="cf441-182">値を返さない JavaScript 関数を呼び出すには、`IJSRuntime.InvokeVoidAsync`を使用します。</span><span class="sxs-lookup"><span data-stu-id="cf441-182">To call a JavaScript function that doesn't return a value, use `IJSRuntime.InvokeVoidAsync`.</span></span> <span data-ttu-id="cf441-183">次のコードは、キャプチャされた `ElementReference`で前の JavaScript 関数を呼び出すことによって、ユーザー名入力にフォーカスを設定します。</span><span class="sxs-lookup"><span data-stu-id="cf441-183">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured `ElementReference`:</span></span>

[!code-razor[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="cf441-184">拡張メソッドを使用するには、`IJSRuntime` インスタンスを受け取る静的拡張メソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="cf441-184">To use an extension method, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="cf441-185">`Focus` メソッドは、オブジェクトに対して直接呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="cf441-185">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="cf441-186">次の例では、`Focus` メソッドが `JsInteropClasses` 名前空間から使用できることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="cf441-186">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](javascript-interop/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="cf441-187">`username` 変数は、コンポーネントがレンダリングされた後にのみ設定されます。</span><span class="sxs-lookup"><span data-stu-id="cf441-187">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="cf441-188">JavaScript コードにいない `ElementReference` が渡されると、JavaScript コードは `null`の値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="cf441-188">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="cf441-189">コンポーネントのレンダリングが完了した後に要素参照を操作するには (要素に初期フォーカスを設定するには)、 [OnAfterRenderAsync または OnAfterRender コンポーネントライフサイクルメソッド](xref:blazor/lifecycle#after-component-render)を使用します。</span><span class="sxs-lookup"><span data-stu-id="cf441-189">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [OnAfterRenderAsync or OnAfterRender component lifecycle methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="cf441-190">ジェネリック型を操作して値を返す場合は、 [Valuetask\<t >](xref:System.Threading.Tasks.ValueTask`1)を使用します。</span><span class="sxs-lookup"><span data-stu-id="cf441-190">When working with generic types and returning a value, use [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="cf441-191">`GenericMethod` は、型を使用してオブジェクトに対して直接呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="cf441-191">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="cf441-192">次の例では、`GenericMethod` が `JsInteropClasses` 名前空間から使用できることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="cf441-192">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](javascript-interop/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="cf441-193">コンポーネント間での参照要素</span><span class="sxs-lookup"><span data-stu-id="cf441-193">Reference elements across components</span></span>

<span data-ttu-id="cf441-194">`ElementReference` は、コンポーネントの `OnAfterRender` メソッドでは有効であることが保証されます (および要素参照は `struct`)。そのため、コンポーネント間で要素参照を渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="cf441-194">An `ElementReference` is only guaranteed valid in a component's `OnAfterRender` method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="cf441-195">親コンポーネントが要素参照を他のコンポーネントで使用できるようにするために、親コンポーネントは次のことを実行できます。</span><span class="sxs-lookup"><span data-stu-id="cf441-195">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="cf441-196">子コンポーネントがコールバックを登録できるようにします。</span><span class="sxs-lookup"><span data-stu-id="cf441-196">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="cf441-197">渡された要素参照を使用して、`OnAfterRender` イベント中に登録されたコールバックを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="cf441-197">Invoke the registered callbacks during the `OnAfterRender` event with the passed element reference.</span></span> <span data-ttu-id="cf441-198">間接的には、この方法により、子コンポーネントが親要素の参照と対話できるようになります。</span><span class="sxs-lookup"><span data-stu-id="cf441-198">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="cf441-199">次の Blazor の例は、この方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="cf441-199">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="cf441-200">*Wwwroot/index.html*の `<head>`:</span><span class="sxs-lookup"><span data-stu-id="cf441-200">In the `<head>` of *wwwroot/index.html*:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="cf441-201">*Wwwroot/index.html*の `<body>`:</span><span class="sxs-lookup"><span data-stu-id="cf441-201">In the `<body>` of *wwwroot/index.html*:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="cf441-202">*Pages/Index. razor* (親コンポーネント):</span><span class="sxs-lookup"><span data-stu-id="cf441-202">*Pages/Index.razor* (parent component):</span></span>

```razor
@page "/"

<h1 @ref="_title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="cf441-203">*Pages/Index. razor. .cs*:</span><span class="sxs-lookup"><span data-stu-id="cf441-203">*Pages/Index.razor.cs*:</span></span>

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

<span data-ttu-id="cf441-204">*Shared/SurveyPrompt* (子コンポーネント):</span><span class="sxs-lookup"><span data-stu-id="cf441-204">*Shared/SurveyPrompt.razor* (child component):</span></span>

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

<span data-ttu-id="cf441-205">*Shared/SurveyPrompt*:</span><span class="sxs-lookup"><span data-stu-id="cf441-205">*Shared/SurveyPrompt.razor.cs*:</span></span>

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

## <a name="invoke-net-methods-from-javascript-functions"></a><span data-ttu-id="cf441-206">JavaScript 関数からの .NET メソッドの呼び出し</span><span class="sxs-lookup"><span data-stu-id="cf441-206">Invoke .NET methods from JavaScript functions</span></span>

### <a name="static-net-method-call"></a><span data-ttu-id="cf441-207">静的 .NET メソッド呼び出し</span><span class="sxs-lookup"><span data-stu-id="cf441-207">Static .NET method call</span></span>

<span data-ttu-id="cf441-208">JavaScript から静的 .NET メソッドを呼び出すには、`DotNet.invokeMethod` または `DotNet.invokeMethodAsync` 関数を使用します。</span><span class="sxs-lookup"><span data-stu-id="cf441-208">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="cf441-209">呼び出す静的メソッドの識別子、関数を含むアセンブリの名前、および任意の引数を渡します。</span><span class="sxs-lookup"><span data-stu-id="cf441-209">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="cf441-210">Blazor サーバーのシナリオをサポートするには、非同期バージョンを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="cf441-210">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="cf441-211">.NET メソッドは、public、static、および `[JSInvokable]` 属性を持っている必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf441-211">The .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="cf441-212">オープンジェネリックメソッドを呼び出すことは現在サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="cf441-212">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="cf441-213">このサンプルアプリにはC# `int`s の配列を返すメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="cf441-213">The sample app includes a C# method to return an array of `int`s.</span></span> <span data-ttu-id="cf441-214">`JSInvokable` 属性がメソッドに適用されます。</span><span class="sxs-lookup"><span data-stu-id="cf441-214">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="cf441-215">*Pages/JsInterop*:</span><span class="sxs-lookup"><span data-stu-id="cf441-215">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="cf441-216">クライアントに提供される JavaScript はC# 、.net メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="cf441-216">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="cf441-217">*wwwroot/exampleJsInterop*:</span><span class="sxs-lookup"><span data-stu-id="cf441-217">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="cf441-218">**[.Net 静的メソッド ReturnArrayAsync のトリガー]** ボタンが選択されている場合は、ブラウザーの web developer ツールでコンソール出力を確認します。</span><span class="sxs-lookup"><span data-stu-id="cf441-218">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="cf441-219">コンソールの出力は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="cf441-219">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="cf441-220">4番目の配列値は、`ReturnArrayAsync`によって返される配列 (`data.push(4);`) にプッシュされます。</span><span class="sxs-lookup"><span data-stu-id="cf441-220">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="cf441-221">既定では、メソッド識別子はメソッド名ですが、`JSInvokableAttribute` コンストラクターを使用して別の識別子を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="cf441-221">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="cf441-222">クライアント側の JavaScript ファイルで、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="cf441-222">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

### <a name="instance-method-call"></a><span data-ttu-id="cf441-223">インスタンスメソッドの呼び出し</span><span class="sxs-lookup"><span data-stu-id="cf441-223">Instance method call</span></span>

<span data-ttu-id="cf441-224">JavaScript から .NET インスタンスメソッドを呼び出すこともできます。</span><span class="sxs-lookup"><span data-stu-id="cf441-224">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="cf441-225">JavaScript から .NET インスタンスメソッドを呼び出すには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="cf441-225">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="cf441-226">.NET インスタンスを JavaScript への参照によって渡します。</span><span class="sxs-lookup"><span data-stu-id="cf441-226">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="cf441-227">`DotNetObjectReference.Create`の静的な呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="cf441-227">Make a static call to `DotNetObjectReference.Create`.</span></span>
  * <span data-ttu-id="cf441-228">インスタンスを `DotNetObjectReference` インスタンスにラップし、`DotNetObjectReference` インスタンスで `Create` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="cf441-228">Wrap the instance in a `DotNetObjectReference` instance and call `Create` on the `DotNetObjectReference` instance.</span></span> <span data-ttu-id="cf441-229">`DotNetObjectReference` オブジェクトを破棄します (このセクションの後半で例を示します)。</span><span class="sxs-lookup"><span data-stu-id="cf441-229">Dispose of `DotNetObjectReference` objects (an example appears later in this section).</span></span>
* <span data-ttu-id="cf441-230">`invokeMethod` または `invokeMethodAsync` 関数を使用して、インスタンスで .NET インスタンスメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="cf441-230">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="cf441-231">.NET インスタンスは、JavaScript から他の .NET メソッドを呼び出すときに引数として渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="cf441-231">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="cf441-232">サンプルアプリは、メッセージをクライアント側コンソールに記録します。</span><span class="sxs-lookup"><span data-stu-id="cf441-232">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="cf441-233">サンプルアプリで示されている次の例については、ブラウザーの開発者ツールでブラウザーのコンソール出力を確認してください。</span><span class="sxs-lookup"><span data-stu-id="cf441-233">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="cf441-234">**[.Net インスタンスメソッドをトリガーする HelloHelper]** ボタンを選択すると、`ExampleJsInterop.CallHelloHelperSayHello` が呼び出され、`Blazor`の名前がメソッドに渡されます。</span><span class="sxs-lookup"><span data-stu-id="cf441-234">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="cf441-235">*Pages/JsInterop*:</span><span class="sxs-lookup"><span data-stu-id="cf441-235">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="cf441-236">`CallHelloHelperSayHello` は、`HelloHelper`の新しいインスタンスを使用して JavaScript 関数 `sayHello` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="cf441-236">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="cf441-237">*JsInteropClasses/ExampleJsInterop*:</span><span class="sxs-lookup"><span data-stu-id="cf441-237">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

<span data-ttu-id="cf441-238">*wwwroot/exampleJsInterop*:</span><span class="sxs-lookup"><span data-stu-id="cf441-238">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="cf441-239">名前は `HelloHelper`のコンストラクターに渡され、`HelloHelper.Name` プロパティが設定されます。</span><span class="sxs-lookup"><span data-stu-id="cf441-239">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="cf441-240">JavaScript 関数 `sayHello` が実行されると、`HelloHelper.SayHello` は `Hello, {Name}!` メッセージを返します。このメッセージは、JavaScript 関数によってコンソールに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="cf441-240">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="cf441-241">*JsInteropClasses/HelloHelper*:</span><span class="sxs-lookup"><span data-stu-id="cf441-241">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="cf441-242">ブラウザーの web 開発者ツールのコンソール出力:</span><span class="sxs-lookup"><span data-stu-id="cf441-242">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="cf441-243">メモリリークを回避し、`DotNetObjectReference`を作成するコンポーネントでガベージコレクションを許可するには、`DotNetObjectReference` インスタンスを作成したクラスでオブジェクトを破棄します。</span><span class="sxs-lookup"><span data-stu-id="cf441-243">To avoid a memory leak and allow garbage collection on a component that creates a `DotNetObjectReference`, dispose of the object in the class that created the `DotNetObjectReference` instance:</span></span>

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
  
<span data-ttu-id="cf441-244">`ExampleJsInterop` クラスに示されている上記のパターンは、コンポーネントに実装することもできます。</span><span class="sxs-lookup"><span data-stu-id="cf441-244">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>
  
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

## <a name="share-interop-code-in-a-class-library"></a><span data-ttu-id="cf441-245">クラスライブラリで相互運用コードを共有する</span><span class="sxs-lookup"><span data-stu-id="cf441-245">Share interop code in a class library</span></span>

<span data-ttu-id="cf441-246">JS 相互運用コードは、NuGet パッケージ内のコードを共有できるクラスライブラリに含めることができます。</span><span class="sxs-lookup"><span data-stu-id="cf441-246">JS interop code can be included in a class library, which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="cf441-247">クラスライブラリは、ビルドされたアセンブリに JavaScript リソースの埋め込みを処理します。</span><span class="sxs-lookup"><span data-stu-id="cf441-247">The class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="cf441-248">JavaScript ファイルは*wwwroot*フォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="cf441-248">The JavaScript files are placed in the *wwwroot* folder.</span></span> <span data-ttu-id="cf441-249">ツールは、ライブラリの構築時にリソースの埋め込みを行います。</span><span class="sxs-lookup"><span data-stu-id="cf441-249">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="cf441-250">ビルド済みの NuGet パッケージは、NuGet パッケージを参照するのと同じ方法で、アプリのプロジェクトファイルで参照されます。</span><span class="sxs-lookup"><span data-stu-id="cf441-250">The built NuGet package is referenced in the app's project file the same way that any NuGet package is referenced.</span></span> <span data-ttu-id="cf441-251">パッケージが復元された後、アプリコードはと同じように JavaScript C#を呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="cf441-251">After the package is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="cf441-252">詳細については、「<xref:blazor/class-libraries>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cf441-252">For more information, see <xref:blazor/class-libraries>.</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="cf441-253">JS 相互運用呼び出しの強化</span><span class="sxs-lookup"><span data-stu-id="cf441-253">Harden JS interop calls</span></span>

<span data-ttu-id="cf441-254">JS 相互運用機能は、ネットワークエラーのために失敗する可能性があり、信頼性の低いものとして扱う必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf441-254">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="cf441-255">既定では、Blazor サーバーアプリは、1分後に JS 相互運用機能の呼び出しをサーバー上でタイムアウトします。</span><span class="sxs-lookup"><span data-stu-id="cf441-255">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="cf441-256">10秒など、アプリでより積極的なタイムアウトが許容される場合は、次のいずれかの方法を使用してタイムアウトを設定します。</span><span class="sxs-lookup"><span data-stu-id="cf441-256">If an app can tolerate a more aggressive timeout, such as 10 seconds, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="cf441-257">`Startup.ConfigureServices`でグローバルに、タイムアウトを指定します。</span><span class="sxs-lookup"><span data-stu-id="cf441-257">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="cf441-258">コンポーネントコードでの呼び出しごとに、1回の呼び出しでタイムアウトを指定できます。</span><span class="sxs-lookup"><span data-stu-id="cf441-258">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="cf441-259">リソース枯渇の詳細については、「<xref:security/blazor/server>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cf441-259">For more information on resource exhaustion, see <xref:security/blazor/server>.</span></span>

## <a name="perform-large-data-transfers-in-opno-locblazor-server-apps"></a><span data-ttu-id="cf441-260">Blazor Server アプリで大規模なデータ転送を実行する</span><span class="sxs-lookup"><span data-stu-id="cf441-260">Perform large data transfers in Blazor Server apps</span></span>

<span data-ttu-id="cf441-261">場合によっては、JavaScript と Blazorの間で大量のデータを転送する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf441-261">In some scenarios, large amounts of data must be transferred between JavaScript and Blazor.</span></span> <span data-ttu-id="cf441-262">通常、大規模なデータ転送は次の場合に発生します。</span><span class="sxs-lookup"><span data-stu-id="cf441-262">Typically, large data transfers occur when:</span></span>

* <span data-ttu-id="cf441-263">ブラウザーファイルシステム Api は、ファイルをアップロードまたはダウンロードするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="cf441-263">Browser file system APIs are used to upload or download a file.</span></span>
* <span data-ttu-id="cf441-264">サードパーティのライブラリとの相互運用が必要です。</span><span class="sxs-lookup"><span data-stu-id="cf441-264">Interop with a third party library is required.</span></span>

<span data-ttu-id="cf441-265">Blazor Server では、パフォーマンスの問題につながる可能性のある1つの大きなメッセージを渡すことができないように制限されています。</span><span class="sxs-lookup"><span data-stu-id="cf441-265">In Blazor Server, a limitation is in place to prevent passing single large messages that may result in performance issues.</span></span>

<span data-ttu-id="cf441-266">JavaScript と Blazor間でデータを転送するコードを開発するときは、次のガイダンスを考慮してください。</span><span class="sxs-lookup"><span data-stu-id="cf441-266">Consider the following guidance when developing code that transfers data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="cf441-267">データをより小さな部分に分割し、すべてのデータがサーバーによって受信されるまでデータセグメントを順番に送信します。</span><span class="sxs-lookup"><span data-stu-id="cf441-267">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="cf441-268">JavaScript およびC#コードでラージオブジェクトを割り当てないでください。</span><span class="sxs-lookup"><span data-stu-id="cf441-268">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="cf441-269">データを送受信するときに、メイン UI スレッドを長時間ブロックしないでください。</span><span class="sxs-lookup"><span data-stu-id="cf441-269">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="cf441-270">プロセスの完了時またはキャンセル時に消費されるメモリを解放します。</span><span class="sxs-lookup"><span data-stu-id="cf441-270">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="cf441-271">セキュリティ上の理由から、次の追加要件を適用します。</span><span class="sxs-lookup"><span data-stu-id="cf441-271">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="cf441-272">渡すことのできるファイルまたはデータの最大サイズを宣言します。</span><span class="sxs-lookup"><span data-stu-id="cf441-272">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="cf441-273">クライアントからサーバーへの最小アップロードレートを宣言します。</span><span class="sxs-lookup"><span data-stu-id="cf441-273">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="cf441-274">データがサーバーによって受信された後、データは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="cf441-274">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="cf441-275">すべてのセグメントが収集されるまで、一時的にメモリバッファーに格納されます。</span><span class="sxs-lookup"><span data-stu-id="cf441-275">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="cf441-276">直ちに消費されます。</span><span class="sxs-lookup"><span data-stu-id="cf441-276">Consumed immediately.</span></span> <span data-ttu-id="cf441-277">たとえば、データは、データベースに直ちに格納することも、各セグメントを受信するたびにディスクに書き込むこともできます。</span><span class="sxs-lookup"><span data-stu-id="cf441-277">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

<span data-ttu-id="cf441-278">次の file アップローダークラスは、クライアントとの JS 相互運用機能を処理します。</span><span class="sxs-lookup"><span data-stu-id="cf441-278">The following file uploader class handles JS interop with the client.</span></span> <span data-ttu-id="cf441-279">アップローダークラスは、JS 相互運用機能を使用して次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="cf441-279">The uploader class uses JS interop to:</span></span>

* <span data-ttu-id="cf441-280">データセグメントを送信するクライアントをポーリングします。</span><span class="sxs-lookup"><span data-stu-id="cf441-280">Poll the client to send a data segment.</span></span>
* <span data-ttu-id="cf441-281">ポーリングがタイムアウトした場合は、トランザクションを中止します。</span><span class="sxs-lookup"><span data-stu-id="cf441-281">Abort the transaction if polling times out.</span></span>

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

<span data-ttu-id="cf441-282">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="cf441-282">In the preceding example:</span></span>

* <span data-ttu-id="cf441-283">`_maxBase64SegmentSize` は `8192`に設定されます。これは `_maxBase64SegmentSize = _segmentSize * 4 / 3`から計算されます。</span><span class="sxs-lookup"><span data-stu-id="cf441-283">The `_maxBase64SegmentSize` is set to `8192`, which is calculated from `_maxBase64SegmentSize = _segmentSize * 4 / 3`.</span></span>
* <span data-ttu-id="cf441-284">低レベルの .NET Core メモリ管理 Api は、`_uploadedSegments`のサーバーにメモリセグメントを格納するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="cf441-284">Low level .NET Core memory management APIs are used to store the memory segments on the server in `_uploadedSegments`.</span></span>
* <span data-ttu-id="cf441-285">`ReceiveFile` メソッドは、JS 相互運用機能によるアップロードを処理するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="cf441-285">A `ReceiveFile` method is used to handle the upload through JS interop:</span></span>
  * <span data-ttu-id="cf441-286">ファイルサイズは、`_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`との JS 相互運用によってバイト単位で決定されます。</span><span class="sxs-lookup"><span data-stu-id="cf441-286">The file size is determined in bytes through JS interop with `_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.</span></span>
  * <span data-ttu-id="cf441-287">受信するセグメントの数が計算され、`numberOfSegments`に格納されます。</span><span class="sxs-lookup"><span data-stu-id="cf441-287">The number of segments to receive are calculated and stored in `numberOfSegments`.</span></span>
  * <span data-ttu-id="cf441-288">セグメントは、`_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`との JS 相互運用を通じて `for` ループで要求されます。</span><span class="sxs-lookup"><span data-stu-id="cf441-288">The segments are requested in a `for` loop through JS interop with `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`.</span></span> <span data-ttu-id="cf441-289">デコードする前に、すべてのセグメントが8192バイトである必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf441-289">All segments but the last must be 8,192 bytes before decoding.</span></span> <span data-ttu-id="cf441-290">クライアントは、効率的な方法でデータを送信することを強制されます。</span><span class="sxs-lookup"><span data-stu-id="cf441-290">The client is forced to send the data in an efficient manner.</span></span>
  * <span data-ttu-id="cf441-291">受信した各セグメントに対して、<xref:System.Convert.TryFromBase64String*>でデコードする前にチェックが実行されます。</span><span class="sxs-lookup"><span data-stu-id="cf441-291">For each segment received, checks are performed before decoding with <xref:System.Convert.TryFromBase64String*>.</span></span>
  * <span data-ttu-id="cf441-292">アップロードが完了すると、データを含むストリームが新しい <xref:System.IO.Stream> (`SegmentedStream`) として返されます。</span><span class="sxs-lookup"><span data-stu-id="cf441-292">A stream with the data is returned as a new <xref:System.IO.Stream> (`SegmentedStream`) after the upload is complete.</span></span>

<span data-ttu-id="cf441-293">セグメント化されたストリームクラスは、セグメントの一覧を読み取り専用ではない <xref:System.IO.Stream>として公開します。</span><span class="sxs-lookup"><span data-stu-id="cf441-293">The segmented stream class exposes the list of segments as a readonly non-seekable <xref:System.IO.Stream>:</span></span>

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

<span data-ttu-id="cf441-294">次のコードは、データを受け取る JavaScript 関数を実装しています。</span><span class="sxs-lookup"><span data-stu-id="cf441-294">The following code implements JavaScript functions to receive the data:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="cf441-295">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="cf441-295">Additional resources</span></span>

* [<span data-ttu-id="cf441-296">InteropComponent の例 (dotnet/AspNetCore GitHub リポジトリ、3.1 リリースブランチ)</span><span class="sxs-lookup"><span data-stu-id="cf441-296">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
