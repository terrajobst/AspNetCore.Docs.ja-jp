---
title: ASP.NET Core Web SDK
author: Rick-Anderson
description: Microsoft .NET の概要について説明します。
ms.author: riande
ms.date: 01/25/2020
no-loc:
- Blazor
uid: razor-pages/web-sdk
ms.openlocfilehash: 49e21e1a432149409a01550452cedf4009dcfba7
ms.sourcegitcommit: b5ceb0a46d0254cc3425578116e2290142eec0f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2020
ms.locfileid: "76830646"
---
# <a name="aspnet-core-web-sdk"></a><span data-ttu-id="36969-103">ASP.NET Core Web SDK</span><span class="sxs-lookup"><span data-stu-id="36969-103">ASP.NET Core Web SDK</span></span>

### <a name="overview"></a><span data-ttu-id="36969-104">の概要</span><span class="sxs-lookup"><span data-stu-id="36969-104">Overview</span></span>

<span data-ttu-id="36969-105">`Microsoft.NET.Sdk.Web` は ASP.NET Core アプリを構築するための[MSBuild プロジェクト SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk)です。</span><span class="sxs-lookup"><span data-stu-id="36969-105">`Microsoft.NET.Sdk.Web` is a [MSBuild project SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) for building ASP.NET Core apps.</span></span> <span data-ttu-id="36969-106">ただし、この SDK を使用せずに ASP.NET Core アプリをビルドすることはできますが、Web SDK は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="36969-106">It's possible to build an ASP.NET Core app without this SDK, however, the Web SDK is:</span></span>

* <span data-ttu-id="36969-107">ファーストクラスのエクスペリエンスを提供することに合わせて調整します。</span><span class="sxs-lookup"><span data-stu-id="36969-107">Tailored towards providing a first-class experience.</span></span>
* <span data-ttu-id="36969-108">ほとんどのユーザーに推奨されるターゲット。</span><span class="sxs-lookup"><span data-stu-id="36969-108">The recommended target for most users.</span></span>

<span data-ttu-id="36969-109">プロジェクト内の Web SDK を使用します。</span><span class="sxs-lookup"><span data-stu-id="36969-109">Use the Web.SDK in a project:</span></span>

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

<span data-ttu-id="36969-110">Web SDK を使用して有効にする機能:</span><span class="sxs-lookup"><span data-stu-id="36969-110">Features enabled by using the Web SDK:</span></span>

* <span data-ttu-id="36969-111">.NET Core 3.0 以降を対象とするプロジェクトは、次のように暗黙的に参照します。</span><span class="sxs-lookup"><span data-stu-id="36969-111">Projects targeting .NET Core 3.0 or later implicitly reference:</span></span>

  * <span data-ttu-id="36969-112">[ASP.NET Core 共有フレームワーク](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="36969-112">The [ASP.NET Core shared framework](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="36969-113">ASP.NET Core アプリを構築するために設計された[アナライザー](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) 。</span><span class="sxs-lookup"><span data-stu-id="36969-113">[Analyzers](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) designed for building ASP.NET Core apps.</span></span>
* <span data-ttu-id="36969-114">WebSDK を使用すると、発行プロファイルの使用を有効にする MSBuild ターゲットと WebDeploy を使用した発行を有効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="36969-114">The WebSDK enables MSBuild targets that enables the use of publish profiles, and publishing using WebDeploy.</span></span>

### <a name="properties"></a><span data-ttu-id="36969-115">[プロパティ]</span><span class="sxs-lookup"><span data-stu-id="36969-115">Properties</span></span>

| <span data-ttu-id="36969-116">property</span><span class="sxs-lookup"><span data-stu-id="36969-116">Property</span></span> | <span data-ttu-id="36969-117">説明</span><span class="sxs-lookup"><span data-stu-id="36969-117">Description</span></span> |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | <span data-ttu-id="36969-118">`Microsoft.AspNetCore.App` 共有フレームワークへの暗黙的な参照を無効にします。</span><span class="sxs-lookup"><span data-stu-id="36969-118">Disables implicit reference to the `Microsoft.AspNetCore.App` shared framework.</span></span> |
| `DisableImplicitAspNetCoreAnalyzers` | <span data-ttu-id="36969-119">ASP.NET Core アナライザーへの暗黙的な参照を無効にします。</span><span class="sxs-lookup"><span data-stu-id="36969-119">Disables implicit reference to ASP.NET Core analyzers.</span></span> |
| `DisableImplicitComponentsAnalyzers` | <span data-ttu-id="36969-120">Blazor (サーバー) アプリケーションをビルドするときに、Razor コンポーネントアナライザーへの暗黙的な参照を無効にします。</span><span class="sxs-lookup"><span data-stu-id="36969-120">Disables implicit reference to Razor Components analyzers when building Blazor (server) applications.</span></span> |
