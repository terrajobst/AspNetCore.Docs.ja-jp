---
title: ASP.NET Core Web SDK
author: Rick-Anderson
description: Microsoft .NET の概要について説明します。
ms.author: riande
ms.date: 01/25/2020
no-loc:
- Blazor
uid: razor-pages/web-sdk
ms.openlocfilehash: 6a9d531efd2188aed525c949bb124914c31119db
ms.sourcegitcommit: fe41cff0b99f3920b727286944e5b652ca301640
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2020
ms.locfileid: "76869767"
---
# <a name="aspnet-core-web-sdk"></a><span data-ttu-id="58a27-103">ASP.NET Core Web SDK</span><span class="sxs-lookup"><span data-stu-id="58a27-103">ASP.NET Core Web SDK</span></span>

### <a name="overview"></a><span data-ttu-id="58a27-104">の概要</span><span class="sxs-lookup"><span data-stu-id="58a27-104">Overview</span></span>

<span data-ttu-id="58a27-105">`Microsoft.NET.Sdk.Web` は ASP.NET Core アプリを構築するための[MSBuild プロジェクト SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk)です。</span><span class="sxs-lookup"><span data-stu-id="58a27-105">`Microsoft.NET.Sdk.Web` is an [MSBuild project SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) for building ASP.NET Core apps.</span></span> <span data-ttu-id="58a27-106">ただし、この SDK を使用せずに ASP.NET Core アプリをビルドすることはできますが、Web SDK は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="58a27-106">It's possible to build an ASP.NET Core app without this SDK, however, the Web SDK is:</span></span>

* <span data-ttu-id="58a27-107">ファーストクラスのエクスペリエンスを提供することに合わせて調整します。</span><span class="sxs-lookup"><span data-stu-id="58a27-107">Tailored towards providing a first-class experience.</span></span>
* <span data-ttu-id="58a27-108">ほとんどのユーザーに推奨されるターゲット。</span><span class="sxs-lookup"><span data-stu-id="58a27-108">The recommended target for most users.</span></span>

<span data-ttu-id="58a27-109">プロジェクト内の Web SDK を使用します。</span><span class="sxs-lookup"><span data-stu-id="58a27-109">Use the Web.SDK in a project:</span></span>

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

<span data-ttu-id="58a27-110">Web SDK を使用して有効にする機能:</span><span class="sxs-lookup"><span data-stu-id="58a27-110">Features enabled by using the Web SDK:</span></span>

* <span data-ttu-id="58a27-111">.NET Core 3.0 以降を対象とするプロジェクトは、次のように暗黙的に参照します。</span><span class="sxs-lookup"><span data-stu-id="58a27-111">Projects targeting .NET Core 3.0 or later implicitly reference:</span></span>

  * <span data-ttu-id="58a27-112">[ASP.NET Core 共有フレームワーク](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="58a27-112">The [ASP.NET Core shared framework](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="58a27-113">ASP.NET Core アプリを構築するために設計された[アナライザー](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) 。</span><span class="sxs-lookup"><span data-stu-id="58a27-113">[Analyzers](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) designed for building ASP.NET Core apps.</span></span>
* <span data-ttu-id="58a27-114">Web SDK は、Web Deploy を使用して発行プロファイルと発行を使用できるようにする MSBuild ターゲットをインポートします。</span><span class="sxs-lookup"><span data-stu-id="58a27-114">The Web SDK imports MSBuild targets that enable the use of publish profiles and publishing using WebDeploy.</span></span>

### <a name="properties"></a><span data-ttu-id="58a27-115">[プロパティ]</span><span class="sxs-lookup"><span data-stu-id="58a27-115">Properties</span></span>

| <span data-ttu-id="58a27-116">property</span><span class="sxs-lookup"><span data-stu-id="58a27-116">Property</span></span> | <span data-ttu-id="58a27-117">説明</span><span class="sxs-lookup"><span data-stu-id="58a27-117">Description</span></span> |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | <span data-ttu-id="58a27-118">`Microsoft.AspNetCore.App` 共有フレームワークへの暗黙的な参照を無効にします。</span><span class="sxs-lookup"><span data-stu-id="58a27-118">Disables implicit reference to the `Microsoft.AspNetCore.App` shared framework.</span></span> |
| `DisableImplicitAspNetCoreAnalyzers` | <span data-ttu-id="58a27-119">ASP.NET Core アナライザーへの暗黙的な参照を無効にします。</span><span class="sxs-lookup"><span data-stu-id="58a27-119">Disables implicit reference to ASP.NET Core analyzers.</span></span> |
| `DisableImplicitComponentsAnalyzers` | <span data-ttu-id="58a27-120">Blazor (サーバー) アプリケーションをビルドするときに、Razor コンポーネントアナライザーへの暗黙的な参照を無効にします。</span><span class="sxs-lookup"><span data-stu-id="58a27-120">Disables implicit reference to Razor Components analyzers when building Blazor (server) applications.</span></span> |
