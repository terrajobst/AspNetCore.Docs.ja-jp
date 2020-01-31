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
# <a name="aspnet-core-opno-locblazor-templates"></a><span data-ttu-id="8709f-103">ASP.NET Core Blazor テンプレート</span><span class="sxs-lookup"><span data-stu-id="8709f-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="8709f-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8709f-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="8709f-105">Blazor framework には、Blazor ホスティングモデルのそれぞれに対応するアプリを開発するためのテンプレートが用意されています。</span><span class="sxs-lookup"><span data-stu-id="8709f-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* Blazor<span data-ttu-id="8709f-106"> Webas(`blazorwasm`)</span><span class="sxs-lookup"><span data-stu-id="8709f-106"> WebAssembly (`blazorwasm`)</span></span>
* Blazor<span data-ttu-id="8709f-107"> サーバー (`blazorserver`)</span><span class="sxs-lookup"><span data-stu-id="8709f-107"> Server (`blazorserver`)</span></span>

<span data-ttu-id="8709f-108">Blazorのホスティングモデルの詳細については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8709f-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="8709f-109">テンプレートから Blazor アプリを作成する手順の詳細については、「<xref:blazor/get-started>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8709f-109">For step-by-step instructions on creating a Blazor app from a template, see <xref:blazor/get-started>.</span></span>

## <a name="opno-locblazor-project-structure"></a>Blazor<span data-ttu-id="8709f-110"> プロジェクトの構造</span><span class="sxs-lookup"><span data-stu-id="8709f-110"> project structure</span></span>

<span data-ttu-id="8709f-111">次のファイルとフォルダーは、Blazor テンプレートから生成された Blazor アプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="8709f-111">The following files and folders make up a Blazor app generated from a Blazor template:</span></span>

* <span data-ttu-id="8709f-112">*Program.cs*は、を設定するアプリのエントリポイント &ndash; ます。</span><span class="sxs-lookup"><span data-stu-id="8709f-112">*Program.cs* &ndash; The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="8709f-113">ASP.NET Core[ホスト](xref:fundamentals/host/generic-host)(Blazor サーバー)</span><span class="sxs-lookup"><span data-stu-id="8709f-113">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="8709f-114">このファイルのコードは、Blazor webassembly のテンプレート (`blazorwasm`) から作成されたアプリに固有のものです。このようなホスト (Blazor webas) です。</span><span class="sxs-lookup"><span data-stu-id="8709f-114">WebAssembly host (Blazor WebAssembly) &ndash; The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="8709f-115">`App` コンポーネント (アプリのルートコンポーネント) は、`Add` メソッドに `app` DOM 要素として指定されます。</span><span class="sxs-lookup"><span data-stu-id="8709f-115">The `App` component, which is the root component of the app, is specified as the `app` DOM element to the `Add` method.</span></span>
    * <span data-ttu-id="8709f-116">サービスは、ホストビルダーの `ConfigureServices` 方法 (`builder.Services.AddSingleton<IMyDependency, MyDependency>();`など) を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="8709f-116">Services can be configured with the `ConfigureServices` method on the host builder (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).</span></span>
    * <span data-ttu-id="8709f-117">構成は、ホストビルダー (`builder.Configuration`) を使用して指定できます。</span><span class="sxs-lookup"><span data-stu-id="8709f-117">Configuration can be supplied via the host builder (`builder.Configuration`).</span></span>

* <span data-ttu-id="8709f-118">*Startup.cs* (Blazor Server) &ndash; には、アプリのスタートアップロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8709f-118">*Startup.cs* (Blazor Server) &ndash; Contains the app's startup logic.</span></span> <span data-ttu-id="8709f-119">`Startup` クラスは、次の2つのメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="8709f-119">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="8709f-120">`ConfigureServices` &ndash; は、アプリの[依存関係挿入 (DI)](xref:fundamentals/dependency-injection)サービスを構成します。</span><span class="sxs-lookup"><span data-stu-id="8709f-120">`ConfigureServices` &ndash; Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="8709f-121">Blazor サーバーアプリでは、<xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>を呼び出すことによってサービスが追加されます。 `WeatherForecastService` は、例の `FetchData` コンポーネントで使用するためにサービスコンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="8709f-121">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="8709f-122">`Configure` &ndash; は、アプリの要求処理パイプラインを構成します。</span><span class="sxs-lookup"><span data-stu-id="8709f-122">`Configure` &ndash; Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="8709f-123">ブラウザーを使用してリアルタイム接続を行うためにエンドポイントを設定するために <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="8709f-123"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="8709f-124">この接続は[SignalR](xref:signalr/introduction)で作成されます。これは、アプリにリアルタイムの web 機能を追加するためのフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="8709f-124">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="8709f-125">[Mapfallbacktopage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*)は、アプリのルートページ (*Pages/_Host*) を設定し、ナビゲーションを有効にするために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="8709f-125">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (*Pages/_Host.cshtml*) and enable navigation.</span></span>

* <span data-ttu-id="8709f-126">*wwwroot/index.html* (Blazor WebAssembly は、アプリのルートページ &ndash; html ページとして実装されています。</span><span class="sxs-lookup"><span data-stu-id="8709f-126">*wwwroot/index.html* (Blazor WebAssembly) &ndash; The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="8709f-127">アプリのいずれかのページが最初に要求されたときに、このページが表示され、応答で返されます。</span><span class="sxs-lookup"><span data-stu-id="8709f-127">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="8709f-128">このページでは、ルート `App` コンポーネントを表示する場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="8709f-128">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="8709f-129">`App` コンポーネント (*app.xaml*) は、`Startup.Configure`の `AddComponent` メソッドに `app` DOM 要素として指定されます。</span><span class="sxs-lookup"><span data-stu-id="8709f-129">The `App` component (*App.razor*) is specified as the `app` DOM element to the `AddComponent` method in `Startup.Configure`.</span></span>
  * <span data-ttu-id="8709f-130">`_framework/blazor.webassembly.js` JavaScript ファイルが読み込まれます。次のようになります。</span><span class="sxs-lookup"><span data-stu-id="8709f-130">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="8709f-131">.NET ランタイム、アプリ、およびアプリの依存関係をダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="8709f-131">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="8709f-132">アプリを実行するランタイムを初期化します。</span><span class="sxs-lookup"><span data-stu-id="8709f-132">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="8709f-133">*Pages/_Host cshtml* (Blazor Server) &ndash; Razor ページとして実装されているアプリのルートページをします。</span><span class="sxs-lookup"><span data-stu-id="8709f-133">*Pages/_Host.cshtml* (Blazor Server) &ndash; The root page of the app implemented as a Razor Page:</span></span>
  * <span data-ttu-id="8709f-134">アプリのいずれかのページが最初に要求されたときに、このページが表示され、応答で返されます。</span><span class="sxs-lookup"><span data-stu-id="8709f-134">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="8709f-135">`_framework/blazor.server.js` JavaScript ファイルが読み込まれ、ブラウザーとサーバーの間のリアルタイム SignalR 接続が設定されます。</span><span class="sxs-lookup"><span data-stu-id="8709f-135">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
  * <span data-ttu-id="8709f-136">[ホスト] ページでは、ルート `App` コンポーネント (*app.xaml*) を表示する場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="8709f-136">The Host page specifies where the root `App` component (*App.razor*) is rendered.</span></span>

* <span data-ttu-id="8709f-137"><xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントを使用してクライアント側のルーティングを設定するアプリのルートコンポーネント &ndash; ます *。*</span><span class="sxs-lookup"><span data-stu-id="8709f-137">*App.razor* &ndash; The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="8709f-138">`Router` コンポーネントは、ブラウザーナビゲーションをインターセプトし、要求されたアドレスと一致するページをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="8709f-138">The `Router` component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="8709f-139">[*ページ*] フォルダー &ndash; Blazor アプリを構成するルーティング可能なコンポーネント/ページ (*Razor*) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="8709f-139">*Pages* folder &ndash; Contains the routable components/pages (*.razor*) that make up the Blazor app.</span></span> <span data-ttu-id="8709f-140">各ページのルートは、 [`@page`](xref:mvc/views/razor#page)ディレクティブを使用して指定します。</span><span class="sxs-lookup"><span data-stu-id="8709f-140">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="8709f-141">このテンプレートには、次のコンポーネントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8709f-141">The template includes the following components:</span></span>
  * <span data-ttu-id="8709f-142">`Index` (*Index*) &ndash; はホームページを実装します。</span><span class="sxs-lookup"><span data-stu-id="8709f-142">`Index` (*Index.razor*) &ndash; Implements the Home page.</span></span>
  * <span data-ttu-id="8709f-143">`Counter` (*counter*) &ndash; カウンターページを実装します。</span><span class="sxs-lookup"><span data-stu-id="8709f-143">`Counter` (*Counter.razor*) &ndash; Implements the Counter page.</span></span>
  * <span data-ttu-id="8709f-144">`Error` (*エラー razor*、Blazor サーバーアプリのみ) &ndash; アプリでハンドルされない例外が発生したときに表示されます。</span><span class="sxs-lookup"><span data-stu-id="8709f-144">`Error` (*Error.razor*, Blazor Server app only) &ndash; Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="8709f-145">`FetchData` (*fetchdata. razor*) &ndash; は、Fetch data ページを実装します。</span><span class="sxs-lookup"><span data-stu-id="8709f-145">`FetchData` (*FetchData.razor*) &ndash; Implements the Fetch data page.</span></span>

* <span data-ttu-id="8709f-146">*共有*フォルダー &ndash; には、アプリで使用される他の UI コンポーネント (*Razor*) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="8709f-146">*Shared* folder &ndash; Contains other UI components (*.razor*) used by the app:</span></span>
  * <span data-ttu-id="8709f-147">アプリのレイアウトコンポーネント &ndash; `MainLayout` (*mainlayout*)。</span><span class="sxs-lookup"><span data-stu-id="8709f-147">`MainLayout` (*MainLayout.razor*) &ndash; The app's layout component.</span></span>
  * <span data-ttu-id="8709f-148">`NavMenu` (*ナビゲーションメニューの razor*) &ndash; サイドバーナビゲーションを実装します。</span><span class="sxs-lookup"><span data-stu-id="8709f-148">`NavMenu` (*NavMenu.razor*) &ndash; Implements sidebar navigation.</span></span> <span data-ttu-id="8709f-149">には、ナビゲーションリンクを他の Razor コンポーネントにレンダリングするナビゲーションリンク[コンポーネント](xref:blazor/routing#navlink-component)(<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="8709f-149">Includes the [NavLink component](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="8709f-150">コンポーネントが読み込まれると、`NavLink` コンポーネントによって選択された状態が自動的に示されます。これは、現在表示されているコンポーネントをユーザーが理解するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="8709f-150">The `NavLink` component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="8709f-151">*_Imports razor* &ndash; には、名前空間の[`@using`](xref:mvc/views/razor#using)ディレクティブなど、アプリのコンポーネント (*razor*) に含める一般的な razor ディレクティブが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8709f-151">*_Imports.razor* &ndash; Includes common Razor directives to include in the app's components (*.razor*), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="8709f-152">*データ*フォルダー (Blazor サーバー) &ndash; には、`WeatherForecast` クラスと、アプリの `FetchData` コンポーネントに気象データの例を提供する `WeatherForecastService` の実装が含まれています。</span><span class="sxs-lookup"><span data-stu-id="8709f-152">*Data* folder (Blazor Server) &ndash; Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="8709f-153">*wwwroot* &ndash;、アプリのパブリックな静的アセットを含むアプリの[Web ルート](xref:fundamentals/index#web-root)フォルダーです。</span><span class="sxs-lookup"><span data-stu-id="8709f-153">*wwwroot* &ndash; The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="8709f-154">*appsettings* (Blazor Server) アプリの構成設定 &ndash; ます。</span><span class="sxs-lookup"><span data-stu-id="8709f-154">*appsettings.json* (Blazor Server) &ndash; Configuration settings for the app.</span></span>
