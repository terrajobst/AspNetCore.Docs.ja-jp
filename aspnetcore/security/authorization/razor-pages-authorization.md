---
title: ASP.NET Core での承認規則の Razor Pages
author: guardrex
description: ユーザーを承認し、匿名ユーザーがページまたはページのフォルダーにアクセスすることを許可する規則を使用して、ページへのアクセスを制御する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: e0102ff64921a83f0330acb6f5d9bfd90f64ca7a
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68994031"
---
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a><span data-ttu-id="b32eb-103">ASP.NET Core での承認規則の Razor Pages</span><span class="sxs-lookup"><span data-stu-id="b32eb-103">Razor Pages authorization conventions in ASP.NET Core</span></span>

<span data-ttu-id="b32eb-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b32eb-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b32eb-105">Razor Pages アプリでアクセスを制御する方法の1つは、起動時に承認規則を使用することです。</span><span class="sxs-lookup"><span data-stu-id="b32eb-105">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="b32eb-106">これらの規則を使用すると、ユーザーを承認し、匿名ユーザーが個々のページやページのフォルダーにアクセスすることを許可できます。</span><span class="sxs-lookup"><span data-stu-id="b32eb-106">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="b32eb-107">このトピックで説明する規則は、アクセスを制御するための[承認フィルター](xref:mvc/controllers/filters#authorization-filters)を自動的に適用します。</span><span class="sxs-lookup"><span data-stu-id="b32eb-107">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="b32eb-108">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="b32eb-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b32eb-109">このサンプルアプリでは、 [ASP.NET Core id を使用せずに cookie 認証](xref:security/authentication/cookie)を使用します。</span><span class="sxs-lookup"><span data-stu-id="b32eb-109">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="b32eb-110">このトピックで示す概念と例は、ASP.NET Core Id を使用するアプリにも同様に適用されます。</span><span class="sxs-lookup"><span data-stu-id="b32eb-110">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="b32eb-111">ASP.NET Core Id を使用するには、「 <xref:security/authentication/identity>」のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="b32eb-111">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="b32eb-112">ページへのアクセスに承認を要求する</span><span class="sxs-lookup"><span data-stu-id="b32eb-112">Require authorization to access a page</span></span>

<span data-ttu-id="b32eb-113">を使用して、指定し<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>たパスのページにを追加します。<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*></span><span class="sxs-lookup"><span data-stu-id="b32eb-113">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="b32eb-114">指定されたパスは、ビューエンジンのパスです。これは、拡張子のない Razor Pages ルートの相対パスで、スラッシュだけを含みます。</span><span class="sxs-lookup"><span data-stu-id="b32eb-114">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="b32eb-115">[承認ポリシー](xref:security/authorization/policies)を指定するには、 [authorizepage オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)を使用します。</span><span class="sxs-lookup"><span data-stu-id="b32eb-115">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="b32eb-116">は<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 、 `[Authorize]`フィルター属性を使用してページモデルクラスに適用できます。</span><span class="sxs-lookup"><span data-stu-id="b32eb-116">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="b32eb-117">詳細については、「[承認フィルター属性](xref:razor-pages/filter#authorize-filter-attribute)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b32eb-117">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="b32eb-118">ページのフォルダーにアクセスするには承認が必要です</span><span class="sxs-lookup"><span data-stu-id="b32eb-118">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="b32eb-119">を使用して、指定し<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>たパスにあるフォルダー内のすべてのページにを追加します。 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*></span><span class="sxs-lookup"><span data-stu-id="b32eb-119">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="b32eb-120">指定されたパスは、Razor Pages ルートの相対パスであるビューエンジンのパスです。</span><span class="sxs-lookup"><span data-stu-id="b32eb-120">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="b32eb-121">[承認ポリシー](xref:security/authorization/policies)を指定するには、 [authorizefolder オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)を使用します。</span><span class="sxs-lookup"><span data-stu-id="b32eb-121">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="b32eb-122">区分ページへのアクセスに承認を要求する</span><span class="sxs-lookup"><span data-stu-id="b32eb-122">Require authorization to access an area page</span></span>

<span data-ttu-id="b32eb-123">を使用して、 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>指定したパスの領域ページにを追加します。<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*></span><span class="sxs-lookup"><span data-stu-id="b32eb-123">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="b32eb-124">ページ名は、指定された領域のページルートディレクトリを基準とした拡張子のないファイルのパスです。</span><span class="sxs-lookup"><span data-stu-id="b32eb-124">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="b32eb-125">たとえば、ファイル*領域/id/ページ/管理/アカウント*のページ名は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="b32eb-125">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="b32eb-126">[承認ポリシー](xref:security/authorization/policies)を指定するには、次のように、 [Authorizeareapage オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)を使用します。</span><span class="sxs-lookup"><span data-stu-id="b32eb-126">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="b32eb-127">領域のフォルダーにアクセスするための承認が必要</span><span class="sxs-lookup"><span data-stu-id="b32eb-127">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="b32eb-128">を使用して、指定し<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>たパスにあるフォルダー内のすべての領域にを追加します。<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*></span><span class="sxs-lookup"><span data-stu-id="b32eb-128">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="b32eb-129">フォルダーパスは、指定された領域のページルートディレクトリを基準としたフォルダーのパスです。</span><span class="sxs-lookup"><span data-stu-id="b32eb-129">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="b32eb-130">たとえば、[*区分/id/ページ/管理/* *管理*] の下にあるファイルのフォルダーパスを使用します。</span><span class="sxs-lookup"><span data-stu-id="b32eb-130">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="b32eb-131">[承認ポリシー](xref:security/authorization/policies)を指定するには、 [AuthorizeAreaFolder オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)を使用します。</span><span class="sxs-lookup"><span data-stu-id="b32eb-131">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="b32eb-132">ページへの匿名アクセスを許可する</span><span class="sxs-lookup"><span data-stu-id="b32eb-132">Allow anonymous access to a page</span></span>

<span data-ttu-id="b32eb-133">を使用<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*>して<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 、 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>指定したパスにあるページにを追加します。</span><span class="sxs-lookup"><span data-stu-id="b32eb-133">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="b32eb-134">指定されたパスは、ビューエンジンのパスです。これは、拡張子のない Razor Pages ルートの相対パスで、スラッシュだけを含みます。</span><span class="sxs-lookup"><span data-stu-id="b32eb-134">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="b32eb-135">ページのフォルダーへの匿名アクセスを許可する</span><span class="sxs-lookup"><span data-stu-id="b32eb-135">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="b32eb-136">を使用して、指定し<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>たパスにあるフォルダー内のすべてのページにを追加します。 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*></span><span class="sxs-lookup"><span data-stu-id="b32eb-136">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="b32eb-137">指定されたパスは、Razor Pages ルートの相対パスであるビューエンジンのパスです。</span><span class="sxs-lookup"><span data-stu-id="b32eb-137">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="b32eb-138">承認済みアクセスと匿名アクセスの組み合わせに関する注意事項</span><span class="sxs-lookup"><span data-stu-id="b32eb-138">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="b32eb-139">承認を必要とするページのフォルダーを指定し、そのフォルダー内のページで匿名アクセスが許可されるように指定することが有効です。</span><span class="sxs-lookup"><span data-stu-id="b32eb-139">It's valid to specify that a folder of pages that require authorization and than specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="b32eb-140">ただし、逆は無効です。</span><span class="sxs-lookup"><span data-stu-id="b32eb-140">The reverse, however, isn't valid.</span></span> <span data-ttu-id="b32eb-141">匿名アクセス用のページのフォルダーを宣言して、承認が必要なフォルダー内のページを指定することはできません。</span><span class="sxs-lookup"><span data-stu-id="b32eb-141">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="b32eb-142">プライベートページで承認を要求すると失敗します。</span><span class="sxs-lookup"><span data-stu-id="b32eb-142">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="b32eb-143">と<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>の両方がページに適用されると、が優先され、アクセスが制御されます。 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter></span><span class="sxs-lookup"><span data-stu-id="b32eb-143">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b32eb-144">その他の資料</span><span class="sxs-lookup"><span data-stu-id="b32eb-144">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b32eb-145">Razor Pages アプリでアクセスを制御する方法の1つは、起動時に承認規則を使用することです。</span><span class="sxs-lookup"><span data-stu-id="b32eb-145">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="b32eb-146">これらの規則を使用すると、ユーザーを承認し、匿名ユーザーが個々のページやページのフォルダーにアクセスすることを許可できます。</span><span class="sxs-lookup"><span data-stu-id="b32eb-146">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="b32eb-147">このトピックで説明する規則は、アクセスを制御するための[承認フィルター](xref:mvc/controllers/filters#authorization-filters)を自動的に適用します。</span><span class="sxs-lookup"><span data-stu-id="b32eb-147">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="b32eb-148">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="b32eb-148">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b32eb-149">このサンプルアプリでは、 [ASP.NET Core id を使用せずに cookie 認証](xref:security/authentication/cookie)を使用します。</span><span class="sxs-lookup"><span data-stu-id="b32eb-149">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="b32eb-150">このトピックで示す概念と例は、ASP.NET Core Id を使用するアプリにも同様に適用されます。</span><span class="sxs-lookup"><span data-stu-id="b32eb-150">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="b32eb-151">ASP.NET Core Id を使用するには、「 <xref:security/authentication/identity>」のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="b32eb-151">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="b32eb-152">ページへのアクセスに承認を要求する</span><span class="sxs-lookup"><span data-stu-id="b32eb-152">Require authorization to access a page</span></span>

<span data-ttu-id="b32eb-153">を使用して、指定し<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>たパスのページにを追加します。<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*></span><span class="sxs-lookup"><span data-stu-id="b32eb-153">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="b32eb-154">指定されたパスは、ビューエンジンのパスです。これは、拡張子のない Razor Pages ルートの相対パスで、スラッシュだけを含みます。</span><span class="sxs-lookup"><span data-stu-id="b32eb-154">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="b32eb-155">[承認ポリシー](xref:security/authorization/policies)を指定するには、 [authorizepage オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)を使用します。</span><span class="sxs-lookup"><span data-stu-id="b32eb-155">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="b32eb-156">は<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 、 `[Authorize]`フィルター属性を使用してページモデルクラスに適用できます。</span><span class="sxs-lookup"><span data-stu-id="b32eb-156">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="b32eb-157">詳細については、「[承認フィルター属性](xref:razor-pages/filter#authorize-filter-attribute)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b32eb-157">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="b32eb-158">ページのフォルダーにアクセスするには承認が必要です</span><span class="sxs-lookup"><span data-stu-id="b32eb-158">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="b32eb-159">を使用して、指定し<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>たパスにあるフォルダー内のすべてのページにを追加します。 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*></span><span class="sxs-lookup"><span data-stu-id="b32eb-159">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="b32eb-160">指定されたパスは、Razor Pages ルートの相対パスであるビューエンジンのパスです。</span><span class="sxs-lookup"><span data-stu-id="b32eb-160">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="b32eb-161">[承認ポリシー](xref:security/authorization/policies)を指定するには、 [authorizefolder オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)を使用します。</span><span class="sxs-lookup"><span data-stu-id="b32eb-161">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="b32eb-162">区分ページへのアクセスに承認を要求する</span><span class="sxs-lookup"><span data-stu-id="b32eb-162">Require authorization to access an area page</span></span>

<span data-ttu-id="b32eb-163">を使用して、 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>指定したパスの領域ページにを追加します。<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*></span><span class="sxs-lookup"><span data-stu-id="b32eb-163">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="b32eb-164">ページ名は、指定された領域のページルートディレクトリを基準とした拡張子のないファイルのパスです。</span><span class="sxs-lookup"><span data-stu-id="b32eb-164">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="b32eb-165">たとえば、ファイル*領域/id/ページ/管理/アカウント*のページ名は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="b32eb-165">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="b32eb-166">[承認ポリシー](xref:security/authorization/policies)を指定するには、次のように、 [Authorizeareapage オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)を使用します。</span><span class="sxs-lookup"><span data-stu-id="b32eb-166">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="b32eb-167">領域のフォルダーにアクセスするための承認が必要</span><span class="sxs-lookup"><span data-stu-id="b32eb-167">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="b32eb-168">を使用して、指定し<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>たパスにあるフォルダー内のすべての領域にを追加します。<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*></span><span class="sxs-lookup"><span data-stu-id="b32eb-168">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="b32eb-169">フォルダーパスは、指定された領域のページルートディレクトリを基準としたフォルダーのパスです。</span><span class="sxs-lookup"><span data-stu-id="b32eb-169">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="b32eb-170">たとえば、[*区分/id/ページ/管理/* *管理*] の下にあるファイルのフォルダーパスを使用します。</span><span class="sxs-lookup"><span data-stu-id="b32eb-170">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="b32eb-171">[承認ポリシー](xref:security/authorization/policies)を指定するには、 [AuthorizeAreaFolder オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)を使用します。</span><span class="sxs-lookup"><span data-stu-id="b32eb-171">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="b32eb-172">ページへの匿名アクセスを許可する</span><span class="sxs-lookup"><span data-stu-id="b32eb-172">Allow anonymous access to a page</span></span>

<span data-ttu-id="b32eb-173">を使用<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*>して<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 、 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>指定したパスにあるページにを追加します。</span><span class="sxs-lookup"><span data-stu-id="b32eb-173">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="b32eb-174">指定されたパスは、ビューエンジンのパスです。これは、拡張子のない Razor Pages ルートの相対パスで、スラッシュだけを含みます。</span><span class="sxs-lookup"><span data-stu-id="b32eb-174">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="b32eb-175">ページのフォルダーへの匿名アクセスを許可する</span><span class="sxs-lookup"><span data-stu-id="b32eb-175">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="b32eb-176">を使用して、指定し<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>たパスにあるフォルダー内のすべてのページにを追加します。 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*></span><span class="sxs-lookup"><span data-stu-id="b32eb-176">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="b32eb-177">指定されたパスは、Razor Pages ルートの相対パスであるビューエンジンのパスです。</span><span class="sxs-lookup"><span data-stu-id="b32eb-177">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="b32eb-178">承認済みアクセスと匿名アクセスの組み合わせに関する注意事項</span><span class="sxs-lookup"><span data-stu-id="b32eb-178">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="b32eb-179">承認を必要とするページのフォルダーを指定し、そのフォルダー内のページで匿名アクセスが許可されるように指定することが有効です。</span><span class="sxs-lookup"><span data-stu-id="b32eb-179">It's valid to specify that a folder of pages that require authorization and than specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="b32eb-180">ただし、逆は無効です。</span><span class="sxs-lookup"><span data-stu-id="b32eb-180">The reverse, however, isn't valid.</span></span> <span data-ttu-id="b32eb-181">匿名アクセス用のページのフォルダーを宣言して、承認が必要なフォルダー内のページを指定することはできません。</span><span class="sxs-lookup"><span data-stu-id="b32eb-181">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="b32eb-182">プライベートページで承認を要求すると失敗します。</span><span class="sxs-lookup"><span data-stu-id="b32eb-182">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="b32eb-183">と<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>の両方がページに適用されると、が優先され、アクセスが制御されます。 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter></span><span class="sxs-lookup"><span data-stu-id="b32eb-183">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b32eb-184">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="b32eb-184">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end
