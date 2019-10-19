---
title: ASP.NET Core の Razor ページのフィルター メソッド
author: Rick-Anderson
description: ASP.NET Core の Razor ページのフィルター メソッドを作成する方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/05/2018
uid: razor-pages/filter
ms.openlocfilehash: 1da46c61617a01698e3c4b1fe6bf9825db6643fd
ms.sourcegitcommit: a166291c6708f5949c417874108332856b53b6a9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2019
ms.locfileid: "72589952"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a>ASP.NET Core の Razor ページのフィルター メソッド

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

Razor ページのフィルターである [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) および [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) を使用すると、Razor ページ ハンドラーの実行の前後に Razor ページでコードを実行できます。 Razor ページ フィルターは、個々のページ ハンドラー メソッドに適用できないことを除き、[ASP.NET Core MVC アクション フィルター](xref:mvc/controllers/filters#action-filters)と類似しています。 

Razor ページ フィルターとは、次のとおりです。

* モデルのバインドが行われる前の、ハンドラー メソッドが選択された後にコードを実行します。
* モデルのバインドの完了後の、ハンドラー メソッドの実行前にコードを実行します。
* ハンドラー メソッドの実行後にコードを実行します。
* ページまたはグローバルに実装できます。
* 特定のページ ハンドラー メソッドには適用できません。

コードは、ページ コンストラクターまたはミドルウェアを使用してハンドラー メソッドの実行前に実行できますが、[HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext) にアクセスできるのは Razor ページ フィルターのみです。 フィルターには、`HttpContext` へのアクセスを提供する [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) 派生のパラメーターがあります。 たとえば、「[フィルター属性を実装する](#ifa)」のサンプルでは、応答にヘッダーが追加されます。これは、コンストラクターやミドルウェアでは実行できません。

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

次のコードは、`Startup` クラスで `SampleAsyncPageFilter` を有効にします。

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

次は、完全な `Startup` クラスのコードです。

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

次のコードは、`AddFolderApplicationModelConvention` を呼び出し、 */subFolder* のページにのみ `SampleAsyncPageFilter` を適用します。

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

次のコードは、同期 `IPageFilter` を実装します。

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

次のコードは、`SamplePageFilter` を有効にします。

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

::: moniker range=">= aspnetcore-2.1"

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a>フィルター メソッドをオーバーライドして Razor ページにフィルターを実装する

次のコードは、同期 Razor ページ フィルターをオーバーライドします。

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

::: moniker-end

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

`PageModel` に [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) 属性を適用できます。

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]
