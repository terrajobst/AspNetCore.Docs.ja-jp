---
title: ASP.NET Core の Razor ページのフィルター メソッド
author: Rick-Anderson
description: ASP.NET Core の Razor ページのフィルター メソッドを作成する方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
uid: razor-pages/filter
ms.openlocfilehash: a60b17685c6f836de7c0afcc5b89a9894fb8b28f
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447232"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a>ASP.NET Core の Razor ページのフィルター メソッド

::: moniker range=">= aspnetcore-3.0"

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

Razor ページのフィルターである [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) および [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) を使用すると、Razor ページ ハンドラーの実行の前後に Razor ページでコードを実行できます。 Razor ページ フィルターは、個々のページ ハンドラー メソッドに適用できないことを除き、[ASP.NET Core MVC アクション フィルター](xref:mvc/controllers/filters#action-filters)と類似しています。

Razor ページ フィルターとは、次のとおりです。

* モデルのバインドが行われる前の、ハンドラー メソッドが選択された後にコードを実行します。
* モデルのバインドの完了後の、ハンドラー メソッドの実行前にコードを実行します。
* ハンドラー メソッドの実行後にコードを実行します。
* ページまたはグローバルに実装できます。
* 特定のページ ハンドラー メソッドには適用できません。
* [依存関係の挿入](xref:fundamentals/dependency-injection)(DI) によってコンストラクターの依存関係を設定できます。 詳細については、「 [Servicefilterattribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) 」と「 [typefilterattribute](/aspnet/core/mvc/controllers/filters#typefilterattribute)」を参照してください。

ページコンストラクターとミドルウェアは、ハンドラーメソッドの実行前にカスタムコードの実行を有効にしますが、Razor ページフィルターのみを使用して <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> およびページにアクセスできます。 ミドルウェアは `HttpContext`にアクセスできますが、"ページコンテキスト" にはアクセスできません。 フィルターには <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> 派生パラメーターがあり、`HttpContext`へのアクセスを提供します。 たとえば、「[フィルター属性を実装する](#ifa)」のサンプルでは、応答にヘッダーが追加されます。これは、コンストラクターやミドルウェアでは実行できません。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

Razor ページ フィルターには、グローバルまたはページ レベルで適用できる次のメソッドがあります。

* 同期メソッド:

  * [OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : モデルのバインドが行われる前の、ハンドラー メソッドが選択された後に呼び出されます。
  * [OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : モデルのバインドの完了後の、ハンドラー メソッドの実行前に呼び出されます。
  * [OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : アクション結果の前の、ハンドラー メソッドの実行前に呼び出されます。

* 非同期メソッド:

  * [OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : モデルのバインドが行われる前の、ハンドラー メソッドが選択された後に非同期で呼び出されます。
  * [OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : モデルのバインドの完了後の、ハンドラー メソッドが呼び出される前に非同期で呼び出されます。

フィルター インターフェイスの同期と非同期バージョンの**両方ではなく**、**いずれか**を実装します。 フレームワークは、最初にフィルターが非同期インターフェイスを実装しているかどうかをチェックして、している場合はそれを呼び出します。 していない場合は、同期インターフェイスのメソッドを呼び出します。 両方のインターフェイスが実装されている場合は、非同期メソッドのみが呼び出されます。 ページのオーバーライドでもこの規則は同じです。オーバーライドの同期バージョンまたは非同期バージョンを実装でき、両方はできません。

## <a name="implement-razor-page-filters-globally"></a>Razor ページにフィルターをグローバルに実装する

`IAsyncPageFilter` は、次のコードによって実装されます。

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

上記のコードでは、`ProcessUserAgent.Write` ユーザーが指定したコードがユーザーエージェント文字列で動作します。

次のコードは、`SampleAsyncPageFilter` クラスで `Startup` を有効にします。

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

次のコードは、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> を呼び出して、`SampleAsyncPageFilter` を */ムービー*内のページのみに適用します。

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

次のコードは、同期 `IPageFilter` を実装します。

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

次のコードは、`SamplePageFilter` を有効にします。

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a>フィルター メソッドをオーバーライドして Razor ページにフィルターを実装する

次のコードは、非同期の Razor ページフィルターをオーバーライドします。

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a>フィルター属性を実装する

組み込みの属性ベースのフィルター <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> フィルターをサブクラス化できます。 次のフィルターは、応答にヘッダーを追加します。

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

次のコードは、`AddHeader` 属性を追加します。

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

ブラウザー開発者ツールなどのツールを使用して、ヘッダーを確認します。 **[応答ヘッダー]** の下に `author: Rick` が表示されます。

順序をオーバーライドする手順については、「[既定の順序のオーバーライド](xref:mvc/controllers/filters#overriding-the-default-order)」を参照してください。

フィルターからフィルター パイプラインをショート サーキットする手順については、「[キャンセルとショート サーキット](xref:mvc/controllers/filters#cancellation-and-short-circuiting)」を参照してください。

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a>Authorize フィルター属性

[ に ](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0)Authorize`PageModel` 属性を適用できます。

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

Razor ページのフィルターである [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) および [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) を使用すると、Razor ページ ハンドラーの実行の前後に Razor ページでコードを実行できます。 Razor ページ フィルターは、個々のページ ハンドラー メソッドに適用できないことを除き、[ASP.NET Core MVC アクション フィルター](xref:mvc/controllers/filters#action-filters)と類似しています。

Razor ページ フィルターとは、次のとおりです。

* モデルのバインドが行われる前の、ハンドラー メソッドが選択された後にコードを実行します。
* モデルのバインドの完了後の、ハンドラー メソッドの実行前にコードを実行します。
* ハンドラー メソッドの実行後にコードを実行します。
* ページまたはグローバルに実装できます。
* 特定のページ ハンドラー メソッドには適用できません。

コードは、ページ コンストラクターまたはミドルウェアを使用してハンドラー メソッドの実行前に実行できますが、[HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext) にアクセスできるのは Razor ページ フィルターのみです。 フィルターには、[ へのアクセスを提供する ](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0)FilterContext`HttpContext` 派生のパラメーターがあります。 たとえば、「[フィルター属性を実装する](#ifa)」のサンプルでは、応答にヘッダーが追加されます。これは、コンストラクターやミドルウェアでは実行できません。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

Razor ページ フィルターには、グローバルまたはページ レベルで適用できる次のメソッドがあります。

* 同期メソッド:

  * [OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : モデルのバインドが行われる前の、ハンドラー メソッドが選択された後に呼び出されます。
  * [OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : モデルのバインドの完了後の、ハンドラー メソッドの実行前に呼び出されます。
  * [OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : アクション結果の前の、ハンドラー メソッドの実行前に呼び出されます。

* 非同期メソッド:

  * [OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : モデルのバインドが行われる前の、ハンドラー メソッドが選択された後に非同期で呼び出されます。
  * [OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : モデルのバインドの完了後の、ハンドラー メソッドが呼び出される前に非同期で呼び出されます。

> [!NOTE]
> フィルター インターフェイスの同期と非同期バージョンの両方ではなく、**いずれか**を実装します。 フレームワークは、最初にフィルターが非同期インターフェイスを実装しているかどうかをチェックして、している場合はそれを呼び出します。 していない場合は、同期インターフェイスのメソッドを呼び出します。 両方のインターフェイスが実装されている場合は、非同期メソッドのみが呼び出されます。 ページのオーバーライドでもこの規則は同じです。オーバーライドの同期バージョンまたは非同期バージョンを実装でき、両方はできません。

## <a name="implement-razor-page-filters-globally"></a>Razor ページにフィルターをグローバルに実装する

`IAsyncPageFilter` は、次のコードによって実装されます。

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

前述のコードでは、[ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) は不要です。 これは、アプリケーションのトレース情報を提供するためにサンプルで使用されています。

次のコードは、`SampleAsyncPageFilter` クラスで `Startup` を有効にします。

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

次は、完全な `Startup` クラスのコードです。

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

次のコードは、`AddFolderApplicationModelConvention` を呼び出し、`SampleAsyncPageFilter`/subFolder*のページにのみ* を適用します。

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

次のコードは、同期 `IPageFilter` を実装します。

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

次のコードは、`SamplePageFilter` を有効にします。

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a>フィルター メソッドをオーバーライドして Razor ページにフィルターを実装する

次のコードは、同期 Razor ページ フィルターをオーバーライドします。

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a>フィルター属性を実装する

組み込みの属性ベースのフィルターである [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) フィルターはサブクラス化することができます。 次のフィルターは、応答にヘッダーを追加します。

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

次のコードは、`AddHeader` 属性を追加します。

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

順序をオーバーライドする手順については、「[既定の順序のオーバーライド](xref:mvc/controllers/filters#overriding-the-default-order)」を参照してください。

フィルターからフィルター パイプラインをショート サーキットする手順については、「[キャンセルとショート サーキット](xref:mvc/controllers/filters#cancellation-and-short-circuiting)」を参照してください。 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a>Authorize フィルター属性

[ に ](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0)Authorize`PageModel` 属性を適用できます。

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end
