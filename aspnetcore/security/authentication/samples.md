---
title: ASP.NET Core の認証サンプル
author: rick-anderson
description: ASP.NET Core リポジトリの認証サンプルへのリンクを提供します。
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: d49aef198e926d88f1a6727f84b06f0861c8812d
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187300"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="9f5f3-103">ASP.NET Core の認証サンプル</span><span class="sxs-lookup"><span data-stu-id="9f5f3-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="9f5f3-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9f5f3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9f5f3-105">[ASP.NET Core リポジトリ](https://github.com/aspnet/AspNetCore)の*AspNetCore/src/Security/samples*フォルダーには次の通り認証についてのサンプルが含まれています 。</span><span class="sxs-lookup"><span data-stu-id="9f5f3-105">The [ASP.NET Core repository](https://github.com/aspnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="9f5f3-106">要求の変換</span><span class="sxs-lookup"><span data-stu-id="9f5f3-106">Claims transformation</span></span>](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="9f5f3-107">Cookie 認証</span><span class="sxs-lookup"><span data-stu-id="9f5f3-107">Cookie authentication</span></span>](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/Cookies)
* [<span data-ttu-id="9f5f3-108">カスタムポリシープロバイダー-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="9f5f3-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="9f5f3-109">動的な認証スキームとオプション</span><span class="sxs-lookup"><span data-stu-id="9f5f3-109">Dynamic authentication schemes and options</span></span>](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="9f5f3-110">外部クレーム</span><span class="sxs-lookup"><span data-stu-id="9f5f3-110">External claims</span></span>](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="9f5f3-111">要求に基づくCookieと別の認証スキームの選択</span><span class="sxs-lookup"><span data-stu-id="9f5f3-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="9f5f3-112">静的ファイルへのアクセス制限</span><span class="sxs-lookup"><span data-stu-id="9f5f3-112">Restricts access to static files</span></span>](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="9f5f3-113">サンプルの実行</span><span class="sxs-lookup"><span data-stu-id="9f5f3-113">Run the samples</span></span>

* <span data-ttu-id="9f5f3-114">[ブランチ](https://github.com/aspnet/AspNetCore)を選択します。</span><span class="sxs-lookup"><span data-stu-id="9f5f3-114">Select a [branch](https://github.com/aspnet/AspNetCore).</span></span> <span data-ttu-id="9f5f3-115">例、`Tag:v3.0.0`</span><span class="sxs-lookup"><span data-stu-id="9f5f3-115">For example, `Tag:v3.0.0`</span></span>
* <span data-ttu-id="9f5f3-116">[ASP.NET Core リポジトリ](https://github.com/aspnet/AspNetCore)を複製またはダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="9f5f3-116">Clone or download the [ASP.NET Core repository](https://github.com/aspnet/AspNetCore).</span></span>
* <span data-ttu-id="9f5f3-117">複製したASP.NET Core リポジトリと一致するバージョンの[.NET Core SDK](https://www.microsoft.com/net/download/all)がインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9f5f3-117">Verify you have installed the [.NET Core SDK](https://www.microsoft.com/net/download/all) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="9f5f3-118">*AspNetCore/src/Security/samples*以下のサンプルに移動し、`dotnet run`でサンプルを実行します。</span><span class="sxs-lookup"><span data-stu-id="9f5f3-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9f5f3-119">[ASP.NET Core リポジトリ](https://github.com/aspnet/AspNetCore)の*AspNetCore/src/Security/samples*フォルダーには次の通り認証についてのサンプルが含まれています 。</span><span class="sxs-lookup"><span data-stu-id="9f5f3-119">The [ASP.NET Core repository](https://github.com/aspnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="9f5f3-120">要求の変換</span><span class="sxs-lookup"><span data-stu-id="9f5f3-120">Claims transformation</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="9f5f3-121">Cookie 認証</span><span class="sxs-lookup"><span data-stu-id="9f5f3-121">Cookie authentication</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [<span data-ttu-id="9f5f3-122">カスタムポリシープロバイダー-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="9f5f3-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="9f5f3-123">動的な認証スキームとオプション</span><span class="sxs-lookup"><span data-stu-id="9f5f3-123">Dynamic authentication schemes and options</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="9f5f3-124">外部クレーム</span><span class="sxs-lookup"><span data-stu-id="9f5f3-124">External claims</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="9f5f3-125">要求に基づくCookieと別の認証スキームの選択</span><span class="sxs-lookup"><span data-stu-id="9f5f3-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="9f5f3-126">静的ファイルへのアクセス制限</span><span class="sxs-lookup"><span data-stu-id="9f5f3-126">Restricts access to static files</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="9f5f3-127">サンプルの実行</span><span class="sxs-lookup"><span data-stu-id="9f5f3-127">Run the samples</span></span>

* <span data-ttu-id="9f5f3-128">[ブランチ](https://github.com/aspnet/AspNetCore)を選択します。</span><span class="sxs-lookup"><span data-stu-id="9f5f3-128">Select a [branch](https://github.com/aspnet/AspNetCore).</span></span> <span data-ttu-id="9f5f3-129">例、`release/2.2`</span><span class="sxs-lookup"><span data-stu-id="9f5f3-129">For example, `release/2.2`</span></span>
* <span data-ttu-id="9f5f3-130">[ASP.NET Core リポジトリ](https://github.com/aspnet/AspNetCore)を複製またはダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="9f5f3-130">Clone or download the [ASP.NET Core repository](https://github.com/aspnet/AspNetCore).</span></span>
* <span data-ttu-id="9f5f3-131">複製したASP.NET Core リポジトリと一致するバージョンの[.NET Core SDK](https://www.microsoft.com/net/download/all)がインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9f5f3-131">Verify you have installed the [.NET Core SDK](https://www.microsoft.com/net/download/all) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="9f5f3-132">*AspNetCore/src/Security/samples*以下のサンプルに移動し、`dotnet run`でサンプルを実行します。</span><span class="sxs-lookup"><span data-stu-id="9f5f3-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
