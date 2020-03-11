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
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a>ASP.NET Core での承認規則の Razor Pages

::: moniker range=">= aspnetcore-3.0"

Razor Pages アプリでアクセスを制御する方法の1つは、起動時に承認規則を使用することです。 これらの規則を使用すると、ユーザーを承認し、匿名ユーザーが個々のページやページのフォルダーにアクセスすることを許可できます。 このトピックで説明する規則は、アクセスを制御するための[承認フィルター](xref:mvc/controllers/filters#authorization-filters)を自動的に適用します。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

このサンプルアプリでは、 [ASP.NET Core id を使用せずに cookie 認証](xref:security/authentication/cookie)を使用します。 このトピックで示す概念と例は、ASP.NET Core Id を使用するアプリにも同様に適用されます。 ASP.NET Core Id を使用するには、<xref:security/authentication/identity>のガイダンスに従ってください。

## <a name="require-authorization-to-access-a-page"></a>ページへのアクセスに承認を要求する

<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> で <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> 規約を使用して、指定されたパスのページに <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> を追加します。

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

指定されたパスは、ビューエンジンのパスです。これは、拡張子のない Razor Pages ルートの相対パスで、スラッシュだけを含みます。

[承認ポリシー](xref:security/authorization/policies)を指定するには、 [authorizepage オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)を使用します。

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> は、`[Authorize]` filter 属性を使用してページモデルクラスに適用できます。 詳細については、「[承認フィルター属性](xref:razor-pages/filter#authorize-filter-attribute)」を参照してください。

## <a name="require-authorization-to-access-a-folder-of-pages"></a>ページのフォルダーにアクセスするには承認が必要です

<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> で <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> 規約を使用して、指定したパスにあるフォルダー内のすべてのページに <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> を追加します。

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

指定されたパスは、Razor Pages ルートの相対パスであるビューエンジンのパスです。

[承認ポリシー](xref:security/authorization/policies)を指定するには、 [authorizefolder オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)を使用します。

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>区分ページへのアクセスに承認を要求する

<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> で <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> 規約を使用して、指定したパスの領域ページに <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> を追加します。

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

ページ名は、指定された領域のページルートディレクトリを基準とした拡張子のないファイルのパスです。 たとえば、ファイル*領域/id/ページ/管理/アカウント*のページ名は、次のように*なります。*

[承認ポリシー](xref:security/authorization/policies)を指定するには、次のように、 [Authorizeareapage オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)を使用します。

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>領域のフォルダーにアクセスするための承認が必要

<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> で <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> 規約を使用して、指定したパスにあるフォルダー内のすべての領域に <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> を追加します。

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

フォルダーパスは、指定された領域のページルートディレクトリを基準としたフォルダーのパスです。 たとえば、[*区分/id/ページ/管理/* *管理*] の下にあるファイルのフォルダーパスを使用します。

[承認ポリシー](xref:security/authorization/policies)を指定するには、 [AuthorizeAreaFolder オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)を使用します。

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>ページへの匿名アクセスを許可する

<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> で <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> 規約を使用して、指定したパスにあるページに <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> を追加します。

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

指定されたパスは、ビューエンジンのパスです。これは、拡張子のない Razor Pages ルートの相対パスで、スラッシュだけを含みます。

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>ページのフォルダーへの匿名アクセスを許可する

<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> で <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> 規約を使用して、指定したパスにあるフォルダー内のすべてのページに <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> を追加します。

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

指定されたパスは、Razor Pages ルートの相対パスであるビューエンジンのパスです。

## <a name="note-on-combining-authorized-and-anonymous-access"></a>承認済みアクセスと匿名アクセスの組み合わせに関する注意事項

ページのフォルダーで承認が必要であることを指定し、そのフォルダー内のページで匿名アクセスが許可されるように指定することが有効です。

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

ただし、逆は無効です。 匿名アクセス用のページのフォルダーを宣言して、承認が必要なフォルダー内のページを指定することはできません。

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

プライベートページで承認を要求すると失敗します。 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> と <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> の両方がページに適用されると、<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> が優先され、アクセスが制御されます。

## <a name="additional-resources"></a>その他のリソース

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Razor Pages アプリでアクセスを制御する方法の1つは、起動時に承認規則を使用することです。 これらの規則を使用すると、ユーザーを承認し、匿名ユーザーが個々のページやページのフォルダーにアクセスすることを許可できます。 このトピックで説明する規則は、アクセスを制御するための[承認フィルター](xref:mvc/controllers/filters#authorization-filters)を自動的に適用します。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

このサンプルアプリでは、 [ASP.NET Core id を使用せずに cookie 認証](xref:security/authentication/cookie)を使用します。 このトピックで示す概念と例は、ASP.NET Core Id を使用するアプリにも同様に適用されます。 ASP.NET Core Id を使用するには、<xref:security/authentication/identity>のガイダンスに従ってください。

## <a name="require-authorization-to-access-a-page"></a>ページへのアクセスに承認を要求する

<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> で <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> 規約を使用して、指定されたパスのページに <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> を追加します。

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

指定されたパスは、ビューエンジンのパスです。これは、拡張子のない Razor Pages ルートの相対パスで、スラッシュだけを含みます。

[承認ポリシー](xref:security/authorization/policies)を指定するには、 [authorizepage オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)を使用します。

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> は、`[Authorize]` filter 属性を使用してページモデルクラスに適用できます。 詳細については、「[承認フィルター属性](xref:razor-pages/filter#authorize-filter-attribute)」を参照してください。

## <a name="require-authorization-to-access-a-folder-of-pages"></a>ページのフォルダーにアクセスするには承認が必要です

<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> で <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> 規約を使用して、指定したパスにあるフォルダー内のすべてのページに <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> を追加します。

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

指定されたパスは、Razor Pages ルートの相対パスであるビューエンジンのパスです。

[承認ポリシー](xref:security/authorization/policies)を指定するには、 [authorizefolder オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)を使用します。

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>区分ページへのアクセスに承認を要求する

<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> で <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> 規約を使用して、指定したパスの領域ページに <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> を追加します。

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

ページ名は、指定された領域のページルートディレクトリを基準とした拡張子のないファイルのパスです。 たとえば、ファイル*領域/id/ページ/管理/アカウント*のページ名は、次のように*なります。*

[承認ポリシー](xref:security/authorization/policies)を指定するには、次のように、 [Authorizeareapage オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)を使用します。

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>領域のフォルダーにアクセスするための承認が必要

<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> で <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> 規約を使用して、指定したパスにあるフォルダー内のすべての領域に <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> を追加します。

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

フォルダーパスは、指定された領域のページルートディレクトリを基準としたフォルダーのパスです。 たとえば、[*区分/id/ページ/管理/* *管理*] の下にあるファイルのフォルダーパスを使用します。

[承認ポリシー](xref:security/authorization/policies)を指定するには、 [AuthorizeAreaFolder オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)を使用します。

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>ページへの匿名アクセスを許可する

<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> で <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> 規約を使用して、指定したパスにあるページに <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> を追加します。

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

指定されたパスは、ビューエンジンのパスです。これは、拡張子のない Razor Pages ルートの相対パスで、スラッシュだけを含みます。

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>ページのフォルダーへの匿名アクセスを許可する

<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> で <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> 規約を使用して、指定したパスにあるフォルダー内のすべてのページに <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> を追加します。

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

指定されたパスは、Razor Pages ルートの相対パスであるビューエンジンのパスです。

## <a name="note-on-combining-authorized-and-anonymous-access"></a>承認済みアクセスと匿名アクセスの組み合わせに関する注意事項

承認を必要とするページのフォルダーを指定し、そのフォルダー内のページで匿名アクセスが許可されるように指定することが有効です。

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

ただし、逆は無効です。 匿名アクセス用のページのフォルダーを宣言して、承認が必要なフォルダー内のページを指定することはできません。

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

プライベートページで承認を要求すると失敗します。 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> と <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> の両方がページに適用されると、<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> が優先され、アクセスが制御されます。

## <a name="additional-resources"></a>その他のリソース

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end
