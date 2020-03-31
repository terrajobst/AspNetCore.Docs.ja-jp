---
title: ASP.NET Core Blazor のルーティング
author: guardrex
description: アプリで要求をルーティングする方法と、NavLink コンポーネントについて説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/routing
ms.openlocfilehash: 87579c88a37e0258921e199db2b5d8c7627f5499
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "80218896"
---
# <a name="aspnet-core-blazor-routing"></a>ASP.NET Core Blazor のルーティング

作成者: [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor アプリで、要求をルーティングする方法と、`NavLink` コンポーネントを使用して、ナビゲーション リンクを作成する方法について説明します。

## <a name="aspnet-core-endpoint-routing-integration"></a>ASP.NET Core エンドポイントのルーティングの統合

Blazor サーバーは [ASP.NET Core エンドポイントのルーティング](xref:fundamentals/routing)に統合されています。 ASP.NET Core アプリは、`Startup.Configure` で `MapBlazorHub` を使用して、対話型コンポーネントの着信接続を受け入れるように構成します。

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

最も一般的な構成は、すべての要求を Razor ページにルーティングすることです。これは、Blazor サーバー アプリのサーバー側部分のホストとして機能します。 通常、*ホスト* ページは、 *_Host.cshtml* という名前になります。 ホスト ファイルに指定されるルートは、ルート照合で低い優先順位で動作するため、*フォールバック ルート*と呼ばれます。 フォールバック ルートは、他のルートが一致しない場合に考慮されます。 これにより、Blazor サーバー アプリと干渉することなく、他のコントローラーやページをアプリで使用できます。

## <a name="route-templates"></a>ルート テンプレート

`Router` コンポーネントでは、指定されたルートによる各コンポーネントへのルーティングが可能になります。 `Router` コンポーネントは *App.razor* ファイルに表示されます。

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

`@page` ディレクティブを含む *razor* ファイルがコンパイルされると、生成されたクラスに、ルート テンプレートを指定する <xref:Microsoft.AspNetCore.Components.RouteAttribute> が指定されます。

実行時に、`RouteView` コンポーネントは、

* `Router` から、必要なパラメーターと共に `RouteData` を受け取ります。
* 指定されたパラメーターを使用して、指定されたコンポーネントを、そのレイアウト (または任意の既定のレイアウト) でレンダリングします。

必要に応じて、レイアウト クラスで `DefaultLayout` パラメーターを指定して、レイアウトを指定しないコンポーネントに使用できます。 既定の Blazor テンプレートでは、`MainLayout` コンポーネントを指定しています。 *MainLayout.razor* は、テンプレート プロジェクトの *Shared* フォルダーにあります。 レイアウトの詳細については、「<xref:blazor/layouts>」を参照してください。

コンポーネントには、複数のルート テンプレートを適用できます。 次のコンポーネントは、`/BlazorRoute` と `/DifferentBlazorRoute` に対する要求に応答します。

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> URL が正しく解決されるように、アプリでは、`href` 属性に指定されているアプリのベース パス (`<base href="/">`) を使用して、その *wwwroot/index.html* ファイル (Blazor WebAssembly) または *Pages/_Host.cshtml* ファイル (Blazor Server) に `<base>` タグを含める必要があります。 詳細については、「<xref:host-and-deploy/blazor/index#app-base-path>」を参照してください。

## <a name="provide-custom-content-when-content-isnt-found"></a>コンテンツが見つからないときにカスタム コンテンツを提供する

`Router` コンポーネントを使用すると、要求されたルートでコンテンツが見つからない場合に、アプリでカスタム コンテンツを指定できます。

*App.razor* ファイルで、`Router` コンポーネントの `NotFound` テンプレート パラメーターにカスタム コンテンツを設定します。

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

`<NotFound>` タグのコンテンツには、他の対話型コンポーネントなど、任意の項目を含めることができます。 `NotFound` コンテンツに既定のレイアウトを適用するには、「<xref:blazor/layouts>」を参照してください。

## <a name="route-to-components-from-multiple-assemblies"></a>複数のアセンブリからコンポーネントにルーティングする

`AdditionalAssemblies` パラメーターを使用して、`Router` コンポーネントで、ルーティング可能なコンポーネントを検索するときに考慮する追加のアセンブリを指定します。 指定されたアセンブリは、`AppAssembly` に指定されたアセンブリに加えて考慮されます。 次の例では、`Component1` は、参照されているクラス ライブラリに定義されているルーティング可能なコンポーネントです。 次の `AdditionalAssemblies` の例では、`Component1` のルーティング サポートの結果を示しています。

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a>ルート パラメーター

ルーターは、ルート パラメーターを使用して、同じ名前の対応するコンポーネント パラメーターを設定します (大文字と小文字は区別されません)。

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

省略可能なパラメーターはサポートされていません。 前の例では、2 つの `@page` ディレクティブが適用されています。 1 つ目は、パラメーターを指定せずにコンポーネントへの移動を許可します。 2 番目の `@page` ディレクティブは、`{text}` ルート パラメーターを受け取り、その値を `Text` プロパティに割り当てます。

## <a name="route-constraints"></a>ルート制約

ルート制約は、コンポーネントへのルート セグメントに型の一致を適用します。

次の例で、`Users` コンポーネントへのルートは、次の場合にのみ一致します。

* 要求 URL に `Id` ルート セグメントが存在する。
* `Id` セグメントは整数 (`int`) である。

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

次の表に示すルート制約を使用できます。 インバリアント カルチャと一致するルート制約については、表の下の警告で詳細を確認してください。

| 制約 | 例           | 一致の例                                                                  | インバリアント<br>カルチャ<br>一致 |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | `true`、`FALSE`                                                                  | いいえ                               |
| `datetime` | `{dob:datetime}`  | `2016-12-31`、`2016-12-31 7:32pm`                                                | はい                              |
| `decimal`  | `{price:decimal}` | `49.99`、`-1,000.01`                                                             | はい                              |
| `double`   | `{weight:double}` | `1.234`、`-1,001.01e8`                                                           | はい                              |
| `float`    | `{weight:float}`  | `1.234`、`-1,001.01e8`                                                           | はい                              |
| `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`、`{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | いいえ                               |
| `int`      | `{id:int}`        | `123456789`、`-123456789`                                                        | はい                              |
| `long`     | `{ticks:long}`    | `123456789`、`-123456789`                                                        | はい                              |

> [!WARNING]
> URL の妥当性を検証し、CLR 型 (`int` や `DateTime` など) に変換されるルート制約では、常にインバリアント カルチャが使用されます。 これらの制約では、URL がローカライズ不可であることが前提となります。

### <a name="routing-with-urls-that-contain-dots"></a>ドットを含む URL によるルーティング

Blazor サーバー アプリでは、 *_Host.cshtml* の既定のルートは `/` (`@page "/"`) です。 ドット (`.`) を含む要求 URL は、既定のルートによって照合されません。URL がファイルを要求しているように見えるためです。 Blazor アプリは、存在しない静的ファイルに対して "*404 見つかりません*" 応答 を返します。 ドットを含むルートを使用するには、次のルート テンプレートを使用して *_Host.cshtml* を構成します。

```cshtml
@page "/{**path}"
```

`"/{**path}"` テンプレートには次のものが含まれます。

* 二重アスタリスクの*キャッチオール*構文 (`**`)。スラッシュ (`/`) をエンコードせずに複数のフォルダー境界をまたがるパスをキャプチャします。
* `path` ルート パラメーター名。

> [!NOTE]
> *キャッチオール* パラメーター構文 (`*`/`**`) は、Razor コンポーネント ( *.razor*) ではサポートされて**いません**。

詳細については、「<xref:fundamentals/routing>」を参照してください。

## <a name="navlink-component"></a>NavLink コンポーネント

ナビゲーション リンクを作成するときは、HTML ハイパーリンク要素 (`<a>`) の代わりに `NavLink` コンポーネントを使用します。 `NavLink` コンポーネントは `<a>` 要素のように動作しますが、`href` が現在の URL と一致するかどうかに基づいて `active` CSS クラスを切り替える点が異なります。 `active` クラスは、表示されているナビゲーション リンクの中でどのページがアクティブ ページであるかをユーザーが理解するのに役立ちます。

次の `NavMenu` コンポーネントでは、`NavLink` コンポーネントの使用方法を示す[ブートストラップ](https://getbootstrap.com/docs/) ナビゲーション バーを作成しています。

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

`<NavLink>` 要素の `Match` 属性に割り当てられる 2 つの `NavLinkMatch` オプションがあります。

* `NavLinkMatch.All` &ndash; `NavLink` は、現在の URL 全体に一致する場合にアクティブになります。
* `NavLinkMatch.Prefix` (*既定*) &ndash; `NavLink` は、現在の URL の任意のプレフィックスに一致する場合にアクティブになります。

前の例では、ホーム `NavLink` `href=""` はホーム URL と一致し、アプリの既定のベース パス URL (`https://localhost:5001/` など) でのみ `active` CSS クラスを受け取ります。 2 番目の `NavLink` は、ユーザーが `MyComponent` プレフィックスを含む任意の URL (`https://localhost:5001/MyComponent` や `https://localhost:5001/MyComponent/AnotherSegment` など) にアクセスしたときに、`active` クラスを受け取ります。

追加の `NavLink` コンポーネント属性は、レンダリングされるアンカー タグに渡されます。 次の例では、`NavLink` コンポーネントに `target` 属性が含まれています。

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

次の HTML マークアップがレンダリングされます。

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a>URI およびナビゲーション状態ヘルパー

C# コード内の URI とナビゲーションを操作するには、<xref:Microsoft.AspNetCore.Components.NavigationManager> を使用します。 `NavigationManager` には、次の表に示すイベントとメソッドがあります。

| メンバー | 説明 |
| ------ | ----------- |
| URI | 現在の絶対 URI を取得します。 |
| BaseUri | 絶対 URI を生成するために、相対 URI パスの前に付加できるベース URI (末尾のスラッシュを含む) を取得します。 通常、`BaseUri` は *wwwroot/index.html* (Blazor WebAssembly)、または *Pages/_Host.cshtml* (Blazor サーバー) 内のドキュメントの `<base>` 要素の `href` 属性に対応します。 |
| NavigateTo | 指定された URI に移動します。 `forceLoad` が `true` の場合:<ul><li>クライアント側のルーティングはバイパスされます。</li><li>URI が通常クライアント側ルーターによって処理されるかどうかにかかわらず、ブラウザーでは、強制的にサーバーから新しいページが読み込まれます。</li></ul> |
| LocationChanged | ナビゲーションの場所が変更されたときに発生するイベントです。 |
| ToAbsoluteUri | 相対 URI を絶対 URI に変換します。 |
| <span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span> | ベース URI (たとえば、`GetBaseUri` によって以前に返された URI) が与えられると、絶対 URI を、ベース URI プレフィックスに相対的な URI に変換します。 |

次のコンポーネントは、ボタンが選択されたときに、アプリの `Counter` コンポーネントに移動します。

```razor
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```

次のコンポーネントは、場所の変更イベントを処理します。 `HandleLocationChanged` メソッドは、`Dispose` がフレームワークによって呼び出されると、アンフックになります。 このメソッドをアンフックすることで、コンポーネントのガベージ コレクションが許可されます。

```razor
@implement IDisposable
@inject NavigationManager NavigationManager

...

protected override void OnInitialized()
{
    NavigationManager.LocationChanged += HandleLocationChanged;
}

private void HandleLocationChanged(object sender, LocationChangedEventArgs e)
{
    ...
}

public void Dispose()
{
    NavigationManager.LocationChanged -= HandleLocationChanged;
}
```

<xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> は、イベントに関する次の情報を提供します。

* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; 新しい場所の URL。
* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; `true` の場合、Blazor によってブラウザーからナビゲーションがインターセプトされました。 `false` の場合、[NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) によってナビゲーションが発生しました。

コンポーネントの破棄の詳細については、「<xref:blazor/lifecycle#component-disposal-with-idisposable>」を参照してください。
