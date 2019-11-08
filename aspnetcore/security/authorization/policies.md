---
title: ASP.NET Core でのポリシーベースの承認
author: rick-anderson
description: ASP.NET Core アプリで承認要件を強制するために承認ポリシーハンドラーを作成して使用する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 10/05/2019
uid: security/authorization/policies
ms.openlocfilehash: e3929fb0f45d4ba28f46a6b42b653940de0badb0
ms.sourcegitcommit: 6628cd23793b66e4ce88788db641a5bbf470c3c1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2019
ms.locfileid: "73761041"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="67b9e-103">ASP.NET Core でのポリシーベースの承認</span><span class="sxs-lookup"><span data-stu-id="67b9e-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="67b9e-104">内部的には、[ロールベースの承認](xref:security/authorization/roles)と[要求ベースの承認](xref:security/authorization/claims)では、要件、要件ハンドラー、および事前に構成されたポリシーを使用します。</span><span class="sxs-lookup"><span data-stu-id="67b9e-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="67b9e-105">これらの構成要素は、コードでの承認評価の式をサポートします。</span><span class="sxs-lookup"><span data-stu-id="67b9e-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="67b9e-106">結果として、より充実した再利用可能な承認の構造が得られます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="67b9e-107">承認ポリシーは、1つまたは複数の要件で構成されます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="67b9e-108">認証サービス構成の一部として、`Startup.ConfigureServices` メソッドに登録されます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="67b9e-109">前の例では、"AtLeast21" ポリシーが作成されています。</span><span class="sxs-lookup"><span data-stu-id="67b9e-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="67b9e-110">これには、要件にパラメーターとして指定されている最小有効期間の1つの要件&mdash;があります。</span><span class="sxs-lookup"><span data-stu-id="67b9e-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="67b9e-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="67b9e-111">IAuthorizationService</span></span> 

<span data-ttu-id="67b9e-112">承認が成功したかどうかを判断するプライマリサービスは <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="67b9e-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="67b9e-113">上記のコードは、 [IAuthorizationService](https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)の2つのメソッドを強調表示しています。</span><span class="sxs-lookup"><span data-stu-id="67b9e-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="67b9e-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> は、メソッドのないマーカーサービスと、承認が成功したかどうかを追跡するための機構です。</span><span class="sxs-lookup"><span data-stu-id="67b9e-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="67b9e-115">各 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> は、要件が満たされているかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="67b9e-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
<!--The following code is a copy/paste from 
https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

<span data-ttu-id="67b9e-116"><xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> クラスは、要件が満たされているかどうかをマークするためにハンドラーが使用するものです。</span><span class="sxs-lookup"><span data-stu-id="67b9e-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="67b9e-117">次のコードは、承認サービスの簡略化された (コメントで注釈が付けられた) 既定の実装を示しています。</span><span class="sxs-lookup"><span data-stu-id="67b9e-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

<span data-ttu-id="67b9e-118">次のコードは、一般的な `ConfigureServices`を示しています。</span><span class="sxs-lookup"><span data-stu-id="67b9e-118">The following code shows a typical `ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddControllersWithViews();
    services.AddRazorPages();
}
```

<span data-ttu-id="67b9e-119">承認には <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> または `[Authorize(Policy = "Something")]` を使用します。</span><span class="sxs-lookup"><span data-stu-id="67b9e-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="67b9e-120">MVC コントローラーへのポリシーの適用</span><span class="sxs-lookup"><span data-stu-id="67b9e-120">Applying policies to MVC controllers</span></span>

<span data-ttu-id="67b9e-121">Razor Pages を使用している場合は、このドキュメントの「 [Razor Pages にポリシーを適用する](#applying-policies-to-razor-pages)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="67b9e-121">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="67b9e-122">ポリシーは、ポリシー名と共に `[Authorize]` 属性を使用して、コントローラーに適用されます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="67b9e-123">(例:</span><span class="sxs-lookup"><span data-stu-id="67b9e-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="67b9e-124">Razor Pages にポリシーを適用する</span><span class="sxs-lookup"><span data-stu-id="67b9e-124">Applying policies to Razor Pages</span></span>

<span data-ttu-id="67b9e-125">ポリシーは、`[Authorize]` 属性をポリシー名と共に使用して、Razor Pages に適用されます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="67b9e-126">(例:</span><span class="sxs-lookup"><span data-stu-id="67b9e-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="67b9e-127">ポリシーは、[承認規則](xref:security/authorization/razor-pages-authorization)を使用して Razor Pages に適用することもできます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="67b9e-128">［要件］</span><span class="sxs-lookup"><span data-stu-id="67b9e-128">Requirements</span></span>

<span data-ttu-id="67b9e-129">承認要件とは、ポリシーが現在のユーザープリンシパルを評価するために使用できるデータパラメーターのコレクションです。</span><span class="sxs-lookup"><span data-stu-id="67b9e-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="67b9e-130">"AtLeast21" ポリシーでは、最小有効期間を&mdash;1 つのパラメーターが必要です。</span><span class="sxs-lookup"><span data-stu-id="67b9e-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="67b9e-131">要件には、空のマーカーインターフェイスである[IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)が実装されています。</span><span class="sxs-lookup"><span data-stu-id="67b9e-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="67b9e-132">パラメーター化された最小年齢要件は、次のように実装できます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="67b9e-133">承認ポリシーに複数の承認要件が含まれている場合は、ポリシーの評価を成功させるためにすべての要件を満たしている必要があります。</span><span class="sxs-lookup"><span data-stu-id="67b9e-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="67b9e-134">つまり、1つの承認ポリシーに追加された複数の承認要件は、**と**の基準で処理されます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="67b9e-135">要件には、データまたはプロパティを含める必要はありません。</span><span class="sxs-lookup"><span data-stu-id="67b9e-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="67b9e-136">承認ハンドラー</span><span class="sxs-lookup"><span data-stu-id="67b9e-136">Authorization handlers</span></span>

<span data-ttu-id="67b9e-137">承認ハンドラーは、要件のプロパティを評価する役割を担います。</span><span class="sxs-lookup"><span data-stu-id="67b9e-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="67b9e-138">承認ハンドラーは、指定された[authorizationhandler コンテキスト](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext)に対して要件を評価して、アクセスが許可されているかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="67b9e-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="67b9e-139">要件には、[複数のハンドラー](#security-authorization-policies-based-multiple-handlers)を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="67b9e-140">ハンドラーは、 [Authorizationhandler\<TRequirement >](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)を継承できます。 `TRequirement` は処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="67b9e-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="67b9e-141">また、1つのハンドラーで[IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler)を実装して、複数の種類の要件を処理することもできます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="67b9e-142">1つの要件に対してハンドラーを使用する</span><span class="sxs-lookup"><span data-stu-id="67b9e-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="67b9e-143">次に示すのは、最小年齢ハンドラが1つの要件を利用する一対一のリレーションシップの例です。</span><span class="sxs-lookup"><span data-stu-id="67b9e-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="67b9e-144">前のコードは、現在のユーザープリンシパルが、既知の信頼された発行者によって発行された生年月日を持っているかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="67b9e-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="67b9e-145">要求が欠落している場合は、承認を行うことができません。この場合、完了したタスクが返されます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="67b9e-146">クレームが存在する場合は、ユーザーの年齢が計算されます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="67b9e-147">ユーザーが要件によって定義された最小経過期間を満たしている場合、承認は成功したと見なされます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="67b9e-148">承認が成功すると、その唯一のパラメーターとして、満たされた要件を使用して `context.Succeed` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="67b9e-149">複数の要件に対してハンドラーを使用する</span><span class="sxs-lookup"><span data-stu-id="67b9e-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="67b9e-150">次に、アクセス許可ハンドラーが3種類の要件を処理できる一対多リレーションシップの例を示します。</span><span class="sxs-lookup"><span data-stu-id="67b9e-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="67b9e-151">上記のコードは、成功とマークされていない要件を含むプロパティ&mdash;、 [Pendingrequirements 要件](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)を走査します。</span><span class="sxs-lookup"><span data-stu-id="67b9e-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="67b9e-152">`ReadPermission` の要件では、要求されたリソースにアクセスするには、ユーザーが所有者またはスポンサーである必要があります。</span><span class="sxs-lookup"><span data-stu-id="67b9e-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="67b9e-153">`EditPermission` または `DeletePermission` 要件の場合は、要求されたリソースにアクセスするための所有者である必要があります。</span><span class="sxs-lookup"><span data-stu-id="67b9e-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="67b9e-154">ハンドラーの登録</span><span class="sxs-lookup"><span data-stu-id="67b9e-154">Handler registration</span></span>

<span data-ttu-id="67b9e-155">ハンドラーは、構成中にサービスコレクションに登録されます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="67b9e-156">(例:</span><span class="sxs-lookup"><span data-stu-id="67b9e-156">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="67b9e-157">上記のコードでは、`services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`を呼び出すことによって `MinimumAgeHandler` をシングルトンとして登録します。</span><span class="sxs-lookup"><span data-stu-id="67b9e-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="67b9e-158">ハンドラーは、組み込みの[サービス有効期間](xref:fundamentals/dependency-injection#service-lifetimes)のいずれかを使用して登録できます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="67b9e-159">ハンドラーが返すもの</span><span class="sxs-lookup"><span data-stu-id="67b9e-159">What should a handler return?</span></span>

<span data-ttu-id="67b9e-160">[ハンドラーの例](#security-authorization-handler-example)の `Handle` メソッドは値を返さないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="67b9e-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="67b9e-161">成功または失敗のいずれかの状態はどのように示されますか。</span><span class="sxs-lookup"><span data-stu-id="67b9e-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="67b9e-162">ハンドラーは `context.Succeed(IAuthorizationRequirement requirement)`を呼び出して成功を示し、正常に検証された要件を渡します。</span><span class="sxs-lookup"><span data-stu-id="67b9e-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="67b9e-163">同じ要件の他のハンドラーが成功する可能性があるため、通常、ハンドラーはエラーを処理する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="67b9e-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="67b9e-164">エラーを保証するには、他の要件ハンドラーが成功した場合でも、`context.Fail`を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="67b9e-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="67b9e-165">ハンドラーが `context.Succeed` または `context.Fail`を呼び出す場合でも、他のすべてのハンドラーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="67b9e-166">これにより、別のハンドラーが要件を正常に検証または失敗した場合でも、ログ記録などの副作用を生成することができます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="67b9e-167">`false`に設定すると、`context.Fail` が呼び出されたときにハンドラーの実行をショートサーキットする[Invokeハンドラの Terfailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure)プロパティ (ASP.NET Core 1.1 以降で使用できます) が表示されます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="67b9e-168">`InvokeHandlersAfterFailure` 既定値は `true`です。この場合、すべてのハンドラーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="67b9e-169">認証ハンドラーは、認証が失敗した場合でも呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="67b9e-170">1つの要件に対して複数のハンドラーが必要なのはなぜですか。</span><span class="sxs-lookup"><span data-stu-id="67b9e-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="67b9e-171">評価を**または**ベースにする場合は、1つの要件に対して複数のハンドラーを実装します。</span><span class="sxs-lookup"><span data-stu-id="67b9e-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="67b9e-172">たとえば、Microsoft には、キーカードだけを開くドアがあります。</span><span class="sxs-lookup"><span data-stu-id="67b9e-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="67b9e-173">キーカードを自宅に置いた場合、受付によって一時的なステッカーが印刷され、ドアが開きます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="67b9e-174">このシナリオでは、1つの要件*BuildingEntry*がありますが、複数のハンドラーが1つの要件を調べています。</span><span class="sxs-lookup"><span data-stu-id="67b9e-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="67b9e-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="67b9e-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="67b9e-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="67b9e-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="67b9e-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="67b9e-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="67b9e-178">両方のハンドラーが[登録](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="67b9e-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="67b9e-179">ポリシーによって `BuildingEntryRequirement`が評価されたときにいずれかのハンドラーが成功した場合、ポリシーの評価は成功します。</span><span class="sxs-lookup"><span data-stu-id="67b9e-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="67b9e-180">Func を使用してポリシーを満たす</span><span class="sxs-lookup"><span data-stu-id="67b9e-180">Using a func to fulfill a policy</span></span>

<span data-ttu-id="67b9e-181">ポリシーを実現することは、コード内で簡単に行うことができます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="67b9e-182">`RequireAssertion` ポリシービルダーを使用してポリシーを構成するときに、`Func<AuthorizationHandlerContext, bool>` を指定できます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="67b9e-183">たとえば、前の `BadgeEntryHandler` は次のように書き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="67b9e-184">ハンドラーでの MVC 要求コンテキストへのアクセス</span><span class="sxs-lookup"><span data-stu-id="67b9e-184">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="67b9e-185">承認ハンドラーに実装する `HandleRequirementAsync` メソッドには、`AuthorizationHandlerContext` と処理する `TRequirement` の2つのパラメーターがあります。</span><span class="sxs-lookup"><span data-stu-id="67b9e-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="67b9e-186">MVC や Jabbr などのフレームワークは、`AuthorizationHandlerContext` の `Resource` プロパティに任意のオブジェクトを追加して、追加情報を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-186">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="67b9e-187">たとえば、MVC は、`Resource` プロパティに[Authorizationfiltercontext](/dotnet/api/?term=AuthorizationFilterContext)のインスタンスを渡します。</span><span class="sxs-lookup"><span data-stu-id="67b9e-187">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="67b9e-188">このプロパティは、MVC および Razor Pages によって提供される `HttpContext`、`RouteData`、およびその他のすべてへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="67b9e-188">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="67b9e-189">`Resource` プロパティの使用は、フレームワーク固有です。</span><span class="sxs-lookup"><span data-stu-id="67b9e-189">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="67b9e-190">`Resource` プロパティの情報を使用すると、承認ポリシーが特定のフレームワークに限定されます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-190">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="67b9e-191">`is` キーワードを使用して `Resource` プロパティをキャストし、キャストが成功したことを確認して、他のフレームワークで実行したときにコードが `InvalidCastException` でクラッシュしないようにします。</span><span class="sxs-lookup"><span data-stu-id="67b9e-191">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="67b9e-192">内部的には、[ロールベースの承認](xref:security/authorization/roles)と[要求ベースの承認](xref:security/authorization/claims)では、要件、要件ハンドラー、および事前に構成されたポリシーを使用します。</span><span class="sxs-lookup"><span data-stu-id="67b9e-192">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="67b9e-193">これらの構成要素は、コードでの承認評価の式をサポートします。</span><span class="sxs-lookup"><span data-stu-id="67b9e-193">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="67b9e-194">結果として、より充実した再利用可能な承認の構造が得られます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-194">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="67b9e-195">承認ポリシーは、1つまたは複数の要件で構成されます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-195">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="67b9e-196">認証サービス構成の一部として、`Startup.ConfigureServices` メソッドに登録されます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-196">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="67b9e-197">前の例では、"AtLeast21" ポリシーが作成されています。</span><span class="sxs-lookup"><span data-stu-id="67b9e-197">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="67b9e-198">これには、要件にパラメーターとして指定されている最小有効期間の1つの要件&mdash;があります。</span><span class="sxs-lookup"><span data-stu-id="67b9e-198">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="67b9e-199">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="67b9e-199">IAuthorizationService</span></span> 

<span data-ttu-id="67b9e-200">承認が成功したかどうかを判断するプライマリサービスは <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="67b9e-200">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="67b9e-201">上記のコードは、 [IAuthorizationService](https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)の2つのメソッドを強調表示しています。</span><span class="sxs-lookup"><span data-stu-id="67b9e-201">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="67b9e-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> は、メソッドのないマーカーサービスと、承認が成功したかどうかを追跡するための機構です。</span><span class="sxs-lookup"><span data-stu-id="67b9e-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="67b9e-203">各 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> は、要件が満たされているかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="67b9e-203">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
<!--The following code is a copy/paste from 
https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

<span data-ttu-id="67b9e-204"><xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> クラスは、要件が満たされているかどうかをマークするためにハンドラーが使用するものです。</span><span class="sxs-lookup"><span data-stu-id="67b9e-204">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="67b9e-205">次のコードは、承認サービスの簡略化された (コメントで注釈が付けられた) 既定の実装を示しています。</span><span class="sxs-lookup"><span data-stu-id="67b9e-205">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

<span data-ttu-id="67b9e-206">次のコードは、一般的な `ConfigureServices`を示しています。</span><span class="sxs-lookup"><span data-stu-id="67b9e-206">The following code shows a typical `ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
}
```

<span data-ttu-id="67b9e-207">承認には <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> または `[Authorize(Policy = "Something")]` を使用します。</span><span class="sxs-lookup"><span data-stu-id="67b9e-207">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="67b9e-208">MVC コントローラーへのポリシーの適用</span><span class="sxs-lookup"><span data-stu-id="67b9e-208">Applying policies to MVC controllers</span></span>

<span data-ttu-id="67b9e-209">Razor Pages を使用している場合は、このドキュメントの「 [Razor Pages にポリシーを適用する](#applying-policies-to-razor-pages)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="67b9e-209">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="67b9e-210">ポリシーは、ポリシー名と共に `[Authorize]` 属性を使用して、コントローラーに適用されます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-210">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="67b9e-211">(例:</span><span class="sxs-lookup"><span data-stu-id="67b9e-211">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="67b9e-212">Razor Pages にポリシーを適用する</span><span class="sxs-lookup"><span data-stu-id="67b9e-212">Applying policies to Razor Pages</span></span>

<span data-ttu-id="67b9e-213">ポリシーは、`[Authorize]` 属性をポリシー名と共に使用して、Razor Pages に適用されます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-213">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="67b9e-214">(例:</span><span class="sxs-lookup"><span data-stu-id="67b9e-214">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="67b9e-215">ポリシーは、[承認規則](xref:security/authorization/razor-pages-authorization)を使用して Razor Pages に適用することもできます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-215">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="67b9e-216">［要件］</span><span class="sxs-lookup"><span data-stu-id="67b9e-216">Requirements</span></span>

<span data-ttu-id="67b9e-217">承認要件とは、ポリシーが現在のユーザープリンシパルを評価するために使用できるデータパラメーターのコレクションです。</span><span class="sxs-lookup"><span data-stu-id="67b9e-217">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="67b9e-218">"AtLeast21" ポリシーでは、最小有効期間を&mdash;1 つのパラメーターが必要です。</span><span class="sxs-lookup"><span data-stu-id="67b9e-218">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="67b9e-219">要件には、空のマーカーインターフェイスである[IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)が実装されています。</span><span class="sxs-lookup"><span data-stu-id="67b9e-219">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="67b9e-220">パラメーター化された最小年齢要件は、次のように実装できます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-220">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="67b9e-221">承認ポリシーに複数の承認要件が含まれている場合は、ポリシーの評価を成功させるためにすべての要件を満たしている必要があります。</span><span class="sxs-lookup"><span data-stu-id="67b9e-221">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="67b9e-222">つまり、1つの承認ポリシーに追加された複数の承認要件は、**と**の基準で処理されます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-222">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="67b9e-223">要件には、データまたはプロパティを含める必要はありません。</span><span class="sxs-lookup"><span data-stu-id="67b9e-223">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="67b9e-224">承認ハンドラー</span><span class="sxs-lookup"><span data-stu-id="67b9e-224">Authorization handlers</span></span>

<span data-ttu-id="67b9e-225">承認ハンドラーは、要件のプロパティを評価する役割を担います。</span><span class="sxs-lookup"><span data-stu-id="67b9e-225">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="67b9e-226">承認ハンドラーは、指定された[authorizationhandler コンテキスト](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext)に対して要件を評価して、アクセスが許可されているかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="67b9e-226">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="67b9e-227">要件には、[複数のハンドラー](#security-authorization-policies-based-multiple-handlers)を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-227">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="67b9e-228">ハンドラーは、 [Authorizationhandler\<TRequirement >](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)を継承できます。 `TRequirement` は処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="67b9e-228">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="67b9e-229">また、1つのハンドラーで[IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler)を実装して、複数の種類の要件を処理することもできます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-229">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="67b9e-230">1つの要件に対してハンドラーを使用する</span><span class="sxs-lookup"><span data-stu-id="67b9e-230">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="67b9e-231">次に示すのは、最小年齢ハンドラが1つの要件を利用する一対一のリレーションシップの例です。</span><span class="sxs-lookup"><span data-stu-id="67b9e-231">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="67b9e-232">前のコードは、現在のユーザープリンシパルが、既知の信頼された発行者によって発行された生年月日を持っているかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="67b9e-232">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="67b9e-233">要求が欠落している場合は、承認を行うことができません。この場合、完了したタスクが返されます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-233">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="67b9e-234">クレームが存在する場合は、ユーザーの年齢が計算されます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-234">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="67b9e-235">ユーザーが要件によって定義された最小経過期間を満たしている場合、承認は成功したと見なされます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-235">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="67b9e-236">承認が成功すると、その唯一のパラメーターとして、満たされた要件を使用して `context.Succeed` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-236">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="67b9e-237">複数の要件に対してハンドラーを使用する</span><span class="sxs-lookup"><span data-stu-id="67b9e-237">Use a handler for multiple requirements</span></span>

<span data-ttu-id="67b9e-238">次に、アクセス許可ハンドラーが3種類の要件を処理できる一対多リレーションシップの例を示します。</span><span class="sxs-lookup"><span data-stu-id="67b9e-238">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="67b9e-239">上記のコードは、成功とマークされていない要件を含むプロパティ&mdash;、 [Pendingrequirements 要件](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)を走査します。</span><span class="sxs-lookup"><span data-stu-id="67b9e-239">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="67b9e-240">`ReadPermission` の要件では、要求されたリソースにアクセスするには、ユーザーが所有者またはスポンサーである必要があります。</span><span class="sxs-lookup"><span data-stu-id="67b9e-240">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="67b9e-241">`EditPermission` または `DeletePermission` 要件の場合は、要求されたリソースにアクセスするための所有者である必要があります。</span><span class="sxs-lookup"><span data-stu-id="67b9e-241">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="67b9e-242">ハンドラーの登録</span><span class="sxs-lookup"><span data-stu-id="67b9e-242">Handler registration</span></span>

<span data-ttu-id="67b9e-243">ハンドラーは、構成中にサービスコレクションに登録されます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-243">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="67b9e-244">(例:</span><span class="sxs-lookup"><span data-stu-id="67b9e-244">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="67b9e-245">上記のコードでは、`services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`を呼び出すことによって `MinimumAgeHandler` をシングルトンとして登録します。</span><span class="sxs-lookup"><span data-stu-id="67b9e-245">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="67b9e-246">ハンドラーは、組み込みの[サービス有効期間](xref:fundamentals/dependency-injection#service-lifetimes)のいずれかを使用して登録できます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-246">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="67b9e-247">ハンドラーが返すもの</span><span class="sxs-lookup"><span data-stu-id="67b9e-247">What should a handler return?</span></span>

<span data-ttu-id="67b9e-248">[ハンドラーの例](#security-authorization-handler-example)の `Handle` メソッドは値を返さないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="67b9e-248">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="67b9e-249">成功または失敗のいずれかの状態はどのように示されますか。</span><span class="sxs-lookup"><span data-stu-id="67b9e-249">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="67b9e-250">ハンドラーは `context.Succeed(IAuthorizationRequirement requirement)`を呼び出して成功を示し、正常に検証された要件を渡します。</span><span class="sxs-lookup"><span data-stu-id="67b9e-250">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="67b9e-251">同じ要件の他のハンドラーが成功する可能性があるため、通常、ハンドラーはエラーを処理する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="67b9e-251">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="67b9e-252">エラーを保証するには、他の要件ハンドラーが成功した場合でも、`context.Fail`を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="67b9e-252">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="67b9e-253">ハンドラーが `context.Succeed` または `context.Fail`を呼び出す場合でも、他のすべてのハンドラーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-253">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="67b9e-254">これにより、別のハンドラーが要件を正常に検証または失敗した場合でも、ログ記録などの副作用を生成することができます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-254">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="67b9e-255">`false`に設定すると、`context.Fail` が呼び出されたときにハンドラーの実行をショートサーキットする[Invokeハンドラの Terfailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure)プロパティ (ASP.NET Core 1.1 以降で使用できます) が表示されます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-255">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="67b9e-256">`InvokeHandlersAfterFailure` 既定値は `true`です。この場合、すべてのハンドラーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-256">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="67b9e-257">認証ハンドラーは、認証が失敗した場合でも呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-257">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="67b9e-258">1つの要件に対して複数のハンドラーが必要なのはなぜですか。</span><span class="sxs-lookup"><span data-stu-id="67b9e-258">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="67b9e-259">評価を**または**ベースにする場合は、1つの要件に対して複数のハンドラーを実装します。</span><span class="sxs-lookup"><span data-stu-id="67b9e-259">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="67b9e-260">たとえば、Microsoft には、キーカードだけを開くドアがあります。</span><span class="sxs-lookup"><span data-stu-id="67b9e-260">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="67b9e-261">キーカードを自宅に置いた場合、受付によって一時的なステッカーが印刷され、ドアが開きます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-261">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="67b9e-262">このシナリオでは、1つの要件*BuildingEntry*がありますが、複数のハンドラーが1つの要件を調べています。</span><span class="sxs-lookup"><span data-stu-id="67b9e-262">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="67b9e-263">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="67b9e-263">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="67b9e-264">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="67b9e-264">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="67b9e-265">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="67b9e-265">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="67b9e-266">両方のハンドラーが[登録](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="67b9e-266">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="67b9e-267">ポリシーによって `BuildingEntryRequirement`が評価されたときにいずれかのハンドラーが成功した場合、ポリシーの評価は成功します。</span><span class="sxs-lookup"><span data-stu-id="67b9e-267">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="67b9e-268">Func を使用してポリシーを満たす</span><span class="sxs-lookup"><span data-stu-id="67b9e-268">Using a func to fulfill a policy</span></span>

<span data-ttu-id="67b9e-269">ポリシーを実現することは、コード内で簡単に行うことができます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-269">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="67b9e-270">`RequireAssertion` ポリシービルダーを使用してポリシーを構成するときに、`Func<AuthorizationHandlerContext, bool>` を指定できます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-270">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="67b9e-271">たとえば、前の `BadgeEntryHandler` は次のように書き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-271">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="67b9e-272">ハンドラーでの MVC 要求コンテキストへのアクセス</span><span class="sxs-lookup"><span data-stu-id="67b9e-272">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="67b9e-273">承認ハンドラーに実装する `HandleRequirementAsync` メソッドには、`AuthorizationHandlerContext` と処理する `TRequirement` の2つのパラメーターがあります。</span><span class="sxs-lookup"><span data-stu-id="67b9e-273">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="67b9e-274">MVC や Jabbr などのフレームワークは、`AuthorizationHandlerContext` の `Resource` プロパティに任意のオブジェクトを追加して、追加情報を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-274">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="67b9e-275">たとえば、MVC は、`Resource` プロパティに[Authorizationfiltercontext](/dotnet/api/?term=AuthorizationFilterContext)のインスタンスを渡します。</span><span class="sxs-lookup"><span data-stu-id="67b9e-275">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="67b9e-276">このプロパティは、MVC および Razor Pages によって提供される `HttpContext`、`RouteData`、およびその他のすべてへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="67b9e-276">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="67b9e-277">`Resource` プロパティの使用は、フレームワーク固有です。</span><span class="sxs-lookup"><span data-stu-id="67b9e-277">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="67b9e-278">`Resource` プロパティの情報を使用すると、承認ポリシーが特定のフレームワークに限定されます。</span><span class="sxs-lookup"><span data-stu-id="67b9e-278">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="67b9e-279">`is` キーワードを使用して `Resource` プロパティをキャストし、キャストが成功したことを確認して、他のフレームワークで実行したときにコードが `InvalidCastException` でクラッシュしないようにします。</span><span class="sxs-lookup"><span data-stu-id="67b9e-279">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end