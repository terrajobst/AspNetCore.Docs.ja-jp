---
title: ASP.NET Core Razor コンポーネントを Razor Pages と MVC アプリに統合する
author: guardrex
description: Blazor アプリのコンポーネントと DOM 要素のデータ バインディングのシナリオについて説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: cf6056e0985d5433bddecac8dd183ca3f4c2af5b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218935"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a>ASP.NET Core Razor コンポーネントを Razor Pages と MVC アプリに統合する

作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)

Razor コンポーネントは、Razor Pages と MVC アプリに統合できます。 ページまたはビューがレンダリングされるときには、コンポーネントを同時に事前レンダリングすることができます。

## <a name="prepare-the-app-to-use-components-in-pages-and-views"></a>ページとビューでコンポーネントを使用するためにアプリを準備する

既存の Razor Pages や MVC アプリでは、Razor コンポーネントをページとビューに統合できます。

1. アプリのレイアウト ファイル ( *_Layout.cshtml*) で次のことを行います。

   * 次の `<base>` タグを `<head>` 要素に追加します。

     ```html
     <base href="~/" />
     ```

     前の例の `href` 値 (*アプリ ベースのパス*) は、アプリがルート URL パス (`/`) に置かれていることを前提としています。 アプリがサブアプリケーションになっている場合は、記事  *の「* アプリのベース パス<xref:host-and-deploy/blazor/index#app-base-path>」セクションのガイダンスに従ってください。

     *_Layout.cshtml* ファイルは、Razor Pages アプリの *Pages/Shared* フォルダーまたは MVC アプリの *Views/Shared* フォルダーにあります。

   * `<script>`blazor.server.js*スクリプトの* タグを、終了 `</body>` タグの直前に追加します。

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     フレームワークによって *blazor.server.js* スクリプトがアプリに追加されます。 手動でアプリにスクリプトを追加する必要はありません。

1. 次の内容を含む *_Imports.razor* ファイルをプロジェクトのルート フォルダーに追加します (最後の名前空間 `MyAppNamespace` をアプリの名前空間に変更します)。

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

1. `Startup.ConfigureServices` で、Blazor Server サービスを登録します。

   ```csharp
   services.AddServerSideBlazor();
   ```

1. `Startup.Configure` で、Blazor Hub エンドポイントを `app.UseEndpoints` に追加します。

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. コンポーネントを任意のページまたはビューに統合します。 詳細については、「[ページまたはビューからコンポーネントをレンダリングする](#render-components-from-a-page-or-view)」セクションを参照してください。

## <a name="use-routable-components-in-a-razor-pages-app"></a>Razor Pages アプリでルーティング可能なコンポーネントを使用する

*ここは、ユーザー要求から直接ルーティング可能なコンポーネントを追加することに関係のあるセクションです。*

Razor Pages アプリでルーティング可能な Razor コンポーネントをサポートするには、次のようにします。

1. 「[ページとビューでコンポーネントを使用するためにアプリを準備する](#prepare-the-app-to-use-components-in-pages-and-views)」セクションのガイダンスに従ってください。

1. 次の内容の *App.razor* ファイルをプロジェクト ルートに追加します。

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

1. 次の内容の *_Host.cshtml* ファイルを *Pages* フォルダーに追加します。

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   コンポーネントは、そのレイアウトで共有される *_Layout.cshtml* ファイルを使用します。

1. *_Host.cshtml* ページの優先度が低いルートを、`Startup.Configure` 内のエンドポイント構成に追加します。

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. ルーティング可能なコンポーネントをアプリに追加します。 (例:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   名前空間の詳細については、「[コンポーネントの名前空間](#component-namespaces)」セクションを参照してください。

## <a name="use-routable-components-in-an-mvc-app"></a>MVC アプリでルーティング可能なコンポーネントを使用する

*ここは、ユーザー要求から直接ルーティング可能なコンポーネントを追加することに関係のあるセクションです。*

MVC アプリでルーティング可能な Razor コンポーネントをサポートするには、次のようにします。

1. 「[ページとビューでコンポーネントを使用するためにアプリを準備する](#prepare-the-app-to-use-components-in-pages-and-views)」セクションのガイダンスに従ってください。

1. 次の内容の *App.razor* ファイルを、プロジェクトのルートに追加します。

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

1. 次の内容の *_Host.cshtml* ファイルを *Views/Home* フォルダーに追加します。

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   コンポーネントは、そのレイアウトで共有される *_Layout.cshtml* ファイルを使用します。

1. Home コントローラーにアクションを追加します。

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. *内のエンドポイント構成に*_Host.cshtml`Startup.Configure` ビューを返すコントローラー アクションのために、優先度が低いルートを追加します。

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. *Pages* フォルダーを作成し、アプリにルーティング可能なコンポーネントを追加します。 (例:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   名前空間の詳細については、「[コンポーネントの名前空間](#component-namespaces)」セクションを参照してください。

## <a name="component-namespaces"></a>コンポーネントの名前空間

カスタム フォルダーを使用してアプリのコンポーネントを保持する場合は、フォルダーを表す名前空間を、ページまたはビューのいずれかに追加するか、 *_ViewImports.cshtml* ファイルに追加します。 次に例を示します。

* `MyAppNamespace` をアプリの名前空間に変更します。
* コンポーネントを保持するために *Components* という名前のフォルダーを使用していない場合は、`Components` を、コンポーネントが置かれているフォルダーに変更します。

```cshtml
@using MyAppNamespace.Components
```

*_ViewImports.cshtml* ファイルは、Razor Pages アプリの *Pages* フォルダーまたは MVC アプリの *Views* フォルダーにあります。

詳細については、「<xref:blazor/components#import-components>」を参照してください。

## <a name="render-components-from-a-page-or-view"></a>ページまたはビューからコンポーネントをレンダリングする

*これは、コンポーネントをユーザー要求から直接ルーティングできないページまたはビューにコンポーネントを追加することに関係するセクションです。*

ページまたはビューからコンポーネントをレンダリングするには、[コンポーネント タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)を使用します。

コンポーネントがどのようにレンダリングされるか、コンポーネントの状態、および `Component` タグ ヘルパーの詳細については、以下の記事を参照してください。

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
* <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>
