---
title: ASP.NET Core Blazor JavaScript 相互運用機能
author: guardrex
description: Blazor アプリで JavaScript から .NET および .NET メソッドから JavaScript 関数を呼び出す方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- Blazor
uid: blazor/javascript-interop
ms.openlocfilehash: f55eda512f8dcf0695c2e7f4655db83b26ea4159
ms.sourcegitcommit: 3e503ef510008e77be6dd82ee79213c9f7b97607
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2019
ms.locfileid: "74317191"
---
# <a name="aspnet-core-opno-locblazor-javascript-interop"></a>ASP.NET Core Blazor JavaScript 相互運用機能

[Javier Calvarro jeannine](https://github.com/javiercn)、 [Daniel Roth](https://github.com/danroth27)、 [Luke latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor アプリは、JavaScript コードから .NET および .NET メソッドから JavaScript 関数を呼び出すことができます。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="invoke-javascript-functions-from-net-methods"></a>.NET メソッドから JavaScript 関数を呼び出す

JavaScript 関数を呼び出すために .NET コードが必要になる場合があります。 たとえば、JavaScript 呼び出しは、ブラウザーの機能や機能を JavaScript ライブラリからアプリに公開できます。 このシナリオは、 *JavaScript 相互運用性*(*JS 相互運用*) と呼ばれます。

.NET から JavaScript を呼び出すには、`IJSRuntime` 抽象化を使用します。 `InvokeAsync<T>` メソッドは、任意の数の JSON シリアル化可能な引数と共に呼び出す JavaScript 関数の識別子を受け取ります。 関数識別子は、グローバルスコープ (`window`) に対して相対的です。 `window.someScope.someFunction`を呼び出す場合は、識別子が `someScope.someFunction`ます。 呼び出される前に、関数を登録する必要はありません。 戻り値の型 `T` も JSON シリアル化可能である必要があります。

Blazor サーバーアプリの場合:

* 複数のユーザー要求が Blazor サーバーアプリによって処理されます。 JavaScript 関数を呼び出すために、コンポーネント内で `JSRuntime.Current` を呼び出さないでください。
* `IJSRuntime` の抽象化を挿入し、挿入されたオブジェクトを使用して、JS 相互運用機能の呼び出しを発行します。
* Blazor アプリのプリレンダリング中に、ブラウザーとの接続が確立されていないため、JavaScript を呼び出すことはできません。 詳細については、「 [Blazor アプリのプリレンダリングを検出する](#detect-when-a-blazor-app-is-prerendering)」セクションを参照してください。

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

[!code-cshtml[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="use-of-ijsruntime"></a>IJSRuntime の使用

`IJSRuntime` の抽象化を使用するには、次のいずれかの方法を採用します。

* Razor コンポーネント (*razor*) に `IJSRuntime` の抽象化を挿入します。

  [!code-cshtml[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

  *Wwwroot/index.html* (Blazor WebAssembly または*Pages/_Host* (Blazor Server) の `<head>` 要素内で、`handleTickerChanged` JavaScript 関数を提供します。 関数は `IJSRuntime.InvokeVoidAsync` と共に呼び出され、値を返しません。

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged1.html)]

* `IJSRuntime` の抽象化をクラス ( *.cs*) に挿入します。

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  *Wwwroot/index.html* (Blazor WebAssembly または*Pages/_Host* (Blazor Server) の `<head>` 要素内で、`handleTickerChanged` JavaScript 関数を提供します。 関数は `JSRuntime.InvokeAsync` を指定して呼び出され、値を返します。

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged2.html)]

* [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic)を使用した動的なコンテンツ生成の場合は、`[Inject]` 属性を使用します。

  ```csharp
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

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=15)]

*Pages/_Host* (Blazor サーバー):

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=21)]

`<script>` タグを動的に更新できないため、コンポーネントファイルに `<script>` タグを配置しないでください。

.NET メソッドは、`IJSRuntime.InvokeAsync<T>`を呼び出すことによって、 *exampleJsInterop*ファイル内の JavaScript 関数と相互運用します。

`IJSRuntime` の抽象化は、Blazor サーバーのシナリオを可能にする非同期です。 アプリが Blazor Webasのアプリで、JavaScript 関数を同期的に呼び出す必要がある場合は、`IJSInProcessRuntime` にダウンキャストし、代わりに `Invoke<T>` を呼び出します。 ほとんどの JS 相互運用機能ライブラリでは、非同期 Api を使用して、すべてのシナリオでライブラリを使用できるようにすることをお勧めします。

サンプルアプリには、JS 相互運用機能を示すためのコンポーネントが含まれています。 コンポーネント:

* JavaScript プロンプトを使用してユーザー入力を受け取ります。
* 処理するテキストをコンポーネントに返します。
* DOM と対話してウェルカムメッセージを表示する2番目の JavaScript 関数を呼び出します。

*Pages/JSInterop*:

[!code-cshtml[](./common/samples/3.x/BlazorWebAssemblySample/Pages/JsInterop.razor?name=snippet_JSInterop1&highlight=3,19-21,23-25)]

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

```cshtml
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
> ```cshtml
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

`IJSRuntime.InvokeAsync<T>` を使用し、`ElementReference` で `exampleJsFunctions.focusElement` を呼び出して、要素にフォーカスを移動します。

[!code-cshtml[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,11-12)]

拡張メソッドを使用して要素にフォーカスを移動するには、`IJSRuntime` インスタンスを受け取る静的拡張メソッドを作成します。

```csharp
public static Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<object>(
        "exampleJsFunctions.focusElement", elementRef);
}
```

メソッドは、オブジェクトで直接呼び出されます。 次の例では、`JsInteropClasses` 名前空間から静的 `Focus` メソッドを使用できることを前提としています。

[!code-cshtml[](javascript-interop/samples_snapshot/component2.razor?highlight=1,4,12)]

> [!IMPORTANT]
> `username` 変数は、コンポーネントがレンダリングされた後にのみ設定されます。 JavaScript コードにいない `ElementReference` が渡されると、JavaScript コードは `null`の値を受け取ります。 コンポーネントのレンダリングが完了した後に要素参照を操作するには (要素に初期フォーカスを設定するには)、`OnAfterRenderAsync` または `OnAfterRender`[コンポーネントライフサイクルメソッド](xref:blazor/components#lifecycle-methods)を使用します。

## <a name="invoke-net-methods-from-javascript-functions"></a>JavaScript 関数からの .NET メソッドの呼び出し

### <a name="static-net-method-call"></a>静的 .NET メソッド呼び出し

JavaScript から静的 .NET メソッドを呼び出すには、`DotNet.invokeMethod` または `DotNet.invokeMethodAsync` 関数を使用します。 呼び出す静的メソッドの識別子、関数を含むアセンブリの名前、および任意の引数を渡します。 Blazor サーバーのシナリオをサポートするには、非同期バージョンを使用することをお勧めします。 .Net メソッドを JavaScript から呼び出すには、.NET メソッドが public、static、および `[JSInvokable]` 属性を持っている必要があります。 既定では、メソッド識別子はメソッド名ですが、`JSInvokableAttribute` コンストラクターを使用して別の識別子を指定することもできます。 オープンジェネリックメソッドを呼び出すことは現在サポートされていません。

このサンプルアプリにはC# `int`s の配列を返すメソッドが含まれています。 `JSInvokable` 属性がメソッドに適用されます。

*Pages/JsInterop*:

[!code-cshtml[](./common/samples/3.x/BlazorWebAssemblySample/Pages/JsInterop.razor?name=snippet_JSInterop2&highlight=7-11)]

クライアントに提供される JavaScript はC# 、.net メソッドを呼び出します。

*wwwroot/exampleJsInterop*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

**[.Net 静的メソッド ReturnArrayAsync のトリガー]** ボタンが選択されている場合は、ブラウザーの web developer ツールでコンソール出力を確認します。

コンソールの出力は次のとおりです。

```console
Array(4) [ 1, 2, 3, 4 ]
```

4番目の配列値は、`ReturnArrayAsync`によって返される配列 (`data.push(4);`) にプッシュされます。

### <a name="instance-method-call"></a>インスタンスメソッドの呼び出し

JavaScript から .NET インスタンスメソッドを呼び出すこともできます。 JavaScript から .NET インスタンスメソッドを呼び出すには、次の手順を実行します。

* .NET インスタンスを `DotNetObjectReference` インスタンスにラップして、JavaScript に渡します。 .NET インスタンスは、JavaScript への参照によって渡されます。
* `invokeMethod` または `invokeMethodAsync` 関数を使用して、インスタンスで .NET インスタンスメソッドを呼び出します。 .NET インスタンスは、JavaScript から他の .NET メソッドを呼び出すときに引数として渡すこともできます。

> [!NOTE]
> サンプルアプリは、メッセージをクライアント側コンソールに記録します。 サンプルアプリで示されている次の例については、ブラウザーの開発者ツールでブラウザーのコンソール出力を確認してください。

**[.Net インスタンスメソッドをトリガーする HelloHelper]** ボタンを選択すると、`ExampleJsInterop.CallHelloHelperSayHello` が呼び出され、`Blazor`の名前がメソッドに渡されます。

*Pages/JsInterop*:

[!code-cshtml[](./common/samples/3.x/BlazorWebAssemblySample/Pages/JsInterop.razor?name=snippet_JSInterop3&highlight=8-9)]

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

## <a name="share-interop-code-in-a-class-library"></a>クラスライブラリで相互運用コードを共有する

JS 相互運用コードは、NuGet パッケージ内のコードを共有できるクラスライブラリに含めることができます。

クラスライブラリは、ビルドされたアセンブリに JavaScript リソースの埋め込みを処理します。 JavaScript ファイルは*wwwroot*フォルダーに配置されます。 ツールは、ライブラリの構築時にリソースの埋め込みを行います。

ビルド済みの NuGet パッケージは、NuGet パッケージを参照するのと同じ方法で、アプリのプロジェクトファイルで参照されます。 パッケージが復元された後、アプリコードはと同じように JavaScript C#を呼び出すことができます。

詳細については、「 <xref:blazor/class-libraries>」を参照してください。

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
