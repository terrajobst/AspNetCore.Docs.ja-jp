---
title: ASP.NETコアのクライアント IP セーフリスト
author: damienbod
description: ミドルウェアまたはアクション フィルタを記述して、承認された IP アドレスの一覧に対してリモート IP アドレスを検証する方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
uid: security/ip-safelist
ms.openlocfilehash: 2db879a6918245cbacff8b1a5dc15786ffab6a34
ms.sourcegitcommit: 196e4a36df5be5b04fedcff484a4261f8046ec57
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2020
ms.locfileid: "80471803"
---
# <a name="client-ip-safelist-for-aspnet-core"></a><span data-ttu-id="98140-103">ASP.NETコアのクライアント IP セーフリスト</span><span class="sxs-lookup"><span data-stu-id="98140-103">Client IP safelist for ASP.NET Core</span></span>

<span data-ttu-id="98140-104">[ダミアン・ボーデン](https://twitter.com/damien_bod)と[トム・ダイクストラ](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="98140-104">By [Damien Bowden](https://twitter.com/damien_bod) and [Tom Dykstra](https://github.com/tdykstra)</span></span>
 
<span data-ttu-id="98140-105">この記事では、ip アドレス セーフリスト (許可リストとも呼ばれます) を ASP.NET Core アプリに実装する 3 つの方法を示します。</span><span class="sxs-lookup"><span data-stu-id="98140-105">This article shows three ways to implement an IP address safelist (also known as an allow list) in an ASP.NET Core app.</span></span> <span data-ttu-id="98140-106">付属のサンプル アプリは、3 つの方法をすべて示しています。</span><span class="sxs-lookup"><span data-stu-id="98140-106">An accompanying sample app demonstrates all three approaches.</span></span> <span data-ttu-id="98140-107">使用できるもの:</span><span class="sxs-lookup"><span data-stu-id="98140-107">You can use:</span></span>

* <span data-ttu-id="98140-108">ミドルウェアを使用して、すべての要求のリモート IP アドレスを確認します。</span><span class="sxs-lookup"><span data-stu-id="98140-108">Middleware to check the remote IP address of every request.</span></span>
* <span data-ttu-id="98140-109">MVC アクション フィルターを使用して、特定のコントローラーまたはアクション メソッドの要求のリモート IP アドレスを確認します。</span><span class="sxs-lookup"><span data-stu-id="98140-109">MVC action filters to check the remote IP address of requests for specific controllers or action methods.</span></span>
* <span data-ttu-id="98140-110">Razor ページフィルターを使用して、Razor ページの要求のリモート IP アドレスを確認します。</span><span class="sxs-lookup"><span data-stu-id="98140-110">Razor Pages filters to check the remote IP address of requests for Razor pages.</span></span>

<span data-ttu-id="98140-111">いずれの場合も、承認されたクライアント IP アドレスを含む文字列がアプリ設定に格納されます。</span><span class="sxs-lookup"><span data-stu-id="98140-111">In each case, a string containing approved client IP addresses is stored in an app setting.</span></span> <span data-ttu-id="98140-112">ミドルウェアまたはフィルタ:</span><span class="sxs-lookup"><span data-stu-id="98140-112">The middleware or filter:</span></span>

* <span data-ttu-id="98140-113">文字列を配列に解析します。</span><span class="sxs-lookup"><span data-stu-id="98140-113">Parses the string into an array.</span></span> 
* <span data-ttu-id="98140-114">アレイ内にリモート IP アドレスが存在するかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="98140-114">Checks if the remote IP address exists in the array.</span></span>

<span data-ttu-id="98140-115">アレイに IP アドレスが含まれている場合は、アクセスが許可されます。</span><span class="sxs-lookup"><span data-stu-id="98140-115">Access is allowed if the array contains the IP address.</span></span> <span data-ttu-id="98140-116">それ以外の場合は、HTTP 403 の禁止状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="98140-116">Otherwise, an HTTP 403 Forbidden status code is returned.</span></span>

<span data-ttu-id="98140-117">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="98140-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ip-address-safelist"></a><span data-ttu-id="98140-118">IP アドレス セーフリスト</span><span class="sxs-lookup"><span data-stu-id="98140-118">IP address safelist</span></span>

<span data-ttu-id="98140-119">サンプル アプリでは、IP アドレス セーフリストは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="98140-119">In the sample app, the IP address safelist is:</span></span>

* <span data-ttu-id="98140-120">`AdminSafeList` *appsettings.json*ファイルのプロパティによって定義されます。</span><span class="sxs-lookup"><span data-stu-id="98140-120">Defined by the `AdminSafeList` property in the *appsettings.json* file.</span></span>
* <span data-ttu-id="98140-121">[インターネット プロトコル バージョン 4 (IPv4) とインターネット プロトコル バージョン 6 (IPv6)](https://wikipedia.org/wiki/IPv4)アドレスの両方を含むセミコロン区切りの文字列。 [Internet Protocol version 6 (IPv6)](https://wikipedia.org/wiki/IPv6)</span><span class="sxs-lookup"><span data-stu-id="98140-121">A semicolon-delimited string that may contain both [Internet Protocol version 4 (IPv4)](https://wikipedia.org/wiki/IPv4) and [Internet Protocol version 6 (IPv6)](https://wikipedia.org/wiki/IPv6) addresses.</span></span>

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

<span data-ttu-id="98140-122">前の例では、IPv4`127.0.0.1`アドレスおよび`192.168.1.5`IPv6 ループバック アドレス`::1`( の`0:0:0:0:0:0:0:1`圧縮形式 ) が許可されています。</span><span class="sxs-lookup"><span data-stu-id="98140-122">In the preceding example, the IPv4 addresses of `127.0.0.1` and `192.168.1.5` and the IPv6 loopback address of `::1` (compressed format for `0:0:0:0:0:0:0:1`) are allowed.</span></span>

## <a name="middleware"></a><span data-ttu-id="98140-123">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="98140-123">Middleware</span></span>

<span data-ttu-id="98140-124">この`Startup.Configure`メソッドは、カスタム`AdminSafeListMiddleware`ミドルウェアの種類をアプリの要求パイプラインに追加します。</span><span class="sxs-lookup"><span data-stu-id="98140-124">The `Startup.Configure` method adds the custom `AdminSafeListMiddleware` middleware type to the app's request pipeline.</span></span> <span data-ttu-id="98140-125">セーフリストは .NET Core 構成プロバイダーを使用して取得され、コンストラクター パラメーターとして渡されます。</span><span class="sxs-lookup"><span data-stu-id="98140-125">The safelist is retrieved with the .NET Core configuration provider and is passed as a constructor parameter.</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

<span data-ttu-id="98140-126">ミドルウェアは文字列を解析して配列にし、配列内のリモート IP アドレスを検索します。</span><span class="sxs-lookup"><span data-stu-id="98140-126">The middleware parses the string into an array and searches for the remote IP address in the array.</span></span> <span data-ttu-id="98140-127">リモート IP アドレスが見つからない場合、ミドルウェアは HTTP 403 Forbidden を返します。</span><span class="sxs-lookup"><span data-stu-id="98140-127">If the remote IP address isn't found, the middleware returns HTTP 403 Forbidden.</span></span> <span data-ttu-id="98140-128">この検証プロセスは、HTTP GET 要求に対してバイパスされます。</span><span class="sxs-lookup"><span data-stu-id="98140-128">This validation process is bypassed for HTTP GET requests.</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a><span data-ttu-id="98140-129">アクション フィルター</span><span class="sxs-lookup"><span data-stu-id="98140-129">Action filter</span></span>

<span data-ttu-id="98140-130">特定の MVC コントローラーまたはアクション メソッドのセーフリスト駆動型アクセス制御が必要な場合は、アクション フィルターを使用します。</span><span class="sxs-lookup"><span data-stu-id="98140-130">If you want safelist-driven access control for specific MVC controllers or action methods, use an action filter.</span></span> <span data-ttu-id="98140-131">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="98140-131">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="98140-132">で`Startup.ConfigureServices`、アクション フィルターを MVC フィルター コレクションに追加します。</span><span class="sxs-lookup"><span data-stu-id="98140-132">In `Startup.ConfigureServices`, add the action filter to the MVC filters collection.</span></span> <span data-ttu-id="98140-133">次の例では、`ClientIpCheckActionFilter`アクション フィルターが追加されます。</span><span class="sxs-lookup"><span data-stu-id="98140-133">In the following example, a `ClientIpCheckActionFilter` action filter is added.</span></span> <span data-ttu-id="98140-134">セーフリストとコンソール ロガーインスタンスは、コンストラクターパラメーターとして渡されます。</span><span class="sxs-lookup"><span data-stu-id="98140-134">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

<span data-ttu-id="98140-135">アクション フィルターは[、[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute)属性を使用して、コントローラーまたはアクション メソッドに適用できます。</span><span class="sxs-lookup"><span data-stu-id="98140-135">The action filter can then be applied to a controller or action method with the [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) attribute:</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

<span data-ttu-id="98140-136">サンプル アプリでは、アクション フィルターがコントローラーの`Get`アクション メソッドに適用されます。</span><span class="sxs-lookup"><span data-stu-id="98140-136">In the sample app, the action filter is applied to the controller's `Get` action method.</span></span> <span data-ttu-id="98140-137">送信してアプリをテストする場合:</span><span class="sxs-lookup"><span data-stu-id="98140-137">When you test the app by sending:</span></span>

* <span data-ttu-id="98140-138">HTTP GET 要求の`[ServiceFilter]`属性は、クライアント IP アドレスを検証します。</span><span class="sxs-lookup"><span data-stu-id="98140-138">An HTTP GET request, the `[ServiceFilter]` attribute validates the client IP address.</span></span> <span data-ttu-id="98140-139">アクション メソッドへのアクセスが`Get`許可されている場合、アクション フィルターとアクション メソッドによって次のコンソール出力のバリエーションが生成されます。</span><span class="sxs-lookup"><span data-stu-id="98140-139">If access is allowed to the `Get` action method, a variation of the following console output is produced by the action filter and action method:</span></span>

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* <span data-ttu-id="98140-140">GET 以外の HTTP 要求動詞`AdminSafeListMiddleware`は、ミドルウェアがクライアント IP アドレスを検証します。</span><span class="sxs-lookup"><span data-stu-id="98140-140">An HTTP request verb other than GET, the `AdminSafeListMiddleware` middleware validates the client IP address.</span></span>

## <a name="razor-pages-filter"></a><span data-ttu-id="98140-141">カミソリページフィルター</span><span class="sxs-lookup"><span data-stu-id="98140-141">Razor Pages filter</span></span>

<span data-ttu-id="98140-142">Razor ページ アプリのセーフリスト駆動型アクセス制御が必要な場合は、Razor ページ フィルターを使用します。</span><span class="sxs-lookup"><span data-stu-id="98140-142">If you want safelist-driven access control for a Razor Pages app, use a Razor Pages filter.</span></span> <span data-ttu-id="98140-143">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="98140-143">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="98140-144">で`Startup.ConfigureServices`、MVC フィルター コレクションに追加して、Razor ページ フィルターを有効にします。</span><span class="sxs-lookup"><span data-stu-id="98140-144">In `Startup.ConfigureServices`, enable the Razor Pages filter by adding it to the MVC filters collection.</span></span> <span data-ttu-id="98140-145">次の例では`ClientIpCheckPageFilter`、Razor ページ フィルターが追加されます。</span><span class="sxs-lookup"><span data-stu-id="98140-145">In the following example, a `ClientIpCheckPageFilter` Razor Pages filter is added.</span></span> <span data-ttu-id="98140-146">セーフリストとコンソール ロガーインスタンスは、コンストラクターパラメーターとして渡されます。</span><span class="sxs-lookup"><span data-stu-id="98140-146">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

<span data-ttu-id="98140-147">サンプル アプリの *[インデックス Razor]* ページが要求されると、Razor ページ フィルターはクライアントの IP アドレスを検証します。</span><span class="sxs-lookup"><span data-stu-id="98140-147">When the sample app's *Index* Razor page is requested, the Razor Pages filter validates the client IP address.</span></span> <span data-ttu-id="98140-148">このフィルターは、次のコンソール出力のバリエーションを生成します。</span><span class="sxs-lookup"><span data-stu-id="98140-148">The filter produces a variation of the following console output:</span></span>

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a><span data-ttu-id="98140-149">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="98140-149">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="98140-150">アクション フィルター</span><span class="sxs-lookup"><span data-stu-id="98140-150">Action filters</span></span>](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
