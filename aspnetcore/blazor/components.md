---
title: ASP.NET Core Razor コンポーネントの作成と使用
author: guardrex
description: データへのバインド、イベントの処理、コンポーネント ライフサイクルの管理の方法など、Razor コンポーネントを作成および使用する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: 7afc9250cdfb4b791ef939ead0f41b503d83fad8
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511276"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>ASP.NET Core Razor コンポーネントの作成と使用

作成者: [Luke Latham](https://github.com/guardrex) と [Daniel Roth](https://github.com/danroth27)

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

Blazor アプリは *コンポーネント*を使用してビルドします。 コンポーネントは、ページ、ダイアログ、フォームなどのユーザー インターフェイス (UI) の自己完結型のチャンクです。 コンポーネントには、データの挿入や UI イベントへの応答に必要な HTML マークアップと、処理ロジックが含まれます。 コンポーネントは、柔軟性があり、軽量です。 それらを入れ子にしたり、再利用したり、プロジェクト間で共有したりできます。

## <a name="component-classes"></a>コンポーネント クラス

コンポーネントは、C# と HTML マークアップの組み合わせを使用して、[Razor](xref:mvc/views/razor) コンポーネント ファイル ( *.razor*) で実装します。 Blazor のコンポーネントは、正式には *Razor コンポーネント* と呼ばれます。

コンポーネントの名前は、大文字で始める必要があります。 たとえば、*MyCoolComponent.razor* は有効で、*myCoolComponent.razor* は無効です。

コンポーネントの UI は、HTML を使用して定義します。 動的なレンダリング ロジック (たとえばループ、条件、式) が、[Razor](xref:mvc/views/razor) と呼ばれる埋め込みの C# 構文を使って追加されています。 アプリがコンパイルされると、HTML マークアップと C# のレンダリング ロジックはコンポーネント クラスに変換されます。 生成されたクラスの名前は、ファイルの名前と一致します。

コンポーネント クラスのメンバーは、`@code` ブロック内で定義されています。 `@code` ブロックには、イベント処理のメソッド、またはその他のコンポーネント ロジックを定義するためのメソッドによって、コンポーネントの状態 (プロパティ、フィールド) を指定します。 複数の `@code` ブロックが許容されます。

コンポーネント メンバーは、`@` で始まる C# 式を使用して、コンポーネントのレンダリング ロジックの一部として使用できます。 たとえば、フィールド名の前に `@` を付けることによって、C# フィールドがレンダリングされます。 次の例では、以下のように評価され、レンダリングされます。

* `_headingFontStyle` が `font-style` の CSS プロパティ値に。
* `_headingText` が `<h1>` 要素のコンテンツに。

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

コンポーネントが最初にレンダリングされた後に、コンポーネントがイベントに応答して、レンダリング ツリーを再生成します。 Blazor によって新旧のレンダリング ツリーが比較され、ブラウザーのドキュメント オブジェクト モデル (DOM) に変更が適用されます。

コンポーネントは通常の C# クラスであり、プロジェクト内の任意の場所に配置できます。 Web ページを生成するコンポーネントは、通常、*Pages* フォルダーに存在します。 ページ以外のコンポーネントは、多くの場合に、*Shared* フォルダー、またはプロジェクトに追加されたカスタム フォルダーに配置されます。

一般に、コンポーネントの名前空間は、アプリのルート名前空間と、アプリ内のコンポーネントの場所 (フォルダー) から派生します。 アプリのルート名前空間が `BlazorApp` で、`Counter` コンポーネントが *Pages* フォルダーに存在する場合:

* `Counter` コンポーネントの名前空間は `BlazorApp.Pages` になります。
* コンポーネントの完全修飾型名は `BlazorApp.Pages.Counter` になります。

詳細については、「[コンポーネントのインポート](#import-components)」セクションを参照してください。

カスタム フォルダーを使用するには、カスタム フォルダーの名前空間を親コンポーネントまたはアプリの *_Imports.razor* ファイルに追加します。 たとえば、アプリのルート名前空間が `BlazorApp` である場合、次の名前空間によって、*Components* フォルダー内のコンポーネントを使用できます。

```razor
@using BlazorApp.Components
```

## <a name="static-assets"></a>静的な資産

Blazor は、プロジェクトの [Web ルート (wwwroot) フォルダー](xref:fundamentals/index#web-root)に静的アセットを配置する ASP.NET Core アプリの規則に従います。

静的アセットの Web ルートを参照するには、ベース相対パス (`/`) を使用します。 次の例では、*logo.png* が物理的に *{PROJECT ROOT}/wwwroot/images* フォルダーに置かれています。

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razor コンポーネントでは、チルダ スラッシュ表記 (`~/`) はサポートされて**いません**。

アプリのベース パスの設定の詳細については、「<xref:host-and-deploy/blazor/index#app-base-path>」を参照してください。

## <a name="tag-helpers-arent-supported-in-components"></a>タグ ヘルパーはコンポーネントでサポートされない

[タグ ヘルパー](xref:mvc/views/tag-helpers/intro) は、Razor コンポーネント ( *.razor* ファイル) でサポートされていません。 Blazor にタグ ヘルパーのような機能を提供するには、タグ ヘルパーと同じ機能を持つコンポーネントを作成し、代わりにそのコンポーネントを使用します。

## <a name="use-components"></a>コンポーネントを使う

コンポーネントには、HTML 要素構文を使用して宣言することで、他のコンポーネントを含めることができます。 コンポーネントを使うためのマークアップは、そのコンポーネントの種類をタグ名とする HTML タグのようになります。

*Index.razor* の次のマークアップは、`HeadingComponent` インスタンスをレンダリングします。

```razor
<HeadingComponent />
```

*Components/HeadingComponent.razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

コンポーネント名と一致しない最初の文字が大文字の HTML 要素がコンポーネントに含まれている場合、要素に予期しない名前が付いていることを示す警告が出力されます。 コンポーネントの名前空間に `@using` ディレクティブを追加すると、コンポーネントを使用できるようになり、警告が解決されます。

## <a name="routing"></a>ルーティング

Blazor でのルーティングは、アプリ内のアクセス可能な各コンポーネントへのルート テンプレートを提供することで実現します。

`@page` ディレクティブを含む Razor ファイルがコンパイルされると、生成されたクラスに、ルート テンプレートを指定する <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> が指定されます。 実行時に、ルーターによって `RouteAttribute` を持つコンポーネント クラスが検索され、要求された URL に一致するルート テンプレートを使用するコンポーネントがレンダリングされます。

```razor
@page "/ParentComponent"

...
```

詳細については、「<xref:blazor/routing>」を参照してください。

## <a name="parameters"></a>パラメーター

### <a name="route-parameters"></a>ルート パラメーター

コンポーネントでは、`@page` ディレクティブに指定されたルート テンプレートからルート パラメーターを受け取ることができます。 ルーターでは、ルート パラメーターを使用して、対応するコンポーネント パラメーターが設定されます。

*Pages/RouteParameter.razor*:

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

オプションのパラメーターはサポートされていないため、前の例では 2 つの `@page` ディレクティブが適用されます。 1 つ目は、パラメーターを指定せずにコンポーネントへの移動を許可します。 2 番目の `@page` ディレクティブは、`{text}` ルート パラメーターを受け取り、その値を `Text` プロパティに割り当てます。

複数のフォルダー境界をまたいだパスをキャプチャする*キャッチオール* パラメーター構文 (`*`/`**`) は、Razor コンポーネント ( *.razor*) ではサポートされて**いません**。

### <a name="component-parameters"></a>コンポーネントのパラメーター

コンポーネントには、*コンポーネント パラメーター*を指定でき、これは `[Parameter]` 属性を指定したコンポーネント クラスで、パブリック プロパティを使用して定義します。 マークアップ内でコンポーネントの引数を指定するには、属性を使います。

*Components/ChildComponent.razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

サンプル アプリの次の例では、`ParentComponent` によって `ChildComponent` の `Title` プロパティの値を設定しています。

*Pages/ParentComponent.razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

## <a name="child-content"></a>子コンテンツ

コンポーネントでは、別のコンポーネントのコンテンツを設定できます。 割り当てコンポーネントでは、受信コンポーネントを指定するタグ間にコンテンツを指定します。

次の例では、`ChildComponent` に、レンダリングする UI のセグメントを表す `RenderFragment` を表す `ChildContent` プロパティがあります。 コンテンツをレンダリングする必要があるコンポーネントのマークアップに、`ChildContent` の値を配置します。 `ChildContent` の値は、親コンポーネントから受け取られ、ブートストラップ パネルの `panel-body` 内にレンダリングされます。

*Components/ChildComponent.razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> `RenderFragment` コンテンツを受け取るプロパティは、規則によって `ChildContent` という名前にする必要があります。

サンプル アプリの `ParentComponent` では、コンテンツを `<ChildComponent>` タグ内に配置することによって、`ChildComponent` をレンダリングするためのコンテンツを提供できます。

*Pages/ParentComponent.razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a>属性スプラッティングと任意のパラメーター

コンポーネントでは、コンポーネントの宣言されたパラメーターに加えて、追加の属性をキャプチャしてレンダリングできます。 追加の属性は、ディクショナリにキャプチャし、[`@attributes`](xref:mvc/views/razor#attributes) Razor ディレクティブを使用して、コンポーネントがレンダリングされるときに、要素に*スプラッティング*できます。 このシナリオは、さまざまなカスタマイズをサポートするマークアップ要素を生成するコンポーネントを定義する場合に便利です。 たとえば、多くのパラメーターをサポートする `<input>` に対して、属性を個別に定義するのは面倒な場合があります。

次の例で、最初の `<input>` 要素 (`id="useIndividualParams"`) では、個々のコンポーネント パラメーターを使用していますが、2 番目の `<input>` 要素 (`id="useAttributesDict"`) では、属性スプラッティングを使用しています。

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

パラメーターの型は、文字列キーで `IEnumerable<KeyValuePair<string, object>>` を実装する必要があります。 このシナリオでは `IReadOnlyDictionary<string, object>` を使用することもできます。

両方の方法を使用してレンダリングされる `<input>` 要素は同じです。

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

任意の属性を受け入れるには、`CaptureUnmatchedValues` プロパティを `true` に設定した `[Parameter]` 属性を使用して、コンポーネント パラメーターを定義します。

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

`[Parameter]` の `CaptureUnmatchedValues` プロパティにより、パラメーターを他のパラメーターと一致しないすべての属性と一致させることができます。 1 つのコンポーネントで、`CaptureUnmatchedValues` を持つパラメーターは 1 つだけ定義できます。 `CaptureUnmatchedValues` で使用されるプロパティの型は、文字列キーを使用して `Dictionary<string, object>` から割り当て可能である必要があります。 このシナリオでは、`IEnumerable<KeyValuePair<string, object>>` または `IReadOnlyDictionary<string, object>` も使用できます。

要素属性の位置を基準とした `@attributes` の位置は重要です。 `@attributes` が要素にスプラッティングされると、属性は右から左 (最後から最初) に処理されます。 `Child` コンポーネントを使用する次のコンポーネントの例を考えます。

*ParentComponent.razor*:

```razor
<ChildComponent extra="10" />
```

*ChildComponent.razor*:

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

`Child` コンポーネントの `extra` 属性が `@attributes` の右側に設定されています。 属性は右から左 (最後から最初) に処理されるため、追加の属性によって渡された場合に、`Parent` コンポーネントのレンダリングされる `<div>` に、`extra="5"` が含まれます。

```html
<div extra="5" />
```

次の例では、`Child` コンポーネントの `<div>` で、`extra` と `@attributes` の順序が逆になります。

*ParentComponent.razor*:

```razor
<ChildComponent extra="10" />
```

*ChildComponent.razor*:

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

追加の属性によって渡された場合に、`Parent` コンポーネント内のレンダリングされる `<div>` には、`extra="10"` が含まれます。

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a>コンポーネントへの参照をキャプチャする

コンポーネント参照によって、コンポーネント インスタンスを参照する方法が得られるため、そのインスタンスに `Show` や `Reset` などのコマンドを発行できます。 コンポーネント参照をキャプチャするには:

* 子コンポーネントに [`@ref`](xref:mvc/views/razor#ref) 属性を追加します。
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

コンポーネントがレンダリングされると、`_loginDialog` フィールドに `MyLoginDialog` 子コンポーネント インスタンスが設定されます。 これにより、コンポーネント インスタンスに対し、.NET メソッドを呼び出すことができます。

> [!IMPORTANT]
> `_loginDialog` 変数は、コンポーネントがレンダリングされた後にのみ設定され、その出力には `MyLoginDialog` 要素が含まれます。 この時点まで、何も参照できません。 コンポーネントのレンダリングが完了した後にコンポーネント参照を操作するには、[OnAfterRenderAsync メソッドまたは OnAfterRender メソッド](xref:blazor/lifecycle#after-component-render)を使用します。

コンポーネント参照のキャプチャでは、[要素参照のキャプチャ](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)と類似の構文を使用しますが、それは JavaScript 相互運用機能ではありません。 コンポーネント参照は、JavaScript コードに渡されません &mdash; それらは .NET コードでのみ使用されます。

> [!NOTE]
> 子コンポーネントの状態を変えるためにコンポーネント参照を使用**しない**でください。 代わりに、通常の宣言型パラメーターを使用して、子コンポーネントにデータを渡します。 通常の宣言型パラメーターを使用すると、子コンポーネントが正しいタイミングで自動的にレンダリングされます。

## <a name="invoke-component-methods-externally-to-update-state"></a>状態を更新するために外部でコンポーネント メソッドを呼び出す

Blazor では、`SynchronizationContext` を使用して、1 つの論理スレッドを強制的に実行します。 コンポーネントの [ライフサイクル メソッド](xref:blazor/lifecycle)と Blazor によって発生するイベント コールバックは、この `SynchronizationContext` で実行されます。 タイマーやその他の通知などの外部のイベントに基づいてコンポーネントを更新する必要がある場合は、`InvokeAsync` メソッドを使用します。これにより、Blazor の `SynchronizationContext` にディスパッチされます。

たとえば、リッスンしているコンポーネントに、更新状態を通知できる*通知サービス* を考えてみます。

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

`NotifierService` をシングルトンとして登録します。

* Blazor WebAssembly で、`Program.Main` にサービスを登録します。

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* Blazor サーバーで、`Startup.ConfigureServices` にサービスを登録します。

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

`NotifierService` を使用して、コンポーネントを更新します。

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

前の例では、`NotifierService` が Blazor の `SynchronizationContext` の外部で、コンポーネントの `OnNotify` メソッドを呼び出します。 `InvokeAsync` を使用して、正しいコンテキストに切り替え、レンダリングをキューに登録します。

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>\@ キーを使用して要素とコンポーネントの保存を制御する

要素またはコンポーネントのリストをレンダリングし、その後に要素またはコンポーネントが変更された場合、Blazor の比較アルゴリズムでは、前のどの要素やコンポーネントを保持できるか、およびモデル オブジェクトをそれらにどのようにマップするかを決定する必要があります。 通常、このプロセスは自動で、無視できますが、プロセスの制御が必要になる場合があります。

次に例を示します。

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

`People` コレクションのコンテンツは、挿入、削除、または順序変更されたエントリによって変更される可能性があります。 コンポーネントのレンダリング時に、`<DetailsEditor>` コンポーネントが変更され、異なる `Details` パラメーター値を受け取ることがあります。 これにより、予期したものよりも複雑な再レンダリングが発生する可能性があります。 場合によっては、再レンダリングによって、要素のフォーカスの喪失などの表示動作の違いが発生する可能性があります。

マッピング プロセスは、`@key` ディレクティブ属性を使用して制御できます。 `@key` により、比較アルゴリズムで、キーの値に基づいて要素やコンポーネントが確実に保持されます。

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

`People` コレクションが変更されても、比較アルゴリズムによって、`<DetailsEditor>` インスタンスと `person` インスタンス間の関連付けが保持されます。

* `Person` が `People` リストから削除された場合、対応する `<DetailsEditor>` インスタンスだけが UI から削除されます。 他のインスタンスは変更されません。
* リスト内の特定の位置に `Person` が挿入されると、その対応する位置に、1 つの新しい `<DetailsEditor>` インスタンスが挿入されます。 他のインスタンスは変更されません。
* `Person` エントリの順序が変更された場合、対応する `<DetailsEditor>` インスタンスは UI で保持され、順序が変更されます。

シナリオによっては、`@key` を使用すると、レンダリングの複雑さが最小限に抑えられ、フォーカス位置など、DOM 変更のステートフルな部分の潜在的な問題を回避できます。

> [!IMPORTANT]
> キーは、各コンテナー要素やコンポーネントに対してローカルです。 キーはドキュメント全体でグローバルに比較されません。

### <a name="when-to-use-key"></a>\@ キーを使用する場面

一般に、リストがレンダリングされ (たとえば、`@foreach` ブロックで)、`@key` を定義するための適切な値が存在する場合は常に、`@key` を使用することは意味があります。

また、`@key` を使用して、オブジェクトが変更されたときに Blazor が要素やコンポーネントのサブツリーを保持しないようにすることもできます。

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

`@currentPerson` が変更された場合、`@key` 属性ディレクティブによって、Blazor に、`<div>` とその子孫全体を破棄させ、新しい要素とコンポーネントで UI 内のサブツリーをリビルドさせます。 これは、`@currentPerson` が変更されたときに、UI の状態が確実に保持されないようにする必要がある場合に役立つ可能性があります。

### <a name="when-not-to-use-key"></a>\@ キーを使用しない場面

`@key` で比較すると、パフォーマンスが低下します。 パフォーマンスの低下は大きくありませんが、要素やコンポーネントの保存規則を制御することによって、アプリにメリットがある場合にのみ `@key` を指定してください。

`@key` を使用しない場合でも、Blazor では可能な限り、子要素とコンポーネント インスタンスが保持されます。 `@key` を使用する唯一の利点は、マッピングを選択する比較アルゴリズムではなく、保持されているコンポーネント インスタンスにモデル インスタンスをマップする*方法*を制御することです。

### <a name="what-values-to-use-for-key"></a>\@ キーに使用する値

一般に、`@key` には、次のいずれかの種類の値を指定するのが適切です。

* モデル オブジェクトインスタンス (たとえば、前の例のように、`Person` インスタンス)。 これにより、オブジェクト参照の等価性に基づいて保持されます。
* 一意の識別子 (たとえば、`int` 型、`string` 型、`Guid` 型の主キー値)。

`@key` に使用される値は競合しないようにしてください。 同じ親要素内で競合する値が検出された場合、Blazor では、古い要素やコンポーネントを新しい要素やコンポーネントに確定的にマップできないため、例外がスローされます。 個別の値 (オブジェクト インスタンスや主キー値など) のみを使用してください。

## <a name="partial-class-support"></a>部分クラスのサポート

Razor コンポーネントは、部分クラスとして生成されます。 Razor コンポーネントは、次のいずれかの方法を使用して作成します。

* C# コードは、1 つのファイルに HTML マークアップと Razor コードを含む [`@code`](xref:mvc/views/razor#code) ブロックで定義します。 Blazor テンプレートでは、この方法を使用して Razor コンポーネントを定義します。
* C# コードは、部分クラスとして定義されている分離コード ファイルに配置されます。

次の例は、Blazor テンプレートから生成されたアプリ内の `@code` ブロックを含む既定の `Counter` コンポーネントを示しています。 HTML マークアップ、Razor コード、C# コードは、同じファイル内にあります。

*Counter.razor*:

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

`Counter` コンポーネントは、部分クラスを含む分離コード ファイルを使用して作成することもできます。

*Counter.razor*:

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

必要に応じて、部分クラスファイルに必要な名前空間を追加します。 Razor コンポーネントで使用される一般的な名前空間には次のものが含まれます。

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a>基本クラスの指定

[`@inherits`](xref:mvc/views/razor#inherits) ディレクティブを使用して、コンポーネントの基本クラスを指定できます。 次の例は、コンポーネントが基本クラス `BlazorRocksBase` を継承して、コンポーネントのプロパティとメソッドを提供する方法を示しています。 基本クラスは `ComponentBase` から派生する必要があります。

*Pages/BlazorRocks.razor*:

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

## <a name="specify-an-attribute"></a>属性の指定

属性は、[`@attribute`](xref:mvc/views/razor#attribute) ディレクティブを使用して Razor コンポーネントに指定できます。 次の例では、`[Authorize]` 属性をコンポーネント クラスに適用しています。

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a>コンポーネントのインポート

Razor で作成されるコンポーネントの名前空間は、次に基づきます (優先順で)。

* Razor ファイル ( *.razor*) マークアップ内の [`@namespace`](xref:mvc/views/razor#namespace) の指定 (`@namespace BlazorSample.MyNamespace`)。
* プロジェクト ファイル内のプロジェクトの `RootNamespace` (`<RootNamespace>BlazorSample</RootNamespace>`)。
* プロジェクト ファイルのファイル名 ( *.csproj*) から取得されたプロジェクト名、およびプロジェクト ルートからコンポーネントへのパス。 たとえば、フレームワークでは *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) が名前空間 `BlazorSample.Pages` に解決されます。 コンポーネントは C# の名前のバインド規則に従います。 この例の `Index` コンポーネントの場合、スコープ内のコンポーネントは、次のすべてのコンポーネントです。
  * 同じ *Pages* フォルダー内。
  * 別の名前空間を明示的に指定しない、プロジェクトのルート内のコンポーネント。

別の名前空間に定義されているコンポーネントは、Razor の [`@using`](xref:mvc/views/razor#using) ディレクティブを使用してスコープ内に取り込みます。

別のコンポーネントの `NavMenu.razor` が *BlazorSample/Shared/* フォルダーに存在する場合は、次の `@using` ステートメントを使用して、そのコンポーネントを `Index.razor` で使用できます。

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

コンポーネントは、完全修飾名を使用して参照することもできます。この場合、[`@using`](xref:mvc/views/razor#using) ディレクティブは必要ありません。

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> `global::` 修飾はサポートされていません。
>
> 別名が付けられた `using` ステートメント (`@using Foo = Bar` など) によるコンポーネントのインポートはサポートされていません。
>
> 部分修飾名はサポートされていません。 たとえば、`<Shared.NavMenu></Shared.NavMenu>` による `@using BlazorSample` の追加と `NavMenu.razor` の参照はサポートされていません。

## <a name="conditional-html-element-attributes"></a>条件付き HTML 要素属性

HTML 要素属性は、.NET 値に基づいて条件付きでレンダリングされます。 値が `false` または `null` の場合、属性はレンダリングされません。 値が `true` の場合、属性が最小化されてレンダリングされます。

次の例では、`IsCompleted` によって `checked` が要素のマークアップにレンダリングされるかどうかを決定します。

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

`IsCompleted` が `true` の場合、このチェック ボックスは次のようにレンダリングされます。

```html
<input type="checkbox" checked />
```

`IsCompleted` が `false` の場合、このチェック ボックスは次のようにレンダリングされます。

```html
<input type="checkbox" />
```

詳細については、「<xref:mvc/views/razor>」を参照してください。

> [!WARNING]
> .NET 型が `bool` の場合、[aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons) などの一部の HTML 属性が正しく機能しません。 そのような場合は、`bool` ではなく `string` 型を使用します。

## <a name="raw-html"></a>生 HTML

通常、文字列は DOM テキスト ノードを使用してレンダリングされます。つまり、それらに含まれている可能性のあるすべてのマークアップが無視され、リテラル テキストとして扱われます。 生 HTML をレンダリングするには、HTML コンテンツを `MarkupString` 値にラップします。 値は HTML または SVG として解析され、DOM に挿入されます。

> [!WARNING]
> 信頼されていないソースから構築された生 HTML をレンダリングすることは、**セキュリティ リスク**であるため、避ける必要があります。

次の例では、`MarkupString` 型を使用して、コンポーネントのレンダリングされた出力に静的 HTML コンテンツのブロックを追加しています。

```html
@((MarkupString)_myMarkup)

@code {
    private string _myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a>値とパラメーターのカスケード

一部のシナリオでは、特に複数のコンポーネント レイヤーがある場合に、[コンポーネント パラメーター](#component-parameters)を使用して、先祖コンポーネントから子孫コンポーネントにデータをフローすることが不便な場合があります。 値とパラメーターのカスケードによって、先祖コンポーネントがそのすべての子孫コンポーネントに値を提供するための便利な方法を用意することで、この問題が解決されます。 値とパラメーターのカスケードによって、コンポーネントで調整する方法も得られます。

### <a name="theme-example"></a>テーマの例

次のサンプル アプリの例では、`ThemeInfo` クラスで、コンポーネント階層の下位に伝達されるテーマ情報を指定して、アプリの特定の部分にあるすべてのボタンが同じスタイルを共有するようにしています。

*UIThemeClasses/ThemeInfo.cs*:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

先祖コンポーネントでは、Cascading Value コンポーネントを使用してカスケード値を指定できます。 `CascadingValue` コンポーネントは、コンポーネント階層のサブツリーをラップし、そのサブツリー内のすべてのコンポーネントに単一の値を指定します。

たとえば、サンプル アプリでは、アプリのレイアウトの 1 つに、`@Body` プロパティのレイアウト本体を構成するすべてのコンポーネントのカスケード パラメーターとして、テーマ情報 (`ThemeInfo`) を指定しています。 `ButtonClass` には、レイアウト コンポーネント内の `btn-success` の値が割り当てられます。 すべての子孫コンポーネントで、`ThemeInfo` カスケード オブジェクトからこのプロパティを使用できます。

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

カスケード値を使用するには、コンポーネントで `[CascadingParameter]` 属性を使用してカスケード パラメーターを宣言します。 カスケード値は、型によってカスケード パラメーターにバインドされます。

サンプル アプリでは、`CascadingValuesParametersTheme` コンポーネントによって `ThemeInfo` カスケード値をカスケード パラメーターにバインドしています。 パラメーターは、コンポーネントによって表示されるボタンの 1 つに CSS クラスを設定するために使用されます。

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

同じサブツリー内で同じ型の複数の値をカスケードするには、各 `CascadingValue` コンポーネントとその対応する `CascadingParameter` に一意の `Name` 文字列を指定します。 次の例では、2 つの `CascadingValue` コンポーネントで、名前によって `MyCascadingType` の異なるインスタンスをカスケードしています。

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

子孫コンポーネントでは、カスケードされたパラメーターは、それらの値を、名前によって、先祖コンポーネント内の対応するカスケードされた値から受け取ります。

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

パラメーターのカスケードによって、コンポーネント階層全体でコンポーネントを連携させることもできます。 たとえば、サンプル アプリの次の *TabSet* の例を考えてみましょう。

このサンプル アプリには、タブに実装されている `ITab` インターフェイスがあります。

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

`CascadingValuesParametersTabSet` コンポーネントでは、いくつかの `Tab` コンポーネントを含む `TabSet` コンポーネントを使用します。

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

子 `Tab` コンポーネントは、パラメーターとして `TabSet` に明示的に渡されません。 代わりに、子 `Tab` コンポーネントは、`TabSet` の子コンテンツに含まれます。 ただし、`TabSet` は、ヘッダーとアクティブなタブをレンダリングできるように、各 `Tab` コンポーネントについて認識している必要があります。追加のコードを必要とせずにこの調整を可能にするために、`TabSet` コンポーネントでは、*それ自体をカスケード値として指定し*、その後に子孫 `Tab` コンポーネントによって取得できるようにします。

`TabSet` コンポーネント:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

子孫の `Tab` コンポーネントでは、含まれている `TabSet` をカスケード パラメーターとしてキャプチャするため、`Tab` コンポーネントはそれ自体を `TabSet` に追加し、どのタブをアクティブにするかを調整します。

`Tab` コンポーネント:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Razor テンプレート

レンダリング フラグメントは、Razor テンプレート構文を使用して定義できます。 Razor テンプレートは、UI スニペットを定義する 1 つの方法であり、次の形式を想定しています。

```razor
@<{HTML tag}>...</{HTML tag}>
```

次の例では、`RenderFragment` と `RenderFragment<T>` の値を指定し、コンポーネント内にテンプレートを直接レンダリングする方法を示しています。 レンダリング フラグメントは、引数として[テンプレート コンポーネント](xref:blazor/templated-components)に渡すこともできます。

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

前のコードのレンダリングされた結果:

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a>スケーラブル ベクター グラフィックス (SVG) イメージ

Blazor は HTML をレンダリングするため、スケーラブル ベクター グラフィックス (SVG) イメージ ( *.svg*) などのブラウザーでサポートされているイメージは、`<img>` タグを介してサポートされます。

```html
<img alt="Example image" src="some-image.svg" />
```

同様に、SVG イメージは、スタイルシート ファイル ( *.css*) の CSS 規則でサポートされています。

```css
.my-element {
    background-image: url("some-image.svg");
}
```

ただし、インライン SVG マークアップは、すべてのシナリオでサポートされているわけではありません。 `<svg>` タグをコンポーネント ファイル ( *.razor*) に直接配置した場合、基本的なイメージ レンダリングはサポートされますが、多くの高度なシナリオはまだサポートされていません。 たとえば、`<use>` タグは現在考慮されないため、一部の SVG タグで `@bind` を使用できません。 将来のリリースで、これらの制限に対処する予定です。

## <a name="additional-resources"></a>その他の技術情報

* <xref:security/blazor/server> &ndash; リソース不足に対処する必要がある Blazor サーバー アプリの構築に関するガイダンスが含まれています。
