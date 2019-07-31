---
title: ASP.NET Core でのポリシーベースの承認
author: rick-anderson
description: ASP.NET Core アプリで承認要件を強制するために承認ポリシーハンドラーを作成して使用する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 04/05/2019
uid: security/authorization/policies
ms.openlocfilehash: 60625944d4ba31da6b98bdf947991088dc75ed87
ms.sourcegitcommit: 7001657c00358b082734ba4273693b9b3ed35d2a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2019
ms.locfileid: "68669970"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="88061-103">ASP.NET Core でのポリシーベースの承認</span><span class="sxs-lookup"><span data-stu-id="88061-103">Policy-based authorization in ASP.NET Core</span></span>

<span data-ttu-id="88061-104">内部的には、[ロールベースの承認](xref:security/authorization/roles)と[要求ベースの承認](xref:security/authorization/claims)では、要件、要件ハンドラー、および事前に構成されたポリシーを使用します。</span><span class="sxs-lookup"><span data-stu-id="88061-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="88061-105">これらの構成要素は、コードでの承認評価の式をサポートします。</span><span class="sxs-lookup"><span data-stu-id="88061-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="88061-106">結果として、より充実した再利用可能な承認の構造が得られます。</span><span class="sxs-lookup"><span data-stu-id="88061-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="88061-107">承認ポリシーは、1つまたは複数の要件で構成されます。</span><span class="sxs-lookup"><span data-stu-id="88061-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="88061-108">認証サービス構成の一部として、 `Startup.ConfigureServices`メソッドに登録されます。</span><span class="sxs-lookup"><span data-stu-id="88061-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="88061-109">前の例では、"AtLeast21" ポリシーが作成されています。</span><span class="sxs-lookup"><span data-stu-id="88061-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="88061-110">最小期間の要件&mdash;が1つあります。これは、要件にパラメーターとして指定されます。</span><span class="sxs-lookup"><span data-stu-id="88061-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="88061-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="88061-111">IAuthorizationService</span></span> 

<span data-ttu-id="88061-112">承認が成功したかどうかを判断<xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>するプライマリサービスは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="88061-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="88061-113">上記のコードは、 [IAuthorizationService](https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)の2つのメソッドを強調表示しています。</span><span class="sxs-lookup"><span data-stu-id="88061-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="88061-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>は、メソッドを持たないマーカーサービスであり、認証が成功したかどうかを追跡するためのメカニズムを備えています。</span><span class="sxs-lookup"><span data-stu-id="88061-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="88061-115">各<xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler>は、要件が満たされているかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="88061-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="88061-116">クラス<xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext>は、要件が満たされているかどうかを示すためにハンドラーが使用するものです。</span><span class="sxs-lookup"><span data-stu-id="88061-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="88061-117">次のコードは、承認サービスの簡略化された (コメントで注釈が付けられた) 既定の実装を示しています。</span><span class="sxs-lookup"><span data-stu-id="88061-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="88061-118">一般的`ConfigureServices`なコードを次に示します。</span><span class="sxs-lookup"><span data-stu-id="88061-118">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="88061-119">承認<xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>に`[Authorize(Policy = "Something")]`はまたはを使用します。</span><span class="sxs-lookup"><span data-stu-id="88061-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="88061-120">MVC コントローラーへのポリシーの適用</span><span class="sxs-lookup"><span data-stu-id="88061-120">Applying policies to MVC controllers</span></span>

<span data-ttu-id="88061-121">Razor Pages を使用している場合は、このドキュメントの「 [Razor Pages にポリシーを適用する](#applying-policies-to-razor-pages)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="88061-121">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="88061-122">ポリシーは、 `[Authorize]`ポリシー名を持つ属性を使用して、コントローラーに適用されます。</span><span class="sxs-lookup"><span data-stu-id="88061-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="88061-123">例えば:</span><span class="sxs-lookup"><span data-stu-id="88061-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="88061-124">Razor Pages にポリシーを適用する</span><span class="sxs-lookup"><span data-stu-id="88061-124">Applying policies to Razor Pages</span></span>

<span data-ttu-id="88061-125">ポリシーは、 `[Authorize]`属性とポリシー名を使用して Razor Pages に適用されます。</span><span class="sxs-lookup"><span data-stu-id="88061-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="88061-126">例えば:</span><span class="sxs-lookup"><span data-stu-id="88061-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="88061-127">ポリシーは、[承認規則](xref:security/authorization/razor-pages-authorization)を使用して Razor Pages に適用することもできます。</span><span class="sxs-lookup"><span data-stu-id="88061-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="88061-128">必要条件</span><span class="sxs-lookup"><span data-stu-id="88061-128">Requirements</span></span>

<span data-ttu-id="88061-129">承認要件とは、ポリシーが現在のユーザープリンシパルを評価するために使用できるデータパラメーターのコレクションです。</span><span class="sxs-lookup"><span data-stu-id="88061-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="88061-130">"AtLeast21" ポリシーでは、最小有効期間を1つ&mdash;のパラメーターとして指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="88061-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="88061-131">要件には、空のマーカーインターフェイスである[IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)が実装されています。</span><span class="sxs-lookup"><span data-stu-id="88061-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="88061-132">パラメーター化された最小年齢要件は、次のように実装できます。</span><span class="sxs-lookup"><span data-stu-id="88061-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="88061-133">承認ポリシーに複数の承認要件が含まれている場合は、ポリシーの評価を成功させるためにすべての要件を満たしている必要があります。</span><span class="sxs-lookup"><span data-stu-id="88061-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="88061-134">つまり、1つの承認ポリシーに追加された複数の承認要件は、**と**の基準で処理されます。</span><span class="sxs-lookup"><span data-stu-id="88061-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="88061-135">要件には、データまたはプロパティを含める必要はありません。</span><span class="sxs-lookup"><span data-stu-id="88061-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="88061-136">承認ハンドラー</span><span class="sxs-lookup"><span data-stu-id="88061-136">Authorization handlers</span></span>

<span data-ttu-id="88061-137">承認ハンドラーは、要件のプロパティを評価する役割を担います。</span><span class="sxs-lookup"><span data-stu-id="88061-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="88061-138">承認ハンドラーは、指定された[authorizationhandler コンテキスト](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext)に対して要件を評価して、アクセスが許可されているかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="88061-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="88061-139">要件には、[複数のハンドラー](#security-authorization-policies-based-multiple-handlers)を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="88061-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="88061-140">ハンドラーは[authorizationhandler\<trequirement >](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)を継承できます`TRequirement` 。ここで、は処理する必要がある要件です。</span><span class="sxs-lookup"><span data-stu-id="88061-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="88061-141">また、1つのハンドラーで[IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler)を実装して、複数の種類の要件を処理することもできます。</span><span class="sxs-lookup"><span data-stu-id="88061-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="88061-142">1つの要件に対してハンドラーを使用する</span><span class="sxs-lookup"><span data-stu-id="88061-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="88061-143">次に示すのは、最小年齢ハンドラが1つの要件を利用する一対一のリレーションシップの例です。</span><span class="sxs-lookup"><span data-stu-id="88061-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="88061-144">前のコードは、現在のユーザープリンシパルが、既知の信頼された発行者によって発行された生年月日を持っているかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="88061-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="88061-145">要求が欠落している場合は、承認を行うことができません。この場合、完了したタスクが返されます。</span><span class="sxs-lookup"><span data-stu-id="88061-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="88061-146">クレームが存在する場合は、ユーザーの年齢が計算されます。</span><span class="sxs-lookup"><span data-stu-id="88061-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="88061-147">ユーザーが要件によって定義された最小経過期間を満たしている場合、承認は成功したと見なされます。</span><span class="sxs-lookup"><span data-stu-id="88061-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="88061-148">承認が成功すると`context.Succeed` 、は、その唯一のパラメーターとして満たされた要件で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="88061-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="88061-149">複数の要件に対してハンドラーを使用する</span><span class="sxs-lookup"><span data-stu-id="88061-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="88061-150">次に、アクセス許可ハンドラーが3種類の要件を処理できる一対多リレーションシップの例を示します。</span><span class="sxs-lookup"><span data-stu-id="88061-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="88061-151">上記のコードは、成功とマークされていない要件を含むプロパティを[pendingrequirements 要件](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;にたどります。</span><span class="sxs-lookup"><span data-stu-id="88061-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="88061-152">要件と`ReadPermission`して、要求されたリソースにアクセスするには、ユーザーが所有者またはスポンサーである必要があります。</span><span class="sxs-lookup"><span data-stu-id="88061-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="88061-153">`EditPermission`または`DeletePermission`要件の場合は、要求されたリソースにアクセスするための所有者である必要があります。</span><span class="sxs-lookup"><span data-stu-id="88061-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="88061-154">ハンドラーの登録</span><span class="sxs-lookup"><span data-stu-id="88061-154">Handler registration</span></span>

<span data-ttu-id="88061-155">ハンドラーは、構成中にサービスコレクションに登録されます。</span><span class="sxs-lookup"><span data-stu-id="88061-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="88061-156">例えば:</span><span class="sxs-lookup"><span data-stu-id="88061-156">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="88061-157">前のコードで`MinimumAgeHandler`は、を呼び出す`services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`ことによってシングルトンとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="88061-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="88061-158">ハンドラーは、組み込みの[サービス有効期間](xref:fundamentals/dependency-injection#service-lifetimes)のいずれかを使用して登録できます。</span><span class="sxs-lookup"><span data-stu-id="88061-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="88061-159">ハンドラーが返すもの</span><span class="sxs-lookup"><span data-stu-id="88061-159">What should a handler return?</span></span>

<span data-ttu-id="88061-160">`Handle`ハンドラーの[例](#security-authorization-handler-example)のメソッドは値を返さないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="88061-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="88061-161">成功または失敗のいずれかの状態はどのように示されますか。</span><span class="sxs-lookup"><span data-stu-id="88061-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="88061-162">ハンドラーはを呼び出し`context.Succeed(IAuthorizationRequirement requirement)`て成功を示し、正常に検証された要件を渡します。</span><span class="sxs-lookup"><span data-stu-id="88061-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="88061-163">同じ要件の他のハンドラーが成功する可能性があるため、通常、ハンドラーはエラーを処理する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="88061-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="88061-164">エラーを保証するには、他の要件ハンドラーが`context.Fail`成功したとしても、を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="88061-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="88061-165">ハンドラーがまたは`context.Succeed` `context.Fail`を呼び出すと、他のすべてのハンドラーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="88061-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="88061-166">これにより、別のハンドラーが要件を正常に検証または失敗した場合でも、ログ記録などの副作用を生成することができます。</span><span class="sxs-lookup"><span data-stu-id="88061-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="88061-167">に`false`設定すると、 [invokeハンドラの terfailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure)プロパティ (ASP.NET Core 1.1 以降で使用可能) が呼び出されたときに`context.Fail`ハンドラーの実行をショートサーキットします。</span><span class="sxs-lookup"><span data-stu-id="88061-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="88061-168">`InvokeHandlersAfterFailure`既定値はです。この場合、すべてのハンドラーが呼び出されます。`true`</span><span class="sxs-lookup"><span data-stu-id="88061-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="88061-169">認証ハンドラーは、認証が失敗した場合でも呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="88061-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="88061-170">1つの要件に対して複数のハンドラーが必要なのはなぜですか。</span><span class="sxs-lookup"><span data-stu-id="88061-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="88061-171">評価を**または**ベースにする場合は、1つの要件に対して複数のハンドラーを実装します。</span><span class="sxs-lookup"><span data-stu-id="88061-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="88061-172">たとえば、Microsoft には、キーカードだけを開くドアがあります。</span><span class="sxs-lookup"><span data-stu-id="88061-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="88061-173">キーカードを自宅に置いた場合、受付によって一時的なステッカーが印刷され、ドアが開きます。</span><span class="sxs-lookup"><span data-stu-id="88061-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="88061-174">このシナリオでは、1つの要件*BuildingEntry*がありますが、複数のハンドラーが1つの要件を調べています。</span><span class="sxs-lookup"><span data-stu-id="88061-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="88061-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="88061-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="88061-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="88061-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="88061-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="88061-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="88061-178">両方のハンドラーが[登録](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="88061-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="88061-179">ポリシーによってが評価された`BuildingEntryRequirement`ときにいずれかのハンドラーが成功した場合、ポリシーの評価は成功します。</span><span class="sxs-lookup"><span data-stu-id="88061-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="88061-180">Func を使用してポリシーを満たす</span><span class="sxs-lookup"><span data-stu-id="88061-180">Using a func to fulfill a policy</span></span>

<span data-ttu-id="88061-181">ポリシーを実現することは、コード内で簡単に行うことができます。</span><span class="sxs-lookup"><span data-stu-id="88061-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="88061-182">ポリシービルダー `Func<AuthorizationHandlerContext, bool>` `RequireAssertion`を使用してポリシーを構成するときに、を指定することができます。</span><span class="sxs-lookup"><span data-stu-id="88061-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="88061-183">たとえば、前`BadgeEntryHandler`のは次のように書き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="88061-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="88061-184">ハンドラーでの MVC 要求コンテキストへのアクセス</span><span class="sxs-lookup"><span data-stu-id="88061-184">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="88061-185">承認ハンドラーに実装する`AuthorizationHandlerContext` `TRequirement` `HandleRequirementAsync`メソッドには、とという2つのパラメーターがあります。</span><span class="sxs-lookup"><span data-stu-id="88061-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="88061-186">MVC や Jabbr などのフレームワークは、の`Resource`プロパティに任意のオブジェクトを追加して、 `AuthorizationHandlerContext`追加情報を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="88061-186">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="88061-187">たとえば、MVC は、 `Resource`プロパティに[authorizationfiltercontext](/dotnet/api/?term=AuthorizationFilterContext)のインスタンスを渡します。</span><span class="sxs-lookup"><span data-stu-id="88061-187">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="88061-188">このプロパティは、 `HttpContext` `RouteData`、、および MVC と Razor Pages によって提供されるすべてのものへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="88061-188">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="88061-189">`Resource`プロパティの使用はフレームワーク固有です。</span><span class="sxs-lookup"><span data-stu-id="88061-189">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="88061-190">プロパティの情報を`Resource`使用すると、承認ポリシーが特定のフレームワークに限定されます。</span><span class="sxs-lookup"><span data-stu-id="88061-190">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="88061-191">キーワードを`InvalidCastException` `Resource` `is`使用してプロパティをキャストし、キャストが成功したことを確認して、他のフレームワークで実行したときにコードがクラッシュしないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="88061-191">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```
