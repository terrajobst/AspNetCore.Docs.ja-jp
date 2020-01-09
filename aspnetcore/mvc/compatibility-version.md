---
title: ASP.NET Core MVC の互換バージョン
author: rick-anderson
description: ASP.NET Core の Startup クラスがサービスとアプリケーションの要求パイプラインをどのように構成しているかを説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 9/25/2019
uid: mvc/compatibility-version
ms.openlocfilehash: b29e2ee49aaf0f557f1acd0cf03e9e82d5ea0105
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75357732"
---
# <a name="compatibility-version-for-aspnet-core-mvc"></a><span data-ttu-id="d6ad6-103">ASP.NET Core MVC の互換バージョン</span><span class="sxs-lookup"><span data-stu-id="d6ad6-103">Compatibility version for ASP.NET Core MVC</span></span>

<span data-ttu-id="d6ad6-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d6ad6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d6ad6-105"><xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> メソッドは ASP.NET Core 3.0 アプリでは何も行いません。</span><span class="sxs-lookup"><span data-stu-id="d6ad6-105">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method is a no-op for ASP.NET Core 3.0 apps.</span></span> <span data-ttu-id="d6ad6-106">つまり、<xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion> のどの値で `SetCompatibilityVersion` を呼び出してもアプリケーションに影響しません。</span><span class="sxs-lookup"><span data-stu-id="d6ad6-106">That is, calling `SetCompatibilityVersion` with any value of <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion> has no impact on the application.</span></span>

* <span data-ttu-id="d6ad6-107">ASP.NET Core の次のマイナー バージョンでは新しい `CompatibilityVersion` 値が提供される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d6ad6-107">The next minor version of ASP.NET Core may provide a new `CompatibilityVersion` value.</span></span>
* <span data-ttu-id="d6ad6-108">`Version_2_0` から `Version_2_2`までの `CompatibilityVersion` 値は `[Obsolete(...)]` とマークされます。</span><span class="sxs-lookup"><span data-stu-id="d6ad6-108">`CompatibilityVersion` values `Version_2_0` through `Version_2_2` are marked `[Obsolete(...)]`.</span></span>
* <span data-ttu-id="d6ad6-109">「[偽造防止、CORS、診断、MVC、ルーティングにおける API の重大な変更](https://github.com/aspnet/Announcements/issues/387)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6ad6-109">See [Breaking API changes in Antiforgery, CORS, Diagnostics, Mvc, and Routing](https://github.com/aspnet/Announcements/issues/387).</span></span> <span data-ttu-id="d6ad6-110">この一覧には、互換性スイッチの重大な変更が含まれています。</span><span class="sxs-lookup"><span data-stu-id="d6ad6-110">This list includes breaking changes for compatibility switches.</span></span>

<span data-ttu-id="d6ad6-111">`SetCompatibilityVersion` が ASP.NET Core 2.x アプリでどのように機能するかを確認するには、[この記事の ASP.NET Core 2.2 バージョン](https://docs.microsoft.com/aspnet/core/mvc/compatibility-version?view=aspnetcore-2.2)を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6ad6-111">To see how `SetCompatibilityVersion` works with ASP.NET Core 2.x apps, select the [ASP.NET Core 2.2 version of this article](https://docs.microsoft.com/aspnet/core/mvc/compatibility-version?view=aspnetcore-2.2).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d6ad6-112">ASP.NET Core 2.x アプリで <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> メソッドを使用すると、ASP.NET Core MVC 2.1 または 2.2 に導入されている、互換性に影響する重大な変更をオプトインまたはオプトアウトすることができます。</span><span class="sxs-lookup"><span data-stu-id="d6ad6-112">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an ASP.NET Core 2.x app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or 2.2.</span></span> <span data-ttu-id="d6ad6-113">互換性に影響する可能性のあるこれらの重大な変更は、ほとんどの場合、MVC サブシステムの動作方法と、ランタイムで**ユーザーのコード**が呼び出される方法についてです。</span><span class="sxs-lookup"><span data-stu-id="d6ad6-113">These potentially breaking behavior changes are generally in how the MVC subsystem behaves and how **your code** is called by the runtime.</span></span> <span data-ttu-id="d6ad6-114">オプトインした場合、最新の動作と ASP.NET Core の最新の動作を得ることができます。</span><span class="sxs-lookup"><span data-stu-id="d6ad6-114">By opting in, you get the latest behavior, and the long-term behavior of ASP.NET Core.</span></span>

<span data-ttu-id="d6ad6-115">次のコードにより、互換性モードは ASP.NET Core 2.2 に設定されます。</span><span class="sxs-lookup"><span data-stu-id="d6ad6-115">The following code sets the compatibility mode to ASP.NET Core 2.2:</span></span>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup.cs?name=snippet1)]

<span data-ttu-id="d6ad6-116">最新のバージョン (`CompatibilityVersion.Latest`) を使用してアプリをテストすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d6ad6-116">We recommend you test your app using the latest version (`CompatibilityVersion.Latest`).</span></span> <span data-ttu-id="d6ad6-117">最新のバージョンを使用しても、ほとんどのアプリで重大な動作変更はないと見込まれます。</span><span class="sxs-lookup"><span data-stu-id="d6ad6-117">We anticipate that most apps won't have breaking behavior changes using the latest version.</span></span>

<span data-ttu-id="d6ad6-118">`SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` を呼び出すアプリは、ASP.NET Core 2.1/2.2 MVC バージョンで導入された重大な動作変更の可能性から保護されています。</span><span class="sxs-lookup"><span data-stu-id="d6ad6-118">Apps that call `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` are protected from potentially breaking behavior changes introduced in the ASP.NET Core 2.1/2.2 MVC versions.</span></span> <span data-ttu-id="d6ad6-119">この保護とは、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="d6ad6-119">This protection:</span></span>

* <span data-ttu-id="d6ad6-120">2\.1 以降のすべての変更には該当しません。これは、MVC サブシステムの ASP.NET Core ランタイムの互換性に影響する可能性のある重大な変更のみを対象としています。</span><span class="sxs-lookup"><span data-stu-id="d6ad6-120">Does not apply to all 2.1 and later changes, it's targeted to potentially breaking ASP.NET Core runtime behavior changes in the MVC subsystem.</span></span>
* <span data-ttu-id="d6ad6-121">ASP.NET Core 3.0 には拡張されません。</span><span class="sxs-lookup"><span data-stu-id="d6ad6-121">Does not extend to ASP.NET Core 3.0.</span></span>

<span data-ttu-id="d6ad6-122">`SetCompatibilityVersion` を呼び出さ**ない** ASP.NET Core 2.1 および 2.2 アプリの既定の互換性は、2.0 の互換性です。</span><span class="sxs-lookup"><span data-stu-id="d6ad6-122">The default compatibility for ASP.NET Core 2.1 and 2.2 apps that do **not** call `SetCompatibilityVersion` is 2.0 compatibility.</span></span> <span data-ttu-id="d6ad6-123">つまり、`SetCompatibilityVersion` を呼び出さないことは、`SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` を呼び出すことと同じです。</span><span class="sxs-lookup"><span data-stu-id="d6ad6-123">That is, not calling `SetCompatibilityVersion` is the same as calling `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.</span></span>

<span data-ttu-id="d6ad6-124">次のコードでは、以下の動作を除き、互換性モードを ASP.NET Core 2.2 に設定します。</span><span class="sxs-lookup"><span data-stu-id="d6ad6-124">The following code sets the compatibility mode to ASP.NET Core 2.2, except for the following behaviors:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowCombiningAuthorizeFilters>
* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.InputFormatterExceptionPolicy>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="d6ad6-125">互換性に影響する重大な変更があったアプリでは、適切な互換性スイッチを使用することにより、次が得られます。</span><span class="sxs-lookup"><span data-stu-id="d6ad6-125">For apps that encounter breaking behavior changes, using the appropriate compatibility switches:</span></span>

* <span data-ttu-id="d6ad6-126">最新のリリースを使用し、互換性に影響する特定の重大な変更をオプトアウトできます。</span><span class="sxs-lookup"><span data-stu-id="d6ad6-126">Allows you to use the latest release and opt out of specific breaking behavior changes.</span></span>
* <span data-ttu-id="d6ad6-127">アプリが最新の変更に対応するよう、更新を行う時間を得られます。</span><span class="sxs-lookup"><span data-stu-id="d6ad6-127">Gives you time to update your app so it works with the latest changes.</span></span>

<span data-ttu-id="d6ad6-128"><xref:Microsoft.AspNetCore.Mvc.MvcOptions> ドキュメントでは、変更があった個所とそれらの改善が多くのユーザーに与えるメリットについて説明しています。</span><span class="sxs-lookup"><span data-stu-id="d6ad6-128">The <xref:Microsoft.AspNetCore.Mvc.MvcOptions> documentation has a good explanation of what changed and why the changes are an improvement for most users.</span></span>

<span data-ttu-id="d6ad6-129">ASP.NET Core 3.0 では、互換性スイッチでサポートされる古い動作は削除されました。</span><span class="sxs-lookup"><span data-stu-id="d6ad6-129">With ASP.NET Core 3.0, old behaviors supported by compatibility switches have been removed.</span></span> <span data-ttu-id="d6ad6-130">これらの正の変更は、ほぼすべてのユーザーにとってメリットとなると感じています。</span><span class="sxs-lookup"><span data-stu-id="d6ad6-130">We feel these are positive changes benefitting nearly all users.</span></span> <span data-ttu-id="d6ad6-131">これらの変更を 2.1 および 2.2 に導入することで、ほとんどのアプリにメリットがありますが、更新が必要になるアプリもあります。</span><span class="sxs-lookup"><span data-stu-id="d6ad6-131">By introducing these changes in 2.1 and 2.2, most apps can benefit, while others have time to update.</span></span>
::: moniker-end
