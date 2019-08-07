---
title: ASP.NET Core のクライアント IP セーフセーフ
author: damienbod
description: 承認済み IP アドレスの一覧に対してリモート IP アドレスを検証するミドルウェアまたはアクションフィルターを作成する方法について説明します。
ms.author: tdykstra
ms.custom: mvc
ms.date: 08/31/2018
uid: security/ip-safelist
ms.openlocfilehash: ca05989efabea3a71c6912e98055a6746e0f5966
ms.sourcegitcommit: 1bf80f4acd62151ff8cce517f03f6fa891136409
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68223928"
---
# <a name="client-ip-safelist-for-aspnet-core"></a><span data-ttu-id="b6693-103">ASP.NET Core のクライアント IP セーフセーフ</span><span class="sxs-lookup"><span data-stu-id="b6693-103">Client IP safelist for ASP.NET Core</span></span>

<span data-ttu-id="b6693-104">By [Damien Bowden](https://twitter.com/damien_bod)および[Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="b6693-104">By [Damien Bowden](https://twitter.com/damien_bod) and [Tom Dykstra](https://github.com/tdykstra)</span></span>
 
<span data-ttu-id="b6693-105">この記事では、ASP.NET Core アプリで IP セーフリスト (ホワイトリストとも呼ばれます) を実装する3つの方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="b6693-105">This article shows three ways to implement an IP safelist (also known as a whitelist) in an ASP.NET Core app.</span></span> <span data-ttu-id="b6693-106">次のものを使用できます。</span><span class="sxs-lookup"><span data-stu-id="b6693-106">You can use:</span></span>

* <span data-ttu-id="b6693-107">ミドルウェア。すべての要求のリモート IP アドレスを確認します。</span><span class="sxs-lookup"><span data-stu-id="b6693-107">Middleware to check the remote IP address of every request.</span></span>
* <span data-ttu-id="b6693-108">アクションフィルターを使用して、特定のコントローラーまたはアクションメソッドに対する要求のリモート IP アドレスを確認します。</span><span class="sxs-lookup"><span data-stu-id="b6693-108">Action filters to check the remote IP address of requests for specific controllers or action methods.</span></span>
* <span data-ttu-id="b6693-109">Razor Pages フィルターを使用して、Razor ページの要求のリモート IP アドレスを確認します。</span><span class="sxs-lookup"><span data-stu-id="b6693-109">Razor Pages filters to check the remote IP address of requests for Razor pages.</span></span>

<span data-ttu-id="b6693-110">どちらの場合も、承認済みのクライアント IP アドレスを含む文字列は、アプリ設定に格納されます。</span><span class="sxs-lookup"><span data-stu-id="b6693-110">In each case, a string containing approved client IP addresses is stored in an app setting.</span></span> <span data-ttu-id="b6693-111">ミドルウェアまたはフィルタは、文字列を一覧に解析し、リモート IP が一覧に含まれているかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="b6693-111">The middleware or filter parses the string into a list and checks if the remote IP is in the list.</span></span> <span data-ttu-id="b6693-112">それ以外の場合は、HTTP 403 の許可されていない状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="b6693-112">If not, an HTTP 403 Forbidden status code is returned.</span></span>

<span data-ttu-id="b6693-113">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="b6693-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="the-safelist"></a><span data-ttu-id="b6693-114">セーフセーフ</span><span class="sxs-lookup"><span data-stu-id="b6693-114">The safelist</span></span>

<span data-ttu-id="b6693-115">この一覧は、 *appsettings*ファイルで構成されています。</span><span class="sxs-lookup"><span data-stu-id="b6693-115">The list is configured in the *appsettings.json* file.</span></span> <span data-ttu-id="b6693-116">これはセミコロンで区切られたリストで、IPv4 と IPv6 のアドレスを含むことができます。</span><span class="sxs-lookup"><span data-stu-id="b6693-116">It's a semicolon-delimited list and can contain IPv4 and IPv6 addresses.</span></span>

[!code-json[](ip-safelist/samples/2.x/ClientIpAspNetCore/appsettings.json?highlight=2)]

## <a name="middleware"></a><span data-ttu-id="b6693-117">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="b6693-117">Middleware</span></span>

<span data-ttu-id="b6693-118">メソッド`Configure`は、ミドルウェアを追加し、そのセーフの文字列をコンストラクターパラメーターに渡します。</span><span class="sxs-lookup"><span data-stu-id="b6693-118">The `Configure` method adds the middleware and passes the safelist string to it in a constructor parameter.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="b6693-119">ミドルウェアは、文字列を配列に解析し、配列内のリモート IP アドレスを検索します。</span><span class="sxs-lookup"><span data-stu-id="b6693-119">The middleware parses the string into an array and looks for the remote IP address in the array.</span></span> <span data-ttu-id="b6693-120">リモート IP アドレスが見つからない場合、ミドルウェアは HTTP 401 禁止を返します。</span><span class="sxs-lookup"><span data-stu-id="b6693-120">If the remote IP address is not found, the middleware returns HTTP 401 Forbidden.</span></span> <span data-ttu-id="b6693-121">この検証プロセスは、HTTP Get 要求ではバイパスされます。</span><span class="sxs-lookup"><span data-stu-id="b6693-121">This validation process is bypassed for HTTP Get requests.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a><span data-ttu-id="b6693-122">アクションフィルター</span><span class="sxs-lookup"><span data-stu-id="b6693-122">Action filter</span></span>

<span data-ttu-id="b6693-123">特定のコントローラーまたはアクションメソッドに対してのみセーフセーフを使用する場合は、アクションフィルターを使用します。</span><span class="sxs-lookup"><span data-stu-id="b6693-123">If you want a safelist only for specific controllers or action methods, use an action filter.</span></span> <span data-ttu-id="b6693-124">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="b6693-124">Here's an example:</span></span> 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckFilter.cs)]

<span data-ttu-id="b6693-125">アクションフィルターがサービスコンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="b6693-125">The action filter is added to the services container.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

<span data-ttu-id="b6693-126">その後、コントローラーまたはアクションメソッドでフィルターを使用できます。</span><span class="sxs-lookup"><span data-stu-id="b6693-126">The filter can then be used on a controller or action method.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_Filter&highlight=1)]

<span data-ttu-id="b6693-127">サンプルアプリでは、フィルターが`Get`メソッドに適用されます。</span><span class="sxs-lookup"><span data-stu-id="b6693-127">In the sample app, the filter is applied to the `Get` method.</span></span> <span data-ttu-id="b6693-128">そのため、 `Get` API 要求を送信してアプリをテストする場合、属性はクライアントの IP アドレスを検証しています。</span><span class="sxs-lookup"><span data-stu-id="b6693-128">So when you test the app by sending a `Get` API request, the attribute is validating the client IP address.</span></span> <span data-ttu-id="b6693-129">他の HTTP メソッドを使用して API を呼び出すことによってテストする場合、ミドルウェアはクライアント IP を検証しています。</span><span class="sxs-lookup"><span data-stu-id="b6693-129">When you test by calling the API with any other HTTP method, the middleware is validating the client IP.</span></span>

## <a name="razor-pages-filter"></a><span data-ttu-id="b6693-130">Razor Pages フィルター</span><span class="sxs-lookup"><span data-stu-id="b6693-130">Razor Pages filter</span></span> 

<span data-ttu-id="b6693-131">Razor Pages アプリのセーフセーフを必要とする場合は、Razor Pages フィルターを使用します。</span><span class="sxs-lookup"><span data-stu-id="b6693-131">If you want a safelist for a Razor Pages app, use a Razor Pages filter.</span></span> <span data-ttu-id="b6693-132">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="b6693-132">Here's an example:</span></span> 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckPageFilter.cs)]

<span data-ttu-id="b6693-133">このフィルターは、MVC フィルターコレクションに追加することで有効になります。</span><span class="sxs-lookup"><span data-stu-id="b6693-133">This filter is enabled by adding it to the MVC Filters collection.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

<span data-ttu-id="b6693-134">アプリを実行して Razor ページを要求すると、Razor Pages フィルターによってクライアント IP が検証されます。</span><span class="sxs-lookup"><span data-stu-id="b6693-134">When you run the app and request a Razor page, the Razor Pages filter is validating the client IP.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b6693-135">次の手順</span><span class="sxs-lookup"><span data-stu-id="b6693-135">Next steps</span></span>

<span data-ttu-id="b6693-136">[ASP.NET Core ミドルウェアの詳細についてはこちらをご覧](xref:fundamentals/middleware/index)ください。</span><span class="sxs-lookup"><span data-stu-id="b6693-136">[Learn more about ASP.NET Core Middleware](xref:fundamentals/middleware/index).</span></span>
