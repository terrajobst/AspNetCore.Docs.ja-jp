---
title: ASP.NET Core 2.0 用の Microsoft.AspNetCore.All メタパッケージ
author: Rick-Anderson
description: Microsoft.AspNetCore.All メタパッケージは、ASP.NET Core 2.1 以降では推奨されていません。
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/25/2018
uid: fundamentals/metapackage
ms.openlocfilehash: e47f583d0fa75bdeb26b669303747a70619117c1
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78648962"
---
# <a name="microsoftaspnetcoreall-metapackage-for-aspnet-core-20"></a><span data-ttu-id="a20b7-103">ASP.NET Core 2.0 用の Microsoft.AspNetCore.All メタパッケージ</span><span class="sxs-lookup"><span data-stu-id="a20b7-103">Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a20b7-104">`Microsoft.AspNetCore.All` メタパケージは ASP.NET Core 3.0 以降には含まれません。</span><span class="sxs-lookup"><span data-stu-id="a20b7-104">The `Microsoft.AspNetCore.All` metapackage isn't included in ASP.NET Core 3.0 and later.</span></span> <span data-ttu-id="a20b7-105">詳細については、[こちらの GitHub の問題](https://github.com/aspnet/Announcements/issues/314)のページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a20b7-105">For more information, see [this GitHub issue](https://github.com/aspnet/Announcements/issues/314).</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="a20b7-106">ASP.NET Core 2.1 以降を対象とするアプリケーションは、このパッケージではなく [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) を使うことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a20b7-106">We recommend applications targeting ASP.NET Core 2.1 and later use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) rather than this package.</span></span> <span data-ttu-id="a20b7-107">この記事の「[Microsoft.AspNetCore.All から Microsoft.AspNetCore.App への移行](#migrate)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a20b7-107">See [Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App](#migrate) in this article.</span></span>

<span data-ttu-id="a20b7-108">この機能では、.NET Core 2.x を対象とする ASP.NET Core 2.x が必要です。</span><span class="sxs-lookup"><span data-stu-id="a20b7-108">This feature requires ASP.NET Core 2.x targeting .NET Core 2.x.</span></span>

<span data-ttu-id="a20b7-109">[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) は、共有フレームワークを参照するメタパッケージです。</span><span class="sxs-lookup"><span data-stu-id="a20b7-109">[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) is a metapackage that refers to a shared framework.</span></span> <span data-ttu-id="a20b7-110">*共有フレームワーク*は、アプリのフォルダー内にはない一連のアセンブリ ( *.dll* ファイル) です。</span><span class="sxs-lookup"><span data-stu-id="a20b7-110">A *shared framework* is a set of assemblies (*.dll* files) that are not in the app's folders.</span></span> <span data-ttu-id="a20b7-111">共有フレームワークは、アプリを実行するコンピューター上にインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a20b7-111">The shared framework must be installed on the machine to run the app.</span></span> <span data-ttu-id="a20b7-112">詳しくは、[共有フレームワーク](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a20b7-112">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="a20b7-113">`Microsoft.AspNetCore.All` が参照する共有フレームワークには、次が含まれています。</span><span class="sxs-lookup"><span data-stu-id="a20b7-113">The shared framework that `Microsoft.AspNetCore.All` refers to includes:</span></span>

* <span data-ttu-id="a20b7-114">ASP.NET Core チームでサポートされるすべてのパッケージ。</span><span class="sxs-lookup"><span data-stu-id="a20b7-114">All supported packages by the ASP.NET Core team.</span></span>
* <span data-ttu-id="a20b7-115">Entity Framework Core でサポートされるすべてのパッケージ。</span><span class="sxs-lookup"><span data-stu-id="a20b7-115">All supported packages by the Entity Framework Core.</span></span>
* <span data-ttu-id="a20b7-116">ASP.NET Core および Entity Framework Core で使用される内部およびサードパーティの依存関係。</span><span class="sxs-lookup"><span data-stu-id="a20b7-116">Internal and 3rd-party dependencies used by ASP.NET Core and Entity Framework Core.</span></span>

<span data-ttu-id="a20b7-117">`Microsoft.AspNetCore.All` パッケージには、ASP.NET Core 2.x および Entity Framework Core 2.x のすべての機能が含まれます。</span><span class="sxs-lookup"><span data-stu-id="a20b7-117">All the features of ASP.NET Core 2.x and Entity Framework Core 2.x are included in the `Microsoft.AspNetCore.All` package.</span></span> <span data-ttu-id="a20b7-118">ASP.NET Core 2.0 を対象とする既定のプロジェクト テンプレートは、このパッケージを使用します。</span><span class="sxs-lookup"><span data-stu-id="a20b7-118">The default project templates targeting ASP.NET Core 2.0 use this package.</span></span>

<span data-ttu-id="a20b7-119">`Microsoft.AspNetCore.All` メタパッケージのバージョン番号は、最小の ASP.NET Core バージョンと Entity Framework Core バージョンを表します。</span><span class="sxs-lookup"><span data-stu-id="a20b7-119">The version number of the `Microsoft.AspNetCore.All` metapackage represents the minimum ASP.NET Core version and Entity Framework Core version.</span></span>

<span data-ttu-id="a20b7-120">次の *.csproj* ファイルは、ASP.NET Core の `Microsoft.AspNetCore.All` メタパッケージを参照しています。</span><span class="sxs-lookup"><span data-stu-id="a20b7-120">The following *.csproj* file references the `Microsoft.AspNetCore.All` metapackage for ASP.NET Core:</span></span>

[!code-xml[](metapackage/samples/Metapackage.All.Example.csproj?highlight=8)]

::: moniker range=">= aspnetcore-2.1"

## <a name="implicit-versioning"></a><span data-ttu-id="a20b7-121">暗黙的なバージョン管理</span><span class="sxs-lookup"><span data-stu-id="a20b7-121">Implicit versioning</span></span>

<span data-ttu-id="a20b7-122">ASP.NET Core 2.1 以降では、バージョンなしで `Microsoft.AspNetCore.All` パッケージ参照を指定することができます。</span><span class="sxs-lookup"><span data-stu-id="a20b7-122">In ASP.NET Core 2.1 or later, you can specify the `Microsoft.AspNetCore.All` package reference without a version.</span></span> <span data-ttu-id="a20b7-123">バージョンが指定されていない場合は、暗黙的なバージョンが SDK によって指定されます (`Microsoft.NET.Sdk.Web`)。</span><span class="sxs-lookup"><span data-stu-id="a20b7-123">When the version isn't specified, an implicit version is specified by the SDK (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="a20b7-124">SDK によって指定される暗黙的なバージョンを利用し、パッケージ参照ではバージョン番号を明示的に設定しないことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a20b7-124">We recommend relying on the implicit version specified by the SDK and not explicitly setting the version number on the package reference.</span></span> <span data-ttu-id="a20b7-125">この方法に関して質問がある場合は、[Microsoft.AspNetCore.App の暗黙的なバージョンについてのディスカッション](https://github.com/dotnet/AspNetCore.Docs/issues/6430)で GitHub にコメントしてください。</span><span class="sxs-lookup"><span data-stu-id="a20b7-125">If you have questions about this approach, leave a GitHub comment at the [Discussion for the Microsoft.AspNetCore.App implicit version](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span></span>

<span data-ttu-id="a20b7-126">ポータブル アプリの場合、暗黙的なバージョンは `major.minor.0` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="a20b7-126">The implicit version is set to `major.minor.0` for portable apps.</span></span> <span data-ttu-id="a20b7-127">共有フレームワークのロールフォワード メカニズムは、インストールされている共有フレームワークの中で最新の互換性のあるバージョンを使ってアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="a20b7-127">The shared framework roll-forward mechanism runs the app on the latest compatible version among the installed shared frameworks.</span></span> <span data-ttu-id="a20b7-128">開発、テスト、運用で確実に同じバージョンが使われるようにするため、すべての環境に同じバージョンの共有フレームワークをインストールしてください。</span><span class="sxs-lookup"><span data-stu-id="a20b7-128">To guarantee the same version is used in development, test, and production, ensure the same version of the shared framework is installed in all environments.</span></span> <span data-ttu-id="a20b7-129">自己完結型アプリの場合は、暗黙的なバージョン番号は、インストールされている SDK にバンドルされている共有フレームワークの `major.minor.patch` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="a20b7-129">For self-contained apps, the implicit version number is set to the `major.minor.patch` of the shared framework bundled in the installed SDK.</span></span>

<span data-ttu-id="a20b7-130">`Microsoft.AspNetCore.All` パッケージ参照でバージョン番号を指定しても、共有フレームワークのバージョンが選択されることは保証**されません**。</span><span class="sxs-lookup"><span data-stu-id="a20b7-130">Specifying a version number on the `Microsoft.AspNetCore.All` package reference does **not** guarantee that version of the shared framework is chosen.</span></span> <span data-ttu-id="a20b7-131">たとえば、バージョン "2.1.1" が指定されているのに、インストールされているのは "2.1.3" であるものとします。</span><span class="sxs-lookup"><span data-stu-id="a20b7-131">For example, suppose version "2.1.1" is specified, but "2.1.3" is installed.</span></span> <span data-ttu-id="a20b7-132">この場合、アプリは "2.1.3" を使います。</span><span class="sxs-lookup"><span data-stu-id="a20b7-132">In that case, the app will use "2.1.3".</span></span> <span data-ttu-id="a20b7-133">お勧めしませんが、ロールフォワード (パッチとマイナーの両方または一方) を無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="a20b7-133">Although not recommended, you can disable roll forward (patch and/or minor).</span></span> <span data-ttu-id="a20b7-134">.NET ホストのロールフォワードに関する詳細、およびその動作を構成する方法については、[.NET ホストのロールフォワード](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a20b7-134">For more information regarding dotnet host roll-forward and how to configure its behavior, see [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span></span>

<span data-ttu-id="a20b7-135">暗黙的なバージョンの `Microsoft.AspNetCore.All` を使用するには、プロジェクト ファイルでプロジェクトの SDK を `Microsoft.NET.Sdk.Web` に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a20b7-135">The project's SDK must be set to `Microsoft.NET.Sdk.Web` in the project file to use the implicit version of `Microsoft.AspNetCore.All`.</span></span> <span data-ttu-id="a20b7-136">`Microsoft.NET.Sdk` SDK が (プロジェクト ファイル上部の `<Project Sdk="Microsoft.NET.Sdk">` で) 指定されている場合、次の警告が生成されます。</span><span class="sxs-lookup"><span data-stu-id="a20b7-136">When the `Microsoft.NET.Sdk` SDK is specified (`<Project Sdk="Microsoft.NET.Sdk">` at the top of the project file), the following warning is generated:</span></span>

<span data-ttu-id="a20b7-137">*Warning NU1604:Project dependency Microsoft.AspNetCore.App does not contain an inclusive lower bound.Include a lower bound in the dependency version to ensure consistent restore results.* (警告 NU1604: プロジェクト依存関係 Microsoft.AspNetCore.App には下限が含まれていません。復元結果に一貫性が与えられるように、依存関係バージョンに下限を追加してください。)</span><span class="sxs-lookup"><span data-stu-id="a20b7-137">*Warning NU1604: Project dependency Microsoft.AspNetCore.All does not contain an inclusive lower bound. Include a lower bound in the dependency version to ensure consistent restore results.*</span></span>

<span data-ttu-id="a20b7-138">これは .NET Core 2.1 SDK に関する既知の問題であり、.NET Core 2.2 SDK で修正されます。</span><span class="sxs-lookup"><span data-stu-id="a20b7-138">This is a known issue with the .NET Core 2.1 SDK and will be fixed in the .NET Core 2.2 SDK.</span></span>

::: moniker-end

<a name="migrate"></a>

## <a name="migrating-from-microsoftaspnetcoreall-to-microsoftaspnetcoreapp"></a><span data-ttu-id="a20b7-139">Microsoft.AspNetCore.All から Microsoft.AspNetCore.App への移行</span><span class="sxs-lookup"><span data-stu-id="a20b7-139">Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App</span></span>

<span data-ttu-id="a20b7-140">以下のパッケージは、`Microsoft.AspNetCore.All` には含まれますが `Microsoft.AspNetCore.App` パッケージには含まれません。</span><span class="sxs-lookup"><span data-stu-id="a20b7-140">The following packages are included in `Microsoft.AspNetCore.All` but not the `Microsoft.AspNetCore.App` package.</span></span>

* `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`
* `Microsoft.AspNetCore.AzureAppServices.HostingStartup`
* `Microsoft.AspNetCore.AzureAppServicesIntegration`
* `Microsoft.AspNetCore.DataProtection.AzureKeyVault`
* `Microsoft.AspNetCore.DataProtection.AzureStorage`
* `Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`
* `Microsoft.AspNetCore.SignalR.Redis`
* `Microsoft.Data.Sqlite`
* `Microsoft.Data.Sqlite.Core`
* `Microsoft.EntityFrameworkCore.Sqlite`
* `Microsoft.EntityFrameworkCore.Sqlite.Core`
* `Microsoft.Extensions.Caching.Redis`
* `Microsoft.Extensions.Configuration.AzureKeyVault`
* `Microsoft.Extensions.Logging.AzureAppServices`
* `Microsoft.VisualStudio.Web.BrowserLink`

<span data-ttu-id="a20b7-141">アプリで上記のパッケージまたは上記のパッケージによって取り込まれるパッケージに含まれるいずれかの API を使っている場合、`Microsoft.AspNetCore.All` から `Microsoft.AspNetCore.App` に移行するには、これらのパッケージへの参照をプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="a20b7-141">To move from `Microsoft.AspNetCore.All` to `Microsoft.AspNetCore.App`, if your app uses any APIs from the above packages, or packages brought in by those packages, add references to those packages in your project.</span></span>

<span data-ttu-id="a20b7-142">上記のパッケージの依存関係のうち、他の部分で `Microsoft.AspNetCore.App` の依存関係になっていないものは、暗黙的に含まれることはありません。</span><span class="sxs-lookup"><span data-stu-id="a20b7-142">Any dependencies of the preceding packages that otherwise aren't dependencies of `Microsoft.AspNetCore.App` are not included implicitly.</span></span> <span data-ttu-id="a20b7-143">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a20b7-143">For example:</span></span>

* <span data-ttu-id="a20b7-144">`Microsoft.Extensions.Caching.Redis` の依存関係としての `StackExchange.Redis`</span><span class="sxs-lookup"><span data-stu-id="a20b7-144">`StackExchange.Redis` as a dependency of `Microsoft.Extensions.Caching.Redis`</span></span>
* <span data-ttu-id="a20b7-145">`Microsoft.AspNetCore.ApplicationInsights.HostingStartup` の依存関係としての `Microsoft.ApplicationInsights`</span><span class="sxs-lookup"><span data-stu-id="a20b7-145">`Microsoft.ApplicationInsights` as a dependency of `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`</span></span>

## <a name="update-aspnet-core-21"></a><span data-ttu-id="a20b7-146">ASP.NET Core 2.1 を更新する</span><span class="sxs-lookup"><span data-stu-id="a20b7-146">Update ASP.NET Core 2.1</span></span>

<span data-ttu-id="a20b7-147">2\.1 以降用の `Microsoft.AspNetCore.App` メタパッケージに移行することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a20b7-147">We recommend migrating to the `Microsoft.AspNetCore.App` metapackage for 2.1 and later.</span></span> <span data-ttu-id="a20b7-148">`Microsoft.AspNetCore.All` メタパッケージを引き続き使用し、最新のバージョンの修正プログラムが配置されていることを確認するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="a20b7-148">To keep using the `Microsoft.AspNetCore.All` metapackage and ensure the latest patch version is deployed:</span></span>

* <span data-ttu-id="a20b7-149">開発用コンピューターおよびビルド サーバーの場合:最新の [.NET Core SDK](https://www.microsoft.com/net/download) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="a20b7-149">On development machines and build servers: Install the latest [.NET Core SDK](https://www.microsoft.com/net/download).</span></span>
* <span data-ttu-id="a20b7-150">配置サーバーの場合:最新の [.NET Core ランタイム](https://www.microsoft.com/net/download)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="a20b7-150">On deployment servers: Install the latest [.NET Core runtime](https://www.microsoft.com/net/download).</span></span>
 <span data-ttu-id="a20b7-151">ご利用のアプリは、アプリケーションの再起動時にインストールされている最新バージョンにロールフォワードされます。</span><span class="sxs-lookup"><span data-stu-id="a20b7-151">Your app will roll forward to the latest installed version on an application restart.</span></span>
