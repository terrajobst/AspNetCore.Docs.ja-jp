---
title: ASP.NET Core MVC の互換バージョン
author: rick-anderson
description: ASP.NET Core の Startup クラスがサービスとアプリケーションの要求パイプラインをどのように構成しているかを説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 9/25/2019
uid: mvc/compatibility-version
ms.openlocfilehash: b29e2ee49aaf0f557f1acd0cf03e9e82d5ea0105
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75357732"
---
# <a name="compatibility-version-for-aspnet-core-mvc"></a>ASP.NET Core MVC の互換バージョン

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> メソッドは ASP.NET Core 3.0 アプリでは何も行いません。 つまり、<xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion> のどの値で `SetCompatibilityVersion` を呼び出してもアプリケーションに影響しません。

* ASP.NET Core の次のマイナー バージョンでは新しい `CompatibilityVersion` 値が提供される可能性があります。
* `Version_2_0` から `Version_2_2`までの `CompatibilityVersion` 値は `[Obsolete(...)]` とマークされます。
* 「[偽造防止、CORS、診断、MVC、ルーティングにおける API の重大な変更](https://github.com/aspnet/Announcements/issues/387)」を参照してください。 この一覧には、互換性スイッチの重大な変更が含まれています。

`SetCompatibilityVersion` が ASP.NET Core 2.x アプリでどのように機能するかを確認するには、[この記事の ASP.NET Core 2.2 バージョン](https://docs.microsoft.com/aspnet/core/mvc/compatibility-version?view=aspnetcore-2.2)を選択します。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 2.x アプリで <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> メソッドを使用すると、ASP.NET Core MVC 2.1 または 2.2 に導入されている、互換性に影響する重大な変更をオプトインまたはオプトアウトすることができます。 互換性に影響する可能性のあるこれらの重大な変更は、ほとんどの場合、MVC サブシステムの動作方法と、ランタイムで**ユーザーのコード**が呼び出される方法についてです。 オプトインした場合、最新の動作と ASP.NET Core の最新の動作を得ることができます。

次のコードにより、互換性モードは ASP.NET Core 2.2 に設定されます。

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup.cs?name=snippet1)]

最新のバージョン (`CompatibilityVersion.Latest`) を使用してアプリをテストすることをお勧めします。 最新のバージョンを使用しても、ほとんどのアプリで重大な動作変更はないと見込まれます。

`SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` を呼び出すアプリは、ASP.NET Core 2.1/2.2 MVC バージョンで導入された重大な動作変更の可能性から保護されています。 この保護とは、次のとおりです。

* 2\.1 以降のすべての変更には該当しません。これは、MVC サブシステムの ASP.NET Core ランタイムの互換性に影響する可能性のある重大な変更のみを対象としています。
* ASP.NET Core 3.0 には拡張されません。

`SetCompatibilityVersion` を呼び出さ**ない** ASP.NET Core 2.1 および 2.2 アプリの既定の互換性は、2.0 の互換性です。 つまり、`SetCompatibilityVersion` を呼び出さないことは、`SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` を呼び出すことと同じです。

次のコードでは、以下の動作を除き、互換性モードを ASP.NET Core 2.2 に設定します。

* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowCombiningAuthorizeFilters>
* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.InputFormatterExceptionPolicy>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup2.cs?name=snippet1)]

互換性に影響する重大な変更があったアプリでは、適切な互換性スイッチを使用することにより、次が得られます。

* 最新のリリースを使用し、互換性に影響する特定の重大な変更をオプトアウトできます。
* アプリが最新の変更に対応するよう、更新を行う時間を得られます。

<xref:Microsoft.AspNetCore.Mvc.MvcOptions> ドキュメントでは、変更があった個所とそれらの改善が多くのユーザーに与えるメリットについて説明しています。

ASP.NET Core 3.0 では、互換性スイッチでサポートされる古い動作は削除されました。 これらの正の変更は、ほぼすべてのユーザーにとってメリットとなると感じています。 これらの変更を 2.1 および 2.2 に導入することで、ほとんどのアプリにメリットがありますが、更新が必要になるアプリもあります。
::: moniker-end
