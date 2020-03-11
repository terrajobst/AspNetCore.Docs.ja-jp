---
title: ASP.NET Core での承認規則の Razor Pages
author: rick-anderson
description: ユーザーを承認し、匿名ユーザーがページまたはページのフォルダーにアクセスすることを許可する規則を使用して、ページへのアクセスを制御する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: 00fc487c6ac802f213bcf83994ecc2b1a1468589
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653114"
---
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a><span data-ttu-id="e875f-103">ASP.NET Core での承認規則の Razor Pages</span><span class="sxs-lookup"><span data-stu-id="e875f-103">Razor Pages authorization conventions in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e875f-104">Razor Pages アプリでアクセスを制御する方法の1つは、起動時に承認規則を使用することです。</span><span class="sxs-lookup"><span data-stu-id="e875f-104">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="e875f-105">これらの規則を使用すると、ユーザーを承認し、匿名ユーザーが個々のページやページのフォルダーにアクセスすることを許可できます。</span><span class="sxs-lookup"><span data-stu-id="e875f-105">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="e875f-106">このトピックで説明する規則は、アクセスを制御するための[承認フィルター](xref:mvc/controllers/filters#authorization-filters)を自動的に適用します。</span><span class="sxs-lookup"><span data-stu-id="e875f-106">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="e875f-107">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="e875f-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e875f-108">このサンプルアプリでは、 [ASP.NET Core id を使用せずに cookie 認証](xref:security/authentication/cookie)を使用します。</span><span class="sxs-lookup"><span data-stu-id="e875f-108">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="e875f-109">このトピックで示す概念と例は、ASP.NET Core Id を使用するアプリにも同様に適用されます。</span><span class="sxs-lookup"><span data-stu-id="e875f-109">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="e875f-110">ASP.NET Core Id を使用するには、<xref:security/authentication/identity>のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="e875f-110">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="e875f-111">ページへのアクセスに承認を要求する</span><span class="sxs-lookup"><span data-stu-id="e875f-111">Require authorization to access a page</span></span>

<span data-ttu-id="e875f-112"><xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> で <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> 規約を使用して、指定されたパスのページに <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> を追加します。</span><span class="sxs-lookup"><span data-stu-id="e875f-112">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="e875f-113">指定されたパスは、ビューエンジンのパスです。これは、拡張子のない Razor Pages ルートの相対パスで、スラッシュだけを含みます。</span><span class="sxs-lookup"><span data-stu-id="e875f-113">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="e875f-114">[承認ポリシー](xref:security/authorization/policies)を指定するには、 [authorizepage オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)を使用します。</span><span class="sxs-lookup"><span data-stu-id="e875f-114">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="e875f-115"><xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> は、`[Authorize]` filter 属性を使用してページモデルクラスに適用できます。</span><span class="sxs-lookup"><span data-stu-id="e875f-115">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="e875f-116">詳細については、「[承認フィルター属性](xref:razor-pages/filter#authorize-filter-attribute)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e875f-116">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="e875f-117">ページのフォルダーにアクセスするには承認が必要です</span><span class="sxs-lookup"><span data-stu-id="e875f-117">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="e875f-118"><xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> で <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> 規約を使用して、指定したパスにあるフォルダー内のすべてのページに <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> を追加します。</span><span class="sxs-lookup"><span data-stu-id="e875f-118">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="e875f-119">指定されたパスは、Razor Pages ルートの相対パスであるビューエンジンのパスです。</span><span class="sxs-lookup"><span data-stu-id="e875f-119">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="e875f-120">[承認ポリシー](xref:security/authorization/policies)を指定するには、 [authorizefolder オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)を使用します。</span><span class="sxs-lookup"><span data-stu-id="e875f-120">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="e875f-121">区分ページへのアクセスに承認を要求する</span><span class="sxs-lookup"><span data-stu-id="e875f-121">Require authorization to access an area page</span></span>

<span data-ttu-id="e875f-122"><xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> で <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> 規約を使用して、指定したパスの領域ページに <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> を追加します。</span><span class="sxs-lookup"><span data-stu-id="e875f-122">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="e875f-123">ページ名は、指定された領域のページルートディレクトリを基準とした拡張子のないファイルのパスです。</span><span class="sxs-lookup"><span data-stu-id="e875f-123">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="e875f-124">たとえば、ファイル*領域/id/ページ/管理/アカウント*のページ名は、次のように*なります。*</span><span class="sxs-lookup"><span data-stu-id="e875f-124">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="e875f-125">[承認ポリシー](xref:security/authorization/policies)を指定するには、次のように、 [Authorizeareapage オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)を使用します。</span><span class="sxs-lookup"><span data-stu-id="e875f-125">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="e875f-126">領域のフォルダーにアクセスするための承認が必要</span><span class="sxs-lookup"><span data-stu-id="e875f-126">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="e875f-127"><xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> で <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> 規約を使用して、指定したパスにあるフォルダー内のすべての領域に <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> を追加します。</span><span class="sxs-lookup"><span data-stu-id="e875f-127">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="e875f-128">フォルダーパスは、指定された領域のページルートディレクトリを基準としたフォルダーのパスです。</span><span class="sxs-lookup"><span data-stu-id="e875f-128">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="e875f-129">たとえば、[*区分/id/ページ/管理/* *管理*] の下にあるファイルのフォルダーパスを使用します。</span><span class="sxs-lookup"><span data-stu-id="e875f-129">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="e875f-130">[承認ポリシー](xref:security/authorization/policies)を指定するには、 [AuthorizeAreaFolder オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)を使用します。</span><span class="sxs-lookup"><span data-stu-id="e875f-130">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="e875f-131">ページへの匿名アクセスを許可する</span><span class="sxs-lookup"><span data-stu-id="e875f-131">Allow anonymous access to a page</span></span>

<span data-ttu-id="e875f-132"><xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> で <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> 規約を使用して、指定したパスにあるページに <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> を追加します。</span><span class="sxs-lookup"><span data-stu-id="e875f-132">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="e875f-133">指定されたパスは、ビューエンジンのパスです。これは、拡張子のない Razor Pages ルートの相対パスで、スラッシュだけを含みます。</span><span class="sxs-lookup"><span data-stu-id="e875f-133">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="e875f-134">ページのフォルダーへの匿名アクセスを許可する</span><span class="sxs-lookup"><span data-stu-id="e875f-134">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="e875f-135"><xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> で <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> 規約を使用して、指定したパスにあるフォルダー内のすべてのページに <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> を追加します。</span><span class="sxs-lookup"><span data-stu-id="e875f-135">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="e875f-136">指定されたパスは、Razor Pages ルートの相対パスであるビューエンジンのパスです。</span><span class="sxs-lookup"><span data-stu-id="e875f-136">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="e875f-137">承認済みアクセスと匿名アクセスの組み合わせに関する注意事項</span><span class="sxs-lookup"><span data-stu-id="e875f-137">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="e875f-138">ページのフォルダーで承認が必要であることを指定し、そのフォルダー内のページで匿名アクセスが許可されるように指定することが有効です。</span><span class="sxs-lookup"><span data-stu-id="e875f-138">It's valid to specify that a folder of pages requires authorization and then specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="e875f-139">ただし、逆は無効です。</span><span class="sxs-lookup"><span data-stu-id="e875f-139">The reverse, however, isn't valid.</span></span> <span data-ttu-id="e875f-140">匿名アクセス用のページのフォルダーを宣言して、承認が必要なフォルダー内のページを指定することはできません。</span><span class="sxs-lookup"><span data-stu-id="e875f-140">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="e875f-141">プライベートページで承認を要求すると失敗します。</span><span class="sxs-lookup"><span data-stu-id="e875f-141">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="e875f-142"><xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> と <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> の両方がページに適用されると、<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> が優先され、アクセスが制御されます。</span><span class="sxs-lookup"><span data-stu-id="e875f-142">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e875f-143">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="e875f-143">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e875f-144">Razor Pages アプリでアクセスを制御する方法の1つは、起動時に承認規則を使用することです。</span><span class="sxs-lookup"><span data-stu-id="e875f-144">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="e875f-145">これらの規則を使用すると、ユーザーを承認し、匿名ユーザーが個々のページやページのフォルダーにアクセスすることを許可できます。</span><span class="sxs-lookup"><span data-stu-id="e875f-145">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="e875f-146">このトピックで説明する規則は、アクセスを制御するための[承認フィルター](xref:mvc/controllers/filters#authorization-filters)を自動的に適用します。</span><span class="sxs-lookup"><span data-stu-id="e875f-146">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="e875f-147">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="e875f-147">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e875f-148">このサンプルアプリでは、 [ASP.NET Core id を使用せずに cookie 認証](xref:security/authentication/cookie)を使用します。</span><span class="sxs-lookup"><span data-stu-id="e875f-148">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="e875f-149">このトピックで示す概念と例は、ASP.NET Core Id を使用するアプリにも同様に適用されます。</span><span class="sxs-lookup"><span data-stu-id="e875f-149">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="e875f-150">ASP.NET Core Id を使用するには、<xref:security/authentication/identity>のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="e875f-150">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="e875f-151">ページへのアクセスに承認を要求する</span><span class="sxs-lookup"><span data-stu-id="e875f-151">Require authorization to access a page</span></span>

<span data-ttu-id="e875f-152"><xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> で <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> 規約を使用して、指定されたパスのページに <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> を追加します。</span><span class="sxs-lookup"><span data-stu-id="e875f-152">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="e875f-153">指定されたパスは、ビューエンジンのパスです。これは、拡張子のない Razor Pages ルートの相対パスで、スラッシュだけを含みます。</span><span class="sxs-lookup"><span data-stu-id="e875f-153">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="e875f-154">[承認ポリシー](xref:security/authorization/policies)を指定するには、 [authorizepage オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)を使用します。</span><span class="sxs-lookup"><span data-stu-id="e875f-154">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="e875f-155"><xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> は、`[Authorize]` filter 属性を使用してページモデルクラスに適用できます。</span><span class="sxs-lookup"><span data-stu-id="e875f-155">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="e875f-156">詳細については、「[承認フィルター属性](xref:razor-pages/filter#authorize-filter-attribute)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e875f-156">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="e875f-157">ページのフォルダーにアクセスするには承認が必要です</span><span class="sxs-lookup"><span data-stu-id="e875f-157">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="e875f-158"><xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> で <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> 規約を使用して、指定したパスにあるフォルダー内のすべてのページに <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> を追加します。</span><span class="sxs-lookup"><span data-stu-id="e875f-158">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="e875f-159">指定されたパスは、Razor Pages ルートの相対パスであるビューエンジンのパスです。</span><span class="sxs-lookup"><span data-stu-id="e875f-159">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="e875f-160">[承認ポリシー](xref:security/authorization/policies)を指定するには、 [authorizefolder オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)を使用します。</span><span class="sxs-lookup"><span data-stu-id="e875f-160">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="e875f-161">区分ページへのアクセスに承認を要求する</span><span class="sxs-lookup"><span data-stu-id="e875f-161">Require authorization to access an area page</span></span>

<span data-ttu-id="e875f-162"><xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> で <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> 規約を使用して、指定したパスの領域ページに <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> を追加します。</span><span class="sxs-lookup"><span data-stu-id="e875f-162">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="e875f-163">ページ名は、指定された領域のページルートディレクトリを基準とした拡張子のないファイルのパスです。</span><span class="sxs-lookup"><span data-stu-id="e875f-163">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="e875f-164">たとえば、ファイル*領域/id/ページ/管理/アカウント*のページ名は、次のように*なります。*</span><span class="sxs-lookup"><span data-stu-id="e875f-164">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="e875f-165">[承認ポリシー](xref:security/authorization/policies)を指定するには、次のように、 [Authorizeareapage オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)を使用します。</span><span class="sxs-lookup"><span data-stu-id="e875f-165">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="e875f-166">領域のフォルダーにアクセスするための承認が必要</span><span class="sxs-lookup"><span data-stu-id="e875f-166">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="e875f-167"><xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> で <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> 規約を使用して、指定したパスにあるフォルダー内のすべての領域に <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> を追加します。</span><span class="sxs-lookup"><span data-stu-id="e875f-167">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="e875f-168">フォルダーパスは、指定された領域のページルートディレクトリを基準としたフォルダーのパスです。</span><span class="sxs-lookup"><span data-stu-id="e875f-168">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="e875f-169">たとえば、[*区分/id/ページ/管理/* *管理*] の下にあるファイルのフォルダーパスを使用します。</span><span class="sxs-lookup"><span data-stu-id="e875f-169">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="e875f-170">[承認ポリシー](xref:security/authorization/policies)を指定するには、 [AuthorizeAreaFolder オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)を使用します。</span><span class="sxs-lookup"><span data-stu-id="e875f-170">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="e875f-171">ページへの匿名アクセスを許可する</span><span class="sxs-lookup"><span data-stu-id="e875f-171">Allow anonymous access to a page</span></span>

<span data-ttu-id="e875f-172"><xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> で <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> 規約を使用して、指定したパスにあるページに <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> を追加します。</span><span class="sxs-lookup"><span data-stu-id="e875f-172">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="e875f-173">指定されたパスは、ビューエンジンのパスです。これは、拡張子のない Razor Pages ルートの相対パスで、スラッシュだけを含みます。</span><span class="sxs-lookup"><span data-stu-id="e875f-173">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="e875f-174">ページのフォルダーへの匿名アクセスを許可する</span><span class="sxs-lookup"><span data-stu-id="e875f-174">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="e875f-175"><xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> で <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> 規約を使用して、指定したパスにあるフォルダー内のすべてのページに <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> を追加します。</span><span class="sxs-lookup"><span data-stu-id="e875f-175">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="e875f-176">指定されたパスは、Razor Pages ルートの相対パスであるビューエンジンのパスです。</span><span class="sxs-lookup"><span data-stu-id="e875f-176">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="e875f-177">承認済みアクセスと匿名アクセスの組み合わせに関する注意事項</span><span class="sxs-lookup"><span data-stu-id="e875f-177">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="e875f-178">承認を必要とするページのフォルダーを指定し、そのフォルダー内のページで匿名アクセスが許可されるように指定することが有効です。</span><span class="sxs-lookup"><span data-stu-id="e875f-178">It's valid to specify that a folder of pages that require authorization and than specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="e875f-179">ただし、逆は無効です。</span><span class="sxs-lookup"><span data-stu-id="e875f-179">The reverse, however, isn't valid.</span></span> <span data-ttu-id="e875f-180">匿名アクセス用のページのフォルダーを宣言して、承認が必要なフォルダー内のページを指定することはできません。</span><span class="sxs-lookup"><span data-stu-id="e875f-180">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="e875f-181">プライベートページで承認を要求すると失敗します。</span><span class="sxs-lookup"><span data-stu-id="e875f-181">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="e875f-182"><xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> と <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> の両方がページに適用されると、<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> が優先され、アクセスが制御されます。</span><span class="sxs-lookup"><span data-stu-id="e875f-182">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e875f-183">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="e875f-183">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end
