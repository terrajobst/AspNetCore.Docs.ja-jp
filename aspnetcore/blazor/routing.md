---
title: ASP.NET Core Blazor ルーティング
author: guardrex
description: アプリで要求をルーティングする方法と、[ナビゲーションリンク] コンポーネントについて説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/routing
ms.openlocfilehash: 0cd15f25ff7975cae3f63a739212aa23062ece23
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2020
ms.locfileid: "76160159"
---
# <a name="aspnet-core-opno-locblazor-routing"></a>ASP.NET Core Blazor ルーティング

作成者: [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

要求をルーティングする方法と、`NavLink` コンポーネントを使用して Blazor アプリでナビゲーションリンクを作成する方法について説明します。

## <a name="aspnet-core-endpoint-routing-integration"></a>ASP.NET Core エンドポイントルーティングの統合

Blazor サーバーは[ASP.NET Core エンドポイントルーティング](xref:fundamentals/routing)に統合されています。 ASP.NET Core アプリは、`Startup.Configure`で `MapBlazorHub` を使用して、対話型コンポーネントの着信接続を受け入れるように構成されています。

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

最も一般的な構成は、すべての要求を Razor ページにルーティングすることです。これは、Blazor サーバーアプリのサーバー側の部分のホストとして機能します。 規則により、通常、*ホスト*ページは _Host という名前になり*ます。* ホストファイルに指定されているルートは、ルート照合の優先順位が低いため、*フォールバックルート*と呼ばれます。 フォールバックルートは、他のルートが一致しない場合に考慮されます。 これにより、アプリは、Blazor Server アプリに干渉することなく他のコントローラーやページを使用できるようになります。

## <a name="route-templates"></a>ルートテンプレート

`Router` コンポーネントでは、指定されたルートを使用して各コンポーネントにルーティングできます。 `Router` コンポーネントが、アプリケーションの*razor*ファイルに表示されます。

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

`@page` ディレクティブを含む*razor*ファイルがコンパイルされると、生成されたクラスにルートテンプレートを指定する <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> が提供されます。

実行時には、`RouteView` コンポーネントが次のようになります。

* `Router` から、必要なパラメーターと共に `RouteData` を受け取ります。
* 指定されたパラメーターを使用して、指定されたコンポーネントをそのレイアウト (または任意の既定のレイアウト) でレンダリングします。

必要に応じて、レイアウトクラスを含む `DefaultLayout` パラメーターを指定して、レイアウトを指定しないコンポーネントに使用することもできます。 既定の Blazor テンプレートでは、`MainLayout` コンポーネントが指定されています。 *Mainlayout。 razor*は、テンプレートプロジェクトの*共有*フォルダーにあります。 レイアウトの詳細については、「<xref:blazor/layouts>」を参照してください。

コンポーネントには、複数のルートテンプレートを適用できます。 次のコンポーネントは、`/BlazorRoute` と `/DifferentBlazorRoute`に対する要求に応答します。

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> Url が正しく解決されるようにするには、アプリで、`href` 属性 (`<base href="/">`) で指定されたアプリの基本パスを使用して、 *wwwroot/index.html*ファイル (Blazor WebAssembly または*Pages/_Host*ファイル (Blazor サーバー) に `<base>` タグを含める必要があります。 詳細については、「 <xref:host-and-deploy/blazor/index#app-base-path>」を参照してください。

## <a name="provide-custom-content-when-content-isnt-found"></a>コンテンツが見つからないときにカスタムコンテンツを提供する

`Router` コンポーネントを使用すると、要求されたルートのコンテンツが見つからない場合に、アプリでカスタムコンテンツを指定できます。

アプリケーションの*razor*ファイルで、`Router` コンポーネントの `NotFound` テンプレートパラメーターにカスタムコンテンツを設定します。

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

`<NotFound>` タグの内容には、他の対話型コンポーネントなど、任意の項目を含めることができます。 `NotFound` コンテンツに既定のレイアウトを適用するには、「<xref:blazor/layouts>」を参照してください。

## <a name="route-to-components-from-multiple-assemblies"></a>複数のアセンブリからコンポーネントへのルーティング

`AdditionalAssemblies` パラメーターを使用して、ルーティング可能なコンポーネントを検索するときに考慮する `Router` コンポーネントの追加のアセンブリを指定します。 指定されたアセンブリは、`AppAssembly`指定されたアセンブリに加えて考慮されます。 次の例では、`Component1` は、参照先クラスライブラリで定義されているルーティング可能なコンポーネントです。 次の `AdditionalAssemblies` 例では、`Component1`のルーティングサポートについての結果を示します。

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a>ルートパラメーター

ルーターはルートパラメーターを使用して、同じ名前の対応するコンポーネントパラメーターを設定します (大文字と小文字は区別されません)。

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

省略可能なパラメーターはサポートされていません。 前の例では、2つの `@page` ディレクティブが適用されています。 最初のは、パラメーターを指定せずにコンポーネントへの移動を許可します。 2番目の `@page` ディレクティブは、`{text}` route パラメーターを受け取り、その値を `Text` プロパティに割り当てます。

## <a name="route-constraints"></a>ルート制約

ルート制約は、ルートセグメントの型の一致をコンポーネントに適用します。

次の例では、`Users` コンポーネントへのルートは、次の場合にのみ一致します。

* 要求 URL に `Id` ルートセグメントが存在します。
* `Id` セグメントは整数 (`int`) です。

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

次の表に示されているルート制約を使用できます。 インバリアントカルチャと一致するルート制約の詳細については、表の下の警告を参照してください。

| 制約 | 使用例           | 一致の例                                                                  | インバリアント<br>カルチャ<br>一致 |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | `true`、`FALSE`                                                                  | いいえ                               |
| `datetime` | `{dob:datetime}`  | `2016-12-31`、`2016-12-31 7:32pm`                                                | ○                              |
| `decimal`  | `{price:decimal}` | `49.99`、`-1,000.01`                                                             | ○                              |
| `double`   | `{weight:double}` | `1.234`、`-1,001.01e8`                                                           | ○                              |
| `float`    | `{weight:float}`  | `1.234`、`-1,001.01e8`                                                           | ○                              |
| `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`、`{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | いいえ                               |
| `int`      | `{id:int}`        | `123456789`、`-123456789`                                                        | ○                              |
| `long`     | `{ticks:long}`    | `123456789`、`-123456789`                                                        | ○                              |

> [!WARNING]
> URL の妥当性を検証し、CLR 型 (`int` や `DateTime` など) に変換されるルート制約では、常にインバリアント カルチャが使用されます。 これらの制約では、URL がローカライズ不可であることが前提となります。

### <a name="routing-with-urls-that-contain-dots"></a>ドットを含む Url を使用したルーティング

Blazor サーバーアプリでは、 *_Host*の既定のルートは `/` (`@page "/"`) です。 ドット (`.`) を含む要求 URL が、既定のルートと一致しません。これは、URL がファイルを要求しているためです。 Blazor アプリは、存在しない静的ファイルに対して*404-Not Found*応答を返します。 ドットを含むルートを使用するには、次のルートテンプレートを使用して *_Host*を構成します。

```cshtml
@page "/{**path}"
```

`"/{**path}"` テンプレートには次のものが含まれます。

* 2つの*アスタリスク (* `**`) を使用すると、スラッシュ (`/`) をエンコードせずに複数のフォルダー境界を越えてパスをキャプチャできます。
* ルートパラメーター名 `path` ます。

> [!NOTE]
> *キャッチオール*パラメーター構文 (`*`/`**`) は、razor コンポーネント (*razor*) ではサポートされて**いません**。

詳細については、「 <xref:fundamentals/routing>」を参照してください。

## <a name="navlink-component"></a>ナビゲーションリンクコンポーネント

ナビゲーションリンクを作成するときは、HTML ハイパーリンク要素 (`<a>`) の代わりに `NavLink` コンポーネントを使用します。 `NavLink` コンポーネントは `<a>` 要素のように動作しますが、`href` が現在の URL と一致するかどうかに基づいて `active` CSS クラスを切り替える点が異なります。 `active` クラスは、表示されているナビゲーションリンク内のどのページがアクティブページであるかをユーザーが理解するのに役立ちます。

次の `NavMenu` コンポーネントでは、`NavLink` コンポーネントの使用方法を示す[ブートストラップ](https://getbootstrap.com/docs/)ナビゲーションバーが作成されます。

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

`<NavLink>` 要素の `Match` 属性には、次の2つの `NavLinkMatch` オプションを割り当てることができます。

* 現在の URL 全体に一致する場合に `NavLink` がアクティブである &ndash; `NavLinkMatch.All` ます。
* `NavLinkMatch.Prefix` (*既定*) の場合、`NavLink` は現在の URL の任意のプレフィックスに一致したときにアクティブ &ndash; ます。

前の例では、ホーム `NavLink` `href=""` ホーム URL と一致し、アプリの既定の基本パス URL (`https://localhost:5001/`など) で `active` CSS クラスのみを受け取ります。 2番目の `NavLink` は、ユーザーが `MyComponent` プレフィックス (`https://localhost:5001/MyComponent` や `https://localhost:5001/MyComponent/AnotherSegment`など) を持つ任意の URL にアクセスしたときに、`active` クラスを受け取ります。

追加の `NavLink` コンポーネント属性は、表示されるアンカータグに渡されます。 次の例では、`NavLink` コンポーネントに `target` 属性が含まれています。

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

次の HTML マークアップがレンダリングされます。

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a>URI およびナビゲーション状態ヘルパー

コード内でC# uri とナビゲーションを操作するには、`Microsoft.AspNetCore.Components.NavigationManager` を使用します。 `NavigationManager` には、次の表に示すイベントとメソッドが用意されています。

| メンバー | 説明 |
| ------ | ----------- |
| `Uri` | 現在の絶対 URI を取得します。 |
| `BaseUri` | 絶対 uri を生成するために、相対 URI パスの前に付加できるベース URI (末尾のスラッシュを含む) を取得します。 通常、`BaseUri` は、 *wwwroot/index.html* (Blazor WebAssembly または*Pages/_Host* (Blazor Server) のドキュメントの `<base>` 要素の `href` 属性に対応します。 |
| `NavigateTo` | 指定された URI に移動します。 `forceLoad` が `true`場合は、次のようになります。<ul><li>クライアント側のルーティングはバイパスされます。</li><li>ブラウザーは、通常、URI がクライアント側ルーターによって処理されるかどうかにかかわらず、サーバーから新しいページを読み込みます。</li></ul> |
| `LocationChanged` | ナビゲーション位置が変更されたときに発生するイベントです。 |
| `ToAbsoluteUri` | 相対 URI を絶対 URI に変換します。 |
| `ToBaseRelativePath` | ベース URI (たとえば、`GetBaseUri`によって以前に返された URI) を指定すると、は、絶対 URI をベース URI プレフィックスに対して相対的な URI に変換します。 |

次のコンポーネントは、ボタンが選択されたときにアプリの `Counter` コンポーネントに移動します。

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
