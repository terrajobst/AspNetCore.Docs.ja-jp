---
title: ASP.NET Core Blazor JavaScript 相互運用機能
author: guardrex
description: Blazor アプリで JavaScript から .NET および .NET メソッドから JavaScript 関数を呼び出す方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/16/2019
uid: blazor/javascript-interop
ms.openlocfilehash: b157e16918975cd522318a02f21824d9a0198b11
ms.sourcegitcommit: eb4fcdeb2f9e8413117624de42841a4997d1d82d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697917"
---
# <a name="aspnet-core-blazor-javascript-interop"></a><span data-ttu-id="6e095-103">ASP.NET Core Blazor JavaScript 相互運用機能</span><span class="sxs-lookup"><span data-stu-id="6e095-103">ASP.NET Core Blazor JavaScript interop</span></span>

<span data-ttu-id="6e095-104">[Javier Calvarro jeannine](https://github.com/javiercn)、 [Daniel Roth](https://github.com/danroth27)、 [Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6e095-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="6e095-105">Blazor アプリは、javascript コードから .NET および .NET メソッドから JavaScript 関数を呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="6e095-105">A Blazor app can invoke JavaScript functions from .NET and .NET methods from JavaScript code.</span></span>

<span data-ttu-id="6e095-106">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="6e095-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="invoke-javascript-functions-from-net-methods"></a><span data-ttu-id="6e095-107">.NET メソッドから JavaScript 関数を呼び出す</span><span class="sxs-lookup"><span data-stu-id="6e095-107">Invoke JavaScript functions from .NET methods</span></span>

<span data-ttu-id="6e095-108">JavaScript 関数を呼び出すために .NET コードが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="6e095-108">There are times when .NET code is required to call a JavaScript function.</span></span> <span data-ttu-id="6e095-109">たとえば、JavaScript 呼び出しは、ブラウザーの機能や機能を JavaScript ライブラリからアプリに公開できます。</span><span class="sxs-lookup"><span data-stu-id="6e095-109">For example, a JavaScript call can expose browser capabilities or functionality from a JavaScript library to the app.</span></span>

<span data-ttu-id="6e095-110">.NET から JavaScript を呼び出すには、`IJSRuntime` 抽象化を使用します。</span><span class="sxs-lookup"><span data-stu-id="6e095-110">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="6e095-111">@No__t_0 メソッドは、任意の数の JSON シリアル化可能な引数と共に呼び出す JavaScript 関数の識別子を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="6e095-111">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="6e095-112">関数識別子は、グローバルスコープ (`window`) に対して相対的です。</span><span class="sxs-lookup"><span data-stu-id="6e095-112">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="6e095-113">@No__t_0 を呼び出す場合は、識別子が `someScope.someFunction` ます。</span><span class="sxs-lookup"><span data-stu-id="6e095-113">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="6e095-114">呼び出される前に、関数を登録する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="6e095-114">There's no need to register the function before it's called.</span></span> <span data-ttu-id="6e095-115">戻り値の型 `T` も JSON シリアル化可能である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6e095-115">The return type `T` must also be JSON serializable.</span></span>

<span data-ttu-id="6e095-116">Blazor Server アプリの場合:</span><span class="sxs-lookup"><span data-stu-id="6e095-116">For Blazor Server apps:</span></span>

* <span data-ttu-id="6e095-117">複数のユーザー要求は、Blazor Server アプリによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="6e095-117">Multiple user requests are processed by the Blazor Server app.</span></span> <span data-ttu-id="6e095-118">JavaScript 関数を呼び出すために、コンポーネントで `JSRuntime.Current` を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="6e095-118">Don't call `JSRuntime.Current` in a component to invoke JavaScript functions.</span></span>
* <span data-ttu-id="6e095-119">@No__t_0 の抽象化を挿入し、挿入されたオブジェクトを使用して、JavaScript の相互運用呼び出しを発行します。</span><span class="sxs-lookup"><span data-stu-id="6e095-119">Inject the `IJSRuntime` abstraction and use the injected object to issue JavaScript interop calls.</span></span>
* <span data-ttu-id="6e095-120">Blazor アプリのプリレンダリング中に、ブラウザーとの接続が確立されていないため、JavaScript を呼び出すことはできません。</span><span class="sxs-lookup"><span data-stu-id="6e095-120">While a Blazor app is prerendering, calling into JavaScript isn't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="6e095-121">詳細については、「 [Blazor アプリのプリレンダリングを検出する](#detect-when-a-blazor-app-is-prerendering)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e095-121">For more information, see the [Detect when a Blazor app is prerendering](#detect-when-a-blazor-app-is-prerendering) section.</span></span>

<span data-ttu-id="6e095-122">次の例は、JavaScript ベースの試験的なデコーダーである[Textdecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder)に基づいています。</span><span class="sxs-lookup"><span data-stu-id="6e095-122">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), an experimental JavaScript-based decoder.</span></span> <span data-ttu-id="6e095-123">この例では、 C#メソッドから JavaScript 関数を呼び出す方法を示します。</span><span class="sxs-lookup"><span data-stu-id="6e095-123">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="6e095-124">JavaScript 関数は、 C#メソッドからバイト配列を受け取り、配列をデコードし、テキストをコンポーネントに返して表示できるようにします。</span><span class="sxs-lookup"><span data-stu-id="6e095-124">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="6e095-125">*Wwwroot/index.html* (Blazor WebAssembly) または*Pages/* (Blazor Server) の `<head>` 要素内で、`TextDecoder` を使用して渡された配列をデコードし、デコードされた値を返す JavaScript 関数を提供します。</span><span class="sxs-lookup"><span data-stu-id="6e095-125">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](javascript-interop/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="6e095-126">前の例で示したコードなどの JavaScript コードは、スクリプトファイルへの参照を使用して JavaScript ファイル ( *.js*) から読み込むこともできます。</span><span class="sxs-lookup"><span data-stu-id="6e095-126">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="6e095-127">次のコンポーネント:</span><span class="sxs-lookup"><span data-stu-id="6e095-127">The following component:</span></span>

* <span data-ttu-id="6e095-128">コンポーネントボタン (**配列の変換**) が選択されている場合に `JSRuntime` を使用して `convertArray` JavaScript 関数を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="6e095-128">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="6e095-129">JavaScript 関数が呼び出されると、渡された配列が文字列に変換されます。</span><span class="sxs-lookup"><span data-stu-id="6e095-129">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="6e095-130">文字列は、表示のためにコンポーネントに返されます。</span><span class="sxs-lookup"><span data-stu-id="6e095-130">The string is returned to the component for display.</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

##  <a name="use-of-ijsruntime"></a><span data-ttu-id="6e095-131">IJSRuntime の使用</span><span class="sxs-lookup"><span data-stu-id="6e095-131">Use of IJSRuntime</span></span>

<span data-ttu-id="6e095-132">@No__t_0 の抽象化を使用するには、次のいずれかの方法を採用します。</span><span class="sxs-lookup"><span data-stu-id="6e095-132">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="6e095-133">Razor コンポーネント (*razor*) に `IJSRuntime` の抽象化を挿入します。</span><span class="sxs-lookup"><span data-stu-id="6e095-133">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-cshtml[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="6e095-134">*Wwwroot/index.html* (Blazor WebAssembly または*Pages/_Host* (Blazor Server) の `<head>` 要素内で、`handleTickerChanged` JavaScript 関数を提供します。</span><span class="sxs-lookup"><span data-stu-id="6e095-134">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="6e095-135">関数は `IJSRuntime.InvokeVoidAsync` と共に呼び出され、値を返しません。</span><span class="sxs-lookup"><span data-stu-id="6e095-135">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="6e095-136">@No__t_0 の抽象化をクラス ( *.cs*) に挿入します。</span><span class="sxs-lookup"><span data-stu-id="6e095-136">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="6e095-137">*Wwwroot/index.html* (Blazor WebAssembly または*Pages/_Host* (Blazor Server) の `<head>` 要素内で、`handleTickerChanged` JavaScript 関数を提供します。</span><span class="sxs-lookup"><span data-stu-id="6e095-137">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="6e095-138">関数は `JSRuntime.InvokeAsync` を指定して呼び出され、値を返します。</span><span class="sxs-lookup"><span data-stu-id="6e095-138">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="6e095-139">[BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic)を使用した動的なコンテンツ生成の場合は、`[Inject]` 属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="6e095-139">For dynamic content generation with [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```csharp
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="6e095-140">このトピックに付属しているクライアント側のサンプルアプリでは、ユーザー入力を受け取り、ウェルカムメッセージを表示するために、DOM と対話する2つの JavaScript 関数をアプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="6e095-140">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="6e095-141">`showPrompt` &ndash; を指定すると、ユーザー入力 (ユーザーの名前) を受け入れ、呼び出し元に名前を返すように求めるメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="6e095-141">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="6e095-142">`displayWelcome` &ndash; は、`welcome` の `id` を使用して、呼び出し元からのウェルカムメッセージを DOM オブジェクトに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="6e095-142">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="6e095-143">*wwwroot/exampleJsInterop*:</span><span class="sxs-lookup"><span data-stu-id="6e095-143">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="6e095-144">JavaScript ファイルを参照する `<script>` タグを、 *wwwroot/index.html*ファイル (Blazor WebAssembly) または*Pages/_Host*ファイル (Blazor Server) に配置します。</span><span class="sxs-lookup"><span data-stu-id="6e095-144">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server).</span></span>

<span data-ttu-id="6e095-145">*wwwroot/index.html* (Blazor webas):</span><span class="sxs-lookup"><span data-stu-id="6e095-145">*wwwroot/index.html* (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=15)]

<span data-ttu-id="6e095-146">*Pages/_Host* (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="6e095-146">*Pages/_Host.cshtml* (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=21)]

<span data-ttu-id="6e095-147">@No__t_1 タグを動的に更新できないため、コンポーネントファイルに `<script>` タグを配置しないでください。</span><span class="sxs-lookup"><span data-stu-id="6e095-147">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="6e095-148">.NET メソッドは、`IJSRuntime.InvokeAsync<T>` を呼び出すことによって、 *exampleJsInterop*ファイル内の JavaScript 関数と相互運用します。</span><span class="sxs-lookup"><span data-stu-id="6e095-148">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="6e095-149">@No__t_0 抽象化は非同期であり、Blazor サーバーシナリオで使用できます。</span><span class="sxs-lookup"><span data-stu-id="6e095-149">The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="6e095-150">アプリが Blazor Webasアプリで、JavaScript 関数を同期的に呼び出す必要がある場合は、`IJSInProcessRuntime` にダウンキャストし、代わりに `Invoke<T>` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="6e095-150">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="6e095-151">ほとんどの JavaScript 相互運用機能ライブラリでは、非同期 Api を使用して、すべてのシナリオでライブラリを使用できるようにすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="6e095-151">We recommend that most JavaScript interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="6e095-152">サンプルアプリには、JavaScript の相互運用機能を示すコンポーネントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="6e095-152">The sample app includes a component to demonstrate JavaScript interop.</span></span> <span data-ttu-id="6e095-153">コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="6e095-153">The component:</span></span>

* <span data-ttu-id="6e095-154">JavaScript プロンプトを使用してユーザー入力を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="6e095-154">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="6e095-155">処理するテキストをコンポーネントに返します。</span><span class="sxs-lookup"><span data-stu-id="6e095-155">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="6e095-156">DOM と対話してウェルカムメッセージを表示する2番目の JavaScript 関数を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="6e095-156">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="6e095-157">*Pages/JSInterop*:</span><span class="sxs-lookup"><span data-stu-id="6e095-157">*Pages/JSInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorWebAssemblySample/Pages/JsInterop.razor?name=snippet_JSInterop1&highlight=3,19-21,23-25)]

1. <span data-ttu-id="6e095-158">コンポーネントの **[トリガー JavaScript プロンプト]** ボタンを選択して `TriggerJsPrompt` を実行すると、 *wwwroot/exampleJsInterop*ファイルに指定されている javascript `showPrompt` 関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="6e095-158">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="6e095-159">@No__t_0 関数は、HTML エンコードされ、コンポーネントに返されるユーザー入力 (ユーザーの名前) を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="6e095-159">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="6e095-160">このコンポーネントでは、ユーザーの名前がローカル変数に格納されます (`name`)。</span><span class="sxs-lookup"><span data-stu-id="6e095-160">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="6e095-161">@No__t_0 に格納されている文字列は、ウェルカムメッセージに組み込まれます。このメッセージは、JavaScript 関数に渡されます。 `displayWelcome` は、ウェルカムメッセージを見出しタグにレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="6e095-161">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="6e095-162">Void JavaScript 関数を呼び出す</span><span class="sxs-lookup"><span data-stu-id="6e095-162">Call a void JavaScript function</span></span>

<span data-ttu-id="6e095-163">[Void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void)または[Undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined)を返す JavaScript 関数は、`IJSRuntime.InvokeVoidAsync` を使用して呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="6e095-163">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeVoidAsync`.</span></span>

## <a name="detect-when-a-blazor-app-is-prerendering"></a><span data-ttu-id="6e095-164">Blazor アプリがプリレンダリングされるタイミングを検出する</span><span class="sxs-lookup"><span data-stu-id="6e095-164">Detect when a Blazor app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="6e095-165">要素への参照をキャプチャする</span><span class="sxs-lookup"><span data-stu-id="6e095-165">Capture references to elements</span></span>

<span data-ttu-id="6e095-166">一部の[JavaScript の相互運用](xref:blazor/javascript-interop)シナリオでは、HTML 要素への参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="6e095-166">Some [JavaScript interop](xref:blazor/javascript-interop) scenarios require references to HTML elements.</span></span> <span data-ttu-id="6e095-167">たとえば、UI ライブラリに初期化のための要素参照が必要な場合や、`focus` や `play` などの要素に対してコマンドに似た Api を呼び出す必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="6e095-167">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="6e095-168">次の方法を使用して、コンポーネント内の HTML 要素への参照をキャプチャします。</span><span class="sxs-lookup"><span data-stu-id="6e095-168">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="6e095-169">@No__t_0 属性を HTML 要素に追加します。</span><span class="sxs-lookup"><span data-stu-id="6e095-169">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="6e095-170">@No__t_1 属性の値に一致する名前を持つ `ElementReference` 型のフィールドを定義します。</span><span class="sxs-lookup"><span data-stu-id="6e095-170">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="6e095-171">次の例は、`username` `<input>` 要素への参照をキャプチャする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="6e095-171">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```cshtml
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!NOTE]
> <span data-ttu-id="6e095-172">キャプチャ**さ**れた要素参照を DOM に設定する方法としては使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="6e095-172">Do **not** use captured element references as a way of populating the DOM.</span></span> <span data-ttu-id="6e095-173">これを行うと、宣言型のレンダリングモデルに干渉する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="6e095-173">Doing so may interfere with the declarative rendering model.</span></span>

<span data-ttu-id="6e095-174">.NET コードに関しては、`ElementReference` は不透明なハンドルです。</span><span class="sxs-lookup"><span data-stu-id="6e095-174">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="6e095-175">@No__t_1 で行うことができるのは、JavaScript の相互運用機能を使用して JavaScript コードに渡すこと*だけ*です。</span><span class="sxs-lookup"><span data-stu-id="6e095-175">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JavaScript interop.</span></span> <span data-ttu-id="6e095-176">これを行うと、JavaScript 側のコードは `HTMLElement` インスタンスを受け取ります。このインスタンスは、通常の DOM Api と共に使用できます。</span><span class="sxs-lookup"><span data-stu-id="6e095-176">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="6e095-177">たとえば、次のコードでは、要素にフォーカスを設定できるようにする .NET 拡張メソッドを定義しています。</span><span class="sxs-lookup"><span data-stu-id="6e095-177">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="6e095-178">*exampleJsInterop*:</span><span class="sxs-lookup"><span data-stu-id="6e095-178">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="6e095-179">@No__t_0 を使用し、`ElementReference` で `exampleJsFunctions.focusElement` を呼び出して、要素にフォーカスを移動します。</span><span class="sxs-lookup"><span data-stu-id="6e095-179">Use `IJSRuntime.InvokeAsync<T>` and call `exampleJsFunctions.focusElement` with an `ElementReference` to focus an element:</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="6e095-180">拡張メソッドを使用して要素にフォーカスを移動するには、`IJSRuntime` インスタンスを受け取る静的拡張メソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="6e095-180">To use an extension method to focus an element, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<object>(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="6e095-181">メソッドは、オブジェクトで直接呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="6e095-181">The method is called directly on the object.</span></span> <span data-ttu-id="6e095-182">次の例では、静的 `Focus` メソッドが `JsInteropClasses` 名前空間から使用できることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="6e095-182">The following example assumes that the static `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/component2.razor?highlight=1,4,12)]

> [!IMPORTANT]
> <span data-ttu-id="6e095-183">@No__t_0 変数は、コンポーネントがレンダリングされた後にのみ設定されます。</span><span class="sxs-lookup"><span data-stu-id="6e095-183">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="6e095-184">いない `ElementReference` が JavaScript コードに渡されると、JavaScript コードは `null` の値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="6e095-184">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="6e095-185">コンポーネントのレンダリングが完了した後に要素参照を操作するには (要素に初期フォーカスを設定するには)、`OnAfterRenderAsync` または `OnAfterRender`[コンポーネントライフサイクルメソッド](xref:blazor/components#lifecycle-methods)を使用します。</span><span class="sxs-lookup"><span data-stu-id="6e095-185">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the `OnAfterRenderAsync` or `OnAfterRender` [component lifecycle methods](xref:blazor/components#lifecycle-methods).</span></span>

## <a name="invoke-net-methods-from-javascript-functions"></a><span data-ttu-id="6e095-186">JavaScript 関数からの .NET メソッドの呼び出し</span><span class="sxs-lookup"><span data-stu-id="6e095-186">Invoke .NET methods from JavaScript functions</span></span>

### <a name="static-net-method-call"></a><span data-ttu-id="6e095-187">静的 .NET メソッド呼び出し</span><span class="sxs-lookup"><span data-stu-id="6e095-187">Static .NET method call</span></span>

<span data-ttu-id="6e095-188">JavaScript から静的 .NET メソッドを呼び出すには、`DotNet.invokeMethod` または `DotNet.invokeMethodAsync` 関数を使用します。</span><span class="sxs-lookup"><span data-stu-id="6e095-188">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="6e095-189">呼び出す静的メソッドの識別子、関数を含むアセンブリの名前、および任意の引数を渡します。</span><span class="sxs-lookup"><span data-stu-id="6e095-189">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="6e095-190">非同期バージョンは、Blazor Server のシナリオをサポートするために推奨されます。</span><span class="sxs-lookup"><span data-stu-id="6e095-190">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="6e095-191">.Net メソッドを JavaScript から呼び出すには、.NET メソッドが public、static、および `[JSInvokable]` 属性を持っている必要があります。</span><span class="sxs-lookup"><span data-stu-id="6e095-191">To invoke a .NET method from JavaScript, the .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="6e095-192">既定では、メソッド識別子はメソッド名ですが、`JSInvokableAttribute` コンストラクターを使用して別の識別子を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="6e095-192">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor.</span></span> <span data-ttu-id="6e095-193">オープンジェネリックメソッドを呼び出すことは現在サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="6e095-193">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="6e095-194">サンプルアプリには、 C# `int`s の配列を返すメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="6e095-194">The sample app includes a C# method to return an array of `int`s.</span></span> <span data-ttu-id="6e095-195">@No__t_0 属性がメソッドに適用されます。</span><span class="sxs-lookup"><span data-stu-id="6e095-195">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="6e095-196">*Pages/JsInterop*:</span><span class="sxs-lookup"><span data-stu-id="6e095-196">*Pages/JsInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorWebAssemblySample/Pages/JsInterop.razor?name=snippet_JSInterop2&highlight=7-11)]

<span data-ttu-id="6e095-197">クライアントに提供される JavaScript はC# 、.net メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="6e095-197">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="6e095-198">*wwwroot/exampleJsInterop*:</span><span class="sxs-lookup"><span data-stu-id="6e095-198">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="6e095-199">**[.Net 静的メソッド ReturnArrayAsync のトリガー]** ボタンが選択されている場合は、ブラウザーの web developer ツールでコンソール出力を確認します。</span><span class="sxs-lookup"><span data-stu-id="6e095-199">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="6e095-200">コンソールの出力は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="6e095-200">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="6e095-201">4番目の配列値は、`ReturnArrayAsync` によって返される配列 (`data.push(4);`) にプッシュされます。</span><span class="sxs-lookup"><span data-stu-id="6e095-201">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

### <a name="instance-method-call"></a><span data-ttu-id="6e095-202">インスタンスメソッドの呼び出し</span><span class="sxs-lookup"><span data-stu-id="6e095-202">Instance method call</span></span>

<span data-ttu-id="6e095-203">JavaScript から .NET インスタンスメソッドを呼び出すこともできます。</span><span class="sxs-lookup"><span data-stu-id="6e095-203">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="6e095-204">JavaScript から .NET インスタンスメソッドを呼び出すには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="6e095-204">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="6e095-205">.NET インスタンスを `DotNetObjectReference` インスタンスにラップして、JavaScript に渡します。</span><span class="sxs-lookup"><span data-stu-id="6e095-205">Pass the .NET instance to JavaScript by wrapping it in a `DotNetObjectReference` instance.</span></span> <span data-ttu-id="6e095-206">.NET インスタンスは、JavaScript への参照によって渡されます。</span><span class="sxs-lookup"><span data-stu-id="6e095-206">The .NET instance is passed by reference to JavaScript.</span></span>
* <span data-ttu-id="6e095-207">@No__t_0 または `invokeMethodAsync` 関数を使用して、インスタンスで .NET インスタンスメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="6e095-207">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="6e095-208">.NET インスタンスは、JavaScript から他の .NET メソッドを呼び出すときに引数として渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="6e095-208">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="6e095-209">サンプルアプリは、メッセージをクライアント側コンソールに記録します。</span><span class="sxs-lookup"><span data-stu-id="6e095-209">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="6e095-210">サンプルアプリで示されている次の例については、ブラウザーの開発者ツールでブラウザーのコンソール出力を確認してください。</span><span class="sxs-lookup"><span data-stu-id="6e095-210">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="6e095-211">**[.Net インスタンスメソッドをトリガーする HelloHelper]** ボタンを選択すると、`ExampleJsInterop.CallHelloHelperSayHello` が呼び出され、`Blazor` の名前がメソッドに渡されます。</span><span class="sxs-lookup"><span data-stu-id="6e095-211">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="6e095-212">*Pages/JsInterop*:</span><span class="sxs-lookup"><span data-stu-id="6e095-212">*Pages/JsInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorWebAssemblySample/Pages/JsInterop.razor?name=snippet_JSInterop3&highlight=8-9)]

<span data-ttu-id="6e095-213">`CallHelloHelperSayHello` は、`HelloHelper` の新しいインスタンスを使用して JavaScript 関数 `sayHello` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="6e095-213">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="6e095-214">*JsInteropClasses/ExampleJsInterop*:</span><span class="sxs-lookup"><span data-stu-id="6e095-214">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

<span data-ttu-id="6e095-215">*wwwroot/exampleJsInterop*:</span><span class="sxs-lookup"><span data-stu-id="6e095-215">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="6e095-216">名前は `HelloHelper` のコンストラクターに渡され、`HelloHelper.Name` プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="6e095-216">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="6e095-217">JavaScript 関数 `sayHello` が実行されると、`HelloHelper.SayHello` は、JavaScript 関数によってコンソールに書き込まれた `Hello, {Name}!` メッセージを返します。</span><span class="sxs-lookup"><span data-stu-id="6e095-217">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="6e095-218">*JsInteropClasses/HelloHelper*:</span><span class="sxs-lookup"><span data-stu-id="6e095-218">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="6e095-219">ブラウザーの web 開発者ツールのコンソール出力:</span><span class="sxs-lookup"><span data-stu-id="6e095-219">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-class-library"></a><span data-ttu-id="6e095-220">クラスライブラリで相互運用コードを共有する</span><span class="sxs-lookup"><span data-stu-id="6e095-220">Share interop code in a class library</span></span>

<span data-ttu-id="6e095-221">JavaScript 相互運用コードをクラスライブラリに含めることができます。これにより、NuGet パッケージ内のコードを共有できます。</span><span class="sxs-lookup"><span data-stu-id="6e095-221">JavaScript interop code can be included in a class library, which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="6e095-222">クラスライブラリは、ビルドされたアセンブリに JavaScript リソースの埋め込みを処理します。</span><span class="sxs-lookup"><span data-stu-id="6e095-222">The class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="6e095-223">JavaScript ファイルは*wwwroot*フォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="6e095-223">The JavaScript files are placed in the *wwwroot* folder.</span></span> <span data-ttu-id="6e095-224">ツールは、ライブラリの構築時にリソースの埋め込みを行います。</span><span class="sxs-lookup"><span data-stu-id="6e095-224">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="6e095-225">ビルド済みの NuGet パッケージは、NuGet パッケージを参照するのと同じ方法で、アプリのプロジェクトファイルで参照されます。</span><span class="sxs-lookup"><span data-stu-id="6e095-225">The built NuGet package is referenced in the app's project file the same way that any NuGet package is referenced.</span></span> <span data-ttu-id="6e095-226">パッケージが復元された後、アプリコードはと同じように JavaScript C#を呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="6e095-226">After the package is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="6e095-227">詳細については、「<xref:blazor/class-libraries>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e095-227">For more information, see <xref:blazor/class-libraries>.</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="6e095-228">JS 相互運用呼び出しの強化</span><span class="sxs-lookup"><span data-stu-id="6e095-228">Harden JS interop calls</span></span>

<span data-ttu-id="6e095-229">JS 相互運用機能は、ネットワークエラーのために失敗する可能性があり、信頼性の低いものとして扱う必要があります。</span><span class="sxs-lookup"><span data-stu-id="6e095-229">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="6e095-230">既定では、Blazor サーバーアプリは、1分後に JS 相互運用機能呼び出しをサーバー上でタイムアウトします。</span><span class="sxs-lookup"><span data-stu-id="6e095-230">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="6e095-231">10秒など、アプリでより積極的なタイムアウトが許容される場合は、次のいずれかの方法を使用してタイムアウトを設定します。</span><span class="sxs-lookup"><span data-stu-id="6e095-231">If an app can tolerate a more aggressive timeout, such as 10 seconds, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="6e095-232">@No__t_0 でグローバルに、タイムアウトを指定します。</span><span class="sxs-lookup"><span data-stu-id="6e095-232">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="6e095-233">コンポーネントコードでの呼び出しごとに、1回の呼び出しでタイムアウトを指定できます。</span><span class="sxs-lookup"><span data-stu-id="6e095-233">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="6e095-234">リソース枯渇の詳細については、「<xref:security/blazor/server>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e095-234">For more information on resource exhaustion, see <xref:security/blazor/server>.</span></span>
