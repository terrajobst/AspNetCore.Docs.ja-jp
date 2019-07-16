---
title: ASP.NET Core 用のクライアント IP のセーフリスト
author: damienbod
description: 承認済みの IP アドレスの一覧に対して、リモートの IP アドレスを検証するミドルウェアまたはアクションのフィルターを作成する方法について説明します。
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
# <a name="client-ip-safelist-for-aspnet-core"></a>ASP.NET Core 用のクライアント IP のセーフリスト

によって[Damien Bowden](https://twitter.com/damien_bod)と[Tom Dykstra](https://github.com/tdykstra)
 
この記事では、ASP.NET Core アプリで IP セーフリスト (ホワイト リストとも呼ばれます) を実装するために 3 つの方法を示しています。 使用できます。

* すべての要求のリモート IP アドレスを確認するミドルウェア。
* アクション フィルターが特定のコント ローラーまたはアクション メソッドの要求のリモート IP アドレスを確認します。
* Razor ページの要求のリモート IP アドレスを確認する razor ページのフィルター。

各ケースでは、承認されたクライアントの IP アドレスを含む文字列がアプリ設定に格納されます。 ミドルウェアやフィルターは、一覧に文字列を解析し、リモート ip アドレスの一覧を確認します。 それ以外の場合は、HTTP 403 Forbidden 状態コードが返されます。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="the-safelist"></a>セーフリスト

一覧が構成されている、 *appsettings.json*ファイル。 セミコロンで区切られた一覧し、IPv4 と IPv6 アドレスを含めることができます。

[!code-json[](ip-safelist/samples/2.x/ClientIpAspNetCore/appsettings.json?highlight=2)]

## <a name="middleware"></a>ミドルウェア

`Configure`メソッドは、ミドルウェアを追加し、コンス トラクターのパラメーターでセーフリスト文字列を渡します。

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_Configure&highlight=10)]

ミドルウェアは、配列に文字列を解析し、配列内のリモート IP アドレスを探します。 リモート IP アドレスが見つからない場合は、HTTP 401 許可されていません、ミドルウェアが返されます。 この検証プロセスが、HTTP Get 要求に対してバイパスされます。

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a>アクション フィルター

特定のコント ローラーまたはアクション メソッドに対してのみ、セーフリストをする場合は、アクション フィルターを使用します。 次に例を示します。 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckFilter.cs)]

アクション フィルターは、サービス コンテナーに追加されます。

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

フィルターは、コント ローラーまたはアクション メソッドで使用できます。

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_Filter&highlight=1)]

フィルターを適用するサンプル アプリで、`Get`メソッド。 送信することによって、アプリをテストする場合は、 `Get` API の要求、属性がクライアントの IP アドレスを検証しています。 その他の任意の HTTP メソッドに、API を呼び出すことでテストするときに、ミドルウェア クライアントの ip アドレスの検証です。

## <a name="razor-pages-filter"></a>Razor ページをフィルター処理します。 

セーフリスト、Razor ページ アプリに必要な場合は、Razor ページのフィルターを使用します。 次に例を示します。 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckPageFilter.cs)]

このフィルターは MVC フィルター コレクションに追加することによって有効にします。

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

アプリを実行する Razor ページを要求すると、Razor ページのフィルターは、クライアントの ip アドレスを検証です。

## <a name="next-steps"></a>次の手順

[詳細については、ASP.NET Core のミドルウェアは](xref:fundamentals/middleware/index)します。
