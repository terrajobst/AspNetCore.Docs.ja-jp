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
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a>ASP.NET Core での承認規則の Razor Pages

作成者: [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

Razor Pages アプリでアクセスを制御する方法の1つは、起動時に承認規則を使用することです。 これらの規則を使用すると、ユーザーを承認し、匿名ユーザーが個々のページやページのフォルダーにアクセスすることを許可できます。 このトピックで説明する規則は、アクセスを制御するための[承認フィルター](xref:mvc/controllers/filters#authorization-filters)を自動的に適用します。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

このサンプルアプリでは、 [ASP.NET Core id を使用せずに cookie 認証](xref:security/authentication/cookie)を使用します。 このトピックで示す概念と例は、ASP.NET Core Id を使用するアプリにも同様に適用されます。 ASP.NET Core Id を使用するには、「 <xref:security/authentication/identity>」のガイダンスに従ってください。

## <a name="require-authorization-to-access-a-page"></a>ページへのアクセスに承認を要求する

を使用して、指定し<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>たパスのページにを追加します。<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

指定されたパスは、ビューエンジンのパスです。これは、拡張子のない Razor Pages ルートの相対パスで、スラッシュだけを含みます。

[承認ポリシー](xref:security/authorization/policies)を指定するには、 [authorizepage オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)を使用します。

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> は<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 、 `[Authorize]`フィルター属性を使用してページモデルクラスに適用できます。 詳細については、「[承認フィルター属性](xref:razor-pages/filter#authorize-filter-attribute)」を参照してください。

## <a name="require-authorization-to-access-a-folder-of-pages"></a>ページのフォルダーにアクセスするには承認が必要です

を使用して、指定し<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>たパスにあるフォルダー内のすべてのページにを追加します。 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

指定されたパスは、Razor Pages ルートの相対パスであるビューエンジンのパスです。

[承認ポリシー](xref:security/authorization/policies)を指定するには、 [authorizefolder オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)を使用します。

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>区分ページへのアクセスに承認を要求する

を使用して、 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>指定したパスの領域ページにを追加します。<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

ページ名は、指定された領域のページルートディレクトリを基準とした拡張子のないファイルのパスです。 たとえば、ファイル*領域/id/ページ/管理/アカウント*のページ名は、次のようになります。

[承認ポリシー](xref:security/authorization/policies)を指定するには、次のように、 [Authorizeareapage オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)を使用します。

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>領域のフォルダーにアクセスするための承認が必要

を使用して、指定し<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>たパスにあるフォルダー内のすべての領域にを追加します。<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

フォルダーパスは、指定された領域のページルートディレクトリを基準としたフォルダーのパスです。 たとえば、[*区分/id/ページ/管理/* *管理*] の下にあるファイルのフォルダーパスを使用します。

[承認ポリシー](xref:security/authorization/policies)を指定するには、 [AuthorizeAreaFolder オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)を使用します。

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>ページへの匿名アクセスを許可する

を使用<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*>して<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 、 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>指定したパスにあるページにを追加します。

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

指定されたパスは、ビューエンジンのパスです。これは、拡張子のない Razor Pages ルートの相対パスで、スラッシュだけを含みます。

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>ページのフォルダーへの匿名アクセスを許可する

を使用して、指定し<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>たパスにあるフォルダー内のすべてのページにを追加します。 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

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

プライベートページで承認を要求すると失敗します。 と<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>の両方がページに適用されると、が優先され、アクセスが制御されます。 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>

## <a name="additional-resources"></a>その他の資料

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Razor Pages アプリでアクセスを制御する方法の1つは、起動時に承認規則を使用することです。 これらの規則を使用すると、ユーザーを承認し、匿名ユーザーが個々のページやページのフォルダーにアクセスすることを許可できます。 このトピックで説明する規則は、アクセスを制御するための[承認フィルター](xref:mvc/controllers/filters#authorization-filters)を自動的に適用します。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

このサンプルアプリでは、 [ASP.NET Core id を使用せずに cookie 認証](xref:security/authentication/cookie)を使用します。 このトピックで示す概念と例は、ASP.NET Core Id を使用するアプリにも同様に適用されます。 ASP.NET Core Id を使用するには、「 <xref:security/authentication/identity>」のガイダンスに従ってください。

## <a name="require-authorization-to-access-a-page"></a>ページへのアクセスに承認を要求する

を使用して、指定し<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>たパスのページにを追加します。<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

指定されたパスは、ビューエンジンのパスです。これは、拡張子のない Razor Pages ルートの相対パスで、スラッシュだけを含みます。

[承認ポリシー](xref:security/authorization/policies)を指定するには、 [authorizepage オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)を使用します。

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> は<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 、 `[Authorize]`フィルター属性を使用してページモデルクラスに適用できます。 詳細については、「[承認フィルター属性](xref:razor-pages/filter#authorize-filter-attribute)」を参照してください。

## <a name="require-authorization-to-access-a-folder-of-pages"></a>ページのフォルダーにアクセスするには承認が必要です

を使用して、指定し<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>たパスにあるフォルダー内のすべてのページにを追加します。 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

指定されたパスは、Razor Pages ルートの相対パスであるビューエンジンのパスです。

[承認ポリシー](xref:security/authorization/policies)を指定するには、 [authorizefolder オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)を使用します。

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>区分ページへのアクセスに承認を要求する

を使用して、 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>指定したパスの領域ページにを追加します。<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

ページ名は、指定された領域のページルートディレクトリを基準とした拡張子のないファイルのパスです。 たとえば、ファイル*領域/id/ページ/管理/アカウント*のページ名は、次のようになります。

[承認ポリシー](xref:security/authorization/policies)を指定するには、次のように、 [Authorizeareapage オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)を使用します。

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>領域のフォルダーにアクセスするための承認が必要

を使用して、指定し<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>たパスにあるフォルダー内のすべての領域にを追加します。<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

フォルダーパスは、指定された領域のページルートディレクトリを基準としたフォルダーのパスです。 たとえば、[*区分/id/ページ/管理/* *管理*] の下にあるファイルのフォルダーパスを使用します。

[承認ポリシー](xref:security/authorization/policies)を指定するには、 [AuthorizeAreaFolder オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)を使用します。

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>ページへの匿名アクセスを許可する

を使用<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*>して<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 、 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>指定したパスにあるページにを追加します。

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

指定されたパスは、ビューエンジンのパスです。これは、拡張子のない Razor Pages ルートの相対パスで、スラッシュだけを含みます。

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>ページのフォルダーへの匿名アクセスを許可する

を使用して、指定し<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>たパスにあるフォルダー内のすべてのページにを追加します。 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>

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

プライベートページで承認を要求すると失敗します。 と<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>の両方がページに適用されると、が優先され、アクセスが制御されます。 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>

## <a name="additional-resources"></a>その他の技術情報

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end
