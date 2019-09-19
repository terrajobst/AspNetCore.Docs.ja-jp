---
title: ASP.NET Core のカスタム承認ポリシープロバイダー
author: mjrousos
description: ASP.NET Core アプリでカスタム IAuthorizationPolicyProvider を使用して、承認ポリシーを動的に生成する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: b11f7f94e1042e65d5f2ff8ab0fe9ea7838bebeb
ms.sourcegitcommit: b1e480e1736b0fe0e4d8dce4a4cf5c8e47fc2101
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2019
ms.locfileid: "71108057"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a><span data-ttu-id="3fc44-103">ASP.NET Core で IAuthorizationPolicyProvider を使用するカスタム承認ポリシープロバイダー</span><span class="sxs-lookup"><span data-stu-id="3fc44-103">Custom Authorization Policy Providers using IAuthorizationPolicyProvider in ASP.NET Core</span></span> 

<span data-ttu-id="3fc44-104">によって[Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="3fc44-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="3fc44-105">通常、[ポリシーベースの承認](xref:security/authorization/policies)を使用する場合、ポリシーは`AuthorizationOptions.AddPolicy` 、承認サービス構成の一部としてを呼び出すことによって登録されます。</span><span class="sxs-lookup"><span data-stu-id="3fc44-105">Typically when using [policy-based authorization](xref:security/authorization/policies), policies are registered by calling `AuthorizationOptions.AddPolicy` as part of authorization service configuration.</span></span> <span data-ttu-id="3fc44-106">場合によっては、すべての承認ポリシーをこの方法で登録することができない (または望ましくない) ことがあります。</span><span class="sxs-lookup"><span data-stu-id="3fc44-106">In some scenarios, it may not be possible (or desirable) to register all authorization policies in this way.</span></span> <span data-ttu-id="3fc44-107">そのような場合は、カスタム`IAuthorizationPolicyProvider`を使用して、承認ポリシーの提供方法を制御できます。</span><span class="sxs-lookup"><span data-stu-id="3fc44-107">In those cases, you can use a custom `IAuthorizationPolicyProvider` to control how authorization policies are supplied.</span></span>

<span data-ttu-id="3fc44-108">カスタム[IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider)が役に立つ可能性のあるシナリオの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="3fc44-108">Examples of scenarios where a custom [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) may be useful include:</span></span>

* <span data-ttu-id="3fc44-109">外部サービスを使用してポリシーの評価を提供する。</span><span class="sxs-lookup"><span data-stu-id="3fc44-109">Using an external service to provide policy evaluation.</span></span>
* <span data-ttu-id="3fc44-110">多数のポリシー (部屋番号や年齢など) を使用すると、個々の承認ポリシーを1つの`AuthorizationOptions.AddPolicy`呼び出しで追加することは意味がありません。</span><span class="sxs-lookup"><span data-stu-id="3fc44-110">Using a large range of policies (for different room numbers or ages, for example), so it doesn’t make sense to add each individual authorization policy with an `AuthorizationOptions.AddPolicy` call.</span></span>
* <span data-ttu-id="3fc44-111">外部データソース (データベースなど) の情報に基づいて実行時にポリシーを作成するか、別のメカニズムを使用して承認要件を動的に決定します。</span><span class="sxs-lookup"><span data-stu-id="3fc44-111">Creating policies at runtime based on information in an external data source (like a database) or determining authorization requirements dynamically through another mechanism.</span></span>

<span data-ttu-id="3fc44-112">[AspNetCore GitHub リポジトリ](https://github.com/aspnet/AspNetCore)から[サンプルコードを表示またはダウンロード](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)します。</span><span class="sxs-lookup"><span data-stu-id="3fc44-112">[View or download sample code](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) from the [AspNetCore GitHub repository](https://github.com/aspnet/AspNetCore).</span></span> <span data-ttu-id="3fc44-113">Aspnet/AspNetCore リポジトリ ZIP ファイルをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="3fc44-113">Download the aspnet/AspNetCore repository ZIP file.</span></span> <span data-ttu-id="3fc44-114">ファイルを解凍します。</span><span class="sxs-lookup"><span data-stu-id="3fc44-114">Unzip the file.</span></span> <span data-ttu-id="3fc44-115">*Src/Security/samples/CustomPolicyProvider*プロジェクトフォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="3fc44-115">Navigate to the *src/Security/samples/CustomPolicyProvider* project folder.</span></span>

## <a name="customize-policy-retrieval"></a><span data-ttu-id="3fc44-116">ポリシーの取得のカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="3fc44-116">Customize policy retrieval</span></span>

<span data-ttu-id="3fc44-117">ASP.NET Core アプリは、インターフェイスの実装`IAuthorizationPolicyProvider`を使用して承認ポリシーを取得します。</span><span class="sxs-lookup"><span data-stu-id="3fc44-117">ASP.NET Core apps use an implementation of the `IAuthorizationPolicyProvider` interface to retrieve authorization policies.</span></span> <span data-ttu-id="3fc44-118">既定では、 [Defaultauthorizationpolicyprovider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider)が登録され、使用されます。</span><span class="sxs-lookup"><span data-stu-id="3fc44-118">By default, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) is registered and used.</span></span> <span data-ttu-id="3fc44-119">`DefaultAuthorizationPolicyProvider`呼び出しで指定`AuthorizationOptions`されたからポリシーを返します。 `IServiceCollection.AddAuthorization`</span><span class="sxs-lookup"><span data-stu-id="3fc44-119">`DefaultAuthorizationPolicyProvider` returns policies from the `AuthorizationOptions` provided in an `IServiceCollection.AddAuthorization` call.</span></span>

<span data-ttu-id="3fc44-120">この動作をカスタマイズするには、アプリ`IAuthorizationPolicyProvider`の[依存関係挿入](xref:fundamentals/dependency-injection)コンテナーに別の実装を登録します。</span><span class="sxs-lookup"><span data-stu-id="3fc44-120">You can customize this behavior by registering a different `IAuthorizationPolicyProvider` implementation in the app’s [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> 

<span data-ttu-id="3fc44-121">インターフェイス`IAuthorizationPolicyProvider`には、次の2つの api が含まれます。</span><span class="sxs-lookup"><span data-stu-id="3fc44-121">The `IAuthorizationPolicyProvider` interface contains two APIs:</span></span>

* <span data-ttu-id="3fc44-122">[Getpolicyasync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_)は、指定された名前の承認ポリシーを返します。</span><span class="sxs-lookup"><span data-stu-id="3fc44-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) returns an authorization policy for a given name.</span></span>
* <span data-ttu-id="3fc44-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync)では、既定の承認ポリシー (ポリシーが`[Authorize]`指定されていない属性に使用されるポリシー) が返されます。</span><span class="sxs-lookup"><span data-stu-id="3fc44-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) returns the default authorization policy (the policy used for `[Authorize]` attributes without a policy specified).</span></span> 

<span data-ttu-id="3fc44-124">これら2つの Api を実装することで、承認ポリシーの提供方法をカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="3fc44-124">By implementing these two APIs, you can customize how authorization policies are provided.</span></span>

## <a name="parameterized-authorize-attribute-example"></a><span data-ttu-id="3fc44-125">パラメーター化された承認属性の例</span><span class="sxs-lookup"><span data-stu-id="3fc44-125">Parameterized authorize attribute example</span></span>

<span data-ttu-id="3fc44-126">`IAuthorizationPolicyProvider`が便利なシナリオの1つは`[Authorize]` 、パラメーターに依存する要件を持つカスタム属性を有効にすることです。</span><span class="sxs-lookup"><span data-stu-id="3fc44-126">One scenario where `IAuthorizationPolicyProvider` is useful is enabling custom `[Authorize]` attributes whose requirements depend on a parameter.</span></span> <span data-ttu-id="3fc44-127">たとえば、[ポリシーベースの承認](xref:security/authorization/policies)ドキュメントでは、age ベース ("AtLeast21") ポリシーがサンプルとして使用されていました。</span><span class="sxs-lookup"><span data-stu-id="3fc44-127">For example, in [policy-based authorization](xref:security/authorization/policies) documentation, an age-based (“AtLeast21”) policy was used as a sample.</span></span> <span data-ttu-id="3fc44-128">アプリ内のさまざまなコントローラーアクションを*異なる*年齢のユーザーが使用できるようにする必要がある場合は、さまざまな年齢ベースのポリシーを作成すると便利です。</span><span class="sxs-lookup"><span data-stu-id="3fc44-128">If different controller actions in an app should be made available to users of *different* ages, it might be useful to have many different age-based policies.</span></span> <span data-ttu-id="3fc44-129">アプリケーションが必要`AuthorizationOptions`とするさまざまな有効期間ベースのポリシーをすべて登録する代わりに、カスタム`IAuthorizationPolicyProvider`のを使用してポリシーを動的に生成できます。</span><span class="sxs-lookup"><span data-stu-id="3fc44-129">Instead of registering all the different age-based policies that the application will need in `AuthorizationOptions`, you can generate the policies dynamically with a custom `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="3fc44-130">ポリシーを簡単に使用できるようにするには、のような`[MinimumAgeAuthorize(20)]`カスタム承認属性を使用してアクションに注釈を設定します。</span><span class="sxs-lookup"><span data-stu-id="3fc44-130">To make using the policies easier, you can annotate actions with custom authorization attribute like `[MinimumAgeAuthorize(20)]`.</span></span>

## <a name="custom-authorization-attributes"></a><span data-ttu-id="3fc44-131">カスタム承認属性</span><span class="sxs-lookup"><span data-stu-id="3fc44-131">Custom Authorization attributes</span></span>

<span data-ttu-id="3fc44-132">承認ポリシーは、名前によって識別されます。</span><span class="sxs-lookup"><span data-stu-id="3fc44-132">Authorization policies are identified by their names.</span></span> <span data-ttu-id="3fc44-133">前に`MinimumAgeAuthorizeAttribute`説明したカスタムは、対応する承認ポリシーを取得するために使用できる文字列に引数をマップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="3fc44-133">The custom `MinimumAgeAuthorizeAttribute` described previously needs to map arguments into a string that can be used to retrieve the corresponding authorization policy.</span></span> <span data-ttu-id="3fc44-134">これを行うには、から`AuthorizeAttribute` `Age`派生させ、プロパティを`AuthorizeAttribute.Policy`ラップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="3fc44-134">You can do this by deriving from `AuthorizeAttribute` and making the `Age` property wrap the `AuthorizeAttribute.Policy` property.</span></span>

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

<span data-ttu-id="3fc44-135">この属性の型に`Policy`は、ハードコーディングされたプレフィックス (`"MinimumAge"`) と、コンストラクターを介して渡される整数に基づく文字列があります。</span><span class="sxs-lookup"><span data-stu-id="3fc44-135">This attribute type has a `Policy` string based on the hard-coded prefix (`"MinimumAge"`) and an integer passed in via the constructor.</span></span>

<span data-ttu-id="3fc44-136">パラメーターとして整数を受け取る点を除いて、 `Authorize`他の属性と同じようにアクションに適用できます。</span><span class="sxs-lookup"><span data-stu-id="3fc44-136">You can apply it to actions in the same way as other `Authorize` attributes except that it takes an integer as a parameter.</span></span>

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a><span data-ttu-id="3fc44-137">カスタム IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="3fc44-137">Custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="3fc44-138">カスタム`MinimumAgeAuthorizeAttribute`を使用すると、必要最小限の期間に承認ポリシーを簡単に要求できます。</span><span class="sxs-lookup"><span data-stu-id="3fc44-138">The custom `MinimumAgeAuthorizeAttribute` makes it easy to request authorization policies for any minimum age desired.</span></span> <span data-ttu-id="3fc44-139">解決すべき次の問題は、すべての異なる年齢に対して承認ポリシーを使用できるようにすることです。</span><span class="sxs-lookup"><span data-stu-id="3fc44-139">The next problem to solve is making sure that authorization policies are available for all of those different ages.</span></span> <span data-ttu-id="3fc44-140">ここでは、 `IAuthorizationPolicyProvider`が役に立ちます。</span><span class="sxs-lookup"><span data-stu-id="3fc44-140">This is where an `IAuthorizationPolicyProvider` is useful.</span></span>

<span data-ttu-id="3fc44-141">を使用`MinimumAgeAuthorizationAttribute`する場合、承認ポリシー名はパターン`"MinimumAge" + Age`に従います。その`IAuthorizationPolicyProvider`ため、カスタムでは、次の方法で承認ポリシーを生成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3fc44-141">When using `MinimumAgeAuthorizationAttribute`, the authorization policy names will follow the pattern `"MinimumAge" + Age`, so the custom `IAuthorizationPolicyProvider` should generate authorization policies by:</span></span>

* <span data-ttu-id="3fc44-142">ポリシー名から age を解析しています。</span><span class="sxs-lookup"><span data-stu-id="3fc44-142">Parsing the age from the policy name.</span></span>
* <span data-ttu-id="3fc44-143">を`AuthorizationPolicyBuilder`使用して新しいを作成する`AuthorizationPolicy`</span><span class="sxs-lookup"><span data-stu-id="3fc44-143">Using `AuthorizationPolicyBuilder` to create a new `AuthorizationPolicy`</span></span>
* <span data-ttu-id="3fc44-144">次の例では、cookie を使用してユーザーが認証されることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="3fc44-144">In this and following examples it will be assumed that the user is authenticated via a cookie.</span></span> <span data-ttu-id="3fc44-145">は`AuthorizationPolicyBuilder` 、少なくとも1つの認証スキーム名を使用して構築するか、常に成功する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3fc44-145">The `AuthorizationPolicyBuilder` should either be constructed with at least one authorization scheme name or always succeed.</span></span> <span data-ttu-id="3fc44-146">そうしないと、ユーザーにチャレンジを提供する方法に関する情報はなく、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="3fc44-146">Otherwise there is no information on how to provide a challenge to the user and an exception will be thrown.</span></span>
* <span data-ttu-id="3fc44-147">の`AuthorizationPolicyBuilder.AddRequirements`年齢に基づいてポリシーに要件を追加します。</span><span class="sxs-lookup"><span data-stu-id="3fc44-147">Adding requirements to the policy based on the age with `AuthorizationPolicyBuilder.AddRequirements`.</span></span> <span data-ttu-id="3fc44-148">他のシナリオでは、 `RequireClaim` `RequireRole`代わりに、、また`RequireUserName`はを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="3fc44-148">In other scenarios, you might use `RequireClaim`, `RequireRole`, or `RequireUserName` instead.</span></span>

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

## <a name="multiple-authorization-policy-providers"></a><span data-ttu-id="3fc44-149">複数の承認ポリシープロバイダー</span><span class="sxs-lookup"><span data-stu-id="3fc44-149">Multiple authorization policy providers</span></span>

<span data-ttu-id="3fc44-150">カスタム`IAuthorizationPolicyProvider`実装を使用する場合、ASP.NET Core はの`IAuthorizationPolicyProvider`インスタンスを1つだけ使用することに注意してください。</span><span class="sxs-lookup"><span data-stu-id="3fc44-150">When using custom `IAuthorizationPolicyProvider` implementations, keep in mind that ASP.NET Core only uses one instance of `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="3fc44-151">使用されるすべてのポリシー名に対してカスタムプロバイダーが承認ポリシーを提供できない場合は、バックアッププロバイダーに戻される必要があります。</span><span class="sxs-lookup"><span data-stu-id="3fc44-151">If a custom provider isn't able to provide authorization policies for all policy names that will be used, it should fall back to a backup provider.</span></span> 

<span data-ttu-id="3fc44-152">たとえば、カスタムの年齢ポリシーと従来のロールベースのポリシーの取得の両方を必要とするアプリケーションについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="3fc44-152">For example, consider an application that needs both custom age policies and more traditional role-based policy retrieval.</span></span> <span data-ttu-id="3fc44-153">このようなアプリでは、次のようなカスタム承認ポリシープロバイダーを使用できます。</span><span class="sxs-lookup"><span data-stu-id="3fc44-153">Such an app could use a custom authorization policy provider that:</span></span>

* <span data-ttu-id="3fc44-154">ポリシー名の解析を試みます。</span><span class="sxs-lookup"><span data-stu-id="3fc44-154">Attempts to parse policy names.</span></span> 
* <span data-ttu-id="3fc44-155">ポリシー名に age が含まれて`DefaultAuthorizationPolicyProvider`いない場合は、別のポリシープロバイダー (など) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="3fc44-155">Calls into a different policy provider (like `DefaultAuthorizationPolicyProvider`) if the policy name doesn't contain an age.</span></span>

<span data-ttu-id="3fc44-156">上の`IAuthorizationPolicyProvider`例の実装は、コンストラクターでフォールバックポリシー `DefaultAuthorizationPolicyProvider`プロバイダーを作成することによって、を使用するように更新できます (ポリシー名が予期された "minimumage" + age のパターンと一致しない場合に使用されます)。</span><span class="sxs-lookup"><span data-stu-id="3fc44-156">The example `IAuthorizationPolicyProvider` implementation shown above can be updated to use the `DefaultAuthorizationPolicyProvider` by creating a fallback policy provider in its constructor (to be used in case the policy name doesn't match its expected pattern of 'MinimumAge' + age).</span></span>

```csharp
private DefaultAuthorizationPolicyProvider FallbackPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    FallbackPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

<span data-ttu-id="3fc44-157">次に`FallbackPolicyProvider` 、メソッドを更新して、nullを返すのではなく、を使用するようにすること`GetPolicyAsync`ができます。</span><span class="sxs-lookup"><span data-stu-id="3fc44-157">Then, the `GetPolicyAsync` method can be updated to use the `FallbackPolicyProvider` instead of returning null:</span></span>

```csharp
...
return FallbackPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a><span data-ttu-id="3fc44-158">既定のポリシー</span><span class="sxs-lookup"><span data-stu-id="3fc44-158">Default policy</span></span>

<span data-ttu-id="3fc44-159">名前付き承認ポリシーを提供するだけでなく`IAuthorizationPolicyProvider` 、カスタムで`GetDefaultPolicyAsync`を実装して、ポリシー `[Authorize]`名が指定されていない属性に対して承認ポリシーを提供する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3fc44-159">In addition to providing named authorization policies, a custom `IAuthorizationPolicyProvider` needs to implement `GetDefaultPolicyAsync` to provide an authorization policy for `[Authorize]` attributes without a policy name specified.</span></span>

<span data-ttu-id="3fc44-160">多くの場合、この承認属性には認証されたユーザーのみが必要であるため、の呼び出し`RequireAuthenticatedUser`で必要なポリシーを作成できます。</span><span class="sxs-lookup"><span data-stu-id="3fc44-160">In many cases, this authorization attribute only requires an authenticated user, so you can make the necessary policy with a call to `RequireAuthenticatedUser`:</span></span>

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

<span data-ttu-id="3fc44-161">カスタム`IAuthorizationPolicyProvider`のすべての側面と同様に、必要に応じてこれをカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="3fc44-161">As with all aspects of a custom `IAuthorizationPolicyProvider`, you can customize this, as needed.</span></span> <span data-ttu-id="3fc44-162">場合によっては、フォールバック`IAuthorizationPolicyProvider`から既定のポリシーを取得することが望ましい場合があります。</span><span class="sxs-lookup"><span data-stu-id="3fc44-162">In some cases, it may be desirable to retrieve the default policy from a fallback `IAuthorizationPolicyProvider`.</span></span>

## <a name="required-policy"></a><span data-ttu-id="3fc44-163">必須のポリシー</span><span class="sxs-lookup"><span data-stu-id="3fc44-163">Required policy</span></span>

<span data-ttu-id="3fc44-164">カスタム`IAuthorizationPolicyProvider`をに実装`GetRequiredPolicyAsync`する必要があります。必要に応じて、常に必須のポリシーを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="3fc44-164">A custom `IAuthorizationPolicyProvider` needs to implement `GetRequiredPolicyAsync` to, optionally, provide a policy that is always required.</span></span> <span data-ttu-id="3fc44-165">が`GetRequiredPolicyAsync` null 以外のポリシーを返す場合、そのポリシーは、要求された他の (名前付きまたは既定の) ポリシーと結合されます。</span><span class="sxs-lookup"><span data-stu-id="3fc44-165">If `GetRequiredPolicyAsync` returns a non-null policy, that policy will be combined with any other (named or default) policy that is requested.</span></span>

<span data-ttu-id="3fc44-166">必要なポリシーが必要ない場合、プロバイダーは、null を返すか、フォールバックプロバイダーに遅延することができます。</span><span class="sxs-lookup"><span data-stu-id="3fc44-166">If no required policy is needed, the provider can just return null or defer to the fallback provider:</span></span>

```csharp
public Task<AuthorizationPolicy> GetRequiredPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a><span data-ttu-id="3fc44-167">カスタム IAuthorizationPolicyProvider を使用する</span><span class="sxs-lookup"><span data-stu-id="3fc44-167">Use a custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="3fc44-168">からカスタムポリシーを使用する`IAuthorizationPolicyProvider`には、次のことを行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="3fc44-168">To use custom policies from an `IAuthorizationPolicyProvider`, you must:</span></span>

* <span data-ttu-id="3fc44-169">ポリシーベースの`AuthorizationHandler`承認シナリオと同様に、適切な型を依存関係の挿入 ([ポリシーベースの承認](xref:security/authorization/policies#authorization-handlers)で説明) に登録します。</span><span class="sxs-lookup"><span data-stu-id="3fc44-169">Register the appropriate `AuthorizationHandler` types with dependency injection (described in [policy-based authorization](xref:security/authorization/policies#authorization-handlers)), as with all policy-based authorization scenarios.</span></span>
* <span data-ttu-id="3fc44-170">アプリの依存`IAuthorizationPolicyProvider`関係挿入サービスコレクション`Startup.ConfigureServices`() にカスタム型を登録して、既定のポリシープロバイダーを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3fc44-170">Register the custom `IAuthorizationPolicyProvider` type in the app's dependency injection service collection (in `Startup.ConfigureServices`) to replace the default policy provider.</span></span>

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

<span data-ttu-id="3fc44-171">完全なカスタム`IAuthorizationPolicyProvider`サンプルについては、 [aspnet/authsamples GitHub リポジトリ](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3fc44-171">A complete custom `IAuthorizationPolicyProvider` sample is available in the [aspnet/AuthSamples GitHub repository](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider).</span></span>
