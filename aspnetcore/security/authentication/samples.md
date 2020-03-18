---
title: ASP.NET Core の認証サンプル
author: rick-anderson
description: ASP.NET Core リポジトリの認証サンプルへのリンクを提供します。
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: b013d02a65e752bbb61a87a0bf502785125378a2
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511614"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="59468-103">ASP.NET Core の認証サンプル</span><span class="sxs-lookup"><span data-stu-id="59468-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="59468-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="59468-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="59468-105">[ASP.NET Core リポジトリ](https://github.com/dotnet/AspNetCore)には、 *AspNetCore/src/Security/samples*フォルダーに次の認証サンプルが含まれています。</span><span class="sxs-lookup"><span data-stu-id="59468-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="59468-106">要求の変換</span><span class="sxs-lookup"><span data-stu-id="59468-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="59468-107">Cookie 認証</span><span class="sxs-lookup"><span data-stu-id="59468-107">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/Cookies)
* [<span data-ttu-id="59468-108">カスタムポリシープロバイダー-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="59468-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="59468-109">動的な認証スキームとオプション</span><span class="sxs-lookup"><span data-stu-id="59468-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="59468-110">外部要求</span><span class="sxs-lookup"><span data-stu-id="59468-110">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="59468-111">要求に基づいて cookie と別の認証スキームのどちらを選択するか</span><span class="sxs-lookup"><span data-stu-id="59468-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="59468-112">静的ファイルへのアクセスを制限する</span><span class="sxs-lookup"><span data-stu-id="59468-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="59468-113">サンプルを実行する</span><span class="sxs-lookup"><span data-stu-id="59468-113">Run the samples</span></span>

* <span data-ttu-id="59468-114">[ブランチ](https://github.com/dotnet/AspNetCore)を選択してください。</span><span class="sxs-lookup"><span data-stu-id="59468-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="59468-115">たとえば、`Tag:v3.0.0` のように指定します。</span><span class="sxs-lookup"><span data-stu-id="59468-115">For example, `Tag:v3.0.0`</span></span>
* <span data-ttu-id="59468-116">[ASP.NET Core リポジトリ](https://github.com/dotnet/AspNetCore)を複製またはダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="59468-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="59468-117">ASP.NET Core リポジトリの複製に一致する[.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core)バージョンがインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="59468-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="59468-118">*AspNetCore/src/Security/samples*のサンプルに移動し、`dotnet run`でサンプルを実行します。</span><span class="sxs-lookup"><span data-stu-id="59468-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="59468-119">[ASP.NET Core リポジトリ](https://github.com/dotnet/AspNetCore)には、 *AspNetCore/src/Security/samples*フォルダーに次の認証サンプルが含まれています。</span><span class="sxs-lookup"><span data-stu-id="59468-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="59468-120">要求の変換</span><span class="sxs-lookup"><span data-stu-id="59468-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="59468-121">Cookie 認証</span><span class="sxs-lookup"><span data-stu-id="59468-121">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [<span data-ttu-id="59468-122">カスタムポリシープロバイダー-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="59468-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="59468-123">動的な認証スキームとオプション</span><span class="sxs-lookup"><span data-stu-id="59468-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="59468-124">外部要求</span><span class="sxs-lookup"><span data-stu-id="59468-124">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="59468-125">要求に基づいて cookie と別の認証スキームのどちらを選択するか</span><span class="sxs-lookup"><span data-stu-id="59468-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="59468-126">静的ファイルへのアクセスを制限する</span><span class="sxs-lookup"><span data-stu-id="59468-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="59468-127">サンプルを実行する</span><span class="sxs-lookup"><span data-stu-id="59468-127">Run the samples</span></span>

* <span data-ttu-id="59468-128">[ブランチ](https://github.com/dotnet/AspNetCore)を選択してください。</span><span class="sxs-lookup"><span data-stu-id="59468-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="59468-129">たとえば、`release/2.2` のように指定します。</span><span class="sxs-lookup"><span data-stu-id="59468-129">For example, `release/2.2`</span></span>
* <span data-ttu-id="59468-130">[ASP.NET Core リポジトリ](https://github.com/dotnet/AspNetCore)を複製またはダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="59468-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="59468-131">ASP.NET Core リポジトリの複製に一致する[.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core)バージョンがインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="59468-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="59468-132">*AspNetCore/src/Security/samples*のサンプルに移動し、`dotnet run`でサンプルを実行します。</span><span class="sxs-lookup"><span data-stu-id="59468-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
