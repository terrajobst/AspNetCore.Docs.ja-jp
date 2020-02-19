---
title: ASP.NET Core Razor コンポーネントを作成して使用する
author: guardrex
description: データにバインドする方法、イベントを処理する方法、コンポーネントのライフサイクルを管理する方法など、Razor コンポーネントを作成および使用する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: f9b4eab29fafe8113528062f57d28dadd0f57577
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447101"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>ASP.NET Core Razor コンポーネントを作成して使用する

[Luke Latham](https://github.com/guardrex)および[Daniel Roth](https://github.com/danroth27)

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

Blazor アプリは*コンポーネント*を使用して構築されます。 コンポーネントは、ページ、ダイアログ、フォームなどのユーザーインターフェイス (UI) の自己完結型のチャンクです。 コンポーネントには、HTML マークアップと、データを挿入したり UI イベントに応答したりするために必要な処理ロジックが含まれています。 コンポーネントは柔軟で軽量です。 入れ子にして再利用したり、プロジェクト間で共有したりすることができます。

## <a name="component-classes"></a>コンポーネントクラス

コンポーネントは、と HTML マークアップの C#組み合わせを使用して[razor](xref:mvc/views/razor)コンポーネントファイル (razor) に実装されます。 Blazor のコンポーネントは、正式に*Razor コンポーネント*として参照されます。

コンポーネントの名前は、大文字で始まる必要があります。 たとえば、 *MyCoolComponent*は有効で、 *MyCoolComponent*は無効です。

コンポーネントの UI は、HTML を使用して定義されます。 動的なレンダリング ロジック (たとえばループ、条件、式) が、[Razor](xref:mvc/views/razor) と呼ばれる埋め込みの C# 構文を使って追加されています。 アプリがコンパイルされると、HTML マークアップC#およびレンダリングロジックはコンポーネントクラスに変換されます。 生成されたクラスの名前は、ファイルの名前と一致します。

コンポーネント クラスのメンバーは、`@code` ブロック内で定義されています。 `@code` ブロックでは、コンポーネントの状態 (プロパティ、フィールド) は、イベント処理のメソッド、またはその他のコンポーネントロジックを定義するために指定されます。 複数の `@code` ブロックが許容されます。

コンポーネントメンバーは、`@`で始まる式を使用してC# 、コンポーネントのレンダリングロジックの一部として使用できます。 たとえば、 C#フィールド名をプレフィックス `@` によって表示されます。 次の例では、が評価され、レンダリングされます。

* `font-style`の CSS プロパティ値に `_headingFontStyle` します。
* `<h1>` 要素の内容に `_headingText` します。

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

コンポーネントが最初にレンダリングされた後、コンポーネントはイベントに応答してレンダリングツリーを再生成します。 Blazor は、新しいレンダリングツリーを前のレンダリングツリーと比較し、ブラウザーのドキュメントオブジェクトモデル (DOM) に変更を適用します。

コンポーネントは通常C#のクラスであり、プロジェクト内の任意の場所に配置できます。 Web ページを生成するコンポーネントは、通常、[*ページ*] フォルダーにあります。 ページ以外のコンポーネントは、多くの場合、プロジェクトに追加された*共有*フォルダーまたはカスタムフォルダーに配置されます。

通常、コンポーネントの名前空間は、アプリのルート名前空間と、アプリ内のコンポーネントの場所 (フォルダー) から派生します。 アプリのルート名前空間が `BlazorApp`、`Counter` コンポーネントが*Pages*フォルダーに存在する場合は、次のようになります。

* `Counter` コンポーネントの名前空間が `BlazorApp.Pages`。
* コンポーネントの完全修飾型名が `BlazorApp.Pages.Counter`ます。

詳細については、「[コンポーネントのインポート](#import-components)」セクションを参照してください。

カスタムフォルダーを使用するには、カスタムフォルダーの名前空間を親コンポーネントまたはアプリの *_Imports razor*ファイルに追加します。 たとえば、次の名前空間は、アプリのルート名前空間が `BlazorApp`場合*に、コンポーネントフォルダー内*のコンポーネントを使用可能にします。

```razor
@using BlazorApp.Components
```

## <a name="tag-helpers-arent-used-in-components"></a>タグヘルパーはコンポーネントで使用されていません

[タグヘルパー](xref:mvc/views/tag-helpers/intro)は razor コンポーネント (*razor*ファイル) ではサポートされていません。 Blazorにタグヘルパーのような機能を提供するには、タグヘルパーと同じ機能を持つコンポーネントを作成し、代わりにそのコンポーネントを使用します。

## <a name="use-components"></a>コンポーネントを使う

コンポーネントには、HTML 要素構文を使用して宣言することで、他のコンポーネントを含めることができます。 コンポーネントを使うためのマークアップは、そのコンポーネントの種類をタグ名とする HTML タグのようになります。

属性のバインドでは大文字と小文字が区別されます。 たとえば、`@bind` が有効で、`@Bind` が無効です。

*Index. razor*の次のマークアップは、`HeadingComponent` インスタンスをレンダリングします。

```razor
<HeadingComponent />
```

*Components/HeadingComponent*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

コンポーネント名と一致しない大文字の最初の文字を含む HTML 要素がコンポーネントに含まれている場合は、要素に予期しない名前が付いていることを示す警告が出力されます。 コンポーネントの名前空間に `@using` ディレクティブを追加すると、コンポーネントが使用可能になり、警告が解決されます。

## <a name="routing"></a>ルーティング

Blazor でのルーティングは、アプリ内のアクセス可能な各コンポーネントにルートテンプレートを提供することで実現されます。

`@page` ディレクティブを含む Razor ファイルがコンパイルされると、生成されたクラスにルートテンプレートを指定する <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> が与えられます。 実行時に、ルーターは `RouteAttribute` を持つコンポーネントクラスを検索し、要求された URL に一致するルートテンプレートを持つコンポーネントをレンダリングします。

```razor
@page "/ParentComponent"

...
```

詳細については、「<xref:blazor/routing>」を参照してください。

## <a name="parameters"></a>パラメーター

### <a name="route-parameters"></a>ルートパラメーター

コンポーネントは、`@page` ディレクティブで指定されたルートテンプレートからルートパラメーターを受け取ることができます。 ルーターは、ルートパラメーターを使用して、対応するコンポーネントパラメーターを設定します。

*Pages/RouteParameter。 razor*:

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

省略可能なパラメーターはサポートされていないため、前の例では2つの `@page` ディレクティブが適用されます。 最初のは、パラメーターを指定せずにコンポーネントへの移動を許可します。 2番目の `@page` ディレクティブは、`{text}` route パラメーターを受け取り、その値を `Text` プロパティに割り当てます。

複数のフォルダー境界をまたいでパスをキャプチャする*キャッチオール*パラメーター構文 (`*`/`**`) は、razor コンポーネント (*razor*) ではサポートされて**いません**。

### <a name="component-parameters"></a>コンポーネントのパラメーター

コンポーネントには、コンポーネントクラスのパブリックプロパティを使用して定義されるコンポーネント*パラメーター*を含めることができます。これは、`[Parameter]` 属性を使用して構成します。 マークアップ内でコンポーネントの引数を指定するには、属性を使います。

*Components/ChildComponent。 razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

サンプルアプリの次の例では、`ParentComponent` によって `ChildComponent`の `Title` プロパティの値が設定されます。

*Pages/ParentComponent。 razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

## <a name="child-content"></a>子コンテンツ

コンポーネントでは、別のコンポーネントのコンテンツを設定できます。 割り当てコンポーネントは、受信コンポーネントを指定するタグの間にコンテンツを提供します。

次の例では、`ChildComponent` に、レンダリングする UI のセグメントを表す `RenderFragment`を表す `ChildContent` プロパティがあります。 `ChildContent` の値は、コンテンツをレンダリングする必要があるコンポーネントのマークアップに配置されます。 `ChildContent` の値は、親コンポーネントから受信され、ブートストラップパネルの `panel-body`内に表示されます。

*Components/ChildComponent。 razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> `RenderFragment` コンテンツを受け取るプロパティには、規約によって `ChildContent` 名前を付ける必要があります。

サンプルアプリの `ParentComponent` では、コンテンツを `<ChildComponent>` タグ内に配置することによって、`ChildComponent` を表示するためのコンテンツを提供できます。

*Pages/ParentComponent。 razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a>属性スプラッティングと任意のパラメーター

コンポーネントは、コンポーネントの宣言されたパラメーターに加えて、追加の属性をキャプチャして表示できます。 Splatted Razor ディレクティブ[`@attributes`](xref:mvc/views/razor#attributes)を使用してコンポーネントがレンダリングされるときに、ディクショナリ内で追加の属性をキャプチャし、要素にすることができます。 このシナリオは、さまざまなカスタマイズをサポートするマークアップ要素を生成するコンポーネントを定義する場合に便利です。 たとえば、多くのパラメーターをサポートする `<input>` に対して、属性を個別に定義するのは面倒な場合があります。

次の例では、最初の `<input>` 要素 (`id="useIndividualParams"`) は個々のコンポーネントパラメーターを使用し、2番目の `<input>` 要素 (`id="useAttributesDict"`) は属性スプラッティングを使用します。

```razor
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    public string Maxlength { get; set; } = "10";

    [Parameter]
    public string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    public string Required { get; set; } = "required";

    [Parameter]
    public string Size { get; set; } = "50";

    [Parameter]
    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

パラメーターの型は、文字列キーを使用して `IEnumerable<KeyValuePair<string, object>>` を実装する必要があります。 このシナリオでは `IReadOnlyDictionary<string, object>` を使用することもできます。

両方の方法を使用して表示される `<input>` 要素は同じです。

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

任意の属性を受け入れるには、`CaptureUnmatchedValues` プロパティを `true`に設定して、`[Parameter]` 属性を使用してコンポーネントパラメーターを定義します。

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

`[Parameter]` の `CaptureUnmatchedValues` プロパティを使用すると、パラメーターを他のパラメーターと一致しないすべての属性と一致させることができます。 コンポーネントは、`CaptureUnmatchedValues`で1つのパラメーターのみを定義できます。 `CaptureUnmatchedValues` で使用されるプロパティの型は、文字列キーを使用して `Dictionary<string, object>` から割り当て可能である必要があります。 このシナリオでは、`IEnumerable<KeyValuePair<string, object>>` または `IReadOnlyDictionary<string, object>` もオプションです。

要素属性の位置を基準とした `@attributes` の位置が重要です。 要素に対して `@attributes` が splatted されると、属性は右から左 (最後から順) に処理されます。 `Child` コンポーネントを使用するコンポーネントの次の例を考えてみます。

*Parentcomponent。 razor*:

```razor
<ChildComponent extra="10" />
```

*Childcomponent。 razor*:

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

`Child` コンポーネントの `extra` 属性が `@attributes`の右側に設定されます。 属性が右から左に処理されるため、`Parent` コンポーネントのレンダリングされる `<div>` には `extra="5"` が含まれます。

```html
<div extra="5" />
```

次の例では、`Child` コンポーネントの `<div>`で `extra` と `@attributes` の順序が逆になっています。

*Parentcomponent。 razor*:

```razor
<ChildComponent extra="10" />
```

*Childcomponent。 razor*:

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

`Parent` コンポーネントに表示される `<div>` には、追加の属性を通じて渡された `extra="10"` が含まれます。

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a>コンポーネントへの参照をキャプチャする

コンポーネント参照を使用すると、`Show` や `Reset`などのコマンドをそのインスタンスに発行できるように、コンポーネントインスタンスを参照することができます。 コンポーネント参照をキャプチャするには、次のようにします。

* 子コンポーネントに[`@ref`](xref:mvc/views/razor#ref)属性を追加します。
* 子コンポーネントと同じ型のフィールドを定義します。

```razor
<MyLoginDialog @ref="_loginDialog" ... />

@code {
    private MyLoginDialog _loginDialog;

    private void OnSomething()
    {
        _loginDialog.Show();
    }
}
```

コンポーネントがレンダリングされると、[`_loginDialog`] フィールドに `MyLoginDialog` 子コンポーネントインスタンスが設定されます。 その後、コンポーネントインスタンスで .NET メソッドを呼び出すことができます。

> [!IMPORTANT]
> `_loginDialog` 変数は、コンポーネントがレンダリングされた後にのみ設定され、その出力には `MyLoginDialog` 要素が含まれます。 この時点までは、参照するものはありません。 コンポーネント参照のレンダリングが完了した後にコンポーネント参照を操作するに[は、OnAfterRenderAsync メソッドまたは OnAfterRender メソッド](xref:blazor/lifecycle#after-component-render)を使用します。

コンポーネント参照のキャプチャは、[要素参照のキャプチャ](xref:blazor/javascript-interop#capture-references-to-elements)に似た構文を使用しますが、 [JavaScript 相互運用](xref:blazor/javascript-interop)機能ではありません。 コンポーネント参照は、.NET コードでのみ使用される&mdash;JavaScript コードに渡されません。

> [!NOTE]
> 子コンポーネントの状態を変化**さ**せるためにコンポーネント参照を使用しないでください。 代わりに、通常の宣言型パラメーターを使用して、子コンポーネントにデータを渡します。 通常の宣言型パラメーターを使用すると、子コンポーネントが自動的にレンダリングされます。

## <a name="invoke-component-methods-externally-to-update-state"></a>状態を更新するためにコンポーネントメソッドを外部に呼び出す

Blazor は、`SynchronizationContext` を使用して、1つの論理スレッドの実行を強制します。 Blazor によって発生するコンポーネントの[ライフサイクルメソッド](xref:blazor/lifecycle)とイベントコールバックは、この `SynchronizationContext`で実行されます。 タイマーやその他の通知など、外部のイベントに基づいてコンポーネントを更新する必要がある場合は、`InvokeAsync` メソッドを使用します。これにより、Blazorの `SynchronizationContext`にディスパッチされます。

たとえば、更新された状態のすべてのリッスンコンポーネントに通知できる通知*サービス*を考えてみます。

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

`NotifierService` を次のように登録します。

* Blazor WebAssembly、`Program.Main`にサービスを登録します。

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* Blazor サーバーで、`Startup.ConfigureServices`にサービスを登録します。

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

コンポーネントを更新するには、`NotifierService` を使用します。

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @_lastNotification.key = @_lastNotification.value</p>

@code {
    private (string key, int value) _lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            _lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

前の例では、`NotifierService` は Blazorの `SynchronizationContext`の外部でコンポーネントの `OnNotify` メソッドを呼び出します。 `InvokeAsync` は、正しいコンテキストに切り替え、レンダリングをキューに移動するために使用されます。

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>\@キーを使用して要素とコンポーネントの保存を制御する

要素またはコンポーネントのリストをレンダリングするときに、要素またはコンポーネントが変更された場合、Blazorの比較アルゴリズムは、前の要素またはコンポーネントを保持できるかどうか、およびモデルオブジェクトをどのようにマップするかを決定する必要があります。 通常、このプロセスは自動的に実行され、無視することができますが、プロセスの制御が必要になる場合があります。

次に例を示します。

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

`People` コレクションの内容は、挿入、削除、または順序変更されたエントリで変更される可能性があります。 コンポーネントのれ時に、`<DetailsEditor>` コンポーネントが異なる `Details` パラメーター値を受け取るように変更されることがあります。 これにより、予想よりも複雑な再リリースが発生する可能性があります。 場合によっては、要素のフォーカスの喪失など、表示される動作の違いが発生する可能性があります。

マッピングプロセスは、`@key` ディレクティブ属性を使用して制御できます。 `@key` によって、キーの値に基づいて要素またはコンポーネントの保持が比較アルゴリズムによって保証されます。

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

`People` コレクションが変更されると、比較アルゴリズムによって、`<DetailsEditor>` インスタンスと `person` インスタンスの間の関連付けが保持されます。

* `Person` が `People` の一覧から削除された場合は、対応する `<DetailsEditor>` インスタンスだけが UI から削除されます。 その他のインスタンスは変更されません。
* リスト内のある位置に `Person` が挿入されると、その対応する位置に1つの新しい `<DetailsEditor>` インスタンスが挿入されます。 その他のインスタンスは変更されません。
* `Person` エントリが再順序付けされている場合は、対応する `<DetailsEditor>` インスタンスが UI に保持され、並べ替えられます。

場合によっては、`@key` を使用すると、回避の複雑さが最小限に抑えられるため、フォーカス位置など、DOM のステートフルな部分に対する潜在的な問題を回避できます。

> [!IMPORTANT]
> キーは、各コンテナー要素またはコンポーネントに対してローカルです。 キーがドキュメント全体でグローバルに比較されることはありません。

### <a name="when-to-use-key"></a>\@キーを使用する場合

通常は、リストがレンダリングされるたびに (たとえば、`@foreach` ブロックで) `@key` を使用し、`@key`を定義するための適切な値が存在することを意味します。

また、`@key` を使用すると、オブジェクトが変更されたときに Blazor によって要素またはコンポーネントのサブツリーが保持されないようにすることもできます。

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

`@currentPerson` が変更された場合、`@key` attribute ディレクティブは、`<div>` とその子孫全体を破棄し、新しい要素とコンポーネントで UI 内のサブツリーを再構築する Blazor を強制的に実行します。 これは、`@currentPerson` 変更時に UI の状態が保持されないことを保証する必要がある場合に役立ちます。

### <a name="when-not-to-use-key"></a>\@キーを使用しない場合

`@key`を使用すると、パフォーマンスが低下します。 パフォーマンスコストは大きくありませんが、要素またはコンポーネントの保存規則を制御することによってアプリにメリットがある場合にのみ `@key` を指定します。

`@key` が使用されていない場合でも Blazor、子要素とコンポーネントインスタンスは可能な限り保持されます。 `@key` を使用する唯一の利点は、マッピングを選択する比較アルゴリズムではなく、保持されているコンポーネントインスタンスにモデルインスタンスをマップする*方法*を制御することです。

### <a name="what-values-to-use-for-key"></a>\@キーに使用する値

一般に、`@key`には、次のいずれかの値を指定するのが理にかなっています。

* モデルオブジェクトインスタンス (たとえば、前の例のように、`Person` インスタンス)。 これにより、オブジェクト参照の等価性に基づいて保持されます。
* 一意の識別子 (たとえば、`int`型、`string`型、`Guid`型の主キー値)。

`@key` に使用される値が競合しないようにしてください。 競合するの値が同じ親要素内で検出された場合、Blazor は、古い要素またはコンポーネントを新しい要素またはコンポーネントに確定的にマップできないため、例外をスローします。 個別の値 (オブジェクトインスタンスや主キー値など) のみを使用してください。

## <a name="partial-class-support"></a>部分クラスのサポート

Razor コンポーネントは部分クラスとして生成されます。 Razor コンポーネントは、次のいずれかの方法を使用して作成されます。

* C#コードは、1つのファイル内に HTML マークアップと Razor コードを含む[`@code`](xref:mvc/views/razor#code)ブロックで定義されます。 Blazor テンプレートでは、この方法を使用して Razor コンポーネントを定義します。
* C#コードは、部分クラスとして定義されている分離コードファイルに配置されます。

次の例は、Blazor テンプレートから生成されたアプリで `@code` ブロックを持つ既定の `Counter` コンポーネントを示しています。 HTML マークアップ、Razor コード、 C#およびコードは、同じファイル内にあります。

*Counter。 razor*:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int _currentCount = 0;

    void IncrementCount()
    {
        _currentCount++;
    }
}
```

`Counter` コンポーネントは、部分クラスの分離コードファイルを使用して作成することもできます。

*Counter。 razor*:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

*Counter.razor.cs*:

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int _currentCount = 0;

        void IncrementCount()
        {
            _currentCount++;
        }
    }
}
```

必要に応じて、部分クラスファイルに必要な名前空間を追加します。 Razor コンポーネントで使用される一般的な名前空間は次のとおりです。

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a>基底クラスの指定

[`@inherits`](xref:mvc/views/razor#inherits)ディレクティブは、コンポーネントの基底クラスを指定するために使用できます。 次の例は、コンポーネントが基本クラス `BlazorRocksBase`を継承して、コンポーネントのプロパティとメソッドを提供する方法を示しています。 基底クラスは `ComponentBase`から派生する必要があります。

*Pages/BlazorRocks*:

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

*BlazorRocksBase.cs*:

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

## <a name="specify-an-attribute"></a>属性を指定する

属性は、 [`@attribute`](xref:mvc/views/razor#attribute)ディレクティブを使用して Razor コンポーネントで指定できます。 次の例では、`[Authorize]` 属性を component クラスに適用します。

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a>コンポーネントのインポート

Razor で作成されるコンポーネントの名前空間は、(優先順位に従って) に基づきます。

* Razor ファイル (*razor*) マークアップ (`@namespace BlazorSample.MyNamespace`) の[`@namespace`](xref:mvc/views/razor#namespace)指定。
* プロジェクトファイル内のプロジェクトの `RootNamespace` (`<RootNamespace>BlazorSample</RootNamespace>`)。
* プロジェクトファイルのファイル名 ( *.csproj*) から取得されたプロジェクト名、およびプロジェクトルートからコンポーネントへのパス。 たとえば、フレームワークは *{PROJECT ROOT}/* *BlazorSample (* ) を名前空間 `BlazorSample.Pages`に解決します。 コンポーネントはC# 、名前のバインド規則に従います。 この例の `Index` コンポーネントの場合、スコープ内のコンポーネントはすべてコンポーネントです。
  * 同じフォルダー内の*ページ*。
  * 別の名前空間を明示的に指定していない、プロジェクトのルート内のコンポーネント。

別の名前空間で定義されているコンポーネントは、Razor の[`@using`](xref:mvc/views/razor#using)ディレクティブを使用してスコープ内に置かれます。

*BlazorSample/Shared/* フォルダーに別のコンポーネント (`NavMenu.razor`) が存在する場合は、次の `@using` ステートメントを使用して `Index.razor` でコンポーネントを使用できます。

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

コンポーネントは、完全修飾名を使用して参照することもできます。この場合、 [`@using`](xref:mvc/views/razor#using)ディレクティブは必要ありません。

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> `global::` の修飾はサポートされていません。
>
> エイリアス付き `using` ステートメント (`@using Foo = Bar`など) を含むコンポーネントのインポートはサポートされていません。
>
> 部分修飾名はサポートされていません。 たとえば、`<Shared.NavMenu></Shared.NavMenu>` での `@using BlazorSample` の追加と `NavMenu.razor` の参照はサポートされていません。

## <a name="conditional-html-element-attributes"></a>条件付き HTML 要素の属性

HTML 要素の属性は、.NET の値に基づいて条件付きで表示されます。 値が `false` または `null`の場合、属性はレンダリングされません。 値が `true`場合は、属性が最小化されて表示されます。

次の例では、`IsCompleted` によって `checked` が要素のマークアップに表示されるかどうかが決まります。

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

`IsCompleted` が `true`場合、このチェックボックスは次のように表示されます。

```html
<input type="checkbox" checked />
```

`IsCompleted` が `false`場合、このチェックボックスは次のように表示されます。

```html
<input type="checkbox" />
```

詳細については、「<xref:mvc/views/razor>」を参照してください。

> [!WARNING]
> [Aria](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)などの一部の HTML 属性は、.net 型が `bool`の場合、正しく機能しません。 そのような場合は、`bool`ではなく `string` の型を使用します。

## <a name="raw-html"></a>未加工の HTML

通常、文字列は DOM テキストノードを使用してレンダリングされます。つまり、含まれているすべてのマークアップは無視され、リテラルテキストとして扱われます。 生の HTML をレンダリングするには、`MarkupString` 値で HTML コンテンツをラップします。 値は HTML または SVG として解析され、DOM に挿入されます。

> [!WARNING]
> 信頼できないソースから構築された生の HTML をレンダリングすることは、**セキュリティ上のリスク**があるため、回避する必要があります。

次の例では、`MarkupString` 型を使用して、コンポーネントのレンダリングされた出力に静的 HTML コンテンツのブロックを追加します。

```html
@((MarkupString)_myMarkup)

@code {
    private string _myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a>カスケード値とパラメーター

場合によっては、[コンポーネントパラメーター](#component-parameters)を使用して先祖コンポーネントから子孫コンポーネントにデータをフローするのは不便です。コンポーネントレイヤーが複数ある場合は特にそうです。 カスケード値およびパラメーターは、先祖コンポーネントがすべての子孫コンポーネントに値を提供するのに便利な方法を提供することで、この問題を解決します。 カスケード値とパラメーターは、コンポーネントでコーディネートする方法も提供します。

### <a name="theme-example"></a>テーマの例

次のサンプルアプリの例では、`ThemeInfo` クラスが、コンポーネント階層の下位にあるテーマ情報を指定して、アプリの特定の部分にあるすべてのボタンが同じスタイルを共有するようにしています。

*UiThemeInfo/* :

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

先祖コンポーネントでは、カスケード値コンポーネントを使用してカスケード値を指定できます。 `CascadingValue` コンポーネントは、コンポーネント階層のサブツリーをラップし、そのサブツリー内のすべてのコンポーネントに単一の値を提供します。

たとえば、サンプルアプリでは、アプリのいずれかのレイアウトのテーマ情報 (`ThemeInfo`) を、`@Body` プロパティのレイアウト本体を構成するすべてのコンポーネントのカスケード型パラメーターとして指定します。 `ButtonClass` には、レイアウトコンポーネントで `btn-success` の値が割り当てられます。 すべての子孫コンポーネントは、`ThemeInfo` カスケードオブジェクトを介してこのプロパティを使用できます。

`CascadingValuesParametersLayout` コンポーネント:

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="_theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo _theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

カスケード値を使用するために、コンポーネントは `[CascadingParameter]` 属性を使用してカスケード型パラメーターを宣言します。 カスケード値は、型によってカスケード型パラメーターにバインドされます。

サンプルアプリでは、`CascadingValuesParametersTheme` コンポーネントによって `ThemeInfo` カスケード値がカスケード型パラメーターにバインドされます。 パラメーターは、コンポーネントによって表示されるボタンの1つに CSS クラスを設定するために使用されます。

`CascadingValuesParametersTheme` コンポーネント:

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @_currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int _currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        _currentCount++;
    }
}
```

同じサブツリー内で同じ型の複数の値を連鎖させるには、各 `CascadingValue` コンポーネントとそれに対応する `CascadingParameter`に一意の `Name` 文字列を指定します。 次の例では、2つの `CascadingValue` コンポーネントが名前で `MyCascadingType` の異なるインスタンスをカスケードしています。

```razor
<CascadingValue Value=@_parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType _parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

子孫コンポーネントでは、カスケードされたパラメーターは、先祖コンポーネントの対応するカスケード値から名前で値を受け取ります。

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a>TabSet の例

カスケード型パラメーターを使用すると、コンポーネント階層全体でコンポーネントを連携させることもできます。 たとえば、サンプルアプリで次の*Tabset*の例を考えてみます。

このサンプルアプリには、タブに実装されている `ITab` インターフェイスがあります。

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

`CascadingValuesParametersTabSet` コンポーネントは、いくつかの `Tab` コンポーネントを含む `TabSet` コンポーネントを使用します。

```razor
<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>
```

子 `Tab` コンポーネントは、パラメーターとして `TabSet`に明示的に渡されません。 代わりに、子 `Tab` コンポーネントは、`TabSet`の子コンテンツの一部になります。 ただし、`TabSet` は、ヘッダーとアクティブなタブをレンダリングできるように、各 `Tab` コンポーネントについて認識している必要があります。追加のコードを必要とせずにこの調整を可能にするために、`TabSet` コンポーネントは、*それ自体をカスケード値として提供*し、その後、子孫 `Tab` コンポーネントによって取得されます。

`TabSet` コンポーネント:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

子孫の `Tab` コンポーネントは、含まれている `TabSet` をカスケード型パラメーターとしてキャプチャするので、`Tab` のコンポーネントは、どのタブがアクティブであるかを `TabSet` および座標に追加します。

`Tab` コンポーネント:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Razor テンプレート

レンダリングフラグメントは、Razor テンプレート構文を使用して定義できます。 Razor テンプレートは、UI スニペットを定義する方法であり、次の形式を想定しています。

```razor
@<{HTML tag}>...</{HTML tag}>
```

次の例は、`RenderFragment` と `RenderFragment<T>` の値を指定し、テンプレートをコンポーネント内で直接表示する方法を示しています。 レンダーフラグメントは、[テンプレートコンポーネント](xref:blazor/templated-components)に引数として渡すこともできます。

```razor
@_timeTemplate

@_petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment _timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> _petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

前のコードの出力をレンダリングします。

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a>スケーラブルベクターグラフィックス (SVG) イメージ

Blazor は HTML をレンダリングするため、スケーラブルベクターグラフィックス (svg) イメージ (*svg*) を含むブラウザーでサポートされているイメージは、`<img>` タグを介してサポートされます。

```html
<img alt="Example image" src="some-image.svg" />
```

同様に、SVG イメージは、スタイルシートファイル ( *.css*) の css 規則でサポートされています。

```css
.my-element {
    background-image: url("some-image.svg");
}
```

ただし、インライン SVG マークアップは、すべてのシナリオでサポートされているわけではありません。 コンポーネントファイル (*razor*) に `<svg>` タグを直接配置した場合、基本的な画像レンダリングはサポートされますが、多くの高度なシナリオはサポートされていません。 たとえば、`<use>` タグは現在尊重されていないため、`@bind` をいくつかの SVG タグと共に使用することはできません。 今後のリリースでは、これらの制限に対処する予定です。

## <a name="additional-resources"></a>その他のリソース

* <xref:security/blazor/server> &ndash; には、リソース枯渇に対処する必要がある Blazor サーバーアプリを構築するためのガイダンスが含まれています。
