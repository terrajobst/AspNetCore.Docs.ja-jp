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
# <a name="aspnet-core-opno-locblazor-templates"></a><span data-ttu-id="f0c9e-103">ASP.NET Core Blazor テンプレート</span><span class="sxs-lookup"><span data-stu-id="f0c9e-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="f0c9e-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f0c9e-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="f0c9e-105">Blazor フレームワークには、Blazor ホスティング モデルのそれぞれに対応するアプリを開発するためのテンプレートが用意されています。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* Blazor<span data-ttu-id="f0c9e-106"> WebAssembly (`blazorwasm`)</span><span class="sxs-lookup"><span data-stu-id="f0c9e-106"> WebAssembly (`blazorwasm`)</span></span>
* Blazor<span data-ttu-id="f0c9e-107"> Server (`blazorserver`)</span><span class="sxs-lookup"><span data-stu-id="f0c9e-107"> Server (`blazorserver`)</span></span>

<span data-ttu-id="f0c9e-108">Blazor のホスティング モデルの詳細については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="f0c9e-109">テンプレートから Blazor アプリを作成する手順の詳細については、「<xref:blazor/get-started>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-109">For step-by-step instructions on creating a Blazor app from a template, see <xref:blazor/get-started>.</span></span>

## <a name="opno-locblazor-project-structure"></a>Blazor<span data-ttu-id="f0c9e-110"> プロジェクトの構造</span><span class="sxs-lookup"><span data-stu-id="f0c9e-110"> project structure</span></span>

<span data-ttu-id="f0c9e-111">次のファイルとフォルダーは、Blazor テンプレートから生成された Blazor アプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-111">The following files and folders make up a Blazor app generated from a Blazor template:</span></span>

* <span data-ttu-id="f0c9e-112">*Program.cs* &ndash; 以下を設定するアプリのエントリポイント。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-112">*Program.cs* &ndash; The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="f0c9e-113">ASP.NET Core [ホスト](xref:fundamentals/host/generic-host) (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="f0c9e-113">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="f0c9e-114">WebAssembly ホスト (Blazor WebAssembly) &ndash; このファイルのコードは、Blazor WebAssembly テンプレート (`blazorwasm`) から作成されたアプリに固有です。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-114">WebAssembly host (Blazor WebAssembly) &ndash; The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="f0c9e-115">`App` コンポーネント (アプリのルート コンポーネント) は、`Add` メソッドの `app` DOM 要素として指定されます。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-115">The `App` component, which is the root component of the app, is specified as the `app` DOM element to the `Add` method.</span></span>
    * <span data-ttu-id="f0c9e-116">サービスは、ホスト ビルダーの `ConfigureServices` メソッド (`builder.Services.AddSingleton<IMyDependency, MyDependency>();`など) を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-116">Services can be configured with the `ConfigureServices` method on the host builder (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).</span></span>
    * <span data-ttu-id="f0c9e-117">構成は、ホスト ビルダー (`builder.Configuration`) を使用して指定できます。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-117">Configuration can be supplied via the host builder (`builder.Configuration`).</span></span>

* <span data-ttu-id="f0c9e-118">*Startup.cs* (Blazor Server) &ndash; アプリのスタートアップ ロジックを含みます。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-118">*Startup.cs* (Blazor Server) &ndash; Contains the app's startup logic.</span></span> <span data-ttu-id="f0c9e-119">`Startup` クラスには、次の 2 つのメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-119">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="f0c9e-120">`ConfigureServices` &ndash; アプリの [ 依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) サービスを構成します。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-120">`ConfigureServices` &ndash; Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="f0c9e-121">Blazor Server アプリでは、<xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*> を呼び出すことによってサービスが追加されます。`WeatherForecastService` は、サンプルの `FetchData` コンポーネントが使用するためにサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-121">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="f0c9e-122">`Configure` &ndash; アプリの要求処理パイプラインを構成します。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-122">`Configure` &ndash; Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="f0c9e-123"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*> は、ブラウザーとのリアルタイム接続用のエンドポイントを設定するために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-123"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="f0c9e-124">この接続は [SignalR](xref:signalr/introduction) で作成されます。これは、アプリにリアルタイム Web 機能を追加するためのフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-124">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="f0c9e-125">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) は、アプリのルート ページ (*Pages/_Host.cshtml*) を設定し、ナビゲーションを有効にするために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-125">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (*Pages/_Host.cshtml*) and enable navigation.</span></span>

* <span data-ttu-id="f0c9e-126">*wwwroot/index.html* (Blazor WebAssembly) &ndash; HTML ページとして実装されているアプリのルート ページ。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-126">*wwwroot/index.html* (Blazor WebAssembly) &ndash; The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="f0c9e-127">アプリのいずれかのページが最初に要求されると、このページが表示されて応答として返されます。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-127">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="f0c9e-128">このページは、ルート `App` コンポーネントを表示する場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-128">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="f0c9e-129">`App` コンポーネント (*App.razor*) は、`Startup.Configure` の `AddComponent` メソッドに `app` DOM 要素として指定されます。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-129">The `App` component (*App.razor*) is specified as the `app` DOM element to the `AddComponent` method in `Startup.Configure`.</span></span>
  * <span data-ttu-id="f0c9e-130">`_framework/blazor.webassembly.js` JavaScript ファイルが読み込まれます。これは以下のことを行います。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-130">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="f0c9e-131">.NET ランタイム、アプリ、およびアプリの依存関係のダウンロード。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-131">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="f0c9e-132">アプリを実行するランタイムの初期化。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-132">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="f0c9e-133">*Pages/_Host.cshtml* (Blazor Server) &ndash; Razor ページとして実装されるアプリのルート ページ。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-133">*Pages/_Host.cshtml* (Blazor Server) &ndash; The root page of the app implemented as a Razor Page:</span></span>
  * <span data-ttu-id="f0c9e-134">アプリのいずれかのページが最初に要求されると、このページが表示されて応答として返されます。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-134">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="f0c9e-135">ブラウザーとサーバーの間のリアルタイム SignalR 接続を設定する `_framework/blazor.server.js` JavaScript ファイルが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-135">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
  * <span data-ttu-id="f0c9e-136">このホスト ページは、ルート `App` コンポーネント (*App.razor*) を表示する場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-136">The Host page specifies where the root `App` component (*App.razor*) is rendered.</span></span>

* <span data-ttu-id="f0c9e-137">*App.razor* &ndash; <xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントを使用してクライアント側のルーティングを設定するアプリのルート コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-137">*App.razor* &ndash; The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="f0c9e-138">`Router` コンポーネントは、ブラウザーのナビゲーションをインターセプトし、要求されたアドレスに一致するページをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-138">The `Router` component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="f0c9e-139">*Pages* フォルダー &ndash; Blazor アプリを構成するルーティング可能なコンポーネント/ページ ( *.razor*) を含みます。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-139">*Pages* folder &ndash; Contains the routable components/pages (*.razor*) that make up the Blazor app.</span></span> <span data-ttu-id="f0c9e-140">各ページのルートは、[`@page`](xref:mvc/views/razor#page) ディレクティブを使用して指定します。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-140">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="f0c9e-141">テンプレートには、以下のコンポーネントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-141">The template includes the following components:</span></span>
  * <span data-ttu-id="f0c9e-142">`Index` (*Index.razor*) &ndash; ホーム ページを実装します。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-142">`Index` (*Index.razor*) &ndash; Implements the Home page.</span></span>
  * <span data-ttu-id="f0c9e-143">`Counter` (*Counter.razor*) &ndash; カウンター ページを実装します。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-143">`Counter` (*Counter.razor*) &ndash; Implements the Counter page.</span></span>
  * <span data-ttu-id="f0c9e-144">`Error` (*Error.razor*、Blazor Server アプリのみ) &ndash; アプリで処理されない例外が発生したときに表示されます。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-144">`Error` (*Error.razor*, Blazor Server app only) &ndash; Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="f0c9e-145">`FetchData` (*FetchData.razor*) &ndash; フェッチ データ ページを実装します。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-145">`FetchData` (*FetchData.razor*) &ndash; Implements the Fetch data page.</span></span>

* <span data-ttu-id="f0c9e-146">*Shared* フォルダー &ndash; アプリで使用する他の UI コンポーネント ( *.razor*) を含みます。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-146">*Shared* folder &ndash; Contains other UI components (*.razor*) used by the app:</span></span>
  * <span data-ttu-id="f0c9e-147">`MainLayout` (*MainLayout.razor*) &ndash; アプリのレイアウト コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-147">`MainLayout` (*MainLayout.razor*) &ndash; The app's layout component.</span></span>
  * <span data-ttu-id="f0c9e-148">`NavMenu` (*NavMenu.razor*) &ndash; サイドバー ナビゲーションを実装します。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-148">`NavMenu` (*NavMenu.razor*) &ndash; Implements sidebar navigation.</span></span> <span data-ttu-id="f0c9e-149">ナビゲーション リンクを他の Razor コンポーネントに表示する [NavLink コンポーネント](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) を含みます。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-149">Includes the [NavLink component](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="f0c9e-150">`NavLink` コンポーネントは、そのコンポーネントが読み込まれると、自動的に選択された状態を示します。これは、ユーザーが現在どのコンポーネントが表示されているかを理解するために役立ちます。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-150">The `NavLink` component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="f0c9e-151">*_Imports razor* &ndash; 名前空間の [`@using`](xref:mvc/views/razor#using) ディレクティブなど、アプリのコンポーネント ( *.razor*) に含める一般的な Razor ディレクティブを含みます。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-151">*_Imports.razor* &ndash; Includes common Razor directives to include in the app's components (*.razor*), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="f0c9e-152">*Data* フォルダー (Blazor Server) &ndash; には、アプリの `FetchData` コンポーネントに気象データの例を提供する、`WeatherForecast` クラスと `WeatherForecastService` の実装を含みます。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-152">*Data* folder (Blazor Server) &ndash; Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="f0c9e-153">*wwwroot* &ndash; アプリのパブリックな静的アセットを含むアプリの [Web ルート](xref:fundamentals/index#web-root) フォルダー。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-153">*wwwroot* &ndash; The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="f0c9e-154">*appsettings.json* (Blazor Server) &ndash; アプリの構成設定。</span><span class="sxs-lookup"><span data-stu-id="f0c9e-154">*appsettings.json* (Blazor Server) &ndash; Configuration settings for the app.</span></span>
