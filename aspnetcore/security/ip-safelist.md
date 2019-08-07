---
title: ASP.NET Core のクライアント IP セーフセーフ
author: damienbod
description: 承認済み IP アドレスの一覧に対してリモート IP アドレスを検証するミドルウェアまたはアクションフィルターを作成する方法について説明します。
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
# <a name="client-ip-safelist-for-aspnet-core"></a>ASP.NET Core のクライアント IP セーフセーフ

By [Damien Bowden](https://twitter.com/damien_bod)および[Tom Dykstra](https://github.com/tdykstra)
 
この記事では、ASP.NET Core アプリで IP セーフリスト (ホワイトリストとも呼ばれます) を実装する3つの方法について説明します。 次のものを使用できます。

* ミドルウェア。すべての要求のリモート IP アドレスを確認します。
* アクションフィルターを使用して、特定のコントローラーまたはアクションメソッドに対する要求のリモート IP アドレスを確認します。
* Razor Pages フィルターを使用して、Razor ページの要求のリモート IP アドレスを確認します。

どちらの場合も、承認済みのクライアント IP アドレスを含む文字列は、アプリ設定に格納されます。 ミドルウェアまたはフィルタは、文字列を一覧に解析し、リモート IP が一覧に含まれているかどうかを確認します。 それ以外の場合は、HTTP 403 の許可されていない状態コードが返されます。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="the-safelist"></a>セーフセーフ

この一覧は、 *appsettings*ファイルで構成されています。 これはセミコロンで区切られたリストで、IPv4 と IPv6 のアドレスを含むことができます。

[!code-json[](ip-safelist/samples/2.x/ClientIpAspNetCore/appsettings.json?highlight=2)]

## <a name="middleware"></a>ミドルウェア

メソッド`Configure`は、ミドルウェアを追加し、そのセーフの文字列をコンストラクターパラメーターに渡します。

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_Configure&highlight=10)]

ミドルウェアは、文字列を配列に解析し、配列内のリモート IP アドレスを検索します。 リモート IP アドレスが見つからない場合、ミドルウェアは HTTP 401 禁止を返します。 この検証プロセスは、HTTP Get 要求ではバイパスされます。

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a>アクションフィルター

特定のコントローラーまたはアクションメソッドに対してのみセーフセーフを使用する場合は、アクションフィルターを使用します。 次に例を示します。 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckFilter.cs)]

アクションフィルターがサービスコンテナーに追加されます。

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

その後、コントローラーまたはアクションメソッドでフィルターを使用できます。

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_Filter&highlight=1)]

サンプルアプリでは、フィルターが`Get`メソッドに適用されます。 そのため、 `Get` API 要求を送信してアプリをテストする場合、属性はクライアントの IP アドレスを検証しています。 他の HTTP メソッドを使用して API を呼び出すことによってテストする場合、ミドルウェアはクライアント IP を検証しています。

## <a name="razor-pages-filter"></a>Razor Pages フィルター 

Razor Pages アプリのセーフセーフを必要とする場合は、Razor Pages フィルターを使用します。 次に例を示します。 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckPageFilter.cs)]

このフィルターは、MVC フィルターコレクションに追加することで有効になります。

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

アプリを実行して Razor ページを要求すると、Razor Pages フィルターによってクライアント IP が検証されます。

## <a name="next-steps"></a>次の手順

[ASP.NET Core ミドルウェアの詳細についてはこちらをご覧](xref:fundamentals/middleware/index)ください。
