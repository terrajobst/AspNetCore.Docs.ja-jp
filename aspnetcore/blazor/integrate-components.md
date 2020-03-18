---
title: ASP.NET Core Razor コンポーネントを Razor Pages と MVC アプリに統合する
author: guardrex
description: Blazor アプリのコンポーネントと DOM 要素のデータ バインディングのシナリオについて説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: de1a37ffd9456c956e3d84fcc69431ecb794513c
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78649082"
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

     前の例の `href` 値 (*アプリ ベースのパス*) は、アプリがルート URL パス (`/`) に置かれていることを前提としています。 アプリがサブアプリケーションになっている場合は、記事 <xref:host-and-deploy/blazor/index#app-base-path> の「*アプリのベース パス*」セクションのガイダンスに従ってください。

     *_Layout.cshtml* ファイルは、Razor Pages アプリの *Pages/Shared* フォルダーまたは MVC アプリの *Views/Shared* フォルダーにあります。

   * *blazor.server.js* スクリプトの `<script>` タグを、終了 `</body>` タグの直前に追加します。

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

1. ルーティング可能なコンポーネントをアプリに追加します。 次に例を示します。

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

1. `Startup.Configure` 内のエンドポイント構成に *_Host.cshtml* ビューを返すコントローラー アクションのために、優先度が低いルートを追加します。

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. *Pages* フォルダーを作成し、アプリにルーティング可能なコンポーネントを追加します。 次に例を示します。

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

ページまたはビューからコンポーネントをレンダリングするには、`Component` タグ ヘルパーを使用します。

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

パラメーターは、JSON のシリアル化可能な型である必要があります。これは通常、その型に既定のコンストラクターと設定できるプロパティがある必要があることを意味します。 たとえば、`IncrementAmount` の型は `int`であるため、`IncrementAmount` の値を指定できます。これは、JSON シリアライザーによってサポートされているプリミティブ型です。

`RenderMode` によって、コンポーネントに対して以下の構成が行われます。

* ページに事前レンダリングするかどうか。
* ページに静的 HTML としてレンダリングするかどうか。または、ユーザー エージェントから Blazor アプリをブートストラップするために必要な情報が含まれているかどうか。

| `RenderMode`        | 説明 |
| ------------------- | ----------- |
| `ServerPrerendered` | コンポーネントを静的 HTML にレンダリングし、Blazor Server アプリのマーカーを含めます。 このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。 |
| `Server`            | Blazor Server アプリのマーカーをレンダリングします。 コンポーネントからの出力は含められません。 このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。 |
| `Static`            | コンポーネントを静的 HTML にレンダリングします。 |

ページとビューはコンポーネントを使用できますが、逆のことはできません。 コンポーネントでは、ビュー固有のシナリオやページ固有のシナリオ (部分ビューや部分セクションなど) を使用できません。 コンポーネントの部分ビューにあるロジックを使用するには、部分ビューのロジックを要素としてコンポーネントに取り入れます。

静的 HTML ページからのサーバー コンポーネントのレンダリングは、サポートされていません。

コンポーネントがどのようにレンダリングされるか、コンポーネントの状態、および `Component` タグ ヘルパーの詳細については、以下の記事を参照してください。

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
