---
title: ASP.NET Core の概要
author: rick-anderson
description: インターネットに接続された最新のクラウド ベース アプリケーションを構築するための、クロス プラットフォームで高パフォーマンスのオープン ソース フレームワークである ASP.NET Core について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- SignalR
uid: index
ms.openlocfilehash: f6e4a624186ccf0d7f6475819df7da9ef99f9bcc
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78644372"
---
# <a name="introduction-to-aspnet-core"></a><span data-ttu-id="01750-103">ASP.NET Core の概要</span><span class="sxs-lookup"><span data-stu-id="01750-103">Introduction to ASP.NET Core</span></span>

<span data-ttu-id="01750-104">著者: [Daniel Roth](https://github.com/danroth27)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Shaun Luttin](https://twitter.com/dicshaunary)</span><span class="sxs-lookup"><span data-stu-id="01750-104">By [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Shaun Luttin](https://twitter.com/dicshaunary)</span></span>

<span data-ttu-id="01750-105">ASP.NET Core は、インターネットに接続された最新のクラウド ベース アプリケーションを構築するための、クロス プラットフォームで高パフォーマンスの[オープン ソース](https://github.com/aspnet/home) フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="01750-105">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/aspnet/home) framework for building modern, cloud-based, Internet-connected applications.</span></span> <span data-ttu-id="01750-106">ASP.NET Core では次のことができます。</span><span class="sxs-lookup"><span data-stu-id="01750-106">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="01750-107">Web アプリ、Web サービス、[IoT](https://www.microsoft.com/internet-of-things/) アプリ、モバイル バックエンドを構築する。</span><span class="sxs-lookup"><span data-stu-id="01750-107">Build web apps and services, [IoT](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="01750-108">Windows、macOS、Linux で好みの開発ツールを使う。</span><span class="sxs-lookup"><span data-stu-id="01750-108">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="01750-109">クラウドまたはオンプレミスに展開する。</span><span class="sxs-lookup"><span data-stu-id="01750-109">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="01750-110">[.NET Core または .NET Framework](/dotnet/articles/standard/choosing-core-framework-server) 上で実行する。</span><span class="sxs-lookup"><span data-stu-id="01750-110">Run on [.NET Core or .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="01750-111">ASP.NET Core を選ぶ理由</span><span class="sxs-lookup"><span data-stu-id="01750-111">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="01750-112">何百万人もの開発者が、これまで、そして現在も、Web アプリの作成に [ASP.NET 4.x](/aspnet/overview) を使っています。</span><span class="sxs-lookup"><span data-stu-id="01750-112">Millions of developers have used (and continue to use) [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="01750-113">ASP.NET Core は ASP.NET 4.x を設計し直したものであり、無駄のないモジュール形式のフレームワークになるようにアーキテクチャが変更されています。</span><span class="sxs-lookup"><span data-stu-id="01750-113">ASP.NET Core is a redesign of ASP.NET 4.x, with architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="01750-114">ASP.NET Core MVC を使って Web API と Web UI を構築する</span><span class="sxs-lookup"><span data-stu-id="01750-114">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="01750-115">ASP.NET Core MVC は、[Web API](xref:tutorials/first-web-api) と [Web アプリ](xref:tutorials/razor-pages/index)を構築する機能を備えています。</span><span class="sxs-lookup"><span data-stu-id="01750-115">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="01750-116">[モデル ビュー コントローラー (MVC) パターン](xref:mvc/overview)は、Web API と Web アプリをテスト可能にするのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="01750-116">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="01750-117">[Razor ページ](xref:razor-pages/index)はページ ベースのプログラミング モデルであり、Web UI の開発を容易にし、生産性を高めます。</span><span class="sxs-lookup"><span data-stu-id="01750-117">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="01750-118">[Razor マークアップ](xref:mvc/views/razor)では、[Razor ページ](xref:razor-pages/index)および [MVC ビュー](xref:mvc/views/overview)用に生産性の高い構文が提供されます。</span><span class="sxs-lookup"><span data-stu-id="01750-118">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="01750-119">[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)を使うと、Razor ファイルでの HTML 要素の作成とレンダリングに、サーバー側コードを組み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="01750-119">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="01750-120">[複数のデータ形式とコンテンツ ネゴシエーション](xref:web-api/advanced/formatting)の組み込みサポートにより、Web API はブラウザーやモバイル デバイスなどのさまざまなクライアントと接続できます。</span><span class="sxs-lookup"><span data-stu-id="01750-120">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="01750-121">[モデル バインド](xref:mvc/models/model-binding)は、HTTP 要求からアクション メソッドのパラメーターにデータを自動的にマップします。</span><span class="sxs-lookup"><span data-stu-id="01750-121">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="01750-122">[モデル検証](xref:mvc/models/validation)では、クライアント側とサーバー側の検証が自動的に実行されます。</span><span class="sxs-lookup"><span data-stu-id="01750-122">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="01750-123">クライアント側の開発</span><span class="sxs-lookup"><span data-stu-id="01750-123">Client-side development</span></span>

<span data-ttu-id="01750-124">ASP.NET Core は、人気のあるクライアント側のフレームワークとライブラリ ([Blazor](xref:blazor/index)、[Angular](xref:spa/angular)、[React](xref:spa/react)、[Bootstrap](https://getbootstrap.com/) など) をシームレスに統合します。</span><span class="sxs-lookup"><span data-stu-id="01750-124">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="01750-125">詳細については、<xref:blazor/index> と "*クライアント側の開発*" の関連トピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="01750-125">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a><span data-ttu-id="01750-126">.NET Framework を対象とする ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="01750-126">ASP.NET Core targeting .NET Framework</span></span>

<span data-ttu-id="01750-127">ASP.NET Core 2.x は、.NET Core または .NET Framework を対象にすることができます。</span><span class="sxs-lookup"><span data-stu-id="01750-127">ASP.NET Core 2.x can target .NET Core or .NET Framework.</span></span> <span data-ttu-id="01750-128">.NET Framework を対象とする ASP.NET Core アプリはクロスプラットフォームではありません&mdash;Windows でのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="01750-128">ASP.NET Core apps targeting .NET Framework aren't cross-platform&mdash;they run on Windows only.</span></span> <span data-ttu-id="01750-129">一般に、ASP.NET Core 2.x は [.NET Standard](/dotnet/standard/net-standard) ライブラリで構成されています。</span><span class="sxs-lookup"><span data-stu-id="01750-129">Generally, ASP.NET Core 2.x is made up of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="01750-130">.NET Standard 2.0 を使って作成されたライブラリは、[.NET Standard 2.0 を実装しているすべての .NET プラットフォーム](/dotnet/standard/net-standard#net-implementation-support)上で動作します。</span><span class="sxs-lookup"><span data-stu-id="01750-130">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="01750-131">ASP.NET Core 2.x は、.NET Standard 2.0 を実装している .NET Framework バージョンにおいてサポートされています。</span><span class="sxs-lookup"><span data-stu-id="01750-131">ASP.NET Core 2.x is supported on .NET Framework versions that implement .NET Standard 2.0:</span></span>

* <span data-ttu-id="01750-132">.NET Framework の最新バージョンを強くお勧めします。</span><span class="sxs-lookup"><span data-stu-id="01750-132">.NET Framework latest version is strongly recommended.</span></span>
* <span data-ttu-id="01750-133">.NET Framework 4.6.1 以降。</span><span class="sxs-lookup"><span data-stu-id="01750-133">.NET Framework 4.6.1 and later.</span></span>

<span data-ttu-id="01750-134">ASP.NET Core 3.0 以降は、.NET Core でのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="01750-134">ASP.NET Core 3.0 and later will only run on .NET Core.</span></span> <span data-ttu-id="01750-135">この変更に関する詳細については、「[ASP.NET Core 3.0 で導入される変更について](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="01750-135">For more details regarding this change, see [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<span data-ttu-id="01750-136">.NET Core を対象とする利点はいくつかあり、リリースのたびにその利点が増えています。</span><span class="sxs-lookup"><span data-stu-id="01750-136">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="01750-137">.NET Framework 経由による .NET Core には次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="01750-137">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="01750-138">クロスプラットフォームである。</span><span class="sxs-lookup"><span data-stu-id="01750-138">Cross-platform.</span></span> <span data-ttu-id="01750-139">macOS、Linux、Windows で実行できる。</span><span class="sxs-lookup"><span data-stu-id="01750-139">Runs on macOS, Linux, and Windows.</span></span>
* <span data-ttu-id="01750-140">パフォーマンスの向上</span><span class="sxs-lookup"><span data-stu-id="01750-140">Improved performance</span></span>
* [<span data-ttu-id="01750-141">side-by-side でのバージョン管理</span><span class="sxs-lookup"><span data-stu-id="01750-141">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#a-need-for-side-by-side-of-net-versions-per-application-level)
* <span data-ttu-id="01750-142">新しい API</span><span class="sxs-lookup"><span data-stu-id="01750-142">New APIs</span></span>
* <span data-ttu-id="01750-143">ソースを開く</span><span class="sxs-lookup"><span data-stu-id="01750-143">Open source</span></span>

<span data-ttu-id="01750-144">.NET Framework と .NET Core の間にある API のギャップを埋めるため、鋭意作業中です。</span><span class="sxs-lookup"><span data-stu-id="01750-144">We're working hard to close the API gap from .NET Framework to .NET Core.</span></span> <span data-ttu-id="01750-145">[Windows 互換機能パック](/dotnet/core/porting/windows-compat-pack)により、多くの Windows 限定の API が .NET Core で利用できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="01750-145">The [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) made thousands of Windows-only APIs available in .NET Core.</span></span> <span data-ttu-id="01750-146">このような API は .NET Core 1.x で利用できませんでした。</span><span class="sxs-lookup"><span data-stu-id="01750-146">These APIs weren't available in .NET Core 1.x.</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="01750-147">推奨のラーニング パス</span><span class="sxs-lookup"><span data-stu-id="01750-147">Recommended learning path</span></span>

<span data-ttu-id="01750-148">ASP.NET Core アプリを開発する場合の概要として、次の順序でチュートリアルと記事を読むことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="01750-148">We recommend the following sequence of tutorials and articles for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="01750-149">開発または管理するアプリの種類別のチュートリアルは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="01750-149">Follow a tutorial for the type of app you want to develop or maintain:</span></span>

   |<span data-ttu-id="01750-150">アプリの種類</span><span class="sxs-lookup"><span data-stu-id="01750-150">App type</span></span>  |<span data-ttu-id="01750-151">シナリオ</span><span class="sxs-lookup"><span data-stu-id="01750-151">Scenario</span></span>  |<span data-ttu-id="01750-152">チュートリアル</span><span class="sxs-lookup"><span data-stu-id="01750-152">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="01750-153">Web アプリ</span><span class="sxs-lookup"><span data-stu-id="01750-153">Web app</span></span>                   | <span data-ttu-id="01750-154">新規の開発</span><span class="sxs-lookup"><span data-stu-id="01750-154">For new development</span></span>        |[<span data-ttu-id="01750-155">Razor ページの概要</span><span class="sxs-lookup"><span data-stu-id="01750-155">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="01750-156">Web アプリ</span><span class="sxs-lookup"><span data-stu-id="01750-156">Web app</span></span>                   | <span data-ttu-id="01750-157">MVC アプリの管理</span><span class="sxs-lookup"><span data-stu-id="01750-157">For maintaining an MVC app</span></span> |[<span data-ttu-id="01750-158">MVC の概要</span><span class="sxs-lookup"><span data-stu-id="01750-158">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="01750-159">Web API</span><span class="sxs-lookup"><span data-stu-id="01750-159">Web API</span></span>                   |                            |<span data-ttu-id="01750-160">[Web API の作成](xref:tutorials/first-web-api)\*</span><span class="sxs-lookup"><span data-stu-id="01750-160">[Create a web API](xref:tutorials/first-web-api)\*</span></span>  |
   |<span data-ttu-id="01750-161">リアルタイムのアプリ</span><span class="sxs-lookup"><span data-stu-id="01750-161">Real-time app</span></span>             |                            |[<span data-ttu-id="01750-162">SignalR の概要</span><span class="sxs-lookup"><span data-stu-id="01750-162">Get started with SignalR</span></span>](xref:tutorials/signalr) |
   |<span data-ttu-id="01750-163">Blazor アプリ</span><span class="sxs-lookup"><span data-stu-id="01750-163">Blazor app</span></span>                |                            |[<span data-ttu-id="01750-164">Blazor の概要</span><span class="sxs-lookup"><span data-stu-id="01750-164">Get started with Blazor</span></span>](xref:blazor/get-started) |
   |<span data-ttu-id="01750-165">リモート プロシージャ コール アプリ</span><span class="sxs-lookup"><span data-stu-id="01750-165">Remote Procedure Call app</span></span> |                            |[<span data-ttu-id="01750-166">gRPC サービスの概要</span><span class="sxs-lookup"><span data-stu-id="01750-166">Get started with a gRPC service</span></span>](xref:tutorials/grpc/grpc-start) |

1. <span data-ttu-id="01750-167">基本のデータ アクセスの実行方法を示すチュートリアルは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="01750-167">Follow a tutorial that shows how to do basic data access:</span></span>

   |<span data-ttu-id="01750-168">シナリオ</span><span class="sxs-lookup"><span data-stu-id="01750-168">Scenario</span></span>  |<span data-ttu-id="01750-169">チュートリアル</span><span class="sxs-lookup"><span data-stu-id="01750-169">Tutorial</span></span>  |
   |----------|----------|
   | <span data-ttu-id="01750-170">新規の開発</span><span class="sxs-lookup"><span data-stu-id="01750-170">For new development</span></span>        |[<span data-ttu-id="01750-171">Entity Framework Core を使用した Razor Pages</span><span class="sxs-lookup"><span data-stu-id="01750-171">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   | <span data-ttu-id="01750-172">MVC アプリの管理</span><span class="sxs-lookup"><span data-stu-id="01750-172">For maintaining an MVC app</span></span> |[<span data-ttu-id="01750-173">Entity Framework Core を使用した MVC</span><span class="sxs-lookup"><span data-stu-id="01750-173">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

1. <span data-ttu-id="01750-174">すべての種類のアプリに該当する ASP.NET Core の機能の概要は、次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="01750-174">Read an overview of ASP.NET Core features that apply to all app types:</span></span>

   * [<span data-ttu-id="01750-175">Entity Framework の基礎</span><span class="sxs-lookup"><span data-stu-id="01750-175">Fundamentals</span></span>](xref:fundamentals/index)

1. <span data-ttu-id="01750-176">興味のあるその他のトピックは、目次から参照してください。</span><span class="sxs-lookup"><span data-stu-id="01750-176">Browse the Table of Contents for other topics of interest.</span></span>

<span data-ttu-id="01750-177">\*[すべてブラウザーでたどる新しい Web API のチュートリアル](https://docs.microsoft.com/learn/modules/build-web-api-net-core)があります。ローカルに IDE をインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="01750-177">\* There is a new [web API tutorial that you follow entirely in the browser](https://docs.microsoft.com/learn/modules/build-web-api-net-core), no local IDE installation required.</span></span>  <span data-ttu-id="01750-178">このコードは、[Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) で実行し、テストには [curl](https://curl.haxx.se/) を使用します。</span><span class="sxs-lookup"><span data-stu-id="01750-178">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migration-from-the-net-framework"></a><span data-ttu-id="01750-179">.NET Framework からの移行</span><span class="sxs-lookup"><span data-stu-id="01750-179">Migration from the .NET Framework</span></span>

<span data-ttu-id="01750-180">ASP.NET アプリを ASP.NET Core に移行するためのリファレンス ガイドについては、「<xref:migration/proper-to-2x/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="01750-180">For a reference guide to migrating ASP.NET apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

## <a name="how-to-download-a-sample"></a><span data-ttu-id="01750-181">サンプルをダウンロードする方法</span><span class="sxs-lookup"><span data-stu-id="01750-181">How to download a sample</span></span>

<span data-ttu-id="01750-182">多くの記事やチュートリアルにサンプル コードへのリンクが含まれています。</span><span class="sxs-lookup"><span data-stu-id="01750-182">Many of the articles and tutorials include links to sample code.</span></span>

1. <span data-ttu-id="01750-183">[ASP.NET リポジトリの zip ファイルをダウンロード](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master)します。</span><span class="sxs-lookup"><span data-stu-id="01750-183">[Download the ASP.NET repository zip file](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span></span>
1. <span data-ttu-id="01750-184">*Docs-master.zip* ファイルを解凍します。</span><span class="sxs-lookup"><span data-stu-id="01750-184">Unzip the *Docs-master.zip* file.</span></span>
1. <span data-ttu-id="01750-185">サンプル リンクの URL を使って、サンプル ディレクトリに移動します。</span><span class="sxs-lookup"><span data-stu-id="01750-185">Use the URL in the sample link to help you navigate to the sample directory.</span></span>

### <a name="preprocessor-directives-in-sample-code"></a><span data-ttu-id="01750-186">サンプル コードのプリプロセッサ ディレクティブ</span><span class="sxs-lookup"><span data-stu-id="01750-186">Preprocessor directives in sample code</span></span>

<span data-ttu-id="01750-187">複数のシナリオを示すため、サンプル アプリでは `#define` と `#if-#else/#elif-#endif` のプリプロセッサ ディレクティブを使用してさまざまなサンプル コードのセクションを選択してコンパイルし、実行します。</span><span class="sxs-lookup"><span data-stu-id="01750-187">To demonstrate multiple scenarios, sample apps use the `#define` and `#if-#else/#elif-#endif` preprocessor directives to selectively compile and run different sections of sample code.</span></span> <span data-ttu-id="01750-188">このアプローチを活用するサンプルでは、C# ファイルの上部にある `#define` ディレクティブを設定して、実行するシナリオに関連付けられたシンボルを定義します。</span><span class="sxs-lookup"><span data-stu-id="01750-188">For those samples that make use of this approach, set the `#define` directive at the top of the C# files to define the symbol associated with the scenario that you want to run.</span></span> <span data-ttu-id="01750-189">一部のサンプルでは、シナリオを実行するために複数のファイルの上部でシンボルを定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="01750-189">Some samples require defining the symbol at the top of multiple files in order to run a scenario.</span></span>

<span data-ttu-id="01750-190">たとえば、次の `#define` のシンボル一覧は、4 つのシナリオが使用可能である (シンボルごとに 1 つのシナリオ) ことを示しています。</span><span class="sxs-lookup"><span data-stu-id="01750-190">For example, the following `#define` symbol list indicates that four scenarios are available (one scenario per symbol).</span></span> <span data-ttu-id="01750-191">現在のサンプル構成では `TemplateCode` のシナリオが実行されます。</span><span class="sxs-lookup"><span data-stu-id="01750-191">The current sample configuration runs the `TemplateCode` scenario:</span></span>

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

<span data-ttu-id="01750-192">`ExpandDefault` のシナリオを実行するサンプルを変更するには、`ExpandDefault` のシンボルを定義し、残りのシンボルをコメント アウトしたままにします。</span><span class="sxs-lookup"><span data-stu-id="01750-192">To change the sample to run the `ExpandDefault` scenario, define the `ExpandDefault` symbol and leave the remaining symbols commented-out:</span></span>

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

<span data-ttu-id="01750-193">[C# プリプロセッサ ディレクティブ](/dotnet/csharp/language-reference/preprocessor-directives/)を使用してコードのセクションを選択的にコンパイルする方法の詳細については、「[#define (C# リファレンス)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define)」および「[#if (C# リファレンス)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="01750-193">For more information on using [C# preprocessor directives](/dotnet/csharp/language-reference/preprocessor-directives/) to selectively compile sections of code, see [#define (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) and [#if (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span></span>

### <a name="regions-in-sample-code"></a><span data-ttu-id="01750-194">サンプル コードのリージョン</span><span class="sxs-lookup"><span data-stu-id="01750-194">Regions in sample code</span></span>

<span data-ttu-id="01750-195">一部のサンプル アプリには、[#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) と [#end-region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) の C# ディレクティブに囲まれたコードのセクションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="01750-195">Some sample apps contain sections of code surrounded by [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) and [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# directives.</span></span> <span data-ttu-id="01750-196">ドキュメントのビルド システムによって、レンダリングされたドキュメントのトピックにこのリージョンが挿入されます。</span><span class="sxs-lookup"><span data-stu-id="01750-196">The documentation build system injects these regions into the rendered documentation topics.</span></span>  

<span data-ttu-id="01750-197">リージョン名には通常、"snippet" という単語が含まれています。</span><span class="sxs-lookup"><span data-stu-id="01750-197">Region names usually contain the word "snippet."</span></span> <span data-ttu-id="01750-198">次の例は `snippet_WebHostDefaults` という名前のリージョンを示しています。</span><span class="sxs-lookup"><span data-stu-id="01750-198">The following example shows a region named `snippet_WebHostDefaults`:</span></span>

```csharp
#region snippet_WebHostDefaults
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
    });
#endregion
```

<span data-ttu-id="01750-199">先述の C# コード スニペットは、トピックのマークダウン ファイルの次の行で示されています。</span><span class="sxs-lookup"><span data-stu-id="01750-199">The preceding C# code snippet is referenced in the topic's markdown file with the following line:</span></span>

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_WebHostDefaults)]
```

<span data-ttu-id="01750-200">コードを囲む `#region` と `#endregion` のディレクティブは安全に無視 (または削除) することができます。</span><span class="sxs-lookup"><span data-stu-id="01750-200">You may safely ignore (or remove) the `#region` and `#endregion` directives that surround the code.</span></span> <span data-ttu-id="01750-201">トピックで説明されているサンプル シナリオを実行する予定がある場合は、これらのディレクティブ内のコードを変更しないでください。</span><span class="sxs-lookup"><span data-stu-id="01750-201">Don't alter the code within these directives if you plan to run the sample scenarios described in the topic.</span></span> <span data-ttu-id="01750-202">他のシナリオを試す場合は、自由にコードを変更できます。</span><span class="sxs-lookup"><span data-stu-id="01750-202">Feel free to alter the code when experimenting with other scenarios.</span></span>

<span data-ttu-id="01750-203">詳細については、「[Contribute to the ASP.NET documentation: Code snippets (ASP.NET に貢献する: コード スニペット)](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="01750-203">For more information, see [Contribute to the ASP.NET documentation: Code snippets](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span></span>

## <a name="next-steps"></a><span data-ttu-id="01750-204">次の手順</span><span class="sxs-lookup"><span data-stu-id="01750-204">Next steps</span></span>

<span data-ttu-id="01750-205">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="01750-205">For more information, see the following resources:</span></span>

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="01750-206">ASP.NET Core の基礎</span><span class="sxs-lookup"><span data-stu-id="01750-206">ASP.NET Core fundamentals</span></span>](xref:fundamentals/index)
* <span data-ttu-id="01750-207">[週 1 回の ASP.NET Community Standup](https://live.asp.net/) では、チームの進行状況とプランが報告され、</span><span class="sxs-lookup"><span data-stu-id="01750-207">[The weekly ASP.NET community standup](https://live.asp.net/) covers the team's progress and plans.</span></span> <span data-ttu-id="01750-208">新しいブログやサード パーティ製ソフトウェアが取り上げられています。</span><span class="sxs-lookup"><span data-stu-id="01750-208">It features new blogs and third-party software.</span></span>
