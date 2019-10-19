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
# <a name="resource-based-authorization-in-aspnet-core"></a><span data-ttu-id="84db2-103">ASP.NET Core でのリソースベースの承認</span><span class="sxs-lookup"><span data-stu-id="84db2-103">Resource-based authorization in ASP.NET Core</span></span>

<span data-ttu-id="84db2-104">承認方法は、アクセスされるリソースによって異なります。</span><span class="sxs-lookup"><span data-stu-id="84db2-104">Authorization strategy depends upon the resource being accessed.</span></span> <span data-ttu-id="84db2-105">Author プロパティを持つドキュメントについて考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="84db2-105">Consider a document that has an author property.</span></span> <span data-ttu-id="84db2-106">作成者だけがドキュメントを更新できます。</span><span class="sxs-lookup"><span data-stu-id="84db2-106">Only the author is allowed to update the document.</span></span> <span data-ttu-id="84db2-107">そのため、承認評価を行う前に、データストアからドキュメントを取得する必要があります。</span><span class="sxs-lookup"><span data-stu-id="84db2-107">Consequently, the document must be retrieved from the data store before authorization evaluation can occur.</span></span>

<span data-ttu-id="84db2-108">属性の評価は、データバインディングの前、およびドキュメントを読み込むページハンドラーまたはアクションの実行前に行われます。</span><span class="sxs-lookup"><span data-stu-id="84db2-108">Attribute evaluation occurs before data binding and before execution of the page handler or action that loads the document.</span></span> <span data-ttu-id="84db2-109">このような理由から、`[Authorize]` 属性を使用した宣言型の承認は十分ではありません。</span><span class="sxs-lookup"><span data-stu-id="84db2-109">For these reasons, declarative authorization with an `[Authorize]` attribute doesn't suffice.</span></span> <span data-ttu-id="84db2-110">代わりに、*強制認証*と呼ばれるカスタム承認メソッド &mdash;a スタイルを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="84db2-110">Instead, you can invoke a custom authorization method&mdash;a style known as *imperative authorization*.</span></span>

<span data-ttu-id="84db2-111">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="84db2-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="84db2-112">[承認によって保護されたユーザーデータを含む ASP.NET Core アプリを作成](xref:security/authorization/secure-data)するには、リソースベースの承認を使用するサンプルアプリが含まれています。</span><span class="sxs-lookup"><span data-stu-id="84db2-112">[Create an ASP.NET Core app with user data protected by authorization](xref:security/authorization/secure-data) contains a sample app that uses resource-based authorization.</span></span>

## <a name="use-imperative-authorization"></a><span data-ttu-id="84db2-113">強制認証を使用する</span><span class="sxs-lookup"><span data-stu-id="84db2-113">Use imperative authorization</span></span>

<span data-ttu-id="84db2-114">承認は[IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice)サービスとして実装され、`Startup` クラス内のサービスコレクションに登録されます。</span><span class="sxs-lookup"><span data-stu-id="84db2-114">Authorization is implemented as an [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) service and is registered in the service collection within the `Startup` class.</span></span> <span data-ttu-id="84db2-115">サービスは、ページハンドラーまたはアクションへの[依存関係の挿入](xref:fundamentals/dependency-injection)によって利用可能になります。</span><span class="sxs-lookup"><span data-stu-id="84db2-115">The service is made available via [dependency injection](xref:fundamentals/dependency-injection) to page handlers or actions.</span></span>

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Controllers/DocumentController.cs?name=snippet_IAuthServiceDI&highlight=6)]

<span data-ttu-id="84db2-116">`IAuthorizationService` には2つの `AuthorizeAsync` メソッドオーバーロードがあります。1つはリソースとポリシー名を受け入れ、もう1つはリソースを受け入れ、もう1つは評価する要件のリストです。</span><span class="sxs-lookup"><span data-stu-id="84db2-116">`IAuthorizationService` has two `AuthorizeAsync` method overloads: one accepting the resource and the policy name and the other accepting the resource and a list of requirements to evaluate.</span></span>

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

<span data-ttu-id="84db2-117">次の例では、セキュリティで保護されるリソースがカスタム `Document` オブジェクトに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="84db2-117">In the following example, the resource to be secured is loaded into a custom `Document` object.</span></span> <span data-ttu-id="84db2-118">現在のユーザーが指定されたドキュメントの編集を許可されているかどうかを判断するために、`AuthorizeAsync` のオーバーロードが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="84db2-118">An `AuthorizeAsync` overload is invoked to determine whether the current user is allowed to edit the provided document.</span></span> <span data-ttu-id="84db2-119">カスタムの "EditPolicy" 承認ポリシーが決定に考慮されます。</span><span class="sxs-lookup"><span data-stu-id="84db2-119">A custom "EditPolicy" authorization policy is factored into the decision.</span></span> <span data-ttu-id="84db2-120">承認ポリシーの作成の詳細については[、「カスタムポリシーベースの承認](xref:security/authorization/policies)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="84db2-120">See [Custom policy-based authorization](xref:security/authorization/policies) for more on creating authorization policies.</span></span>

> [!NOTE]
> <span data-ttu-id="84db2-121">次のコードサンプルでは、認証が実行されていることを前提として、`User` プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="84db2-121">The following code samples assume authentication has run and set the `User` property.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Pages/Document/Edit.cshtml.cs?name=snippet_DocumentEditHandler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentEditAction)]

::: moniker-end

## <a name="write-a-resource-based-handler"></a><span data-ttu-id="84db2-122">リソースベースのハンドラーを記述する</span><span class="sxs-lookup"><span data-stu-id="84db2-122">Write a resource-based handler</span></span>

<span data-ttu-id="84db2-123">リソースベースの承認のハンドラーを記述することは、[プレーンな要件ハンドラーを記述](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler)する方法とは大きく異なります。</span><span class="sxs-lookup"><span data-stu-id="84db2-123">Writing a handler for resource-based authorization isn't much different than [writing a plain requirements handler](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler).</span></span> <span data-ttu-id="84db2-124">カスタム要件クラスを作成し、要件ハンドラークラスを実装します。</span><span class="sxs-lookup"><span data-stu-id="84db2-124">Create a custom requirement class, and implement a requirement handler class.</span></span> <span data-ttu-id="84db2-125">要件クラスの作成の詳細については、「[要件](xref:security/authorization/policies#requirements)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="84db2-125">For more information on creating a requirement class, see [Requirements](xref:security/authorization/policies#requirements).</span></span>

<span data-ttu-id="84db2-126">ハンドラークラスは、要件とリソースの種類の両方を指定します。</span><span class="sxs-lookup"><span data-stu-id="84db2-126">The handler class specifies both the requirement and resource type.</span></span> <span data-ttu-id="84db2-127">たとえば、`SameAuthorRequirement` と `Document` リソースを使用するハンドラーは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="84db2-127">For example, a handler utilizing a `SameAuthorRequirement` and a `Document` resource follows:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

<span data-ttu-id="84db2-128">前の例では、`SameAuthorRequirement` が、より汎用的な `SpecificAuthorRequirement` クラスの特殊なケースであると仮定します。</span><span class="sxs-lookup"><span data-stu-id="84db2-128">In the preceding example, imagine that `SameAuthorRequirement` is a special case of a more generic `SpecificAuthorRequirement` class.</span></span> <span data-ttu-id="84db2-129">@No__t_0 クラス (表示されません) には、作成者の名前を表す `Name` プロパティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="84db2-129">The `SpecificAuthorRequirement` class (not shown) contains a `Name` property representing the name of the author.</span></span> <span data-ttu-id="84db2-130">@No__t_0 プロパティは、現在のユーザーに設定できます。</span><span class="sxs-lookup"><span data-stu-id="84db2-130">The `Name` property could be set to the current user.</span></span>

<span data-ttu-id="84db2-131">@No__t_0 に要件とハンドラーを登録します。</span><span class="sxs-lookup"><span data-stu-id="84db2-131">Register the requirement and handler in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]

### <a name="operational-requirements"></a><span data-ttu-id="84db2-132">運用上の要件</span><span class="sxs-lookup"><span data-stu-id="84db2-132">Operational requirements</span></span>

<span data-ttu-id="84db2-133">CRUD (作成、読み取り、更新、削除) 操作の結果に基づいて意思決定を行う場合は、 [Operationauthorizationrequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement)ヘルパークラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="84db2-133">If you're making decisions based on the outcomes of CRUD (Create, Read, Update, Delete) operations, use the [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) helper class.</span></span> <span data-ttu-id="84db2-134">このクラスを使用すると、操作の種類ごとに個別のクラスではなく、1つのハンドラーを記述できます。</span><span class="sxs-lookup"><span data-stu-id="84db2-134">This class enables you to write a single handler instead of an individual class for each operation type.</span></span> <span data-ttu-id="84db2-135">これを使用するには、いくつかの操作名を指定します。</span><span class="sxs-lookup"><span data-stu-id="84db2-135">To use it, provide some operation names:</span></span>

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_OperationsClass)]

<span data-ttu-id="84db2-136">このハンドラーは、`OperationAuthorizationRequirement` 要件と `Document` リソースを使用して、次のように実装されます。</span><span class="sxs-lookup"><span data-stu-id="84db2-136">The handler is implemented as follows, using an `OperationAuthorizationRequirement` requirement and a `Document` resource:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

<span data-ttu-id="84db2-137">上のハンドラーは、リソース、ユーザーの id、および要件の `Name` プロパティを使用して操作を検証します。</span><span class="sxs-lookup"><span data-stu-id="84db2-137">The preceding handler validates the operation using the resource, the user's identity, and the requirement's `Name` property.</span></span>

## <a name="challenge-and-forbid-with-an-operational-resource-handler"></a><span data-ttu-id="84db2-138">運用リソースハンドラーを使用したチャレンジと禁止</span><span class="sxs-lookup"><span data-stu-id="84db2-138">Challenge and forbid with an operational resource handler</span></span>

<span data-ttu-id="84db2-139">このセクションでは、チャレンジと禁止アクションの結果がどのように処理されるか、およびチャレンジと禁止の違いについて説明します。</span><span class="sxs-lookup"><span data-stu-id="84db2-139">This section shows how the challenge and forbid action results are processed and how challenge and forbid differ.</span></span>

<span data-ttu-id="84db2-140">操作リソースハンドラーを呼び出すには、ページハンドラーまたはアクションで `AuthorizeAsync` を呼び出すときに操作を指定します。</span><span class="sxs-lookup"><span data-stu-id="84db2-140">To call an operational resource handler, specify the operation when invoking `AuthorizeAsync` in your page handler or action.</span></span> <span data-ttu-id="84db2-141">次の例では、認証されたユーザーが、指定されたドキュメントを表示できるかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="84db2-141">The following example determines whether the authenticated user is permitted to view the provided document.</span></span>

> [!NOTE]
> <span data-ttu-id="84db2-142">次のコードサンプルでは、認証が実行されていることを前提として、`User` プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="84db2-142">The following code samples assume authentication has run and set the `User` property.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Pages/Document/View.cshtml.cs?name=snippet_DocumentViewHandler&highlight=10-11)]

<span data-ttu-id="84db2-143">承認が成功した場合は、ドキュメントを表示するためのページが返されます。</span><span class="sxs-lookup"><span data-stu-id="84db2-143">If authorization succeeds, the page for viewing the document is returned.</span></span> <span data-ttu-id="84db2-144">認証に失敗しても、ユーザーが認証されると、認証が失敗したことを認証ミドルウェアに通知する `ForbidResult` を返します。</span><span class="sxs-lookup"><span data-stu-id="84db2-144">If authorization fails but the user is authenticated, returning `ForbidResult` informs any authentication middleware that authorization failed.</span></span> <span data-ttu-id="84db2-145">認証を実行する必要がある場合は、`ChallengeResult` が返されます。</span><span class="sxs-lookup"><span data-stu-id="84db2-145">A `ChallengeResult` is returned when authentication must be performed.</span></span> <span data-ttu-id="84db2-146">対話型ブラウザークライアントの場合は、ユーザーをログインページにリダイレクトすることが適切な場合があります。</span><span class="sxs-lookup"><span data-stu-id="84db2-146">For interactive browser clients, it may be appropriate to redirect the user to a login page.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentViewAction&highlight=11-12)]

<span data-ttu-id="84db2-147">承認が成功すると、ドキュメントのビューが返されます。</span><span class="sxs-lookup"><span data-stu-id="84db2-147">If authorization succeeds, the view for the document is returned.</span></span> <span data-ttu-id="84db2-148">承認が失敗した場合、認証が失敗したことを認証ミドルウェアに通知し、ミドルウェアが適切な応答を受け取ることができることを `ChallengeResult` 返します。</span><span class="sxs-lookup"><span data-stu-id="84db2-148">If authorization fails, returning `ChallengeResult` informs any authentication middleware that authorization failed, and the middleware can take the appropriate response.</span></span> <span data-ttu-id="84db2-149">適切な応答は、401または403状態コードを返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="84db2-149">An appropriate response could be returning a 401 or 403 status code.</span></span> <span data-ttu-id="84db2-150">対話型ブラウザークライアントの場合、ユーザーをログインページにリダイレクトすることがあります。</span><span class="sxs-lookup"><span data-stu-id="84db2-150">For interactive browser clients, it could mean redirecting the user to a login page.</span></span>

::: moniker-end
