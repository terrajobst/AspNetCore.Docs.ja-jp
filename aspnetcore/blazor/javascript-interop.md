---
title: ASP.NET Core Blazor JavaScript 相互運用機能
author: guardrex
description: Blazor アプリで JavaScript から .NET および .NET メソッドから JavaScript 関数を呼び出す方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/javascript-interop
ms.openlocfilehash: e578a8ad1484a2ef93bdc7470985937c4f28b7ed
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310456"
---
# <a name="aspnet-core-blazor-javascript-interop"></a><span data-ttu-id="f588b-103">ASP.NET Core Blazor JavaScript 相互運用機能</span><span class="sxs-lookup"><span data-stu-id="f588b-103">ASP.NET Core Blazor JavaScript interop</span></span>

<span data-ttu-id="f588b-104">[Javier Calvarro jeannine](https://github.com/javiercn)、 [Daniel Roth](https://github.com/danroth27)、 [Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f588b-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f588b-105">Blazor アプリは、javascript コードから .NET および .NET メソッドから JavaScript 関数を呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="f588b-105">A Blazor app can invoke JavaScript functions from .NET and .NET methods from JavaScript code.</span></span>

<span data-ttu-id="f588b-106">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="f588b-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="invoke-javascript-functions-from-net-methods"></a><span data-ttu-id="f588b-107">.NET メソッドから JavaScript 関数を呼び出す</span><span class="sxs-lookup"><span data-stu-id="f588b-107">Invoke JavaScript functions from .NET methods</span></span>

<span data-ttu-id="f588b-108">JavaScript 関数を呼び出すために .NET コードが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="f588b-108">There are times when .NET code is required to call a JavaScript function.</span></span> <span data-ttu-id="f588b-109">たとえば、JavaScript 呼び出しは、ブラウザーの機能や機能を JavaScript ライブラリからアプリに公開できます。</span><span class="sxs-lookup"><span data-stu-id="f588b-109">For example, a JavaScript call can expose browser capabilities or functionality from a JavaScript library to the app.</span></span>

<span data-ttu-id="f588b-110">.Net から JavaScript を呼び出すには、 `IJSRuntime`抽象化を使用します。</span><span class="sxs-lookup"><span data-stu-id="f588b-110">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="f588b-111">メソッド`InvokeAsync<T>`は、任意の数の JSON シリアル化可能な引数と共に、呼び出す JavaScript 関数の識別子を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="f588b-111">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="f588b-112">関数識別子は、グローバルスコープ (`window`) を基準にしています。</span><span class="sxs-lookup"><span data-stu-id="f588b-112">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="f588b-113">を呼び出す`window.someScope.someFunction`場合、識別子は`someScope.someFunction`になります。</span><span class="sxs-lookup"><span data-stu-id="f588b-113">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="f588b-114">呼び出される前に、関数を登録する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="f588b-114">There's no need to register the function before it's called.</span></span> <span data-ttu-id="f588b-115">戻り値の`T`型は、JSON シリアル化可能である必要もあります。</span><span class="sxs-lookup"><span data-stu-id="f588b-115">The return type `T` must also be JSON serializable.</span></span>

<span data-ttu-id="f588b-116">サーバー側アプリの場合:</span><span class="sxs-lookup"><span data-stu-id="f588b-116">For server-side apps:</span></span>

* <span data-ttu-id="f588b-117">複数のユーザー要求は、サーバー側アプリによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="f588b-117">Multiple user requests are processed by the server-side app.</span></span> <span data-ttu-id="f588b-118">JavaScript 関数`JSRuntime.Current`を呼び出すために、コンポーネントでを呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="f588b-118">Don't call `JSRuntime.Current` in a component to invoke JavaScript functions.</span></span>
* <span data-ttu-id="f588b-119">抽象化を`IJSRuntime`挿入し、挿入されたオブジェクトを使用して、JavaScript の相互運用呼び出しを発行します。</span><span class="sxs-lookup"><span data-stu-id="f588b-119">Inject the `IJSRuntime` abstraction and use the injected object to issue JavaScript interop calls.</span></span>
* <span data-ttu-id="f588b-120">Blazor アプリのプリレンダリング中に、ブラウザーとの接続が確立されていないため、JavaScript を呼び出すことはできません。</span><span class="sxs-lookup"><span data-stu-id="f588b-120">While a Blazor app is prerendering, calling into JavaScript isn't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="f588b-121">詳細については、「 [Blazor アプリのプリレンダリングを検出する](#detect-when-a-blazor-app-is-prerendering)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="f588b-121">For more information, see the [Detect when a Blazor app is prerendering](#detect-when-a-blazor-app-is-prerendering) section.</span></span>

<span data-ttu-id="f588b-122">次の例は、JavaScript ベースの試験的なデコーダーである[Textdecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder)に基づいています。</span><span class="sxs-lookup"><span data-stu-id="f588b-122">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), an experimental JavaScript-based decoder.</span></span> <span data-ttu-id="f588b-123">この例では、 C#メソッドから JavaScript 関数を呼び出す方法を示します。</span><span class="sxs-lookup"><span data-stu-id="f588b-123">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="f588b-124">JavaScript 関数は、 C#メソッドからバイト配列を受け取り、配列をデコードし、テキストをコンポーネントに返して表示できるようにします。</span><span class="sxs-lookup"><span data-stu-id="f588b-124">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="f588b-125">Wwwroot/index.html (Blazor client側) または*Pages/_Host* (Blazor サーバー側) の`TextDecoder` 要素内で、渡された配列をデコードするために使用する関数を指定します。`<head>`</span><span class="sxs-lookup"><span data-stu-id="f588b-125">Inside the `<head>` element of *wwwroot/index.html* (Blazor client-side) or *Pages/_Host.cshtml* (Blazor server-side), provide a function that uses `TextDecoder` to decode a passed array:</span></span>

[!code-html[](javascript-interop/samples_snapshot/index-script.html)]

<span data-ttu-id="f588b-126">前の例で示したコードなどの JavaScript コードは、スクリプトファイルへの参照を使用して JavaScript ファイル ( *.js*) から読み込むこともできます。</span><span class="sxs-lookup"><span data-stu-id="f588b-126">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="f588b-127">次のコンポーネント:</span><span class="sxs-lookup"><span data-stu-id="f588b-127">The following component:</span></span>

* <span data-ttu-id="f588b-128">コンポーネントボタン`ConvertArray` (配列の`JsRuntime` **変換**) が選択されている場合に、を使用して JavaScript 関数を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f588b-128">Invokes the `ConvertArray` JavaScript function using `JsRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="f588b-129">JavaScript 関数が呼び出されると、渡された配列が文字列に変換されます。</span><span class="sxs-lookup"><span data-stu-id="f588b-129">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="f588b-130">文字列は、表示のためにコンポーネントに返されます。</span><span class="sxs-lookup"><span data-stu-id="f588b-130">The string is returned to the component for display.</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

<span data-ttu-id="f588b-131">`IJSRuntime`抽象化を使用するには、次のいずれかの方法を採用します。</span><span class="sxs-lookup"><span data-stu-id="f588b-131">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="f588b-132">Razor コンポーネント`IJSRuntime` (*razor*) に抽象を挿入します。</span><span class="sxs-lookup"><span data-stu-id="f588b-132">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-cshtml[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

* <span data-ttu-id="f588b-133">抽象化を`IJSRuntime`クラス ( *.cs*) に挿入します。</span><span class="sxs-lookup"><span data-stu-id="f588b-133">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

* <span data-ttu-id="f588b-134">[BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic)を使用した動的なコンテンツ生成`[Inject]`の場合は、属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="f588b-134">For dynamic content generation with [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```csharp
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="f588b-135">このトピックに付属しているクライアント側のサンプルアプリでは、クライアント側アプリで2つの JavaScript 関数を使用して、ユーザー入力を受け取り、ウェルカムメッセージを表示するために DOM と対話することができます。</span><span class="sxs-lookup"><span data-stu-id="f588b-135">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the client-side app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="f588b-136">`showPrompt`&ndash;ユーザー入力 (ユーザーの名前) を受け入れ、呼び出し元に名前を返すように求めるプロンプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="f588b-136">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="f588b-137">`displayWelcome`呼び出し元から`id` の`welcome`を持つ DOM オブジェクトにウェルカムメッセージを割り当てます。 &ndash;</span><span class="sxs-lookup"><span data-stu-id="f588b-137">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="f588b-138">*wwwroot/exampleJsInterop*:</span><span class="sxs-lookup"><span data-stu-id="f588b-138">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="f588b-139">JavaScript ファイルを参照する タグをwwwroot/index.htmlファイル(Blazorclient側)またはPages/_Hostファイル(Blazorサーバー側)に配置し`<script>`ます。</span><span class="sxs-lookup"><span data-stu-id="f588b-139">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor client-side) or *Pages/_Host.cshtml* file (Blazor server-side).</span></span>

<span data-ttu-id="f588b-140">*wwwroot/index.html*(Blazor client 側):</span><span class="sxs-lookup"><span data-stu-id="f588b-140">*wwwroot/index.html* (Blazor client-side):</span></span>

[!code-html[](./common/samples/3.x/BlazorSample/wwwroot/index.html?highlight=15)]

<span data-ttu-id="f588b-141">*Pages/_Host*(Blazor サーバー側):</span><span class="sxs-lookup"><span data-stu-id="f588b-141">*Pages/_Host.cshtml* (Blazor server-side):</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/_Host.cshtml?highlight=29)]

<span data-ttu-id="f588b-142">タグを動的`<script>`に更新できない`<script>`ため、コンポーネントファイルにタグを配置しないでください。</span><span class="sxs-lookup"><span data-stu-id="f588b-142">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="f588b-143">.NET メソッドは、を呼び出す`IJSRuntime.InvokeAsync<T>`ことによって、 *ExampleJsInterop*ファイル内の JavaScript 関数と相互運用します。</span><span class="sxs-lookup"><span data-stu-id="f588b-143">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="f588b-144">抽象化`IJSRuntime`は非同期であり、サーバー側のシナリオで使用できます。</span><span class="sxs-lookup"><span data-stu-id="f588b-144">The `IJSRuntime` abstraction is asynchronous to allow for server-side scenarios.</span></span> <span data-ttu-id="f588b-145">アプリがクライアント側で実行され、JavaScript 関数を同期的に呼び出す必要がある`IJSInProcessRuntime`場合は`Invoke<T>` 、にダウンキャストし、代わりにを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f588b-145">If the app runs client-side and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="f588b-146">ほとんどの JavaScript 相互運用機能ライブラリでは、非同期 Api を使用して、すべてのシナリオ、クライアント側またはサーバー側でライブラリを使用できるようにすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="f588b-146">We recommend that most JavaScript interop libraries use the async APIs to ensure that the libraries are available in all scenarios, client-side or server-side.</span></span>

<span data-ttu-id="f588b-147">サンプルアプリには、JavaScript の相互運用機能を示すコンポーネントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="f588b-147">The sample app includes a component to demonstrate JavaScript interop.</span></span> <span data-ttu-id="f588b-148">コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="f588b-148">The component:</span></span>

* <span data-ttu-id="f588b-149">JavaScript プロンプトを使用してユーザー入力を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="f588b-149">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="f588b-150">処理するテキストをコンポーネントに返します。</span><span class="sxs-lookup"><span data-stu-id="f588b-150">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="f588b-151">DOM と対話してウェルカムメッセージを表示する2番目の JavaScript 関数を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f588b-151">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="f588b-152">*Pages/JSInterop*:</span><span class="sxs-lookup"><span data-stu-id="f588b-152">*Pages/JSInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop1&highlight=3,19-21,23-25)]

1. <span data-ttu-id="f588b-153">コンポーネント`TriggerJsPrompt`の **[トリガー JavaScript プロンプト]** ボタンを選択してを実行する`showPrompt`と、 *wwwroot/exampleJsInterop*ファイルに指定された javascript 関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f588b-153">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="f588b-154">関数`showPrompt`は、HTML エンコードされ、コンポーネントに返されるユーザー入力 (ユーザーの名前) を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="f588b-154">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="f588b-155">コンポーネントは、ユーザーの名前をローカル変数`name`に格納します。</span><span class="sxs-lookup"><span data-stu-id="f588b-155">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="f588b-156">に`name`格納されている文字列は、ウェルカムメッセージに組み込まれています。これ`displayWelcome`は、ウェルカムメッセージを見出しタグに表示する JavaScript 関数に渡されます。</span><span class="sxs-lookup"><span data-stu-id="f588b-156">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="f588b-157">Void JavaScript 関数を呼び出す</span><span class="sxs-lookup"><span data-stu-id="f588b-157">Call a void JavaScript function</span></span>

<span data-ttu-id="f588b-158">[Void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void)または[undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined)を返す JavaScript 関数は、 `IJSRuntime.InvokeAsync<object>`を返す`null`を使用して呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f588b-158">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeAsync<object>`, which returns `null`.</span></span>

## <a name="detect-when-a-blazor-app-is-prerendering"></a><span data-ttu-id="f588b-159">Blazor アプリがプリレンダリングされるタイミングを検出する</span><span class="sxs-lookup"><span data-stu-id="f588b-159">Detect when a Blazor app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="f588b-160">要素への参照をキャプチャする</span><span class="sxs-lookup"><span data-stu-id="f588b-160">Capture references to elements</span></span>

<span data-ttu-id="f588b-161">一部の[JavaScript の相互運用](xref:blazor/javascript-interop)シナリオでは、HTML 要素への参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="f588b-161">Some [JavaScript interop](xref:blazor/javascript-interop) scenarios require references to HTML elements.</span></span> <span data-ttu-id="f588b-162">たとえば、UI ライブラリに初期化のための要素参照が必要な場合や、要素 ( `focus`や`play`など) でコマンドに似た api を呼び出す必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="f588b-162">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="f588b-163">次の方法を使用して、コンポーネント内の HTML 要素への参照をキャプチャします。</span><span class="sxs-lookup"><span data-stu-id="f588b-163">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="f588b-164">`@ref`属性を HTML 要素に追加します。</span><span class="sxs-lookup"><span data-stu-id="f588b-164">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="f588b-165">属性の値と一致`ElementReference`する名前を持つ型のフィールドを定義します。 `@ref`</span><span class="sxs-lookup"><span data-stu-id="f588b-165">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="f588b-166">要素`<input>`へ`username`の参照をキャプチャする例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="f588b-166">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```cshtml
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!NOTE]
> <span data-ttu-id="f588b-167">Blazor が参照されている要素と対話するときに、DOM を作成または操作する方法として、キャプチャ**さ**れた要素参照を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="f588b-167">Do **not** use captured element references as a way of populating or manipulating the DOM when Blazor interacts with the elements referenced.</span></span> <span data-ttu-id="f588b-168">これを行うと、宣言型のレンダリングモデルに干渉する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f588b-168">Doing so may interfere with the declarative rendering model.</span></span>

<span data-ttu-id="f588b-169">.Net コードに関し`ElementReference`ては、は不透明なハンドルです。</span><span class="sxs-lookup"><span data-stu-id="f588b-169">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="f588b-170">この操作を実行`ElementReference`できるのは、javascript の相互運用機能を使用して javascript コードに渡すことだけです。</span><span class="sxs-lookup"><span data-stu-id="f588b-170">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JavaScript interop.</span></span> <span data-ttu-id="f588b-171">これを行うと、JavaScript 側のコードは、通常`HTMLElement`の DOM api で使用できるインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="f588b-171">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="f588b-172">たとえば、次のコードでは、要素にフォーカスを設定できるようにする .NET 拡張メソッドを定義しています。</span><span class="sxs-lookup"><span data-stu-id="f588b-172">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="f588b-173">*exampleJsInterop*:</span><span class="sxs-lookup"><span data-stu-id="f588b-173">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="f588b-174">を`IJSRuntime.InvokeAsync<T>`使用し`exampleJsFunctions.focusElement` 、を`ElementReference`使用してを呼び出し、要素にフォーカスを移動します。</span><span class="sxs-lookup"><span data-stu-id="f588b-174">Use `IJSRuntime.InvokeAsync<T>` and call `exampleJsFunctions.focusElement` with an `ElementReference` to focus an element:</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="f588b-175">拡張メソッドを使用して要素にフォーカスを移動するには、 `IJSRuntime`インスタンスを受け取る静的拡張メソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="f588b-175">To use an extension method to focus an element, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<object>(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="f588b-176">メソッドは、オブジェクトで直接呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f588b-176">The method is called directly on the object.</span></span> <span data-ttu-id="f588b-177">次の例では、静的`Focus`メソッドを`JsInteropClasses`名前空間から使用できることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="f588b-177">The following example assumes that the static `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/component2.razor?highlight=1,4,12)]

> [!IMPORTANT]
> <span data-ttu-id="f588b-178">`username`変数は、コンポーネントがレンダリングされた後にのみ設定されます。</span><span class="sxs-lookup"><span data-stu-id="f588b-178">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="f588b-179">いない`ElementReference`が javascript コードに渡されると、javascript コードは`null`値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="f588b-179">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="f588b-180">コンポーネントのレンダリングが完了した後に要素参照を操作する (要素に初期フォーカスを設定する`OnAfterRenderAsync` ) `OnAfterRender`には、または[コンポーネントライフサイクルメソッド](xref:blazor/components#lifecycle-methods)を使用します。</span><span class="sxs-lookup"><span data-stu-id="f588b-180">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the `OnAfterRenderAsync` or `OnAfterRender` [component lifecycle methods](xref:blazor/components#lifecycle-methods).</span></span>

## <a name="invoke-net-methods-from-javascript-functions"></a><span data-ttu-id="f588b-181">JavaScript 関数からの .NET メソッドの呼び出し</span><span class="sxs-lookup"><span data-stu-id="f588b-181">Invoke .NET methods from JavaScript functions</span></span>

### <a name="static-net-method-call"></a><span data-ttu-id="f588b-182">静的 .NET メソッド呼び出し</span><span class="sxs-lookup"><span data-stu-id="f588b-182">Static .NET method call</span></span>

<span data-ttu-id="f588b-183">JavaScript から静的 .net メソッドを呼び出すに`DotNet.invokeMethod`は、関数または`DotNet.invokeMethodAsync`関数を使用します。</span><span class="sxs-lookup"><span data-stu-id="f588b-183">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="f588b-184">呼び出す静的メソッドの識別子、関数を含むアセンブリの名前、および任意の引数を渡します。</span><span class="sxs-lookup"><span data-stu-id="f588b-184">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="f588b-185">サーバー側のシナリオをサポートするには、非同期バージョンを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="f588b-185">The asynchronous version is preferred to support server-side scenarios.</span></span> <span data-ttu-id="f588b-186">.Net メソッドを JavaScript から呼び出すには、.net メソッドが public、static、および`[JSInvokable]`属性を持っている必要があります。</span><span class="sxs-lookup"><span data-stu-id="f588b-186">To invoke a .NET method from JavaScript, the .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="f588b-187">既定では、メソッド識別子はメソッド名ですが、 `JSInvokableAttribute`コンストラクターを使用して別の識別子を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="f588b-187">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor.</span></span> <span data-ttu-id="f588b-188">オープンジェネリックメソッドを呼び出すことは現在サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="f588b-188">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="f588b-189">サンプルアプリには、 C#の`int`配列を返すメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="f588b-189">The sample app includes a C# method to return an array of `int`s.</span></span> <span data-ttu-id="f588b-190">`JSInvokable`属性はメソッドに適用されます。</span><span class="sxs-lookup"><span data-stu-id="f588b-190">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="f588b-191">*Pages/JsInterop*:</span><span class="sxs-lookup"><span data-stu-id="f588b-191">*Pages/JsInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop2&highlight=7-11)]

<span data-ttu-id="f588b-192">クライアントに提供される JavaScript はC# 、.net メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f588b-192">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="f588b-193">*wwwroot/exampleJsInterop*:</span><span class="sxs-lookup"><span data-stu-id="f588b-193">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="f588b-194">**[.Net 静的メソッド ReturnArrayAsync のトリガー]** ボタンが選択されている場合は、ブラウザーの web developer ツールでコンソール出力を確認します。</span><span class="sxs-lookup"><span data-stu-id="f588b-194">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="f588b-195">コンソールの出力は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="f588b-195">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="f588b-196">4番目の配列値は、によっ`data.push(4);`て`ReturnArrayAsync`返される配列 () にプッシュされます。</span><span class="sxs-lookup"><span data-stu-id="f588b-196">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

### <a name="instance-method-call"></a><span data-ttu-id="f588b-197">インスタンスメソッドの呼び出し</span><span class="sxs-lookup"><span data-stu-id="f588b-197">Instance method call</span></span>

<span data-ttu-id="f588b-198">JavaScript から .NET インスタンスメソッドを呼び出すこともできます。</span><span class="sxs-lookup"><span data-stu-id="f588b-198">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="f588b-199">JavaScript から .NET インスタンスメソッドを呼び出すには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="f588b-199">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="f588b-200">`DotNetObjectReference`インスタンスにラップして、.net インスタンスを JavaScript に渡します。</span><span class="sxs-lookup"><span data-stu-id="f588b-200">Pass the .NET instance to JavaScript by wrapping it in a `DotNetObjectReference` instance.</span></span> <span data-ttu-id="f588b-201">.NET インスタンスは、JavaScript への参照によって渡されます。</span><span class="sxs-lookup"><span data-stu-id="f588b-201">The .NET instance is passed by reference to JavaScript.</span></span>
* <span data-ttu-id="f588b-202">`invokeMethod`または`invokeMethodAsync`関数を使用して、インスタンスで .net インスタンスメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f588b-202">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="f588b-203">.NET インスタンスは、JavaScript から他の .NET メソッドを呼び出すときに引数として渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="f588b-203">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="f588b-204">サンプルアプリは、メッセージをクライアント側コンソールに記録します。</span><span class="sxs-lookup"><span data-stu-id="f588b-204">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="f588b-205">サンプルアプリで示されている次の例については、ブラウザーの開発者ツールでブラウザーのコンソール出力を確認してください。</span><span class="sxs-lookup"><span data-stu-id="f588b-205">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="f588b-206">**[.Net インスタンスメソッドをトリガーする HelloHelper]** ボタンが選択さ`ExampleJsInterop.CallHelloHelperSayHello`れている場合、が呼び出さ`Blazor`れ、メソッドに名前が渡されます。</span><span class="sxs-lookup"><span data-stu-id="f588b-206">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="f588b-207">*Pages/JsInterop*:</span><span class="sxs-lookup"><span data-stu-id="f588b-207">*Pages/JsInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop3&highlight=8-9)]

<span data-ttu-id="f588b-208">`CallHelloHelperSayHello``sayHello` の`HelloHelper`新しいインスタンスを使用して JavaScript 関数を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f588b-208">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="f588b-209">*JsInteropClasses/ExampleJsInterop*:</span><span class="sxs-lookup"><span data-stu-id="f588b-209">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

<span data-ttu-id="f588b-210">*wwwroot/exampleJsInterop*:</span><span class="sxs-lookup"><span data-stu-id="f588b-210">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="f588b-211">この名前は、 `HelloHelper` `HelloHelper.Name`プロパティを設定するのコンストラクターに渡されます。</span><span class="sxs-lookup"><span data-stu-id="f588b-211">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="f588b-212">Javascript 関数`sayHello`が実行されると`HelloHelper.SayHello` 、は`Hello, {Name}!`メッセージを返します。これは、javascript 関数によってコンソールに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="f588b-212">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="f588b-213">*JsInteropClasses/HelloHelper*:</span><span class="sxs-lookup"><span data-stu-id="f588b-213">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="f588b-214">ブラウザーの web 開発者ツールのコンソール出力:</span><span class="sxs-lookup"><span data-stu-id="f588b-214">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-class-library"></a><span data-ttu-id="f588b-215">クラスライブラリで相互運用コードを共有する</span><span class="sxs-lookup"><span data-stu-id="f588b-215">Share interop code in a class library</span></span>

<span data-ttu-id="f588b-216">JavaScript 相互運用コードをクラスライブラリに含めることができます。これにより、NuGet パッケージ内のコードを共有できます。</span><span class="sxs-lookup"><span data-stu-id="f588b-216">JavaScript interop code can be included in a class library, which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="f588b-217">クラスライブラリは、ビルドされたアセンブリに JavaScript リソースの埋め込みを処理します。</span><span class="sxs-lookup"><span data-stu-id="f588b-217">The class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="f588b-218">JavaScript ファイルは*wwwroot*フォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="f588b-218">The JavaScript files are placed in the *wwwroot* folder.</span></span> <span data-ttu-id="f588b-219">ツールは、ライブラリの構築時にリソースの埋め込みを行います。</span><span class="sxs-lookup"><span data-stu-id="f588b-219">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="f588b-220">ビルド済みの NuGet パッケージは、NuGet パッケージを参照するのと同じ方法で、アプリのプロジェクトファイルで参照されます。</span><span class="sxs-lookup"><span data-stu-id="f588b-220">The built NuGet package is referenced in the app's project file the same way that any NuGet package is referenced.</span></span> <span data-ttu-id="f588b-221">パッケージが復元された後、アプリコードはと同じように JavaScript C#を呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="f588b-221">After the package is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="f588b-222">詳細については、「 <xref:blazor/class-libraries> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f588b-222">For more information, see <xref:blazor/class-libraries>.</span></span>
