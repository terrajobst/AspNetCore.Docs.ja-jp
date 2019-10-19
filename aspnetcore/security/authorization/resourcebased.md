---
title: ASP.NET Core でのリソースベースの承認
author: scottaddie
description: 承認属性では不十分な場合に、ASP.NET Core アプリにリソースベースの承認を実装する方法について説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 11/15/2018
uid: security/authorization/resourcebased
ms.openlocfilehash: 835592521c714e270595e1448ae6e0aed1707b77
ms.sourcegitcommit: a166291c6708f5949c417874108332856b53b6a9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2019
ms.locfileid: "72590000"
---
# <a name="resource-based-authorization-in-aspnet-core"></a>ASP.NET Core でのリソースベースの承認

承認方法は、アクセスされるリソースによって異なります。 Author プロパティを持つドキュメントについて考えてみましょう。 作成者だけがドキュメントを更新できます。 そのため、承認評価を行う前に、データストアからドキュメントを取得する必要があります。

属性の評価は、データバインディングの前、およびドキュメントを読み込むページハンドラーまたはアクションの実行前に行われます。 このような理由から、`[Authorize]` 属性を使用した宣言型の承認は十分ではありません。 代わりに、*強制認証*と呼ばれるカスタム承認メソッド &mdash;a スタイルを呼び出すことができます。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

[承認によって保護されたユーザーデータを含む ASP.NET Core アプリを作成](xref:security/authorization/secure-data)するには、リソースベースの承認を使用するサンプルアプリが含まれています。

## <a name="use-imperative-authorization"></a>強制認証を使用する

承認は[IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice)サービスとして実装され、`Startup` クラス内のサービスコレクションに登録されます。 サービスは、ページハンドラーまたはアクションへの[依存関係の挿入](xref:fundamentals/dependency-injection)によって利用可能になります。

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Controllers/DocumentController.cs?name=snippet_IAuthServiceDI&highlight=6)]

`IAuthorizationService` には2つの `AuthorizeAsync` メソッドオーバーロードがあります。1つはリソースとポリシー名を受け入れ、もう1つはリソースを受け入れ、もう1つは評価する要件のリストです。

::: moniker range=">= aspnetcore-2.0"

```csharp
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

```csharp
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

<a name="security-authorization-resource-based-imperative"></a>

次の例では、セキュリティで保護されるリソースがカスタム `Document` オブジェクトに読み込まれます。 現在のユーザーが指定されたドキュメントの編集を許可されているかどうかを判断するために、`AuthorizeAsync` のオーバーロードが呼び出されます。 カスタムの "EditPolicy" 承認ポリシーが決定に考慮されます。 承認ポリシーの作成の詳細については[、「カスタムポリシーベースの承認](xref:security/authorization/policies)」を参照してください。

> [!NOTE]
> 次のコードサンプルでは、認証が実行されていることを前提として、`User` プロパティを設定します。

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Pages/Document/Edit.cshtml.cs?name=snippet_DocumentEditHandler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentEditAction)]

::: moniker-end

## <a name="write-a-resource-based-handler"></a>リソースベースのハンドラーを記述する

リソースベースの承認のハンドラーを記述することは、[プレーンな要件ハンドラーを記述](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler)する方法とは大きく異なります。 カスタム要件クラスを作成し、要件ハンドラークラスを実装します。 要件クラスの作成の詳細については、「[要件](xref:security/authorization/policies#requirements)」を参照してください。

ハンドラークラスは、要件とリソースの種類の両方を指定します。 たとえば、`SameAuthorRequirement` と `Document` リソースを使用するハンドラーは、次のようになります。

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

前の例では、`SameAuthorRequirement` が、より汎用的な `SpecificAuthorRequirement` クラスの特殊なケースであると仮定します。 @No__t_0 クラス (表示されません) には、作成者の名前を表す `Name` プロパティが含まれています。 @No__t_0 プロパティは、現在のユーザーに設定できます。

@No__t_0 に要件とハンドラーを登録します。

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]

### <a name="operational-requirements"></a>運用上の要件

CRUD (作成、読み取り、更新、削除) 操作の結果に基づいて意思決定を行う場合は、 [Operationauthorizationrequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement)ヘルパークラスを使用します。 このクラスを使用すると、操作の種類ごとに個別のクラスではなく、1つのハンドラーを記述できます。 これを使用するには、いくつかの操作名を指定します。

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_OperationsClass)]

このハンドラーは、`OperationAuthorizationRequirement` 要件と `Document` リソースを使用して、次のように実装されます。

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

上のハンドラーは、リソース、ユーザーの id、および要件の `Name` プロパティを使用して操作を検証します。

## <a name="challenge-and-forbid-with-an-operational-resource-handler"></a>運用リソースハンドラーを使用したチャレンジと禁止

このセクションでは、チャレンジと禁止アクションの結果がどのように処理されるか、およびチャレンジと禁止の違いについて説明します。

操作リソースハンドラーを呼び出すには、ページハンドラーまたはアクションで `AuthorizeAsync` を呼び出すときに操作を指定します。 次の例では、認証されたユーザーが、指定されたドキュメントを表示できるかどうかを判断します。

> [!NOTE]
> 次のコードサンプルでは、認証が実行されていることを前提として、`User` プロパティを設定します。

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Pages/Document/View.cshtml.cs?name=snippet_DocumentViewHandler&highlight=10-11)]

承認が成功した場合は、ドキュメントを表示するためのページが返されます。 認証に失敗しても、ユーザーが認証されると、認証が失敗したことを認証ミドルウェアに通知する `ForbidResult` を返します。 認証を実行する必要がある場合は、`ChallengeResult` が返されます。 対話型ブラウザークライアントの場合は、ユーザーをログインページにリダイレクトすることが適切な場合があります。

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentViewAction&highlight=11-12)]

承認が成功すると、ドキュメントのビューが返されます。 承認が失敗した場合、認証が失敗したことを認証ミドルウェアに通知し、ミドルウェアが適切な応答を受け取ることができることを `ChallengeResult` 返します。 適切な応答は、401または403状態コードを返す可能性があります。 対話型ブラウザークライアントの場合、ユーザーをログインページにリダイレクトすることがあります。

::: moniker-end
