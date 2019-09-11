---
title: ASP.NET Core Blazor JavaScript 相互運用機能
author: guardrex
description: Blazor アプリで JavaScript から .NET および .NET メソッドから JavaScript 関数を呼び出す方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/07/2019
uid: blazor/javascript-interop
ms.openlocfilehash: fa485420c01e6a6d4181f733d6848de08ffca730
ms.sourcegitcommit: e7c56e8da5419bbc20b437c2dd531dedf9b0dc6b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70878358"
---
# <a name="aspnet-core-blazor-javascript-interop"></a>ASP.NET Core Blazor JavaScript 相互運用機能

[Javier Calvarro jeannine](https://github.com/javiercn)、 [Daniel Roth](https://github.com/danroth27)、 [Luke latham](https://github.com/guardrex)

Blazor アプリは、javascript コードから .NET および .NET メソッドから JavaScript 関数を呼び出すことができます。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="invoke-javascript-functions-from-net-methods"></a>.NET メソッドから JavaScript 関数を呼び出す

JavaScript 関数を呼び出すために .NET コードが必要になる場合があります。 たとえば、JavaScript 呼び出しは、ブラウザーの機能や機能を JavaScript ライブラリからアプリに公開できます。

.Net から JavaScript を呼び出すには、 `IJSRuntime`抽象化を使用します。 メソッド`InvokeAsync<T>`は、任意の数の JSON シリアル化可能な引数と共に、呼び出す JavaScript 関数の識別子を受け取ります。 関数識別子は、グローバルスコープ (`window`) を基準にしています。 を呼び出す`window.someScope.someFunction`場合、識別子は`someScope.someFunction`になります。 呼び出される前に、関数を登録する必要はありません。 戻り値の`T`型は、JSON シリアル化可能である必要もあります。

サーバー側アプリの場合:

* 複数のユーザー要求は、サーバー側アプリによって処理されます。 JavaScript 関数`JSRuntime.Current`を呼び出すために、コンポーネントでを呼び出さないでください。
* 抽象化を`IJSRuntime`挿入し、挿入されたオブジェクトを使用して、JavaScript の相互運用呼び出しを発行します。
* Blazor アプリのプリレンダリング中に、ブラウザーとの接続が確立されていないため、JavaScript を呼び出すことはできません。 詳細については、「 [Blazor アプリのプリレンダリングを検出する](#detect-when-a-blazor-app-is-prerendering)」セクションを参照してください。

次の例は、JavaScript ベースの試験的なデコーダーである[Textdecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder)に基づいています。 この例では、 C#メソッドから JavaScript 関数を呼び出す方法を示します。 JavaScript 関数は、 C#メソッドからバイト配列を受け取り、配列をデコードし、テキストをコンポーネントに返して表示できるようにします。

Wwwroot/index.html (Blazor client側) または*Pages/_Host* (Blazor サーバー側) の`TextDecoder` 要素内で、渡された配列をデコードするために使用する関数を指定します。`<head>`

[!code-html[](javascript-interop/samples_snapshot/index-script.html)]

前の例で示したコードなどの JavaScript コードは、スクリプトファイルへの参照を使用して JavaScript ファイル ( *.js*) から読み込むこともできます。

```html
<script src="exampleJsInterop.js"></script>
```

次のコンポーネント:

* コンポーネントボタン`ConvertArray` (配列の`JsRuntime` **変換**) が選択されている場合に、を使用して JavaScript 関数を呼び出します。
* JavaScript 関数が呼び出されると、渡された配列が文字列に変換されます。 文字列は、表示のためにコンポーネントに返されます。

[!code-cshtml[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

`IJSRuntime`抽象化を使用するには、次のいずれかの方法を採用します。

* Razor コンポーネント`IJSRuntime` (*razor*) に抽象を挿入します。

  [!code-cshtml[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

* 抽象化を`IJSRuntime`クラス ( *.cs*) に挿入します。

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

* [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic)を使用した動的なコンテンツ生成`[Inject]`の場合は、属性を使用します。

  ```csharp
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

このトピックに付属しているクライアント側のサンプルアプリでは、クライアント側アプリで2つの JavaScript 関数を使用して、ユーザー入力を受け取り、ウェルカムメッセージを表示するために DOM と対話することができます。

* `showPrompt`&ndash;ユーザー入力 (ユーザーの名前) を受け入れ、呼び出し元に名前を返すように求めるプロンプトを生成します。
* `displayWelcome`呼び出し元から`id` の`welcome`を持つ DOM オブジェクトにウェルカムメッセージを割り当てます。 &ndash;

*wwwroot/exampleJsInterop*:

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=2-7)]

JavaScript ファイルを参照する タグをwwwroot/index.htmlファイル(Blazorclient側)またはPages/_Hostファイル(Blazorサーバー側)に配置し`<script>`ます。

*wwwroot/index.html*(Blazor client 側):

[!code-html[](./common/samples/3.x/BlazorSample/wwwroot/index.html?highlight=15)]

*Pages/_Host*(Blazor サーバー側):

[!code-cshtml[](javascript-interop/samples_snapshot/_Host.cshtml?highlight=29)]

タグを動的`<script>`に更新できない`<script>`ため、コンポーネントファイルにタグを配置しないでください。

.NET メソッドは、を呼び出す`IJSRuntime.InvokeAsync<T>`ことによって、 *ExampleJsInterop*ファイル内の JavaScript 関数と相互運用します。

抽象化`IJSRuntime`は非同期であり、サーバー側のシナリオで使用できます。 アプリがクライアント側で実行され、JavaScript 関数を同期的に呼び出す必要がある`IJSInProcessRuntime`場合は`Invoke<T>` 、にダウンキャストし、代わりにを呼び出します。 ほとんどの JavaScript 相互運用機能ライブラリでは、非同期 Api を使用して、すべてのシナリオ、クライアント側またはサーバー側でライブラリを使用できるようにすることをお勧めします。

サンプルアプリには、JavaScript の相互運用機能を示すコンポーネントが含まれています。 コンポーネント:

* JavaScript プロンプトを使用してユーザー入力を受け取ります。
* 処理するテキストをコンポーネントに返します。
* DOM と対話してウェルカムメッセージを表示する2番目の JavaScript 関数を呼び出します。

*Pages/JSInterop*:

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop1&highlight=3,19-21,23-25)]

1. コンポーネント`TriggerJsPrompt`の **[トリガー JavaScript プロンプト]** ボタンを選択してを実行する`showPrompt`と、 *wwwroot/exampleJsInterop*ファイルに指定された javascript 関数が呼び出されます。
1. 関数`showPrompt`は、HTML エンコードされ、コンポーネントに返されるユーザー入力 (ユーザーの名前) を受け取ります。 コンポーネントは、ユーザーの名前をローカル変数`name`に格納します。
1. に`name`格納されている文字列は、ウェルカムメッセージに組み込まれています。これ`displayWelcome`は、ウェルカムメッセージを見出しタグに表示する JavaScript 関数に渡されます。

## <a name="call-a-void-javascript-function"></a>Void JavaScript 関数を呼び出す

[Void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void)または[undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined)を返す JavaScript 関数は、 `IJSRuntime.InvokeAsync<object>`を返す`null`を使用して呼び出されます。

## <a name="detect-when-a-blazor-app-is-prerendering"></a>Blazor アプリがプリレンダリングされるタイミングを検出する
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>要素への参照をキャプチャする

一部の[JavaScript の相互運用](xref:blazor/javascript-interop)シナリオでは、HTML 要素への参照が必要です。 たとえば、UI ライブラリに初期化のための要素参照が必要な場合や、要素 ( `focus`や`play`など) でコマンドに似た api を呼び出す必要がある場合があります。

次の方法を使用して、コンポーネント内の HTML 要素への参照をキャプチャします。

* `@ref`属性を HTML 要素に追加します。
* 属性の値と一致`ElementReference`する名前を持つ型のフィールドを定義します。 `@ref`

要素`<input>`へ`username`の参照をキャプチャする例を次に示します。

```cshtml
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!NOTE]
> Blazor が参照されている要素と対話するときに、DOM を作成または操作する方法として、キャプチャ**さ**れた要素参照を使用しないでください。 これを行うと、宣言型のレンダリングモデルに干渉する可能性があります。

.Net コードに関し`ElementReference`ては、は不透明なハンドルです。 この操作を実行`ElementReference`できるのは、javascript の相互運用機能を使用して javascript コードに渡すことだけです。 これを行うと、JavaScript 側のコードは、通常`HTMLElement`の DOM api で使用できるインスタンスを受け取ります。

たとえば、次のコードでは、要素にフォーカスを設定できるようにする .NET 拡張メソッドを定義しています。

*exampleJsInterop*:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

を`IJSRuntime.InvokeAsync<T>`使用し`exampleJsFunctions.focusElement` 、を`ElementReference`使用してを呼び出し、要素にフォーカスを移動します。

[!code-cshtml[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,11-12)]

拡張メソッドを使用して要素にフォーカスを移動するには、 `IJSRuntime`インスタンスを受け取る静的拡張メソッドを作成します。

```csharp
public static Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<object>(
        "exampleJsFunctions.focusElement", elementRef);
}
```

メソッドは、オブジェクトで直接呼び出されます。 次の例では、静的`Focus`メソッドを`JsInteropClasses`名前空間から使用できることを前提としています。

[!code-cshtml[](javascript-interop/samples_snapshot/component2.razor?highlight=1,4,12)]

> [!IMPORTANT]
> `username`変数は、コンポーネントがレンダリングされた後にのみ設定されます。 いない`ElementReference`が javascript コードに渡されると、javascript コードは`null`値を受け取ります。 コンポーネントのレンダリングが完了した後に要素参照を操作する (要素に初期フォーカスを設定する`OnAfterRenderAsync` ) `OnAfterRender`には、または[コンポーネントライフサイクルメソッド](xref:blazor/components#lifecycle-methods)を使用します。

## <a name="invoke-net-methods-from-javascript-functions"></a>JavaScript 関数からの .NET メソッドの呼び出し

### <a name="static-net-method-call"></a>静的 .NET メソッド呼び出し

JavaScript から静的 .net メソッドを呼び出すに`DotNet.invokeMethod`は、関数または`DotNet.invokeMethodAsync`関数を使用します。 呼び出す静的メソッドの識別子、関数を含むアセンブリの名前、および任意の引数を渡します。 サーバー側のシナリオをサポートするには、非同期バージョンを使用することをお勧めします。 .Net メソッドを JavaScript から呼び出すには、.net メソッドが public、static、および`[JSInvokable]`属性を持っている必要があります。 既定では、メソッド識別子はメソッド名ですが、 `JSInvokableAttribute`コンストラクターを使用して別の識別子を指定することもできます。 オープンジェネリックメソッドを呼び出すことは現在サポートされていません。

サンプルアプリには、 C#の`int`配列を返すメソッドが含まれています。 `JSInvokable`属性はメソッドに適用されます。

*Pages/JsInterop*:

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop2&highlight=7-11)]

クライアントに提供される JavaScript はC# 、.net メソッドを呼び出します。

*wwwroot/exampleJsInterop*:

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=8-14)]

**[.Net 静的メソッド ReturnArrayAsync のトリガー]** ボタンが選択されている場合は、ブラウザーの web developer ツールでコンソール出力を確認します。

コンソールの出力は次のとおりです。

```console
Array(4) [ 1, 2, 3, 4 ]
```

4番目の配列値は、によっ`data.push(4);`て`ReturnArrayAsync`返される配列 () にプッシュされます。

### <a name="instance-method-call"></a>インスタンスメソッドの呼び出し

JavaScript から .NET インスタンスメソッドを呼び出すこともできます。 JavaScript から .NET インスタンスメソッドを呼び出すには、次の手順を実行します。

* `DotNetObjectReference`インスタンスにラップして、.net インスタンスを JavaScript に渡します。 .NET インスタンスは、JavaScript への参照によって渡されます。
* `invokeMethod`または`invokeMethodAsync`関数を使用して、インスタンスで .net インスタンスメソッドを呼び出します。 .NET インスタンスは、JavaScript から他の .NET メソッドを呼び出すときに引数として渡すこともできます。

> [!NOTE]
> サンプルアプリは、メッセージをクライアント側コンソールに記録します。 サンプルアプリで示されている次の例については、ブラウザーの開発者ツールでブラウザーのコンソール出力を確認してください。

**[.Net インスタンスメソッドをトリガーする HelloHelper]** ボタンが選択さ`ExampleJsInterop.CallHelloHelperSayHello`れている場合、が呼び出さ`Blazor`れ、メソッドに名前が渡されます。

*Pages/JsInterop*:

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop3&highlight=8-9)]

`CallHelloHelperSayHello``sayHello` の`HelloHelper`新しいインスタンスを使用して JavaScript 関数を呼び出します。

*JsInteropClasses/ExampleJsInterop*:

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

*wwwroot/exampleJsInterop*:

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=15-18)]

この名前は、 `HelloHelper` `HelloHelper.Name`プロパティを設定するのコンストラクターに渡されます。 Javascript 関数`sayHello`が実行されると`HelloHelper.SayHello` 、は`Hello, {Name}!`メッセージを返します。これは、javascript 関数によってコンソールに書き込まれます。

*JsInteropClasses/HelloHelper*:

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

ブラウザーの web 開発者ツールのコンソール出力:

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-class-library"></a>クラスライブラリで相互運用コードを共有する

JavaScript 相互運用コードをクラスライブラリに含めることができます。これにより、NuGet パッケージ内のコードを共有できます。

クラスライブラリは、ビルドされたアセンブリに JavaScript リソースの埋め込みを処理します。 JavaScript ファイルは*wwwroot*フォルダーに配置されます。 ツールは、ライブラリの構築時にリソースの埋め込みを行います。

ビルド済みの NuGet パッケージは、NuGet パッケージを参照するのと同じ方法で、アプリのプロジェクトファイルで参照されます。 パッケージが復元された後、アプリコードはと同じように JavaScript C#を呼び出すことができます。

詳細については、「 <xref:blazor/class-libraries> 」を参照してください。

## <a name="harden-js-interop-calls"></a>JS 相互運用呼び出しの強化

JS 相互運用機能は、ネットワークエラーのために失敗する可能性があり、信頼性の低いものとして扱う必要があります。 既定では、Blazor サーバーアプリは、1分後に JS 相互運用機能呼び出しをサーバー上でタイムアウトします。 10秒など、アプリでより積極的なタイムアウトが許容される場合は、次のいずれかの方法を使用してタイムアウトを設定します。

* でグローバル`Startup.ConfigureServices`にタイムアウトを指定します。

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* コンポーネントコードでの呼び出しごとに、1回の呼び出しでタイムアウトを指定できます。

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

リソース枯渇の詳細については<xref:security/blazor/server-side>、「」を参照してください。
