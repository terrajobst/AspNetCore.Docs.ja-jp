---
title: ASP.NET Core のクライアント IP セーフリスト
author: damienbod
description: 承認済み IP アドレスの一覧に対してリモート IP アドレスを検証するミドルウェアまたはアクションフィルターを作成する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 08/31/2018
uid: security/ip-safelist
ms.openlocfilehash: d25c375f7e659168ab8cc9d8e11753cb7dfde831
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652052"
---
# <a name="client-ip-safelist-for-aspnet-core"></a>ASP.NET Core のクライアント IP セーフリスト

By [Damien Bowden](https://twitter.com/damien_bod)および[Tom Dykstra](https://github.com/tdykstra)
 
この記事では、ASP.NET Core アプリで IP セーフリスト (ホワイトリストとも呼ばれます) を実装する3つの方法について説明します。 使用できるもの:

* ミドルウェアを使用して、すべての要求のリモート IP アドレスを確認します。
* アクションフィルターを使用して、特定のコントローラーまたはアクションメソッドに対する要求のリモート IP アドレスを確認します。
* Razor Pages フィルターを使用して、Razor ページに対する要求のリモート IP アドレスを確認します。

どちらの場合も、承認済みのクライアント IP アドレスを含む文字列は、アプリ設定に格納されます。 ミドルウェアまたはフィルタは、文字列を解析してリストにし、リモート IP がそのリストに含まれているかどうかを確認します。 含まれていない場合は、HTTP 403 Forbidden ステータスコードが返されます。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="the-safelist"></a>セーフリスト

この一覧は、 *appsettings*ファイルで構成されています。 これはセミコロンで区切られたリストで、IPv4 と IPv6 のアドレスを含むことができます。

[!code-json[](ip-safelist/samples/2.x/ClientIpAspNetCore/appsettings.json?highlight=2)]

## <a name="middleware"></a>ミドルウェア

`Configure` メソッドは、ミドルウェアを追加し、そのセーフの文字列をコンストラクターパラメーターに渡します。

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_Configure&highlight=10)]

ミドルウェアは、文字列を解析して配列にし、その配列内でリモート IP アドレスを検索します。 リモート IP アドレスが見つからない場合、ミドルウェアは HTTP 401 Forbidden を返します。 この検証プロセスは、HTTP Get 要求ではバイパスされます。

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a>アクションフィルター

特定のコントローラーまたはアクションメソッドに対してのみセーフリストを使用する場合は、アクションフィルターを使用します。 次に例を示します。 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIpCheckFilter.cs)]

アクションフィルターがサービスコンテナーに追加されます。

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

その後、コントローラーまたはアクションメソッドでフィルターを使用できます。

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_Filter&highlight=1)]

サンプルアプリでは、フィルターが `Get` メソッドに適用されます。 そのため、`Get` API 要求を送信してアプリをテストする場合、属性はクライアントの IP アドレスを検証しています。 他の HTTP メソッドを使用して API を呼び出すことによってテストする場合、ミドルウェアがクライアント IP を検証しています。

## <a name="razor-pages-filter"></a>Razor Pages フィルター 

Razor Pages アプリでセーフリストを必要とする場合は、Razor Pages フィルターを使用します。 次に例を示します。 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIpCheckPageFilter.cs)]

このフィルターは、MVC フィルターコレクションに追加することで有効になります。

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

アプリを実行して Razor ページを要求すると、Razor Pages フィルターによってクライアント IP が検証されます。

## <a name="next-steps"></a>次のステップ:

[ASP.NET Core ミドルウェアの詳細についてはこちらをご覧](xref:fundamentals/middleware/index)ください。
