---
title: ASP.NET Core のポリシースキーム
author: rick-anderson
description: 認証ポリシースキームを使用すると、単一の論理認証スキームを簡単に使用できるようになります。
ms.author: riande
ms.date: 12/05/2019
uid: security/authentication/policyschemes
ms.openlocfilehash: f02d8e5cac20a9b60c5eddbd28253efacf682ea1
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880718"
---
# <a name="policy-schemes-in-aspnet-core"></a><span data-ttu-id="ed670-103">ASP.NET Core のポリシースキーム</span><span class="sxs-lookup"><span data-stu-id="ed670-103">Policy schemes in ASP.NET Core</span></span>

<span data-ttu-id="ed670-104">認証ポリシースキームを使用すると、単一の論理認証スキームを使用して、複数の方法を使用することが容易になります。</span><span class="sxs-lookup"><span data-stu-id="ed670-104">Authentication policy schemes make it easier to have a single logical authentication scheme potentially use multiple approaches.</span></span> <span data-ttu-id="ed670-105">たとえば、ポリシースキームは、問題には Google 認証を使用し、それ以外の場合は cookie 認証を使用します。</span><span class="sxs-lookup"><span data-stu-id="ed670-105">For example, a policy scheme might use Google authentication for challenges, and cookie authentication for everything else.</span></span> <span data-ttu-id="ed670-106">認証ポリシーのスキームによって次のようになります。</span><span class="sxs-lookup"><span data-stu-id="ed670-106">Authentication policy schemes make it:</span></span>

* <span data-ttu-id="ed670-107">任意の認証アクションを別のスキームに簡単に転送できます。</span><span class="sxs-lookup"><span data-stu-id="ed670-107">Easy to forward any authentication action to another scheme.</span></span>
* <span data-ttu-id="ed670-108">要求に基づいて動的に転送します。</span><span class="sxs-lookup"><span data-stu-id="ed670-108">Forward dynamically based on the request.</span></span>

<span data-ttu-id="ed670-109">派生 <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> を使用するすべての認証方式と、関連付けられている[Authenticationhandler\<TOptions >](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span><span class="sxs-lookup"><span data-stu-id="ed670-109">All authentication schemes that use derived <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> and the associated [AuthenticationHandler\<TOptions>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span></span>

* <span data-ttu-id="ed670-110">は、2.1 以降の ASP.NET Core で自動的にポリシースキームになります。</span><span class="sxs-lookup"><span data-stu-id="ed670-110">Are automatically policy schemes in ASP.NET Core 2.1 and later.</span></span>
* <span data-ttu-id="ed670-111">は、スキームのオプションを構成することによって有効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="ed670-111">Can be enabled via configuring the scheme's options.</span></span>

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a><span data-ttu-id="ed670-112">使用例</span><span class="sxs-lookup"><span data-stu-id="ed670-112">Examples</span></span>

<span data-ttu-id="ed670-113">次の例は、下位レベルのスキームを結合する上位のスキームを示しています。</span><span class="sxs-lookup"><span data-stu-id="ed670-113">The following example shows a higher level scheme that combines lower level schemes.</span></span> <span data-ttu-id="ed670-114">Google 認証はチャレンジに使用され、その他のすべてに cookie 認証が使用されます。</span><span class="sxs-lookup"><span data-stu-id="ed670-114">Google authentication is used for challenges, and cookie authentication is used for everything else:</span></span>

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

<span data-ttu-id="ed670-115">次の例では、要求ごとにスキームを動的に選択できます。</span><span class="sxs-lookup"><span data-stu-id="ed670-115">The following example enables dynamic selection of schemes on a per request basis.</span></span> <span data-ttu-id="ed670-116">つまり、cookie と API 認証を混在させる方法は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="ed670-116">That is, how to mix cookies and API authentication:</span></span>

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
