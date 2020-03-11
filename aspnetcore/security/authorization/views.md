---
title: ASP.NET Core MVC でのビューベースの承認
author: rick-anderson
description: このドキュメントでは、ASP.NET Core Razor ビュー内で承認サービスを挿入および利用する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
uid: security/authorization/views
ms.openlocfilehash: fc03da9eb98d36ffdda932ee5b16f327c2be9f83
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653564"
---
# <a name="view-based-authorization-in-aspnet-core-mvc"></a>ASP.NET Core MVC でのビューベースの承認

多くの場合、開発者は、現在のユーザー id に基づいて UI を表示、非表示にする、または変更する必要があります。 MVC ビュー内の承認サービスには、[依存関係の挿入](xref:fundamentals/dependency-injection)によってアクセスできます。 Razor ビューに承認サービスを挿入するには、`@inject` ディレクティブを使用します。

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

すべてのビューで承認サービスを使用する場合は、`@inject` ディレクティブを*Views*ディレクトリの *_ViewImports*ファイルに置きます。 詳しくは、「[ビューへの依存関係の挿入](xref:mvc/views/dependency-injection)」をご覧ください。

挿入された承認サービスを使用して、[リソースベースの承認](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative)時に確認するのとまったく同じ方法で `AuthorizeAsync` を呼び出します。

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

場合によっては、リソースがビューモデルになります。 [リソースベースの承認](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative)時に確認するのとまったく同じ方法で `AuthorizeAsync` を呼び出します。

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

前のコードでは、モデルはポリシーの評価で考慮する必要があるリソースとして渡されます。

> [!WARNING]
> 唯一の承認チェックとして、アプリの UI 要素の表示の切り替えに依存しないでください。 UI 要素を非表示にしても、関連付けられているコントローラーアクションへのアクセスを完全に防ぐことはできません。 たとえば、前のコードスニペットのボタンについて考えてみます。 ユーザーは、相対リソース URL が */Document/Edit/1*であることを知っていれば、`Edit` アクションメソッドを呼び出すことができます。 このため、`Edit` アクションメソッドでは、独自の承認チェックを実行する必要があります。
