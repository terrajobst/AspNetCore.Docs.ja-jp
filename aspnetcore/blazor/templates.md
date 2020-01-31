---
title: ASP.NET Core Blazor テンプレート
author: guardrex
description: ASP.NET Core Blazor アプリテンプレートと Blazor プロジェクトの構造について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templates
ms.openlocfilehash: acfa4b8a42cbd310c6fc6dc973573578e94ef999
ms.sourcegitcommit: c81ef12a1b6e6ac838e5e07042717cf492e6635b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2020
ms.locfileid: "76885508"
---
# <a name="aspnet-core-opno-locblazor-templates"></a>ASP.NET Core Blazor テンプレート

作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor framework には、Blazor ホスティングモデルのそれぞれに対応するアプリを開発するためのテンプレートが用意されています。

* Blazor Webas(`blazorwasm`)
* Blazor サーバー (`blazorserver`)

Blazorのホスティングモデルの詳細については、「<xref:blazor/hosting-models>」を参照してください。

テンプレートから Blazor アプリを作成する手順の詳細については、「<xref:blazor/get-started>」を参照してください。

## <a name="opno-locblazor-project-structure"></a>Blazor プロジェクトの構造

次のファイルとフォルダーは、Blazor テンプレートから生成された Blazor アプリを構成します。

* *Program.cs*は、を設定するアプリのエントリポイント &ndash; ます。

  * ASP.NET Core[ホスト](xref:fundamentals/host/generic-host)(Blazor サーバー)
  * このファイルのコードは、Blazor webassembly のテンプレート (`blazorwasm`) から作成されたアプリに固有のものです。このようなホスト (Blazor webas) です。
    * `App` コンポーネント (アプリのルートコンポーネント) は、`Add` メソッドに `app` DOM 要素として指定されます。
    * サービスは、ホストビルダーの `ConfigureServices` 方法 (`builder.Services.AddSingleton<IMyDependency, MyDependency>();`など) を使用して構成できます。
    * 構成は、ホストビルダー (`builder.Configuration`) を使用して指定できます。

* *Startup.cs* (Blazor Server) &ndash; には、アプリのスタートアップロジックが含まれています。 `Startup` クラスは、次の2つのメソッドを定義します。

  * `ConfigureServices` &ndash; は、アプリの[依存関係挿入 (DI)](xref:fundamentals/dependency-injection)サービスを構成します。 Blazor サーバーアプリでは、<xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>を呼び出すことによってサービスが追加されます。 `WeatherForecastService` は、例の `FetchData` コンポーネントで使用するためにサービスコンテナーに追加されます。
  * `Configure` &ndash; は、アプリの要求処理パイプラインを構成します。
    * ブラウザーを使用してリアルタイム接続を行うためにエンドポイントを設定するために <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*> が呼び出されます。 この接続は[SignalR](xref:signalr/introduction)で作成されます。これは、アプリにリアルタイムの web 機能を追加するためのフレームワークです。
    * [Mapfallbacktopage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*)は、アプリのルートページ (*Pages/_Host*) を設定し、ナビゲーションを有効にするために呼び出されます。

* *wwwroot/index.html* (Blazor WebAssembly は、アプリのルートページ &ndash; html ページとして実装されています。
  * アプリのいずれかのページが最初に要求されたときに、このページが表示され、応答で返されます。
  * このページでは、ルート `App` コンポーネントを表示する場所を指定します。 `App` コンポーネント (*app.xaml*) は、`Startup.Configure`の `AddComponent` メソッドに `app` DOM 要素として指定されます。
  * `_framework/blazor.webassembly.js` JavaScript ファイルが読み込まれます。次のようになります。
    * .NET ランタイム、アプリ、およびアプリの依存関係をダウンロードします。
    * アプリを実行するランタイムを初期化します。

* *Pages/_Host cshtml* (Blazor Server) &ndash; Razor ページとして実装されているアプリのルートページをします。
  * アプリのいずれかのページが最初に要求されたときに、このページが表示され、応答で返されます。
  * `_framework/blazor.server.js` JavaScript ファイルが読み込まれ、ブラウザーとサーバーの間のリアルタイム SignalR 接続が設定されます。
  * [ホスト] ページでは、ルート `App` コンポーネント (*app.xaml*) を表示する場所を指定します。

* <xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントを使用してクライアント側のルーティングを設定するアプリのルートコンポーネント &ndash; ます *。* `Router` コンポーネントは、ブラウザーナビゲーションをインターセプトし、要求されたアドレスと一致するページをレンダリングします。

* [*ページ*] フォルダー &ndash; Blazor アプリを構成するルーティング可能なコンポーネント/ページ (*Razor*) が含まれています。 各ページのルートは、 [`@page`](xref:mvc/views/razor#page)ディレクティブを使用して指定します。 このテンプレートには、次のコンポーネントが含まれています。
  * `Index` (*Index*) &ndash; はホームページを実装します。
  * `Counter` (*counter*) &ndash; カウンターページを実装します。
  * `Error` (*エラー razor*、Blazor サーバーアプリのみ) &ndash; アプリでハンドルされない例外が発生したときに表示されます。
  * `FetchData` (*fetchdata. razor*) &ndash; は、Fetch data ページを実装します。

* *共有*フォルダー &ndash; には、アプリで使用される他の UI コンポーネント (*Razor*) が含まれています。
  * アプリのレイアウトコンポーネント &ndash; `MainLayout` (*mainlayout*)。
  * `NavMenu` (*ナビゲーションメニューの razor*) &ndash; サイドバーナビゲーションを実装します。 には、ナビゲーションリンクを他の Razor コンポーネントにレンダリングするナビゲーションリンク[コンポーネント](xref:blazor/routing#navlink-component)(<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) が含まれています。 コンポーネントが読み込まれると、`NavLink` コンポーネントによって選択された状態が自動的に示されます。これは、現在表示されているコンポーネントをユーザーが理解するのに役立ちます。

* *_Imports razor* &ndash; には、名前空間の[`@using`](xref:mvc/views/razor#using)ディレクティブなど、アプリのコンポーネント (*razor*) に含める一般的な razor ディレクティブが含まれています。

* *データ*フォルダー (Blazor サーバー) &ndash; には、`WeatherForecast` クラスと、アプリの `FetchData` コンポーネントに気象データの例を提供する `WeatherForecastService` の実装が含まれています。

* *wwwroot* &ndash;、アプリのパブリックな静的アセットを含むアプリの[Web ルート](xref:fundamentals/index#web-root)フォルダーです。

* *appsettings* (Blazor Server) アプリの構成設定 &ndash; ます。
