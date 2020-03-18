---
title: ASP.NET Core Blazor テンプレート
author: guardrex
description: ASP.NET Core Blazor アプリ テンプレートと Blazor プロジェクトの構造について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templates
ms.openlocfilehash: acfa4b8a42cbd310c6fc6dc973573578e94ef999
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78649448"
---
# <a name="aspnet-core-opno-locblazor-templates"></a>ASP.NET Core Blazor テンプレート

作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor フレームワークには、Blazor ホスティング モデルのそれぞれに対応するアプリを開発するためのテンプレートが用意されています。

* Blazor WebAssembly (`blazorwasm`)
* Blazor Server (`blazorserver`)

Blazor のホスティング モデルの詳細については、「<xref:blazor/hosting-models>」を参照してください。

テンプレートから Blazor アプリを作成する手順の詳細については、「<xref:blazor/get-started>」を参照してください。

## <a name="opno-locblazor-project-structure"></a>Blazor プロジェクトの構造

次のファイルとフォルダーは、Blazor テンプレートから生成された Blazor アプリを構成します。

* *Program.cs* &ndash; 以下を設定するアプリのエントリポイント。

  * ASP.NET Core [ホスト](xref:fundamentals/host/generic-host) (Blazor Server)
  * WebAssembly ホスト (Blazor WebAssembly) &ndash; このファイルのコードは、Blazor WebAssembly テンプレート (`blazorwasm`) から作成されたアプリに固有です。
    * `App` コンポーネント (アプリのルート コンポーネント) は、`Add` メソッドの `app` DOM 要素として指定されます。
    * サービスは、ホスト ビルダーの `ConfigureServices` メソッド (`builder.Services.AddSingleton<IMyDependency, MyDependency>();`など) を使用して構成できます。
    * 構成は、ホスト ビルダー (`builder.Configuration`) を使用して指定できます。

* *Startup.cs* (Blazor Server) &ndash; アプリのスタートアップ ロジックを含みます。 `Startup` クラスには、次の 2 つのメソッドがあります。

  * `ConfigureServices` &ndash; アプリの [ 依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) サービスを構成します。 Blazor Server アプリでは、<xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*> を呼び出すことによってサービスが追加されます。`WeatherForecastService` は、サンプルの `FetchData` コンポーネントが使用するためにサービス コンテナーに追加されます。
  * `Configure` &ndash; アプリの要求処理パイプラインを構成します。
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*> は、ブラウザーとのリアルタイム接続用のエンドポイントを設定するために呼び出されます。 この接続は [SignalR](xref:signalr/introduction) で作成されます。これは、アプリにリアルタイム Web 機能を追加するためのフレームワークです。
    * [MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) は、アプリのルート ページ (*Pages/_Host.cshtml*) を設定し、ナビゲーションを有効にするために呼び出されます。

* *wwwroot/index.html* (Blazor WebAssembly) &ndash; HTML ページとして実装されているアプリのルート ページ。
  * アプリのいずれかのページが最初に要求されると、このページが表示されて応答として返されます。
  * このページは、ルート `App` コンポーネントを表示する場所を指定します。 `App` コンポーネント (*App.razor*) は、`Startup.Configure` の `AddComponent` メソッドに `app` DOM 要素として指定されます。
  * `_framework/blazor.webassembly.js` JavaScript ファイルが読み込まれます。これは以下のことを行います。
    * .NET ランタイム、アプリ、およびアプリの依存関係のダウンロード。
    * アプリを実行するランタイムの初期化。

* *Pages/_Host.cshtml* (Blazor Server) &ndash; Razor ページとして実装されるアプリのルート ページ。
  * アプリのいずれかのページが最初に要求されると、このページが表示されて応答として返されます。
  * ブラウザーとサーバーの間のリアルタイム SignalR 接続を設定する `_framework/blazor.server.js` JavaScript ファイルが読み込まれます。
  * このホスト ページは、ルート `App` コンポーネント (*App.razor*) を表示する場所を指定します。

* *App.razor* &ndash; <xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントを使用してクライアント側のルーティングを設定するアプリのルート コンポーネント。 `Router` コンポーネントは、ブラウザーのナビゲーションをインターセプトし、要求されたアドレスに一致するページをレンダリングします。

* *Pages* フォルダー &ndash; Blazor アプリを構成するルーティング可能なコンポーネント/ページ ( *.razor*) を含みます。 各ページのルートは、[`@page`](xref:mvc/views/razor#page) ディレクティブを使用して指定します。 テンプレートには、以下のコンポーネントが含まれています。
  * `Index` (*Index.razor*) &ndash; ホーム ページを実装します。
  * `Counter` (*Counter.razor*) &ndash; カウンター ページを実装します。
  * `Error` (*Error.razor*、Blazor Server アプリのみ) &ndash; アプリで処理されない例外が発生したときに表示されます。
  * `FetchData` (*FetchData.razor*) &ndash; フェッチ データ ページを実装します。

* *Shared* フォルダー &ndash; アプリで使用する他の UI コンポーネント ( *.razor*) を含みます。
  * `MainLayout` (*MainLayout.razor*) &ndash; アプリのレイアウト コンポーネント。
  * `NavMenu` (*NavMenu.razor*) &ndash; サイドバー ナビゲーションを実装します。 ナビゲーション リンクを他の Razor コンポーネントに表示する [NavLink コンポーネント](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) を含みます。 `NavLink` コンポーネントは、そのコンポーネントが読み込まれると、自動的に選択された状態を示します。これは、ユーザーが現在どのコンポーネントが表示されているかを理解するために役立ちます。

* *_Imports razor* &ndash; 名前空間の [`@using`](xref:mvc/views/razor#using) ディレクティブなど、アプリのコンポーネント ( *.razor*) に含める一般的な Razor ディレクティブを含みます。

* *Data* フォルダー (Blazor Server) &ndash; には、アプリの `FetchData` コンポーネントに気象データの例を提供する、`WeatherForecast` クラスと `WeatherForecastService` の実装を含みます。

* *wwwroot* &ndash; アプリのパブリックな静的アセットを含むアプリの [Web ルート](xref:fundamentals/index#web-root) フォルダー。

* *appsettings.json* (Blazor Server) &ndash; アプリの構成設定。
