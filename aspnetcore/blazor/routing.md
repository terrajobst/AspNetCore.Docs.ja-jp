---
title: ASP.NET Core Blazor ルーティング
author: guardrex
description: アプリで要求をルーティングする方法と、[ナビゲーションリンク] コンポーネントについて説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/23/2019
uid: blazor/routing
ms.openlocfilehash: 067dad657c1e89a31fac45fdfa095cce4b10798d
ms.sourcegitcommit: e6bd2bbe5683e9a7dbbc2f2eab644986e6dc8a87
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2019
ms.locfileid: "70238065"
---
# <a name="aspnet-core-blazor-routing"></a>ASP.NET Core Blazor ルーティング

作成者: [Luke Latham](https://github.com/guardrex)

要求をルーティングする方法と、 `NavLink`コンポーネントを使用して Blazor アプリでナビゲーションリンクを作成する方法について説明します。

## <a name="aspnet-core-endpoint-routing-integration"></a>ASP.NET Core エンドポイントルーティングの統合

Blazor サーバー側は[ASP.NET Core エンドポイントルーティング](xref:fundamentals/routing)に統合されています。 次の`MapBlazorHub` `Startup.Configure`のを使用して、対話型コンポーネントの着信接続を受け入れるように ASP.NET Core アプリを構成します。

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

## <a name="route-templates"></a>ルートテンプレート

`Router`コンポーネントによってルーティングが有効になり、各ユーザー補助コンポーネントにルートテンプレートが提供されます。 この`Router`コンポーネントは、*アプリケーションの razor*ファイルに表示されます。

Blazor サーバー側アプリの場合:

```cshtml
<Router AppAssembly="typeof(Startup).Assembly" />
```

Blazor クライアント側アプリの場合:

```cshtml
<Router AppAssembly="typeof(Program).Assembly" />
```

ディレクティブを含む<xref:Microsoft.AspNetCore.Mvc.RouteAttribute> `@page` razor ファイルがコンパイルされると、生成されたクラスにルートテンプレートを指定するが提供されます。 実行時に、ルーターはを`RouteAttribute`使用してコンポーネントクラスを検索し、要求された URL に一致するルートテンプレートを使用してコンポーネントをレンダリングします。

コンポーネントには、複数のルートテンプレートを適用できます。 次のコンポーネントは、と`/BlazorRoute` `/DifferentBlazorRoute`に対する要求に応答します。

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> ルートを正しく生成するには、アプリで`<base>` 、 `href`属性に指定されたアプリのベースパスを使用して、 *wwwroot/index.html*ファイル (Blazor client 側) または*Pages/_Host*ファイル (Blazor サーバー側) にタグを含める必要があります。`<base href="/">`). 詳細については、「 <xref:host-and-deploy/blazor/client-side#app-base-path> 」を参照してください。

## <a name="provide-custom-content-when-content-isnt-found"></a>コンテンツが見つからないときにカスタムコンテンツを提供する

`Router`コンポーネントを使用すると、要求されたルートのコンテンツが見つからない場合に、アプリでカスタムコンテンツを指定できます。

アプリケーションの*razor*ファイルで、 `<NotFoundContent>` `Router`コンポーネントの要素にカスタムコンテンツを設定します。

```cshtml
<Router AppAssembly="typeof(Startup).Assembly">
    <NotFoundContent>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFoundContent>
</Router>
```

の`<NotFoundContent>`コンテンツには、他の対話型コンポーネントなど、任意の項目を含めることができます。

## <a name="route-parameters"></a>ルートパラメーター

ルーターはルートパラメーターを使用して、同じ名前の対応するコンポーネントパラメーターを設定します (大文字と小文字は区別されません)。

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

ASP.NET Core 3.0 Preview の Blazor アプリでは、省略可能なパラメーターはサポートされていません。 前`@page`の例では、2つのディレクティブが適用されています。 最初のは、パラメーターを指定せずにコンポーネントへの移動を許可します。 2番`@page`目のディレクティブ`{text}`は route パラメーターを受け取り、その値`Text`をプロパティに割り当てます。

## <a name="route-constraints"></a>ルート制約

ルート制約は、ルートセグメントの型の一致をコンポーネントに適用します。

次の例では、 `Users`コンポーネントへのルートのみが一致します。

* `Id`ルートセグメントは、要求 URL に存在します。
* セグメントは整数 (`int`) です。 `Id`

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

次の表に示されているルート制約を使用できます。 インバリアントカルチャと一致するルート制約の詳細については、表の下の警告を参照してください。

| 制約 | 例           | 一致の例                                                                  | インバリアント<br>カルチャ<br>一致 |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | いいえ                               |
| `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | [はい]                              |
| `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | [はい]                              |
| `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | [はい]                              |
| `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | [はい]                              |
| `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | いいえ                               |
| `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | [はい]                              |
| `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | [はい]                              |

> [!WARNING]
> URL の妥当性を検証し、CLR 型 (`int` や `DateTime` など) に変換されるルート制約では、常にインバリアント カルチャが使用されます。 これらの制約では、URL がローカライズ不可であることが前提となります。

### <a name="routing-with-urls-that-contain-dots"></a>ドットを含む Url を使用したルーティング

Blazor サーバー側アプリでは、 *_Host*の既定のルートは`/` (`@page "/"`) です。 ドット (`.`) を含む要求 url が、既定のルートと一致しません。これは、url がファイルの要求によって表示されるためです。 Blazor アプリは、存在しない静的ファイルに対して*404-Not Found*応答を返します。 ドットを含むルートを使用するには、次のルートテンプレートを使用して *_Host*を構成します。

```cshtml
@page "/{**path}"
```

テンプレート`"/{**path}"`には次のものが含まれます。

* 2つの*アスタリスク*`**`() を使用すると、スラッシュ (`/`) をエンコードせずに複数のフォルダー境界を越えてパスをキャプチャできます。
* `path`ルートパラメーター名。

詳細については、「 <xref:fundamentals/routing> 」を参照してください。

## <a name="navlink-component"></a>ナビゲーションリンクコンポーネント

ナビゲーションリンク`NavLink`を作成するときは、HTML ハイパーリンク`<a>`要素 () の代わりにコンポーネントを使用します。 コンポーネント`NavLink` `<a>`は要素のように動作しますが、 `active`が現在の URL `href`と一致するかどうかに基づいて CSS クラスを切り替える点が異なります。 クラス`active`は、表示されているナビゲーションリンク内のどのページがアクティブページであるかをユーザーが理解するのに役立ちます。

次の `NavMenu` コンポーネントでは、`NavLink` コンポーネントの使用方法を示す[ブートストラップ](https://getbootstrap.com/docs/)ナビゲーションバーが作成されます。

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

要素の`Match` `NavLinkMatch` 属性には、次の2つのオプションを割り当てることができます`<NavLink>` 。

* `NavLinkMatch.All`は、現在の`NavLink` URL 全体に一致するとアクティブになります。 &ndash;
* `NavLinkMatch.Prefix`(*既定値*)は、`NavLink`現在の URL の任意のプレフィックスに一致するとアクティブになります。 &ndash;

前の例では、ホーム`NavLink` `href=""`はホーム`active` URL と一致し、アプリの既定の基本パス URL (たとえば、 `https://localhost:5001/`) でのみ CSS クラスを受け取ります。 2番`NavLink`目の`active`は、ユーザーが`MyComponent`プレフィックス (や`https://localhost:5001/MyComponent/AnotherSegment`など`https://localhost:5001/MyComponent` ) を持つ任意の URL にアクセスしたときにクラスを受け取ります。

追加`NavLink`のコンポーネント属性は、表示されるアンカータグに渡されます。 次の例では、 `NavLink`コンポーネントに`target`属性が含まれています。

```cshtml
<NavLink href="my-page" target="_blank">My page</NavLink>
```

次の HTML マークアップがレンダリングされます。

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a>URI およびナビゲーション状態ヘルパー

コード`Microsoft.AspNetCore.Components.IUriHelper`内でC# uri とナビゲーションを操作するには、を使用します。 `IUriHelper`次の表に示すイベントとメソッドを提供します。

| メンバー | 説明 |
| ------ | ----------- |
| `GetAbsoluteUri` | 現在の絶対 URI を取得します。 |
| `GetBaseUri` | 絶対 uri を生成するために、相対 URI パスの前に付加できるベース URI (末尾のスラッシュを含む) を取得します。 通常、 `GetBaseUri`は、 *wwwroot/index.html* (Blazor client 側) `<base>`または*Pages/_Host* (Blazor サーバー側) のドキュメントの要素の`href`属性に対応します。 |
| `NavigateTo` | 指定された URI に移動します。 が`forceLoad` の`true`場合:<ul><li>クライアント側のルーティングはバイパスされます。</li><li>ブラウザーは、通常、URI がクライアント側ルーターによって処理されるかどうかにかかわらず、サーバーから新しいページを読み込みます。</li></ul> |
| `OnLocationChanged` | ナビゲーション位置が変更されたときに発生するイベントです。 |
| `ToAbsoluteUri` | 相対 URI を絶対 URI に変換します。 |
| `ToBaseRelativePath` | ベース uri (たとえば、によって以前に`GetBaseUri`返された uri) を指定すると、は、絶対 uri をベース uri プレフィックスに対する相対 uri に変換します。 |

次のコンポーネントは、ボタンが選択`Counter`されたときにアプリのコンポーネントに移動します。

```cshtml
@page "/navigate"
@using Microsoft.AspNetCore.Components
@inject IUriHelper UriHelper

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        UriHelper.NavigateTo("counter");
    }
}
```

