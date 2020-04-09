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
# <a name="client-ip-safelist-for-aspnet-core"></a>ASP.NETコアのクライアント IP セーフリスト

[ダミアン・ボーデン](https://twitter.com/damien_bod)と[トム・ダイクストラ](https://github.com/tdykstra)
 
この記事では、ip アドレス セーフリスト (許可リストとも呼ばれます) を ASP.NET Core アプリに実装する 3 つの方法を示します。 付属のサンプル アプリは、3 つの方法をすべて示しています。 使用できるもの:

* ミドルウェアを使用して、すべての要求のリモート IP アドレスを確認します。
* MVC アクション フィルターを使用して、特定のコントローラーまたはアクション メソッドの要求のリモート IP アドレスを確認します。
* Razor ページフィルターを使用して、Razor ページの要求のリモート IP アドレスを確認します。

いずれの場合も、承認されたクライアント IP アドレスを含む文字列がアプリ設定に格納されます。 ミドルウェアまたはフィルタ:

* 文字列を配列に解析します。 
* アレイ内にリモート IP アドレスが存在するかどうかを確認します。

アレイに IP アドレスが含まれている場合は、アクセスが許可されます。 それ以外の場合は、HTTP 403 の禁止状態コードが返されます。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="ip-address-safelist"></a>IP アドレス セーフリスト

サンプル アプリでは、IP アドレス セーフリストは次のとおりです。

* `AdminSafeList` *appsettings.json*ファイルのプロパティによって定義されます。
* [インターネット プロトコル バージョン 4 (IPv4) とインターネット プロトコル バージョン 6 (IPv6)](https://wikipedia.org/wiki/IPv4)アドレスの両方を含むセミコロン区切りの文字列。 [Internet Protocol version 6 (IPv6)](https://wikipedia.org/wiki/IPv6)

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

前の例では、IPv4`127.0.0.1`アドレスおよび`192.168.1.5`IPv6 ループバック アドレス`::1`( の`0:0:0:0:0:0:0:1`圧縮形式 ) が許可されています。

## <a name="middleware"></a>ミドルウェア

この`Startup.Configure`メソッドは、カスタム`AdminSafeListMiddleware`ミドルウェアの種類をアプリの要求パイプラインに追加します。 セーフリストは .NET Core 構成プロバイダーを使用して取得され、コンストラクター パラメーターとして渡されます。

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

ミドルウェアは文字列を解析して配列にし、配列内のリモート IP アドレスを検索します。 リモート IP アドレスが見つからない場合、ミドルウェアは HTTP 403 Forbidden を返します。 この検証プロセスは、HTTP GET 要求に対してバイパスされます。

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a>アクション フィルター

特定の MVC コントローラーまたはアクション メソッドのセーフリスト駆動型アクセス制御が必要な場合は、アクション フィルターを使用します。 次に例を示します。

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

で`Startup.ConfigureServices`、アクション フィルターを MVC フィルター コレクションに追加します。 次の例では、`ClientIpCheckActionFilter`アクション フィルターが追加されます。 セーフリストとコンソール ロガーインスタンスは、コンストラクターパラメーターとして渡されます。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

アクション フィルターは[、[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute)属性を使用して、コントローラーまたはアクション メソッドに適用できます。

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

サンプル アプリでは、アクション フィルターがコントローラーの`Get`アクション メソッドに適用されます。 送信してアプリをテストする場合:

* HTTP GET 要求の`[ServiceFilter]`属性は、クライアント IP アドレスを検証します。 アクション メソッドへのアクセスが`Get`許可されている場合、アクション フィルターとアクション メソッドによって次のコンソール出力のバリエーションが生成されます。

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* GET 以外の HTTP 要求動詞`AdminSafeListMiddleware`は、ミドルウェアがクライアント IP アドレスを検証します。

## <a name="razor-pages-filter"></a>カミソリページフィルター

Razor ページ アプリのセーフリスト駆動型アクセス制御が必要な場合は、Razor ページ フィルターを使用します。 次に例を示します。

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

で`Startup.ConfigureServices`、MVC フィルター コレクションに追加して、Razor ページ フィルターを有効にします。 次の例では`ClientIpCheckPageFilter`、Razor ページ フィルターが追加されます。 セーフリストとコンソール ロガーインスタンスは、コンストラクターパラメーターとして渡されます。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

サンプル アプリの *[インデックス Razor]* ページが要求されると、Razor ページ フィルターはクライアントの IP アドレスを検証します。 このフィルターは、次のコンソール出力のバリエーションを生成します。

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a>その他のリソース

* <xref:fundamentals/middleware/index>
* [アクション フィルター](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
