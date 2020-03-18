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
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-opno-locblazor"></a><span data-ttu-id="209a0-103">ASP.NET Core Blazor で .NET メソッドから JavaScript 関数を呼び出す</span><span class="sxs-lookup"><span data-stu-id="209a0-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="209a0-104">作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="209a0-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="209a0-105">Blazor アプリでは、.NET メソッドから JavaScript 関数を呼び出すことも、JavaScript 関数から .NET メソッドを呼び出すこともできます。</span><span class="sxs-lookup"><span data-stu-id="209a0-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="209a0-106">これらのシナリオは、"*JavaScript 相互運用*" ("*JS 相互運用*") と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="209a0-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="209a0-107">この記事では、.NET から JavaScript 関数を呼び出す方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="209a0-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="209a0-108">JavaScript から .NET メソッドを呼び出す方法については、「<xref:blazor/call-dotnet-from-javascript>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="209a0-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="209a0-109">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="209a0-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="209a0-110">.NET から JavaScript を呼び出すには、`IJSRuntime` 抽象化を使用します。</span><span class="sxs-lookup"><span data-stu-id="209a0-110">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="209a0-111">JS 相互運用呼び出しを発行するには、コンポーネントに `IJSRuntime` 抽象化を挿入します。</span><span class="sxs-lookup"><span data-stu-id="209a0-111">To issue JS interop calls, inject the `IJSRuntime` abstraction in your component.</span></span> <span data-ttu-id="209a0-112">`InvokeAsync<T>` メソッドでは、JSON シリアル化可能な任意の数の引数と共に呼び出す JavaScript 関数の識別子を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="209a0-112">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="209a0-113">関数の識別子は、グローバル スコープ (`window`) に関連しています。</span><span class="sxs-lookup"><span data-stu-id="209a0-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="209a0-114">`window.someScope.someFunction` を呼び出す場合、識別子は `someScope.someFunction` です。</span><span class="sxs-lookup"><span data-stu-id="209a0-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="209a0-115">関数は、呼び出す前に登録する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="209a0-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="209a0-116">また、戻り値の型 `T` も JSON シリアル化可能である必要があります。</span><span class="sxs-lookup"><span data-stu-id="209a0-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="209a0-117">`T` は、返される JSON 型に最適にマップされる .NET 型と一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="209a0-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="209a0-118">Blazor サーバー アプリでプリレンダリングが有効になっている場合、最初のプリレンダリング中に JavaScript を呼び出すことはできません。</span><span class="sxs-lookup"><span data-stu-id="209a0-118">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="209a0-119">JavaScript 相互運用呼び出しは、ブラウザーとの接続が確立されるまで遅延させる必要があります。</span><span class="sxs-lookup"><span data-stu-id="209a0-119">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="209a0-120">詳細については、「[Blazor サーバー アプリがプリレンダリングされていることを検出する](#detect-when-a-blazor-server-app-is-prerendering)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="209a0-120">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="209a0-121">次の例は、JavaScript ベースのデコーダーである [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder) に基づいています。</span><span class="sxs-lookup"><span data-stu-id="209a0-121">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="209a0-122">この例では、C# メソッドから JavaScript 関数を呼び出す方法を示します。</span><span class="sxs-lookup"><span data-stu-id="209a0-122">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="209a0-123">JavaScript 関数は、C# メソッドからバイト配列を受け取り、配列をデコードし、テキストをコンポーネントに返して表示できるようにします。</span><span class="sxs-lookup"><span data-stu-id="209a0-123">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="209a0-124">*wwwroot/index.html* (Blazor WebAssembly) または *Pages/_Host.cshtml* (Blazor サーバー) の `<head>` 要素内で、`TextDecoder` を使用して、渡された配列をデコードし、デコードした値を返す JavaScript 関数を提供します。</span><span class="sxs-lookup"><span data-stu-id="209a0-124">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="209a0-125">JavaScript コードでは、前の例で示したコードのように、スクリプト ファイルへの参照を使用して JavaScript ファイル ( *.js*) から読み込むこともできます。</span><span class="sxs-lookup"><span data-stu-id="209a0-125">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="209a0-126">次のコンポーネント:</span><span class="sxs-lookup"><span data-stu-id="209a0-126">The following component:</span></span>

* <span data-ttu-id="209a0-127">コンポーネント ボタン (**配列の変換**) が選択された場合、`JSRuntime` を使用して `convertArray` JavaScript 関数を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="209a0-127">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="209a0-128">JavaScript 関数が呼び出されると、渡された配列が文字列に変換されます。</span><span class="sxs-lookup"><span data-stu-id="209a0-128">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="209a0-129">文字列は、表示できるようにコンポーネントに返されます。</span><span class="sxs-lookup"><span data-stu-id="209a0-129">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="209a0-130">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="209a0-130">IJSRuntime</span></span>

<span data-ttu-id="209a0-131">`IJSRuntime` 抽象化を使用するには、次のいずれかの方法を採用します。</span><span class="sxs-lookup"><span data-stu-id="209a0-131">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="209a0-132">Razor コンポーネント ( *.razor*) に `IJSRuntime` 抽象化を挿入します。</span><span class="sxs-lookup"><span data-stu-id="209a0-132">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="209a0-133">*wwwroot/index.html* (Blazor WebAssembly) または *Pages/_Host.cshtml* (Blazor サーバー) の `<head>` 要素内で、`handleTickerChanged` JavaScript 関数を提供します。</span><span class="sxs-lookup"><span data-stu-id="209a0-133">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="209a0-134">関数は `IJSRuntime.InvokeVoidAsync` を指定して呼び出され、値を返しません。</span><span class="sxs-lookup"><span data-stu-id="209a0-134">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="209a0-135">`IJSRuntime` 抽象化をクラス ( *.cs*) に挿入します。</span><span class="sxs-lookup"><span data-stu-id="209a0-135">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="209a0-136">*wwwroot/index.html* (Blazor WebAssembly) または *Pages/_Host.cshtml* (Blazor サーバー) の `<head>` 要素内で、`handleTickerChanged` JavaScript 関数を提供します。</span><span class="sxs-lookup"><span data-stu-id="209a0-136">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="209a0-137">関数は `JSRuntime.InvokeAsync` を指定して呼び出され、値を返します。</span><span class="sxs-lookup"><span data-stu-id="209a0-137">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="209a0-138">[BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic) でコンテンツを動的に生成するには、`[Inject]` 属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="209a0-138">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="209a0-139">このトピックに添付されているクライアント側のサンプル アプリでは、ユーザー入力を受け取り、ウェルカム メッセージを表示するために、DOM とやりとりする 2 つの JavaScript 関数をアプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="209a0-139">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="209a0-140">`showPrompt` &ndash; ユーザー入力 (ユーザーの名前) を受け入れるプロンプトを生成し、名前を呼び出し元に返します。</span><span class="sxs-lookup"><span data-stu-id="209a0-140">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="209a0-141">`displayWelcome` &ndash; `welcome` の `id` を持つ DOM オブジェクトに、呼び出し元からのウェルカム メッセージを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="209a0-141">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="209a0-142">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="209a0-142">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="209a0-143">JavaScript ファイルを参照する `<script>` タグを *wwwroot/index.html* ファイル (Blazor WebAssembly) または *Pages/_Host.cshtml* ファイル (Blazor サーバー) に配置します。</span><span class="sxs-lookup"><span data-stu-id="209a0-143">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server).</span></span>

<span data-ttu-id="209a0-144">*wwwroot/index.html* (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="209a0-144">*wwwroot/index.html* (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="209a0-145">*Pages/_Host cshtml* (Blazor サーバー):</span><span class="sxs-lookup"><span data-stu-id="209a0-145">*Pages/_Host.cshtml* (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="209a0-146">`<script>` タグを動的に更新できないため、`<script>` タグをコンポーネント ファイル内に配置しないでください。</span><span class="sxs-lookup"><span data-stu-id="209a0-146">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="209a0-147">.NET メソッドは、`IJSRuntime.InvokeAsync<T>` を呼び出して、*exampleJsInterop* ファイル内の JavaScript 関数と相互運用します。</span><span class="sxs-lookup"><span data-stu-id="209a0-147">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="209a0-148">`IJSRuntime` 抽象化は、Blazor サーバーのシナリオを可能にするために非同期です。</span><span class="sxs-lookup"><span data-stu-id="209a0-148">The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="209a0-149">アプリが Blazor WebAssembly アプリであり、JavaScript 関数を同期的に呼び出す必要がある場合は、`IJSInProcessRuntime` にダウンキャストし、代わりに `Invoke<T>` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="209a0-149">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="209a0-150">ほとんどの JS 相互運用ライブラリでは、確実にすべてのシナリオでライブラリを使用できるように、非同期 API を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="209a0-150">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="209a0-151">サンプル アプリには、JS 相互運用を示すコンポーネントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="209a0-151">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="209a0-152">コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="209a0-152">The component:</span></span>

* <span data-ttu-id="209a0-153">JavaScript プロンプトを介してユーザー入力を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="209a0-153">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="209a0-154">テキストをコンポーネントに返して処理します。</span><span class="sxs-lookup"><span data-stu-id="209a0-154">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="209a0-155">DOM とやりとりしてウェルカム メッセージを表示する 2 番目の JavaScript 関数を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="209a0-155">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="209a0-156">*Pages/JSInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="209a0-156">*Pages/JSInterop.razor*:</span></span>

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

1. <span data-ttu-id="209a0-157">コンポーネントの **[Trigger JavaScript Prompt]** ボタンを選択して `TriggerJsPrompt` を実行すると、*wwwroot/exampleJsInterop.js* ファイル内に指定した JavaScript `showPrompt` 関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="209a0-157">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="209a0-158">`showPrompt` 関数は、ユーザー入力 (ユーザーの名前) を受け取ります。これは、HTML エンコードされ、コンポーネントに返されます。</span><span class="sxs-lookup"><span data-stu-id="209a0-158">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="209a0-159">コンポーネントにより、ユーザーの名前がローカル変数 `name` に格納されます。</span><span class="sxs-lookup"><span data-stu-id="209a0-159">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="209a0-160">`name` に格納された文字列は、ウェルカム メッセージに組み込まれます。このメッセージが JavaScript 関数 `displayWelcome` に渡され、ウェルカム メッセージが見出しタグにレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="209a0-160">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="209a0-161">void JavaScript 関数を呼び出す</span><span class="sxs-lookup"><span data-stu-id="209a0-161">Call a void JavaScript function</span></span>

<span data-ttu-id="209a0-162">[void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) または [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) を返す JavaScript 関数は、`IJSRuntime.InvokeVoidAsync` を指定して呼び出します。</span><span class="sxs-lookup"><span data-stu-id="209a0-162">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeVoidAsync`.</span></span>

## <a name="detect-when-a-opno-locblazor-server-app-is-prerendering"></a><span data-ttu-id="209a0-163">Blazor サーバー アプリがプリレンダリングされていることを検出する</span><span class="sxs-lookup"><span data-stu-id="209a0-163">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="209a0-164">要素への参照をキャプチャする</span><span class="sxs-lookup"><span data-stu-id="209a0-164">Capture references to elements</span></span>

<span data-ttu-id="209a0-165">一部の JS 相互運用シナリオでは、HTML 要素への参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="209a0-165">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="209a0-166">たとえば、UI ライブラリで初期化のための要素参照が必要な場合、`focus` や `play` などの要素でコマンドのような API の呼び出し必要になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="209a0-166">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="209a0-167">次の方法を使用して、コンポーネント内の HTML 要素への参照をキャプチャします。</span><span class="sxs-lookup"><span data-stu-id="209a0-167">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="209a0-168">`@ref` 属性を HTML 要素に追加します。</span><span class="sxs-lookup"><span data-stu-id="209a0-168">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="209a0-169">名前が `@ref` 属性の値に一致する `ElementReference` 型のフィールドを定義します。</span><span class="sxs-lookup"><span data-stu-id="209a0-169">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="209a0-170">次の例は、`username` `<input>` 要素への参照をキャプチャする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="209a0-170">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="209a0-171">Blazor とやりとりしない空の要素のコンテンツを変化させるには、要素参照のみを使用します。</span><span class="sxs-lookup"><span data-stu-id="209a0-171">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="209a0-172">このシナリオは、サードパーティの API が要素にコンテンツを提供する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="209a0-172">This scenario is useful when a 3rd party API supplies content to the element.</span></span> <span data-ttu-id="209a0-173">Blazor は要素とやりとりしないため、Blazor の要素表現と DOM との間に競合が発生する可能性がありません。</span><span class="sxs-lookup"><span data-stu-id="209a0-173">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="209a0-174">次の例では、Blazor が DOM とやりとりしてこの要素のリスト項目 (`<li>`) を設定するため、順序なしリスト (`ul`) のコンテンツを変化させるのは "*危険*" です。</span><span class="sxs-lookup"><span data-stu-id="209a0-174">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="209a0-175">JS 相互運用により要素 `MyList` のコンテンツが変更され、Blazor でその要素に差分を適用しようとした場合、差分は DOM と一致しません。</span><span class="sxs-lookup"><span data-stu-id="209a0-175">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="209a0-176">.NET コードに関しては、`ElementReference` は不透明なハンドルです。</span><span class="sxs-lookup"><span data-stu-id="209a0-176">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="209a0-177">`ElementReference` を使用して実行できるのは、JS 相互運用を介して JavaScript コードに渡すこと "*のみ*" です。</span><span class="sxs-lookup"><span data-stu-id="209a0-177">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="209a0-178">これを行うと、JavaScript 側のコードが `HTMLElement` インスタンスを受け取り、通常の DOM API で使用できます。</span><span class="sxs-lookup"><span data-stu-id="209a0-178">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="209a0-179">たとえば、次のコードでは、要素にフォーカスを設定できるようにする .NET 拡張メソッドを定義しています。</span><span class="sxs-lookup"><span data-stu-id="209a0-179">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="209a0-180">*exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="209a0-180">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="209a0-181">値を返さない JavaScript 関数を呼び出すには、`IJSRuntime.InvokeVoidAsync` を使用します。</span><span class="sxs-lookup"><span data-stu-id="209a0-181">To call a JavaScript function that doesn't return a value, use `IJSRuntime.InvokeVoidAsync`.</span></span> <span data-ttu-id="209a0-182">次のコードは、キャプチャされた `ElementReference` で前述の JavaScript 関数を呼び出して、ユーザー名入力にフォーカスを設定します。</span><span class="sxs-lookup"><span data-stu-id="209a0-182">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured `ElementReference`:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="209a0-183">拡張メソッドを使用するには、`IJSRuntime` インスタンスを受け取る静的拡張メソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="209a0-183">To use an extension method, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="209a0-184">`Focus` メソッドは、オブジェクトで直接呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="209a0-184">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="209a0-185">次の例では、`Focus` メソッドが `JsInteropClasses` 名前空間から使用できることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="209a0-185">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="209a0-186">`username` 変数は、コンポーネントがレンダリングされた後にのみ設定されます。</span><span class="sxs-lookup"><span data-stu-id="209a0-186">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="209a0-187">未入力の `ElementReference` が JavaScript コードに渡された場合、JavaScript コードは `null` の値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="209a0-187">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="209a0-188">コンポーネントのレンダリングが完了した後に要素参照を操作する (要素に初期フォーカスを設定する) には、[OnAfterRenderAsync または OnAfterRender コンポーネント ライフサイクル メソッド](xref:blazor/lifecycle#after-component-render)を使用します。</span><span class="sxs-lookup"><span data-stu-id="209a0-188">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [OnAfterRenderAsync or OnAfterRender component lifecycle methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="209a0-189">ジェネリック型を操作して値を返す場合は、[ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1) を使用します。</span><span class="sxs-lookup"><span data-stu-id="209a0-189">When working with generic types and returning a value, use [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="209a0-190">`GenericMethod` は、型を持つオブジェクトで直接呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="209a0-190">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="209a0-191">次の例では、`GenericMethod` が `JsInteropClasses` 名前空間から使用できることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="209a0-191">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="209a0-192">コンポーネント間で要素を参照する</span><span class="sxs-lookup"><span data-stu-id="209a0-192">Reference elements across components</span></span>

<span data-ttu-id="209a0-193">`ElementReference` は、コンポーネントの `OnAfterRender` メソッドでのみ有効であることが保証されます (要素参照は `struct` です)。そのため、コンポーネント間で要素参照を渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="209a0-193">An `ElementReference` is only guaranteed valid in a component's `OnAfterRender` method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="209a0-194">親コンポーネントが要素参照を他のコンポーネントで使用できるようにするために、親コンポーネントは次のことを実行できます。</span><span class="sxs-lookup"><span data-stu-id="209a0-194">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="209a0-195">子コンポーネントがコールバックを登録できるようにします。</span><span class="sxs-lookup"><span data-stu-id="209a0-195">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="209a0-196">渡された要素参照を使用して、登録されたコールバックを`OnAfterRender` イベント中に呼び出します。</span><span class="sxs-lookup"><span data-stu-id="209a0-196">Invoke the registered callbacks during the `OnAfterRender` event with the passed element reference.</span></span> <span data-ttu-id="209a0-197">間接的には、この方法により、子コンポーネントが親の要素参照とやりとりできるようになります。</span><span class="sxs-lookup"><span data-stu-id="209a0-197">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="209a0-198">次の Blazor WebAssembly の例は、この方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="209a0-198">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="209a0-199">*wwwroot/index.html* の `<head>` 内:</span><span class="sxs-lookup"><span data-stu-id="209a0-199">In the `<head>` of *wwwroot/index.html*:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="209a0-200">*wwwroot/index.html* の `<body>` 内:</span><span class="sxs-lookup"><span data-stu-id="209a0-200">In the `<body>` of *wwwroot/index.html*:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="209a0-201">*Pages/Index. razor* (親コンポーネント):</span><span class="sxs-lookup"><span data-stu-id="209a0-201">*Pages/Index.razor* (parent component):</span></span>

```razor
@page "/"

<h1 @ref="_title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="209a0-202">*Pages/Index.razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="209a0-202">*Pages/Index.razor.cs*:</span></span>

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

<span data-ttu-id="209a0-203">*Shared/SurveyPrompt.razor* (子コンポーネント):</span><span class="sxs-lookup"><span data-stu-id="209a0-203">*Shared/SurveyPrompt.razor* (child component):</span></span>

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

<span data-ttu-id="209a0-204">*Shared/SurveyPrompt.razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="209a0-204">*Shared/SurveyPrompt.razor.cs*:</span></span>

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

## <a name="harden-js-interop-calls"></a><span data-ttu-id="209a0-205">JS 相互運用呼び出しの強化</span><span class="sxs-lookup"><span data-stu-id="209a0-205">Harden JS interop calls</span></span>

<span data-ttu-id="209a0-206">JS 相互運用は、ネットワーク エラーにより失敗する可能性があるため、信頼性の低いものとして扱う必要があります。</span><span class="sxs-lookup"><span data-stu-id="209a0-206">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="209a0-207">既定では、Blazor サーバー アプリでは、サーバー上の JS 相互運用の呼び出しは 1 分後にタイムアウトします。</span><span class="sxs-lookup"><span data-stu-id="209a0-207">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="209a0-208">10 秒など、アプリでより積極的なタイムアウトが許容される場合は、次のいずれかの方法を使用してタイムアウトを設定します。</span><span class="sxs-lookup"><span data-stu-id="209a0-208">If an app can tolerate a more aggressive timeout, such as 10 seconds, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="209a0-209">`Startup.ConfigureServices` でグローバルに、タイムアウトを指定します。</span><span class="sxs-lookup"><span data-stu-id="209a0-209">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="209a0-210">コンポーネント コードでの呼び出しごとに、1 回の呼び出しでタイムアウトを指定できます。</span><span class="sxs-lookup"><span data-stu-id="209a0-210">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="209a0-211">リソース枯渇の詳細については、「<xref:security/blazor/server>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="209a0-211">For more information on resource exhaustion, see <xref:security/blazor/server>.</span></span>

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="additional-resources"></a><span data-ttu-id="209a0-212">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="209a0-212">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="209a0-213">InteropComponent.razor の例 (dotnet/AspNetCore GitHub リポジトリ、3.1 リリース ブランチ)</span><span class="sxs-lookup"><span data-stu-id="209a0-213">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="209a0-214">[Blazor サーバー アプリで大規模なデータ転送を実行する](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="209a0-214">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
