---
title: ASP.NET Core のカスタム承認ポリシープロバイダー
author: mjrousos
description: ASP.NET Core アプリでカスタム IAuthorizationPolicyProvider を使用して、承認ポリシーを動的に生成する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 9f0a0cd5337f7f8d2fc8a4b6902a63b98f6bd702
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651752"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a><span data-ttu-id="d85bc-103">ASP.NET Core で IAuthorizationPolicyProvider を使用するカスタム承認ポリシープロバイダー</span><span class="sxs-lookup"><span data-stu-id="d85bc-103">Custom Authorization Policy Providers using IAuthorizationPolicyProvider in ASP.NET Core</span></span> 

<span data-ttu-id="d85bc-104">作成者: [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="d85bc-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="d85bc-105">通常、[ポリシーベースの承認](xref:security/authorization/policies)を使用する場合、ポリシーは、承認サービス構成の一部として `AuthorizationOptions.AddPolicy` を呼び出すことによって登録されます。</span><span class="sxs-lookup"><span data-stu-id="d85bc-105">Typically when using [policy-based authorization](xref:security/authorization/policies), policies are registered by calling `AuthorizationOptions.AddPolicy` as part of authorization service configuration.</span></span> <span data-ttu-id="d85bc-106">場合によっては、すべての承認ポリシーをこの方法で登録することができない (または望ましくない) ことがあります。</span><span class="sxs-lookup"><span data-stu-id="d85bc-106">In some scenarios, it may not be possible (or desirable) to register all authorization policies in this way.</span></span> <span data-ttu-id="d85bc-107">そのような場合は、カスタム `IAuthorizationPolicyProvider` を使用して、承認ポリシーをどのように提供するかを制御できます。</span><span class="sxs-lookup"><span data-stu-id="d85bc-107">In those cases, you can use a custom `IAuthorizationPolicyProvider` to control how authorization policies are supplied.</span></span>

<span data-ttu-id="d85bc-108">カスタム[IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider)が役に立つ可能性のあるシナリオの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="d85bc-108">Examples of scenarios where a custom [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) may be useful include:</span></span>

* <span data-ttu-id="d85bc-109">外部サービスを使用してポリシーの評価を提供する。</span><span class="sxs-lookup"><span data-stu-id="d85bc-109">Using an external service to provide policy evaluation.</span></span>
* <span data-ttu-id="d85bc-110">多数のポリシー (部屋番号や年齢など) を使用しているため、`AuthorizationOptions.AddPolicy` 呼び出しを使用して個々の承認ポリシーを追加することは意味がありません。</span><span class="sxs-lookup"><span data-stu-id="d85bc-110">Using a large range of policies (for different room numbers or ages, for example), so it doesn't make sense to add each individual authorization policy with an `AuthorizationOptions.AddPolicy` call.</span></span>
* <span data-ttu-id="d85bc-111">外部データソース (データベースなど) の情報に基づいて実行時にポリシーを作成するか、別のメカニズムを使用して承認要件を動的に決定します。</span><span class="sxs-lookup"><span data-stu-id="d85bc-111">Creating policies at runtime based on information in an external data source (like a database) or determining authorization requirements dynamically through another mechanism.</span></span>

<span data-ttu-id="d85bc-112">[AspNetCore GitHub リポジトリ](https://github.com/dotnet/AspNetCore)から[サンプルコードを表示またはダウンロード](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)します。</span><span class="sxs-lookup"><span data-stu-id="d85bc-112">[View or download sample code](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) from the [AspNetCore GitHub repository](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="d85bc-113">Dotnet/AspNetCore リポジトリ ZIP ファイルをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="d85bc-113">Download the dotnet/AspNetCore repository ZIP file.</span></span> <span data-ttu-id="d85bc-114">ファイルを解凍します。</span><span class="sxs-lookup"><span data-stu-id="d85bc-114">Unzip the file.</span></span> <span data-ttu-id="d85bc-115">*Src/Security/samples/CustomPolicyProvider*プロジェクトフォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="d85bc-115">Navigate to the *src/Security/samples/CustomPolicyProvider* project folder.</span></span>

## <a name="customize-policy-retrieval"></a><span data-ttu-id="d85bc-116">ポリシーの取得のカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="d85bc-116">Customize policy retrieval</span></span>

<span data-ttu-id="d85bc-117">ASP.NET Core アプリは、`IAuthorizationPolicyProvider` インターフェイスの実装を使用して承認ポリシーを取得します。</span><span class="sxs-lookup"><span data-stu-id="d85bc-117">ASP.NET Core apps use an implementation of the `IAuthorizationPolicyProvider` interface to retrieve authorization policies.</span></span> <span data-ttu-id="d85bc-118">既定では、 [Defaultauthorizationpolicyprovider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider)が登録され、使用されます。</span><span class="sxs-lookup"><span data-stu-id="d85bc-118">By default, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) is registered and used.</span></span> <span data-ttu-id="d85bc-119">`DefaultAuthorizationPolicyProvider` は、`IServiceCollection.AddAuthorization` の呼び出しで指定された `AuthorizationOptions` からポリシーを返します。</span><span class="sxs-lookup"><span data-stu-id="d85bc-119">`DefaultAuthorizationPolicyProvider` returns policies from the `AuthorizationOptions` provided in an `IServiceCollection.AddAuthorization` call.</span></span>

<span data-ttu-id="d85bc-120">この動作をカスタマイズするには、アプリの[依存関係挿入](xref:fundamentals/dependency-injection)コンテナーに別の `IAuthorizationPolicyProvider` の実装を登録します。</span><span class="sxs-lookup"><span data-stu-id="d85bc-120">Customize this behavior by registering a different `IAuthorizationPolicyProvider` implementation in the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> 

<span data-ttu-id="d85bc-121">`IAuthorizationPolicyProvider` インターフェイスには、次の3つの Api が含まれています。</span><span class="sxs-lookup"><span data-stu-id="d85bc-121">The `IAuthorizationPolicyProvider` interface contains three APIs:</span></span>

* <span data-ttu-id="d85bc-122">[Getpolicyasync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_)は、指定された名前の承認ポリシーを返します。</span><span class="sxs-lookup"><span data-stu-id="d85bc-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) returns an authorization policy for a given name.</span></span>
* <span data-ttu-id="d85bc-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync)は、既定の承認ポリシー (ポリシーが指定されていない `[Authorize]` 属性に使用されるポリシー) を返します。</span><span class="sxs-lookup"><span data-stu-id="d85bc-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) returns the default authorization policy (the policy used for `[Authorize]` attributes without a policy specified).</span></span> 
* <span data-ttu-id="d85bc-124">[Getfallbackpolicyasync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync)は、フォールバック承認ポリシー (ポリシーが指定されていない場合に、承認ミドルウェアによって使用されるポリシー) を返します。</span><span class="sxs-lookup"><span data-stu-id="d85bc-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) returns the fallback authorization policy (the policy used by the Authorization Middleware when no policy is specified).</span></span> 

<span data-ttu-id="d85bc-125">これらの Api を実装することで、承認ポリシーの提供方法をカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="d85bc-125">By implementing these APIs, you can customize how authorization policies are provided.</span></span>

## <a name="parameterized-authorize-attribute-example"></a><span data-ttu-id="d85bc-126">パラメーター化された承認属性の例</span><span class="sxs-lookup"><span data-stu-id="d85bc-126">Parameterized authorize attribute example</span></span>

<span data-ttu-id="d85bc-127">`IAuthorizationPolicyProvider` が役立つシナリオの1つは、パラメーターに依存する要件を持つカスタム `[Authorize]` 属性を有効にすることです。</span><span class="sxs-lookup"><span data-stu-id="d85bc-127">One scenario where `IAuthorizationPolicyProvider` is useful is enabling custom `[Authorize]` attributes whose requirements depend on a parameter.</span></span> <span data-ttu-id="d85bc-128">たとえば、[ポリシーベースの承認](xref:security/authorization/policies)ドキュメントでは、age ベース ("AtLeast21") ポリシーがサンプルとして使用されていました。</span><span class="sxs-lookup"><span data-stu-id="d85bc-128">For example, in [policy-based authorization](xref:security/authorization/policies) documentation, an age-based (“AtLeast21”) policy was used as a sample.</span></span> <span data-ttu-id="d85bc-129">アプリ内のさまざまなコントローラーアクションを*異なる*年齢のユーザーが使用できるようにする必要がある場合は、さまざまな年齢ベースのポリシーを作成すると便利です。</span><span class="sxs-lookup"><span data-stu-id="d85bc-129">If different controller actions in an app should be made available to users of *different* ages, it might be useful to have many different age-based policies.</span></span> <span data-ttu-id="d85bc-130">アプリケーションが `AuthorizationOptions`に必要とする、さまざまな期間ベースのポリシーを登録する代わりに、カスタム `IAuthorizationPolicyProvider`を使用してポリシーを動的に生成することができます。</span><span class="sxs-lookup"><span data-stu-id="d85bc-130">Instead of registering all the different age-based policies that the application will need in `AuthorizationOptions`, you can generate the policies dynamically with a custom `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="d85bc-131">ポリシーを簡単に使用できるようにするには、`[MinimumAgeAuthorize(20)]`のようなカスタム承認属性を使用してアクションに注釈を設定します。</span><span class="sxs-lookup"><span data-stu-id="d85bc-131">To make using the policies easier, you can annotate actions with custom authorization attribute like `[MinimumAgeAuthorize(20)]`.</span></span>

## <a name="custom-authorization-attributes"></a><span data-ttu-id="d85bc-132">カスタム承認属性</span><span class="sxs-lookup"><span data-stu-id="d85bc-132">Custom Authorization attributes</span></span>

<span data-ttu-id="d85bc-133">承認ポリシーは、名前によって識別されます。</span><span class="sxs-lookup"><span data-stu-id="d85bc-133">Authorization policies are identified by their names.</span></span> <span data-ttu-id="d85bc-134">前に説明したカスタム `MinimumAgeAuthorizeAttribute` は、対応する承認ポリシーを取得するために使用できる文字列に引数をマップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d85bc-134">The custom `MinimumAgeAuthorizeAttribute` described previously needs to map arguments into a string that can be used to retrieve the corresponding authorization policy.</span></span> <span data-ttu-id="d85bc-135">これを行うには、`AuthorizeAttribute` から派生させ、`Age` プロパティが `AuthorizeAttribute.Policy` プロパティをラップするようにします。</span><span class="sxs-lookup"><span data-stu-id="d85bc-135">You can do this by deriving from `AuthorizeAttribute` and making the `Age` property wrap the `AuthorizeAttribute.Policy` property.</span></span>

```csharp
internal class MinimumAgeAuthorizeAttribute : AuthorizeAttribute
{
    const string POLICY_PREFIX = "MinimumAge";

    public MinimumAgeAuthorizeAttribute(int age) => Age = age;

    // Get or set the Age property by manipulating the underlying Policy property
    public int Age
    {
        get
        {
            if (int.TryParse(Policy.Substring(POLICY_PREFIX.Length), out var age))
            {
                return age;
            }
            return default(int);
        }
        set
        {
            Policy = $"{POLICY_PREFIX}{value.ToString()}";
        }
    }
}
```

<span data-ttu-id="d85bc-136">この属性の型には、ハードコーディングされたプレフィックス (`"MinimumAge"`) に基づく `Policy` 文字列と、コンストラクターを介して渡される整数があります。</span><span class="sxs-lookup"><span data-stu-id="d85bc-136">This attribute type has a `Policy` string based on the hard-coded prefix (`"MinimumAge"`) and an integer passed in via the constructor.</span></span>

<span data-ttu-id="d85bc-137">これは、パラメーターとして整数を受け取る点を除いて、他の `Authorize` 属性と同じようにアクションに適用できます。</span><span class="sxs-lookup"><span data-stu-id="d85bc-137">You can apply it to actions in the same way as other `Authorize` attributes except that it takes an integer as a parameter.</span></span>

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a><span data-ttu-id="d85bc-138">カスタム IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="d85bc-138">Custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="d85bc-139">カスタム `MinimumAgeAuthorizeAttribute` を使用すると、必要最小限の期間に承認ポリシーを簡単に要求できます。</span><span class="sxs-lookup"><span data-stu-id="d85bc-139">The custom `MinimumAgeAuthorizeAttribute` makes it easy to request authorization policies for any minimum age desired.</span></span> <span data-ttu-id="d85bc-140">解決すべき次の問題は、すべての異なる年齢に対して承認ポリシーを使用できるようにすることです。</span><span class="sxs-lookup"><span data-stu-id="d85bc-140">The next problem to solve is making sure that authorization policies are available for all of those different ages.</span></span> <span data-ttu-id="d85bc-141">ここで `IAuthorizationPolicyProvider` が役に立ちます。</span><span class="sxs-lookup"><span data-stu-id="d85bc-141">This is where an `IAuthorizationPolicyProvider` is useful.</span></span>

<span data-ttu-id="d85bc-142">`MinimumAgeAuthorizationAttribute`を使用する場合、承認ポリシー名は `"MinimumAge" + Age`のパターンに従います。そのため、カスタム `IAuthorizationPolicyProvider` では、次の方法で承認ポリシーを生成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d85bc-142">When using `MinimumAgeAuthorizationAttribute`, the authorization policy names will follow the pattern `"MinimumAge" + Age`, so the custom `IAuthorizationPolicyProvider` should generate authorization policies by:</span></span>

* <span data-ttu-id="d85bc-143">ポリシー名から age を解析しています。</span><span class="sxs-lookup"><span data-stu-id="d85bc-143">Parsing the age from the policy name.</span></span>
* <span data-ttu-id="d85bc-144">`AuthorizationPolicyBuilder` を使用した新しい `AuthorizationPolicy` の作成</span><span class="sxs-lookup"><span data-stu-id="d85bc-144">Using `AuthorizationPolicyBuilder` to create a new `AuthorizationPolicy`</span></span>
* <span data-ttu-id="d85bc-145">次の例では、cookie を使用してユーザーが認証されることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="d85bc-145">In this and following examples it will be assumed that the user is authenticated via a cookie.</span></span> <span data-ttu-id="d85bc-146">`AuthorizationPolicyBuilder` は、少なくとも1つの認証スキーム名を使用して構築するか、常に成功する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d85bc-146">The `AuthorizationPolicyBuilder` should either be constructed with at least one authorization scheme name or always succeed.</span></span> <span data-ttu-id="d85bc-147">そうしないと、ユーザーにチャレンジを提供する方法に関する情報はなく、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="d85bc-147">Otherwise there is no information on how to provide a challenge to the user and an exception will be thrown.</span></span>
* <span data-ttu-id="d85bc-148">`AuthorizationPolicyBuilder.AddRequirements`の年齢に基づいてポリシーに要件を追加します。</span><span class="sxs-lookup"><span data-stu-id="d85bc-148">Adding requirements to the policy based on the age with `AuthorizationPolicyBuilder.AddRequirements`.</span></span> <span data-ttu-id="d85bc-149">他のシナリオでは、代わりに `RequireClaim`、`RequireRole`、または `RequireUserName` を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="d85bc-149">In other scenarios, you might use `RequireClaim`, `RequireRole`, or `RequireUserName` instead.</span></span>

```csharp
internal class MinimumAgePolicyProvider : IAuthorizationPolicyProvider
{
    const string POLICY_PREFIX = "MinimumAge";

    // Policies are looked up by string name, so expect 'parameters' (like age)
    // to be embedded in the policy names. This is abstracted away from developers
    // by the more strongly-typed attributes derived from AuthorizeAttribute
    // (like [MinimumAgeAuthorize()] in this sample)
    public Task<AuthorizationPolicy> GetPolicyAsync(string policyName)
    {
        if (policyName.StartsWith(POLICY_PREFIX, StringComparison.OrdinalIgnoreCase) &&
            int.TryParse(policyName.Substring(POLICY_PREFIX.Length), out var age))
        {
            var policy = new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme);
            policy.AddRequirements(new MinimumAgeRequirement(age));
            return Task.FromResult(policy.Build());
        }

        return Task.FromResult<AuthorizationPolicy>(null);
    }
}
```

## <a name="multiple-authorization-policy-providers"></a><span data-ttu-id="d85bc-150">複数の承認ポリシープロバイダー</span><span class="sxs-lookup"><span data-stu-id="d85bc-150">Multiple authorization policy providers</span></span>

<span data-ttu-id="d85bc-151">カスタム `IAuthorizationPolicyProvider` 実装を使用する場合、ASP.NET Core は `IAuthorizationPolicyProvider`のインスタンスを1つだけ使用することに注意してください。</span><span class="sxs-lookup"><span data-stu-id="d85bc-151">When using custom `IAuthorizationPolicyProvider` implementations, keep in mind that ASP.NET Core only uses one instance of `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="d85bc-152">使用されるすべてのポリシー名に対してカスタムプロバイダーが承認ポリシーを提供できない場合は、バックアッププロバイダーに従う必要があります。</span><span class="sxs-lookup"><span data-stu-id="d85bc-152">If a custom provider isn't able to provide authorization policies for all policy names that will be used, it should defer to a backup provider.</span></span> 

<span data-ttu-id="d85bc-153">たとえば、カスタムの年齢ポリシーと従来のロールベースのポリシーの取得の両方を必要とするアプリケーションについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="d85bc-153">For example, consider an application that needs both custom age policies and more traditional role-based policy retrieval.</span></span> <span data-ttu-id="d85bc-154">このようなアプリでは、次のようなカスタム承認ポリシープロバイダーを使用できます。</span><span class="sxs-lookup"><span data-stu-id="d85bc-154">Such an app could use a custom authorization policy provider that:</span></span>

* <span data-ttu-id="d85bc-155">ポリシー名の解析を試みます。</span><span class="sxs-lookup"><span data-stu-id="d85bc-155">Attempts to parse policy names.</span></span> 
* <span data-ttu-id="d85bc-156">ポリシー名に age が含まれていない場合は、別のポリシープロバイダー (`DefaultAuthorizationPolicyProvider`など) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d85bc-156">Calls into a different policy provider (like `DefaultAuthorizationPolicyProvider`) if the policy name doesn't contain an age.</span></span>

<span data-ttu-id="d85bc-157">上に示した `IAuthorizationPolicyProvider` の実装例を更新して、コンストラクターでバックアップポリシープロバイダーを作成することによって、`DefaultAuthorizationPolicyProvider` を使用することができます (ポリシー名が予期された ' MinimumAge ' + age) と一致しない場合に使用します)。</span><span class="sxs-lookup"><span data-stu-id="d85bc-157">The example `IAuthorizationPolicyProvider` implementation shown above can be updated to use the `DefaultAuthorizationPolicyProvider` by creating a backup policy provider in its constructor (to be used in case the policy name doesn't match its expected pattern of 'MinimumAge' + age).</span></span>

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

<span data-ttu-id="d85bc-158">次に、null を返すのではなく、`BackupPolicyProvider` を使用するように `GetPolicyAsync` メソッドを更新できます。</span><span class="sxs-lookup"><span data-stu-id="d85bc-158">Then, the `GetPolicyAsync` method can be updated to use the `BackupPolicyProvider` instead of returning null:</span></span>

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a><span data-ttu-id="d85bc-159">既定のポリシー</span><span class="sxs-lookup"><span data-stu-id="d85bc-159">Default policy</span></span>

<span data-ttu-id="d85bc-160">カスタム `IAuthorizationPolicyProvider` は、名前付き承認ポリシーを提供するだけでなく、ポリシー名が指定されていない `[Authorize]` 属性に対して承認ポリシーを提供する `GetDefaultPolicyAsync` を実装する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d85bc-160">In addition to providing named authorization policies, a custom `IAuthorizationPolicyProvider` needs to implement `GetDefaultPolicyAsync` to provide an authorization policy for `[Authorize]` attributes without a policy name specified.</span></span>

<span data-ttu-id="d85bc-161">多くの場合、この承認属性には認証されたユーザーのみが必要であるため、`RequireAuthenticatedUser`の呼び出しを使用して必要なポリシーを作成できます。</span><span class="sxs-lookup"><span data-stu-id="d85bc-161">In many cases, this authorization attribute only requires an authenticated user, so you can make the necessary policy with a call to `RequireAuthenticatedUser`:</span></span>

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

<span data-ttu-id="d85bc-162">カスタム `IAuthorizationPolicyProvider`のすべての側面と同様に、必要に応じてこれをカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="d85bc-162">As with all aspects of a custom `IAuthorizationPolicyProvider`, you can customize this, as needed.</span></span> <span data-ttu-id="d85bc-163">場合によっては、フォールバック `IAuthorizationPolicyProvider`から既定のポリシーを取得することが望ましい場合があります。</span><span class="sxs-lookup"><span data-stu-id="d85bc-163">In some cases, it may be desirable to retrieve the default policy from a fallback `IAuthorizationPolicyProvider`.</span></span>

## <a name="fallback-policy"></a><span data-ttu-id="d85bc-164">フォールバックポリシー</span><span class="sxs-lookup"><span data-stu-id="d85bc-164">Fallback policy</span></span>

<span data-ttu-id="d85bc-165">カスタム `IAuthorizationPolicyProvider` は、必要に応じて `GetFallbackPolicyAsync` を実装して、ポリシーを[組み合わせる](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine)とき、およびポリシーが指定されていない場合に使用されるポリシーを提供できます。</span><span class="sxs-lookup"><span data-stu-id="d85bc-165">A custom `IAuthorizationPolicyProvider` can optionally implement `GetFallbackPolicyAsync` to provide a policy that's used when [combining policies](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) and when no policies are specified.</span></span> <span data-ttu-id="d85bc-166">`GetFallbackPolicyAsync` が null 以外のポリシーを返す場合は、要求にポリシーが指定されていない場合に、返されたポリシーが承認ミドルウェアによって使用されます。</span><span class="sxs-lookup"><span data-stu-id="d85bc-166">If `GetFallbackPolicyAsync` returns a non-null policy, the returned policy is used by the Authorization Middleware when no policies are specified for the request.</span></span>

<span data-ttu-id="d85bc-167">フォールバックポリシーが必要ない場合、プロバイダーは `null` を返すか、フォールバックプロバイダーに遅延させることができます。</span><span class="sxs-lookup"><span data-stu-id="d85bc-167">If no fallback policy is required, the provider can return `null` or defer to the fallback provider:</span></span>

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a><span data-ttu-id="d85bc-168">カスタム IAuthorizationPolicyProvider を使用する</span><span class="sxs-lookup"><span data-stu-id="d85bc-168">Use a custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="d85bc-169">`IAuthorizationPolicyProvider`からカスタムポリシーを使用するには、次のことを行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="d85bc-169">To use custom policies from an `IAuthorizationPolicyProvider`, you must:</span></span>

* <span data-ttu-id="d85bc-170">ポリシーベースの承認シナリオと同様に、適切な `AuthorizationHandler` の種類を依存関係の挿入 ([ポリシーベースの承認](xref:security/authorization/policies#authorization-handlers)で説明) に登録します。</span><span class="sxs-lookup"><span data-stu-id="d85bc-170">Register the appropriate `AuthorizationHandler` types with dependency injection (described in [policy-based authorization](xref:security/authorization/policies#authorization-handlers)), as with all policy-based authorization scenarios.</span></span>
* <span data-ttu-id="d85bc-171">アプリの依存関係挿入サービスコレクション (`Startup.ConfigureServices`) にカスタムの `IAuthorizationPolicyProvider` の種類を登録して、既定のポリシープロバイダーを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="d85bc-171">Register the custom `IAuthorizationPolicyProvider` type in the app's dependency injection service collection (in `Startup.ConfigureServices`) to replace the default policy provider.</span></span>

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

<span data-ttu-id="d85bc-172">完全なカスタム `IAuthorizationPolicyProvider` サンプルについては、 [aspnet/AuthSamples GitHub リポジトリ](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d85bc-172">A complete custom `IAuthorizationPolicyProvider` sample is available in the [aspnet/AuthSamples GitHub repository](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider).</span></span>
