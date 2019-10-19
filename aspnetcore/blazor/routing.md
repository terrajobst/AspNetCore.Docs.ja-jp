---
title: ASP.NET Core Blazor ルーティング
author: guardrex
description: アプリで要求をルーティングする方法と、[ナビゲーションリンク] コンポーネントについて説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
no-loc:
- Blazor
uid: blazor/routing
ms.openlocfilehash: d4b76c00f79f333884fa7e30b27eadc6e36de287
ms.sourcegitcommit: a166291c6708f5949c417874108332856b53b6a9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2019
ms.locfileid: "72589934"
---
# <a name="aspnet-core-opno-locblazor-routing"></a>ASP.NET Core Blazor ルーティング

作成者: [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

要求をルーティングする方法と、`NavLink` コンポーネントを使用して Blazor アプリでナビゲーションリンクを作成する方法について説明します。

## <a name="aspnet-core-endpoint-routing-integration"></a>ASP.NET Core エンドポイントルーティングの統合

Blazor サーバーは[ASP.NET Core エンドポイントルーティング](xref:fundamentals/routing)に統合されています。 ASP.NET Core アプリは、`Startup.Configure` で `MapBlazorHub` を使用して、対話型コンポーネントの着信接続を受け入れるように構成されています。

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

最も一般的な構成は、すべての要求を Razor ページにルーティングすることです。これは、Blazor サーバーアプリのサーバー側の部分のホストとして機能します。 規則により、通常、*ホスト*ページは *_Host*という名前になります。 ホストファイルに指定されているルートは、ルート照合の優先順位が低いため、*フォールバックルート*と呼ばれます。 フォールバックルートは、他のルートが一致しない場合に考慮されます。 これにより、アプリは、Blazor Server アプリに干渉することなく他のコントローラーやページを使用できるようになります。

## <a name="route-templates"></a>ルートテンプレート

@No__t_0 コンポーネントでは、指定されたルートを使用して各コンポーネントにルーティングできます。 @No__t_0 コンポーネントが、アプリケーションの*razor*ファイルに表示されます。

```cshtml
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

@No__t_1 ディレクティブを含む*razor*ファイルがコンパイルされると、生成されたクラスにルートテンプレートを指定する <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> が提供されます。

実行時には、`RouteView` コンポーネントが次のようになります。

* @No__t_1 から、必要なパラメーターと共に `RouteData` を受け取ります。
* 指定されたパラメーターを使用して、指定されたコンポーネントをそのレイアウト (または任意の既定のレイアウト) でレンダリングします。

必要に応じて、レイアウトクラスを含む `DefaultLayout` パラメーターを指定して、レイアウトを指定しないコンポーネントに使用することもできます。 既定の Blazor テンプレートでは、`MainLayout` コンポーネントが指定されています。 *Mainlayout。 razor*は、テンプレートプロジェクトの*共有*フォルダーにあります。 レイアウトの詳細については、「<xref:blazor/layouts>」を参照してください。

コンポーネントには、複数のルートテンプレートを適用できます。 次のコンポーネントは `/BlazorRoute` および `/DifferentBlazorRoute` の要求に応答します。

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> Url が正しく解決されるようにするには、アプリに、`href` 属性 (`<base href="/">`) で指定されたアプリの基本パスを使用して、 *wwwroot/index.html*ファイル (Blazor WebAssembly または*Pages/_Host*ファイル (Blazor サーバー) に `<base>` タグを含める必要があります。 詳細については、「<xref:host-and-deploy/blazor/index#app-base-path>」を参照してください。

## <a name="provide-custom-content-when-content-isnt-found"></a>コンテンツが見つからないときにカスタムコンテンツを提供する

@No__t_0 コンポーネントを使用すると、要求されたルートのコンテンツが見つからない場合に、アプリでカスタムコンテンツを指定できます。

*App.xaml*ファイルで、`Router` コンポーネントの `NotFound` テンプレートパラメーターにカスタムコンテンツを設定します。

```cshtml
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

@No__t_0 タグの内容には、他の対話型コンポーネントなど、任意の項目を含めることができます。 @No__t_0 コンテンツに既定のレイアウトを適用するには、「<xref:blazor/layouts>」を参照してください。

## <a name="route-to-components-from-multiple-assemblies"></a>複数のアセンブリからコンポーネントへのルーティング

@No__t_0 パラメーターを使用して、ルーティング可能なコンポーネントを検索するときに考慮する `Router` コンポーネントの追加のアセンブリを指定します。 指定されたアセンブリは、`AppAssembly` 指定されたアセンブリに加えて考慮されます。 次の例では、`Component1` は、参照先クラスライブラリで定義されているルーティング可能なコンポーネントです。 次の `AdditionalAssemblies` 例では、`Component1` のルーティングサポートについての結果を示します。

```cshtml
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a>ルートパラメーター

ルーターはルートパラメーターを使用して、同じ名前の対応するコンポーネントパラメーターを設定します (大文字と小文字は区別されません)。

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

ASP.NET Core 3.0 の Blazor アプリでは、省略可能なパラメーターはサポートされていません。 前の例では、2つの `@page` ディレクティブが適用されています。 最初のは、パラメーターを指定せずにコンポーネントへの移動を許可します。 2番目の `@page` ディレクティブは、`{text}` route パラメーターを受け取り、`Text` プロパティに値を割り当てます。

## <a name="route-constraints"></a>ルート制約

ルート制約は、ルートセグメントの型の一致をコンポーネントに適用します。

次の例では、`Users` コンポーネントへのルートは、次の場合にのみ一致します。

* 要求 URL に `Id` ルートセグメントが存在します。
* @No__t_0 セグメントは整数 (`int`) です。

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

次の表に示されているルート制約を使用できます。 インバリアントカルチャと一致するルート制約の詳細については、表の下の警告を参照してください。

| 制約 | 例           | 一致の例                                                                  | インバリアント<br>カルチャ<br>一致 |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | `true`、 `FALSE`                                                                  | Ｘ                               |
| `datetime` | `{dob:datetime}`  | `2016-12-31`、 `2016-12-31 7:32pm`                                                | [はい]                              |
| `decimal`  | `{price:decimal}` | `49.99`、 `-1,000.01`                                                             | [はい]                              |
| `double`   | `{weight:double}` | `1.234`、 `-1,001.01e8`                                                           | [はい]                              |
| `float`    | `{weight:float}`  | `1.234`、 `-1,001.01e8`                                                           | [はい]                              |
| `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`、 `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Ｘ                               |
| `int`      | `{id:int}`        | `123456789`、 `-123456789`                                                        | [はい]                              |
| `long`     | `{ticks:long}`    | `123456789`、 `-123456789`                                                        | [はい]                              |

> [!WARNING]
> URL の妥当性を検証し、CLR 型 (`int` や `DateTime` など) に変換されるルート制約では、常にインバリアント カルチャが使用されます。 これらの制約では、URL がローカライズ不可であることが前提となります。

### <a name="routing-with-urls-that-contain-dots"></a>ドットを含む Url を使用したルーティング

@No__t_0 サーバーアプリでは、 *_Host*の既定のルートは `/` (`@page "/"`) です。 ドット (`.`) を含む要求 URL が、既定のルートと一致しません。これは、URL がファイルを要求しているためです。 @No__t_0 アプリは、存在しない静的ファイルに対して*404-Not Found*応答を返します。 ドットを含むルートを使用するには、次のルートテンプレートを使用して *_Host*を構成します。

```cshtml
@page "/{**path}"
```

@No__t_0 テンプレートには次のものが含まれます。

* 2つの*アスタリスク (* `**`) を使用すると、スラッシュ (`/`) をエンコードせずに複数のフォルダー境界を越えてパスをキャプチャできます。
* @No__t_0 ルートパラメーター名。

詳細については、「<xref:fundamentals/routing>」を参照してください。

## <a name="navlink-component"></a>ナビゲーションリンクコンポーネント

ナビゲーションリンクを作成するときは、HTML ハイパーリンク要素の代わりに `NavLink` コンポーネントを使用します (`<a>`)。 @No__t_0 コンポーネントは `<a>` 要素のように動作しますが、`href` が現在の URL と一致するかどうかに基づいて `active` CSS クラスを切り替える点が異なります。 @No__t_0 クラスは、表示されているナビゲーションリンク内のどのページがアクティブページであるかをユーザーが理解するのに役立ちます。

次の `NavMenu` コンポーネントでは、`NavLink` コンポーネントの使用方法を示す[ブートストラップ](https://getbootstrap.com/docs/)ナビゲーションバーが作成されます。

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

@No__t_2 要素の `Match` 属性には、次の2つの `NavLinkMatch` オプションを割り当てることができます。

* `NavLinkMatch.All` &ndash; `NavLink` は、現在の URL 全体に一致するとアクティブになります。
* `NavLinkMatch.Prefix` (*既定値*) &ndash; `NavLink` は、現在の URL の任意のプレフィックスに一致するとアクティブになります。

前の例では、ホーム `NavLink` `href=""` ホーム URL と一致し、アプリの既定の基本パス URL (`https://localhost:5001/` など) で `active` CSS クラスのみを受け取ります。 2番目の `NavLink` は、ユーザーが `MyComponent` プレフィックス (たとえば、`https://localhost:5001/MyComponent` と `https://localhost:5001/MyComponent/AnotherSegment`) を持つ任意の URL にアクセスしたときに、`active` クラスを受け取ります。

追加の `NavLink` コンポーネント属性は、表示されるアンカータグに渡されます。 次の例では、`NavLink` コンポーネントに `target` 属性が含まれています。

```cshtml
<NavLink href="my-page" target="_blank">My page</NavLink>
```

次の HTML マークアップがレンダリングされます。

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a>URI およびナビゲーション状態ヘルパー

コード内でC# uri とナビゲーションを操作するには `Microsoft.AspNetCore.Components.NavigationManager` を使用します。 `NavigationManager` は、次の表に示すイベントとメソッドを提供します。

| メンバー | 説明 |
| ------ | ----------- |
| `Uri` | 現在の絶対 URI を取得します。 |
| `BaseUri` | 絶対 uri を生成するために、相対 URI パスの前に付加できるベース URI (末尾のスラッシュを含む) を取得します。 通常、`BaseUri` は、 *wwwroot/index.html* (Blazor WebAssembly) または*Pages/* (Blazor Server) のドキュメントの `<base>` 要素の `href` 属性に対応します。 |
| `NavigateTo` | 指定された URI に移動します。 @No__t_0 が `true` 場合は、次のようになります。<ul><li>クライアント側のルーティングはバイパスされます。</li><li>ブラウザーは、通常、URI がクライアント側ルーターによって処理されるかどうかにかかわらず、サーバーから新しいページを読み込みます。</li></ul> |
| `LocationChanged` | ナビゲーション位置が変更されたときに発生するイベントです。 |
| `ToAbsoluteUri` | 相対 URI を絶対 URI に変換します。 |
| `ToBaseRelativePath` | ベース URI (たとえば、前に `GetBaseUri` によって返された URI) を指定すると、は、絶対 URI をベース URI プレフィックスに対して相対的な URI に変換します。 |

次のコンポーネントは、ボタンが選択されたときに、アプリの `Counter` コンポーネントに移動します。

```cshtml
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
