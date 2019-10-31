---
title: ASP.NET Core での要求ベースの承認
author: rick-anderson
description: ASP.NET Core アプリで承認の要求チェックを追加する方法について説明します。
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/claims
ms.openlocfilehash: e289851aafcbc7e3b3f60ab9fbe4b182a78bdf8a
ms.sourcegitcommit: de0fc77487a4d342bcc30965ec5c142d10d22c03
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2019
ms.locfileid: "73143431"
---
# <a name="claims-based-authorization-in-aspnet-core"></a><span data-ttu-id="9fae8-103">ASP.NET Core での要求ベースの承認</span><span class="sxs-lookup"><span data-stu-id="9fae8-103">Claims-based authorization in ASP.NET Core</span></span>

<a name="security-authorization-claims-based"></a>

<span data-ttu-id="9fae8-104">Id が作成されると、信頼されたパーティによって発行された1つまたは複数の要求が割り当てられる場合があります。</span><span class="sxs-lookup"><span data-stu-id="9fae8-104">When an identity is created it may be assigned one or more claims issued by a trusted party.</span></span> <span data-ttu-id="9fae8-105">クレームは、サブジェクトの内容を表す名前と値のペアであり、サブジェクトが実行できる内容ではありません。</span><span class="sxs-lookup"><span data-stu-id="9fae8-105">A claim is a name value pair that represents what the subject is, not what the subject can do.</span></span> <span data-ttu-id="9fae8-106">たとえば、ローカル運転免許証機関によって発行された運転免許証があるとします。</span><span class="sxs-lookup"><span data-stu-id="9fae8-106">For example, you may have a driver's license, issued by a local driving license authority.</span></span> <span data-ttu-id="9fae8-107">運転免許証には、お客様のドライバーのライセンスがあります。</span><span class="sxs-lookup"><span data-stu-id="9fae8-107">Your driver's license has your date of birth on it.</span></span> <span data-ttu-id="9fae8-108">この場合、要求名は `DateOfBirth`になります。要求の値は生の日付になります。たとえば `8th June 1970` と発行者は運転ライセンス機関になります。</span><span class="sxs-lookup"><span data-stu-id="9fae8-108">In this case the claim name would be `DateOfBirth`, the claim value would be your date of birth, for example `8th June 1970` and the issuer would be the driving license authority.</span></span> <span data-ttu-id="9fae8-109">クレームベースの承認では、最も単純にクレームの値を確認し、その値に基づいてリソースへのアクセスを許可します。</span><span class="sxs-lookup"><span data-stu-id="9fae8-109">Claims based authorization, at its simplest, checks the value of a claim and allows access to a resource based upon that value.</span></span> <span data-ttu-id="9fae8-110">たとえば、夜のクラブへのアクセスが必要な場合、承認プロセスは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="9fae8-110">For example if you want access to a night club the authorization process might be:</span></span>

<span data-ttu-id="9fae8-111">ドアのセキュリティ担当者は、お客様がアクセス権を付与する前に、誕生日の請求日の値と、発行者 (運転免許機関) を信頼するかどうかを評価します。</span><span class="sxs-lookup"><span data-stu-id="9fae8-111">The door security officer would evaluate the value of your date of birth claim and whether they trust the issuer (the driving license authority) before granting you access.</span></span>

<span data-ttu-id="9fae8-112">Id には複数の値を持つ複数の要求を含めることができ、同じ種類の複数の要求を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="9fae8-112">An identity can contain multiple claims with multiple values and can contain multiple claims of the same type.</span></span>

## <a name="adding-claims-checks"></a><span data-ttu-id="9fae8-113">要求チェックの追加</span><span class="sxs-lookup"><span data-stu-id="9fae8-113">Adding claims checks</span></span>

<span data-ttu-id="9fae8-114">クレームベースの承認チェックは宣言型です。開発者は、コントローラーまたはコントローラー内のアクションに対してコード内にそれらを埋め込み、現在のユーザーが所有している必要がある要求を指定します。また、必要に応じて、要求が要求されたリソース。</span><span class="sxs-lookup"><span data-stu-id="9fae8-114">Claim based authorization checks are declarative - the developer embeds them within their code, against a controller or an action within a controller, specifying claims which the current user must possess, and optionally the value the claim must hold to access the requested resource.</span></span> <span data-ttu-id="9fae8-115">要求要件はポリシーベースです。開発者は、要求要件を表すポリシーを作成して登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9fae8-115">Claims requirements are policy based, the developer must build and register a policy expressing the claims requirements.</span></span>

<span data-ttu-id="9fae8-116">最も単純な種類の要求ポリシーでは、要求の存在を検索し、値を確認しません。</span><span class="sxs-lookup"><span data-stu-id="9fae8-116">The simplest type of claim policy looks for the presence of a claim and doesn't check the value.</span></span>

<span data-ttu-id="9fae8-117">まず、ポリシーをビルドして登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9fae8-117">First you need to build and register the policy.</span></span> <span data-ttu-id="9fae8-118">これは承認サービス構成の一部として行われ、通常は*Startup.cs*ファイルの `ConfigureServices()` に含まれます。</span><span class="sxs-lookup"><span data-stu-id="9fae8-118">This takes place as part of the Authorization service configuration, which normally takes part in `ConfigureServices()` in your *Startup.cs* file.</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
    });
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
    });
}
```

::: moniker-end

<span data-ttu-id="9fae8-119">この場合、`EmployeeOnly` ポリシーは、現在の id に `EmployeeNumber` 要求が存在するかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="9fae8-119">In this case the `EmployeeOnly` policy checks for the presence of an `EmployeeNumber` claim on the current identity.</span></span>

<span data-ttu-id="9fae8-120">次に、`AuthorizeAttribute` 属性の `Policy` プロパティを使用してポリシーを適用し、ポリシー名を指定します。</span><span class="sxs-lookup"><span data-stu-id="9fae8-120">You then apply the policy using the `Policy` property on the `AuthorizeAttribute` attribute to specify the policy name;</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

<span data-ttu-id="9fae8-121">`AuthorizeAttribute` 属性はコントローラー全体に適用できます。このインスタンスでは、ポリシーに一致する id のみが、コントローラー上の任意のアクションへのアクセスを許可されます。</span><span class="sxs-lookup"><span data-stu-id="9fae8-121">The `AuthorizeAttribute` attribute can be applied to an entire controller, in this instance only identities matching the policy will be allowed access to any Action on the controller.</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

<span data-ttu-id="9fae8-122">`AuthorizeAttribute` 属性によって保護されているコントローラーがあり、特定のアクションへの匿名アクセスを許可する場合は、`AllowAnonymousAttribute` 属性を適用します。</span><span class="sxs-lookup"><span data-stu-id="9fae8-122">If you have a controller that's protected by the `AuthorizeAttribute` attribute, but want to allow anonymous access to particular actions you apply the `AllowAnonymousAttribute` attribute.</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }

    [AllowAnonymous]
    public ActionResult VacationPolicy()
    {
    }
}
```

<span data-ttu-id="9fae8-123">ほとんどの要求には値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="9fae8-123">Most claims come with a value.</span></span> <span data-ttu-id="9fae8-124">ポリシーを作成するときに、許可される値の一覧を指定できます。</span><span class="sxs-lookup"><span data-stu-id="9fae8-124">You can specify a list of allowed values when creating the policy.</span></span> <span data-ttu-id="9fae8-125">次の例は、従業員番号が1、2、3、4、または5である従業員に対してのみ成功します。</span><span class="sxs-lookup"><span data-stu-id="9fae8-125">The following example would only succeed for employees whose employee number was 1, 2, 3, 4 or 5.</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
}
```

::: moniker-end
### <a name="add-a-generic-claim-check"></a><span data-ttu-id="9fae8-126">汎用要求チェックの追加</span><span class="sxs-lookup"><span data-stu-id="9fae8-126">Add a generic claim check</span></span>

<span data-ttu-id="9fae8-127">要求値が単一の値ではない場合、または変換が必要な場合は、 [Requireassertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion)を使用します。</span><span class="sxs-lookup"><span data-stu-id="9fae8-127">If the claim value isn't a single value or a transformation is required, use [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion).</span></span> <span data-ttu-id="9fae8-128">詳細については、「 [func を使用してポリシーを満たす](xref:security/authorization/policies#using-a-func-to-fulfill-a-policy)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9fae8-128">For more information, see [Using a func to fulfill a policy](xref:security/authorization/policies#using-a-func-to-fulfill-a-policy).</span></span>

## <a name="multiple-policy-evaluation"></a><span data-ttu-id="9fae8-129">複数のポリシーの評価</span><span class="sxs-lookup"><span data-stu-id="9fae8-129">Multiple Policy Evaluation</span></span>

<span data-ttu-id="9fae8-130">複数のポリシーをコントローラーまたはアクションに適用する場合は、アクセスが許可される前にすべてのポリシーを渡す必要があります。</span><span class="sxs-lookup"><span data-stu-id="9fae8-130">If you apply multiple policies to a controller or action, then all policies must pass before access is granted.</span></span> <span data-ttu-id="9fae8-131">(例:</span><span class="sxs-lookup"><span data-stu-id="9fae8-131">For example:</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class SalaryController : Controller
{
    public ActionResult Payslip()
    {
    }

    [Authorize(Policy = "HumanResources")]
    public ActionResult UpdateSalary()
    {
    }
}
```

<span data-ttu-id="9fae8-132">上の例では、`EmployeeOnly` ポリシーを満たす id は、コントローラーでポリシーが適用されているため、`Payslip` アクションにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="9fae8-132">In the above example any identity which fulfills the `EmployeeOnly` policy can access the `Payslip` action as that policy is enforced on the controller.</span></span> <span data-ttu-id="9fae8-133">ただし、`UpdateSalary` アクションを呼び出すには、id が `EmployeeOnly` ポリシーと `HumanResources` ポリシーの*両方*を満たしている必要があります。</span><span class="sxs-lookup"><span data-stu-id="9fae8-133">However in order to call the `UpdateSalary` action the identity must fulfill *both* the `EmployeeOnly` policy and the `HumanResources` policy.</span></span>

<span data-ttu-id="9fae8-134">誕生日の請求書を取得するなど、より複雑なポリシーが必要な場合は、年数を計算し、その年齢を確認してから、[カスタムポリシーハンドラー](xref:security/authorization/policies)を記述する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9fae8-134">If you want more complicated policies, such as taking a date of birth claim, calculating an age from it then checking the age is 21 or older then you need to write [custom policy handlers](xref:security/authorization/policies).</span></span>
