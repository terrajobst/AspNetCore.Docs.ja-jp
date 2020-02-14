---
title: ASP.NET Core Blazor ホスティングモデルの構成
author: guardrex
description: Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティングモデルの構成について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 91dd1aa32bb5165845eb4794377a50ccbd01adc3
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2020
ms.locfileid: "77214941"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>ASP.NET Core Blazor ホスティングモデルの構成

[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

この記事では、ホスティングモデルの構成について説明します。

<!-- For future use:

## Blazor WebAssembly

-->

## <a name="blazor-server"></a>Blazor サーバー

### <a name="integrate-razor-components-into-razor-pages-and-mvc-apps"></a>Razor コンポーネントを Razor Pages および MVC アプリに統合する

#### <a name="use-components-in-pages-and-views"></a>ページおよびビューでのコンポーネントの使用

既存の Razor Pages または MVC アプリでは、Razor コンポーネントをページとビューに統合できます。

1. アプリのレイアウトファイル ( *_Layout*) で、次のようにします。

   * 次の `<base>` タグを `<head>` 要素に追加します。

     ```html
     <base href="~/" />
     ```

     前の例の `href` 値 (*アプリのベースパス*) は、アプリがルート URL パス (`/`) に存在することを前提としています。 アプリがサブアプリケーションである場合は、<xref:host-and-deploy/blazor/index#app-base-path> に関する記事の「*アプリの基本パス*」セクションのガイダンスに従ってください。

     *_Layout*のファイルは、MVC アプリの Razor Pages アプリまたは*Views/shared*フォルダー内の*Pages/shared*フォルダーにあります。

   * *Blazor*スクリプトの `<script>` タグを、終了 `</body>` タグの直前に追加します。

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     フレームワークによって、 *blazor*スクリプトがアプリに追加されます。 アプリにスクリプトを手動で追加する必要はありません。

1. 次の内容を使用して、プロジェクトのルートフォルダーに *_Imports razor*ファイルを追加します (最後の名前空間、`MyAppNamespace`をアプリの名前空間に変更します)。

   ```csharp
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. `Startup.ConfigureServices`で、Blazor Server サービスを登録します。

   ```csharp
   services.AddServerSideBlazor();
   ```

1. `Startup.Configure`で、`app.UseEndpoints`に Blazor Hub エンドポイントを追加します。

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. コンポーネントを任意のページまたはビューに統合します。 詳細については、<xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps> の記事の「*コンポーネントを Razor Pages と MVC アプリに統合*する」セクションを参照してください。

#### <a name="use-routable-components-in-a-razor-pages-app"></a>Razor Pages アプリでルーティング可能なコンポーネントを使用する

Razor Pages アプリでルーティング可能な Razor コンポーネントをサポートするには:

1. 「[ページおよびビューでコンポーネントを使用する](#use-components-in-pages-and-views)」セクションのガイダンスに従ってください。

1. 次の内容を含むプロジェクトのルートに、*アプリケーションの razor*ファイルを追加します。

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. 次の内容を含む *_Host*ファイルを*Pages*フォルダーに追加します。

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   コンポーネントは、そのレイアウトのために共有 *_Layout*ファイルを使用します。

1. `Startup.Configure`のエンドポイント構成に *_Host*の、次のように低優先度のルートを追加します。

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. ルーティング可能なコンポーネントをアプリに追加します。 例 :

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   カスタムフォルダーを使用してアプリのコンポーネントを保持する場合は、フォルダーを表す名前空間を*Pages/_ViewImports cshtml*ファイルに追加します。 詳細については、<xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps> を参照してください。

#### <a name="use-routable-components-in-an-mvc-app"></a>MVC アプリでルーティング可能なコンポーネントを使用する

MVC アプリでルーティング可能な Razor コンポーネントをサポートするには、次のようにします。

1. 「[ページおよびビューでコンポーネントを使用する](#use-components-in-pages-and-views)」セクションのガイダンスに従ってください。

1. 次の内容を含むプロジェクトのルートに、*アプリケーションの razor*ファイルを追加します。

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. 次の内容を含む *_Host*ファイルを*Views/Home*フォルダーに追加します。

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   コンポーネントは、そのレイアウトのために共有 *_Layout*ファイルを使用します。

1. Home コントローラーにアクションを追加します。

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. `Startup.Configure`のエンドポイント構成に *_Host*のビューを返すコントローラーアクションの優先度の低いルートを追加します。

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. *ページ*フォルダーを作成し、ルーティング可能なコンポーネントをアプリに追加します。 例 :

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   カスタムフォルダーを使用してアプリのコンポーネントを保持する場合は、フォルダーを表す名前空間を*Views/_ViewImports cshtml*ファイルに追加します。 詳細については、<xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps> を参照してください。

### <a name="reflect-the-connection-state-in-the-ui"></a>UI の接続状態を反映します。

クライアントが接続が失われたことを検出すると、クライアントが再接続しようとしているときに、既定の UI がユーザーに表示されます。 再接続に失敗した場合、ユーザーには再試行のオプションが表示されます。

UI をカスタマイズするには、 *_Host*の `<body>` の `components-reconnect-modal` の `id` を持つ要素を定義します。

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

次の表では、`components-reconnect-modal` 要素に適用される CSS クラスについて説明します。

| CSS クラス                       | &hellip; を示します。 |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | 失われた接続。 クライアントが再接続しようとしています。 モーダルを表示します。 |
| `components-reconnect-hide`     | サーバーへのアクティブな接続が再確立されます。 モーダルを非表示にします。 |
| `components-reconnect-failed`   | 再接続に失敗しました。ネットワーク障害が原因である可能性があります。 再接続を試みるには、`window.Blazor.reconnect()`を呼び出します。 |
| `components-reconnect-rejected` | 再接続は拒否されました。 サーバーに到達したが接続を拒否したため、サーバー上のユーザーの状態が失われています。 アプリを再度読み込むには、`location.reload()`を呼び出します。 この接続状態は、次の場合に発生する可能性があります。<ul><li>サーバー側回線でクラッシュが発生した場合。</li><li>サーバーがユーザーの状態を削除するのに十分な時間、クライアントが接続されていません。 ユーザーが対話しているコンポーネントのインスタンスは破棄されます。</li><li>サーバーが再起動されたか、アプリのワーカープロセスがリサイクルされています。</li></ul> |

### <a name="render-mode"></a>表示モード

Blazor サーバーアプリは、サーバーへのクライアント接続が確立される前に、サーバー上の UI を事前に作成するために既定で設定されます。 これは *_Host. cshtml* Razor ページで設定されます。

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

コンポーネントの `RenderMode` を構成します。

* ページに prerendered ます。
* は、ページに静的 HTML として表示されるか、ユーザーエージェントから Blazor アプリをブートストラップするために必要な情報が含まれている場合に表示されます。

| `RenderMode`        | 説明 |
| ------------------- | ----------- |
| `ServerPrerendered` | コンポーネントを静的 HTML にレンダリングし、Blazor サーバーアプリのマーカーを含めます。 ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。 |
| `Server`            | Blazor サーバーアプリのマーカーをレンダリングします。 コンポーネントからの出力は含まれていません。 ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。 |
| `Static`            | コンポーネントを静的 HTML にレンダリングします。 |

静的な HTML ページからのサーバーコンポーネントのレンダリングはサポートされていません。

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>Razor ページとビューからのステートフル対話型コンポーネントのレンダリング

ステートフル対話型コンポーネントは、Razor ページまたはビューに追加できます。

ページまたはビューが表示される場合:

* コンポーネントは、ページまたはビューと prerendered ます。
* プリレンダリングに使用される初期コンポーネントの状態は失われます。
* SignalR 接続が確立されると、新しいコンポーネントの状態が作成されます。

次の Razor ページでは、`Counter` コンポーネントがレンダリングされます。

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a>Razor ページとビューからの非対話型コンポーネントのレンダリング

次の Razor ページでは、フォームを使用して指定された初期値を使用して、`Counter` コンポーネントが静的にレンダリングされます。

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

`MyComponent` は静的にレンダリングされるため、コンポーネントを対話形式にすることはできません。

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a>Blazor Server アプリ用に SignalR クライアントを構成する

場合によっては、Blazor サーバーアプリによって使用される SignalR クライアントを構成する必要があります。 たとえば、接続の問題を診断するために SignalR クライアントのログ記録を構成することができます。

*Pages/_Host cshtml*ファイルで SignalR クライアントを構成するには、次のようにします。

* `blazor.server.js` スクリプトの `<script>` タグに `autostart="false"` 属性を追加します。
* `Blazor.start` を呼び出し、SignalR ビルダーを指定する構成オブジェクトを渡します。

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```
