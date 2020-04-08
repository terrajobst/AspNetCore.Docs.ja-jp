---
title: ASP.NET Core アプリの Details メソッドと Delete メソッドの確認
author: rick-anderson
description: 基本的な ASP.NET Core MVC アプリの Details コントローラー メソッドとビューについて説明します。
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: 04eb2efa4e67d84e575580a6248d0b5b567064af
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78648866"
---
# <a name="examine-the-details-and-delete-methods-of-an-aspnet-core-app"></a>ASP.NET Core アプリの Details メソッドと Delete メソッドの確認

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

Movie コントローラーを開き、`Details` メソッドを調べます。

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_details)]

このアクション メソッドを作成した MVC スキャフォールディング エンジンがコメントを追加します。そのコメントには、メソッドを呼び出した HTTP 要求が示されます。 この場合は、URL セグメントが 3 つある GET 要求です。セグメントの内訳は、`Movies` コントローラー、`Details` メソッド、`id` 値です。 これらのセグメントは *Startup.cs* で定義されていることを思い出してください。

[!code-csharp[](start-mvc/sample/MvcMovie3/Startup.cs?highlight=5&name=snippet_1)]

EF は、`FirstOrDefaultAsync` メソッドによるデータの検索を簡単にします。 このメソッドには重要なセキュリティ機能が組み込まれています。それは、検索メソッドがムービーを見つけたということをコードが確認し、それからその見つけたムービーで何らかの処理を行うということです。 たとえば、リンクが作成する URL を `http://localhost:{PORT}/Movies/Details/1` から `http://localhost:{PORT}/Movies/Details/12345` のようなものに変更することで、ハッカーはサイトにエラーを誘発できます。 null のムービーを確認しなかった場合、アプリは例外をスローします。

`Delete` メソッドと `DeleteConfirmed` メソッドを調べます。

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_delete)]

`HTTP GET Delete` メソッドは指定のムービーを削除せず、削除を送信 (HttpPost) できるムービービューを返します。 GET 要求の応答で削除操作を実行すると (さらに言えば、編集操作、作成操作、データを変更するその他のあらゆる操作を実行すると)、セキュリティに穴が空きます。

データを削除する `[HttpPost]` メソッドの名前は「`DeleteConfirmed`」になり、HTTP POST メソッドに一意のシグネチャまたは名前が与えられます。 2 つのメソッド シグネチャは下の画像のようになります。

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]

共通言語ランタイム (CLR) は、オーバーロードのメソッドに一意のパラメーター シグネチャを持つことを要求します (メソッド名は同じであるが、パラメーターの一覧が異なる)。 ただし、ここでは、同じパラメーター シグネチャを持つ 2 つの `Delete` メソッドが必要です。GET に 1 つ、POST に 1 つです。 (いずれも、1 つの整数をパラメーターとして受け取る必要があります。)

この問題には 2 つの取り組み方があります。その 1 つは、メソッドに異なる名前を与えることです。 先の例では、スキャフォールディング メカニズムがこれを行いました。 ただし、これは小さな問題を引き起こします。ASP.NET が URL のセグメントをアクション メソッドに名前でマッピングします。メソッドの名前を変更すると、通常、ルーティングでそのメソッドが見つからなくなります。 この解決策はこの例で確認できます。`ActionName("Delete")` 属性を `DeleteConfirmed` メソッドに追加しています。 この属性はルーティング システムにマッピングを実行します。POST 要求の /Delete/ を含む URL が `DeleteConfirmed` メソッドを見つけます。

同じ名前とシグネチャを持つメソッドに対するもう 1 つの一般的な回避策は、POST メソッドのシグネチャを人為的に変更し、(使用されない) 余分のパラメーターを追加することです。 前の投稿ではこれを行いました。`notUsed` パラメーターを追加しました。 同じことをここで、`[HttpPost] Delete` メソッドに対して行うことができます。

```csharp
// POST: Movies/Delete/6
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

### <a name="publish-to-azure"></a>Azure に発行する

Azure へのデプロイについては、「[チュートリアル: Azure App Service での .NET Core および SQL Database の Web アプリの作成](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)」を参照してください。

> [!div class="step-by-step"]
> [前へ](validation.md)
