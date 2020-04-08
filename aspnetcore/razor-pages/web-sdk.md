---
title: ASP.NET Core Web SDK
author: Rick-Anderson
description: Microsoft.NET.Sdk.Web の概要。
ms.author: riande
ms.date: 01/25/2020
no-loc:
- Blazor
uid: razor-pages/web-sdk
ms.openlocfilehash: 6a9d531efd2188aed525c949bb124914c31119db
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78648206"
---
# <a name="aspnet-core-web-sdk"></a><span data-ttu-id="1e03c-103">ASP.NET Core Web SDK</span><span class="sxs-lookup"><span data-stu-id="1e03c-103">ASP.NET Core Web SDK</span></span>

### <a name="overview"></a><span data-ttu-id="1e03c-104">概要</span><span class="sxs-lookup"><span data-stu-id="1e03c-104">Overview</span></span>

<span data-ttu-id="1e03c-105">`Microsoft.NET.Sdk.Web` は、ASP.NET Core アプリをビルドするための [MSBuild プロジェクト SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) です。</span><span class="sxs-lookup"><span data-stu-id="1e03c-105">`Microsoft.NET.Sdk.Web` is an [MSBuild project SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) for building ASP.NET Core apps.</span></span> <span data-ttu-id="1e03c-106">この SDK を使用せずに ASP.NET Core アプリをビルドすることもできますが、Web SDK は、</span><span class="sxs-lookup"><span data-stu-id="1e03c-106">It's possible to build an ASP.NET Core app without this SDK, however, the Web SDK is:</span></span>

* <span data-ttu-id="1e03c-107">最上級のエクスペリエンスを提供するために作成されています。</span><span class="sxs-lookup"><span data-stu-id="1e03c-107">Tailored towards providing a first-class experience.</span></span>
* <span data-ttu-id="1e03c-108">ほとんどのユーザーに推奨されるターゲットです。</span><span class="sxs-lookup"><span data-stu-id="1e03c-108">The recommended target for most users.</span></span>

<span data-ttu-id="1e03c-109">プロジェクトで Web SDK を使用する:</span><span class="sxs-lookup"><span data-stu-id="1e03c-109">Use the Web.SDK in a project:</span></span>

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

<span data-ttu-id="1e03c-110">Web SDK を使用して有効にされる機能:</span><span class="sxs-lookup"><span data-stu-id="1e03c-110">Features enabled by using the Web SDK:</span></span>

* <span data-ttu-id="1e03c-111">.NET Core 3.0 以降を対象とするプロジェクトは暗黙的に次を参照します。</span><span class="sxs-lookup"><span data-stu-id="1e03c-111">Projects targeting .NET Core 3.0 or later implicitly reference:</span></span>

  * <span data-ttu-id="1e03c-112">[ASP.NET Core 共有フレームワーク](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="1e03c-112">The [ASP.NET Core shared framework](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="1e03c-113">ASP.NET Core アプリをビルドするために設計された[アナライザー](/visualstudio/extensibility/getting-started-with-roslyn-analyzers)。</span><span class="sxs-lookup"><span data-stu-id="1e03c-113">[Analyzers](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) designed for building ASP.NET Core apps.</span></span>
* <span data-ttu-id="1e03c-114">Web SDK は、発行プロファイルの使用と WebDeploy を使用した発行を可能にする MSBuild ターゲットをインポートします。</span><span class="sxs-lookup"><span data-stu-id="1e03c-114">The Web SDK imports MSBuild targets that enable the use of publish profiles and publishing using WebDeploy.</span></span>

### <a name="properties"></a><span data-ttu-id="1e03c-115">プロパティ</span><span class="sxs-lookup"><span data-stu-id="1e03c-115">Properties</span></span>

| <span data-ttu-id="1e03c-116">property</span><span class="sxs-lookup"><span data-stu-id="1e03c-116">Property</span></span> | <span data-ttu-id="1e03c-117">説明</span><span class="sxs-lookup"><span data-stu-id="1e03c-117">Description</span></span> |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | <span data-ttu-id="1e03c-118">`Microsoft.AspNetCore.App` 共有フレームワークへの暗黙的な参照を無効にします。</span><span class="sxs-lookup"><span data-stu-id="1e03c-118">Disables implicit reference to the `Microsoft.AspNetCore.App` shared framework.</span></span> |
| `DisableImplicitAspNetCoreAnalyzers` | <span data-ttu-id="1e03c-119">ASP.NET Core アナライザーへの暗黙的な参照を無効にします。</span><span class="sxs-lookup"><span data-stu-id="1e03c-119">Disables implicit reference to ASP.NET Core analyzers.</span></span> |
| `DisableImplicitComponentsAnalyzers` | <span data-ttu-id="1e03c-120">Blazor (サーバー) アプリケーションをビルドするときに、Razor コンポーネント アナライザーへの暗黙的な参照を無効にします。</span><span class="sxs-lookup"><span data-stu-id="1e03c-120">Disables implicit reference to Razor Components analyzers when building Blazor (server) applications.</span></span> |
