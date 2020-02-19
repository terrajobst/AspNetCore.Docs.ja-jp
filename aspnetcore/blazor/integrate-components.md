---
title: ASP.NET Core Razor コンポーネントを Razor Pages と MVC アプリに統合する
author: guardrex
description: Blazor アプリのコンポーネントと DOM 要素のデータバインディングのシナリオについて説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: de1a37ffd9456c956e3d84fcc69431ecb794513c
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453212"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a>ASP.NET Core Razor コンポーネントを Razor Pages と MVC アプリに統合する

[Luke Latham](https://github.com/guardrex)および[Daniel Roth](https://github.com/danroth27)

Razor コンポーネントは Razor Pages と MVC アプリに統合できます。 ページまたはビューが表示されると、コンポーネントを同時に prerendered することができます。

## <a name="prepare-the-app-to-use-components-in-pages-and-views"></a>ページおよびビューでコンポーネントを使用するようにアプリを準備する

既存の Razor Pages または MVC アプリでは、Razor コンポーネントをページとビューに統合できます。

1. アプリのレイアウトファイル ( *_Layout*) で、次のようにします。

   * 次の `<base>` タグを `<head>` 要素に追加します。

     ```html
     <base href="~/" />
     ```

     前の例の `href` 値 (*アプリのベースパス*) は、アプリがルート URL パス (`/`) に存在することを前提としています。 アプリがサブアプリケーションである場合は、<xref:host-and-deploy/blazor/index#app-base-path> に関する記事の「*アプリの基本パス*」セクションのガイダンスに従ってください。

     *_Layout*のファイルは、MVC アプリの Razor Pages アプリまたは*Views/shared*フォルダー内の*Pages/shared*フォルダーにあります。

   * Blazor スクリプトの `<script>` タグを、終了 `</body>` タグの直前に追加*し*ます。

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     フレームワークによって、 *blazor*スクリプトがアプリに追加されます。 アプリにスクリプトを手動で追加する必要はありません。

1. 次の内容を使用して、プロジェクトのルートフォルダーに *_Imports razor*ファイルを追加します (最後の名前空間、`MyAppNamespace`をアプリの名前空間に変更します)。

   ```razor
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

1. コンポーネントを任意のページまたはビューに統合します。 詳細については、「[ページまたはビューからのコンポーネントのレンダリング](#render-components-from-a-page-or-view)」を参照してください。

## <a name="use-routable-components-in-a-razor-pages-app"></a>Razor Pages アプリでルーティング可能なコンポーネントを使用する

*このセクションでは、ユーザー要求から直接ルーティング可能なコンポーネントを追加する方法について説明します。*

Razor Pages アプリでルーティング可能な Razor コンポーネントをサポートするには:

1. 「[ページとビューでコンポーネントを使用するようにアプリを準備する](#prepare-the-app-to-use-components-in-pages-and-views)」セクションのガイダンスに従ってください。

1. 次のコンテンツを使用して、プロジェクトルートに*アプリケーションの razor*ファイルを追加します。

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

1. ルーティング可能なコンポーネントをアプリに追加します。 次に例を示します。

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   名前空間の詳細については、「[コンポーネントの名前空間](#component-namespaces)」を参照してください。

## <a name="use-routable-components-in-an-mvc-app"></a>MVC アプリでルーティング可能なコンポーネントを使用する

*このセクションでは、ユーザー要求から直接ルーティング可能なコンポーネントを追加する方法について説明します。*

MVC アプリでルーティング可能な Razor コンポーネントをサポートするには、次のようにします。

1. 「[ページとビューでコンポーネントを使用するようにアプリを準備する](#prepare-the-app-to-use-components-in-pages-and-views)」セクションのガイダンスに従ってください。

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

1. *ページ*フォルダーを作成し、ルーティング可能なコンポーネントをアプリに追加します。 次に例を示します。

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   名前空間の詳細については、「[コンポーネントの名前空間](#component-namespaces)」を参照してください。

## <a name="component-namespaces"></a>コンポーネントの名前空間

カスタムフォルダーを使用してアプリのコンポーネントを保持する場合は、フォルダーを表す名前空間をページ/ビューまたは *_ViewImports*ファイルに追加します。 次の例では

* `MyAppNamespace` をアプリの名前空間に変更します。
* *コンポーネントという名前*のフォルダーを使用してコンポーネントを保持していない場合は、コンポーネントが存在するフォルダーに `Components` を変更します。

```cshtml
@using MyAppNamespace.Components
```

*_ViewImports*のファイルは、Razor Pages アプリの*Pages*フォルダーまたは MVC アプリの*Views*フォルダーにあります。

詳細については、<xref:blazor/components#import-components> を参照してください。

## <a name="render-components-from-a-page-or-view"></a>ページまたはビューからのコンポーネントのレンダリング

*このセクションでは、コンポーネントをユーザー要求から直接ルーティングできないページまたはビューに追加する方法について説明します。*

ページまたはビューからコンポーネントを表示するには、`Component` タグヘルパーを使用します。

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

パラメーターの型は、JSON シリアル化可能である必要があります。これは通常、型が既定のコンストラクターと設定可能なプロパティを持つ必要があることを意味します。 たとえば、`IncrementAmount` の型は `int`であるため、`IncrementAmount` の値を指定できます。これは、JSON シリアライザーによってサポートされるプリミティブ型です。

コンポーネントの `RenderMode` を構成します。

* ページに prerendered ます。
* は、ページに静的 HTML として表示されるか、ユーザーエージェントから Blazor アプリをブートストラップするために必要な情報が含まれている場合に表示されます。

| `RenderMode`        | Description |
| ------------------- | ----------- |
| `ServerPrerendered` | コンポーネントを静的 HTML にレンダリングし、Blazor サーバーアプリのマーカーを含めます。 ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。 |
| `Server`            | Blazor サーバーアプリのマーカーをレンダリングします。 コンポーネントからの出力は含まれていません。 ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。 |
| `Static`            | コンポーネントを静的 HTML にレンダリングします。 |

ページとビューはコンポーネントを使用できますが、逆の場合は真実ではありません。 コンポーネントでは、ビューおよびページ固有のシナリオ (部分ビューやセクションなど) を使用できません。 コンポーネントの部分ビューからロジックを使用するには、部分ビューのロジックをコンポーネントにします。

静的な HTML ページからのサーバーコンポーネントのレンダリングはサポートされていません。

コンポーネントのレンダリング方法、コンポーネントの状態、および `Component` タグヘルパーの詳細については、次の記事を参照してください。

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
