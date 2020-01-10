---
title: ASP.NET Core のカスタム承認ポリシープロバイダー
author: mjrousos
description: ASP.NET Core アプリでカスタム IAuthorizationPolicyProvider を使用して、承認ポリシーを動的に生成する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 9f0a0cd5337f7f8d2fc8a4b6902a63b98f6bd702
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828985"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a>ASP.NET Core で IAuthorizationPolicyProvider を使用するカスタム承認ポリシープロバイダー 

作成者: [Mike Rousos](https://github.com/mjrousos)

通常、[ポリシーベースの承認](xref:security/authorization/policies)を使用する場合、ポリシーは、承認サービス構成の一部として `AuthorizationOptions.AddPolicy` を呼び出すことによって登録されます。 場合によっては、すべての承認ポリシーをこの方法で登録することができない (または望ましくない) ことがあります。 そのような場合は、カスタム `IAuthorizationPolicyProvider` を使用して、承認ポリシーをどのように提供するかを制御できます。

カスタム[IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider)が役に立つ可能性のあるシナリオの例を次に示します。

* 外部サービスを使用してポリシーの評価を提供する。
* 多数のポリシー (部屋番号や年齢など) を使用しているため、`AuthorizationOptions.AddPolicy` 呼び出しを使用して個々の承認ポリシーを追加することは意味がありません。
* 外部データソース (データベースなど) の情報に基づいて実行時にポリシーを作成するか、別のメカニズムを使用して承認要件を動的に決定します。

[AspNetCore GitHub リポジトリ](https://github.com/dotnet/AspNetCore)から[サンプルコードを表示またはダウンロード](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)します。 Dotnet/AspNetCore リポジトリ ZIP ファイルをダウンロードします。 ファイルを解凍します。 *Src/Security/samples/CustomPolicyProvider*プロジェクトフォルダーに移動します。

## <a name="customize-policy-retrieval"></a>ポリシーの取得のカスタマイズ

ASP.NET Core アプリは、`IAuthorizationPolicyProvider` インターフェイスの実装を使用して承認ポリシーを取得します。 既定では、 [Defaultauthorizationpolicyprovider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider)が登録され、使用されます。 `DefaultAuthorizationPolicyProvider` は、`IServiceCollection.AddAuthorization` の呼び出しで指定された `AuthorizationOptions` からポリシーを返します。

この動作をカスタマイズするには、アプリの[依存関係挿入](xref:fundamentals/dependency-injection)コンテナーに別の `IAuthorizationPolicyProvider` の実装を登録します。 

`IAuthorizationPolicyProvider` インターフェイスには、次の3つの Api が含まれています。

* [Getpolicyasync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_)は、指定された名前の承認ポリシーを返します。
* [GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync)は、既定の承認ポリシー (ポリシーが指定されていない `[Authorize]` 属性に使用されるポリシー) を返します。 
* [Getfallbackpolicyasync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync)は、フォールバック承認ポリシー (ポリシーが指定されていない場合に、承認ミドルウェアによって使用されるポリシー) を返します。 

これらの Api を実装することで、承認ポリシーの提供方法をカスタマイズできます。

## <a name="parameterized-authorize-attribute-example"></a>パラメーター化された承認属性の例

`IAuthorizationPolicyProvider` が役立つシナリオの1つは、パラメーターに依存する要件を持つカスタム `[Authorize]` 属性を有効にすることです。 たとえば、[ポリシーベースの承認](xref:security/authorization/policies)ドキュメントでは、age ベース ("AtLeast21") ポリシーがサンプルとして使用されていました。 アプリ内のさまざまなコントローラーアクションを*異なる*年齢のユーザーが使用できるようにする必要がある場合は、さまざまな年齢ベースのポリシーを作成すると便利です。 アプリケーションが `AuthorizationOptions`に必要とする、さまざまな期間ベースのポリシーを登録する代わりに、カスタム `IAuthorizationPolicyProvider`を使用してポリシーを動的に生成することができます。 ポリシーを簡単に使用できるようにするには、`[MinimumAgeAuthorize(20)]`のようなカスタム承認属性を使用してアクションに注釈を設定します。

## <a name="custom-authorization-attributes"></a>カスタム承認属性

承認ポリシーは、名前によって識別されます。 前に説明したカスタム `MinimumAgeAuthorizeAttribute` は、対応する承認ポリシーを取得するために使用できる文字列に引数をマップする必要があります。 これを行うには、`AuthorizeAttribute` から派生させ、`Age` プロパティが `AuthorizeAttribute.Policy` プロパティをラップするようにします。

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

この属性の型には、ハードコーディングされたプレフィックス (`"MinimumAge"`) に基づく `Policy` 文字列と、コンストラクターを介して渡される整数があります。

これは、パラメーターとして整数を受け取る点を除いて、他の `Authorize` 属性と同じようにアクションに適用できます。

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a>カスタム IAuthorizationPolicyProvider

カスタム `MinimumAgeAuthorizeAttribute` を使用すると、必要最小限の期間に承認ポリシーを簡単に要求できます。 解決すべき次の問題は、すべての異なる年齢に対して承認ポリシーを使用できるようにすることです。 ここで `IAuthorizationPolicyProvider` が役に立ちます。

`MinimumAgeAuthorizationAttribute`を使用する場合、承認ポリシー名は `"MinimumAge" + Age`のパターンに従います。そのため、カスタム `IAuthorizationPolicyProvider` では、次の方法で承認ポリシーを生成する必要があります。

* ポリシー名から age を解析しています。
* `AuthorizationPolicyBuilder` を使用した新しい `AuthorizationPolicy` の作成
* 次の例では、cookie を使用してユーザーが認証されることを前提としています。 `AuthorizationPolicyBuilder` は、少なくとも1つの認証スキーム名を使用して構築するか、常に成功する必要があります。 そうしないと、ユーザーにチャレンジを提供する方法に関する情報はなく、例外がスローされます。
* `AuthorizationPolicyBuilder.AddRequirements`の年齢に基づいてポリシーに要件を追加します。 他のシナリオでは、代わりに `RequireClaim`、`RequireRole`、または `RequireUserName` を使用することもできます。

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

## <a name="multiple-authorization-policy-providers"></a>複数の承認ポリシープロバイダー

カスタム `IAuthorizationPolicyProvider` 実装を使用する場合、ASP.NET Core は `IAuthorizationPolicyProvider`のインスタンスを1つだけ使用することに注意してください。 使用されるすべてのポリシー名に対してカスタムプロバイダーが承認ポリシーを提供できない場合は、バックアッププロバイダーに従う必要があります。 

たとえば、カスタムの年齢ポリシーと従来のロールベースのポリシーの取得の両方を必要とするアプリケーションについて考えてみます。 このようなアプリでは、次のようなカスタム承認ポリシープロバイダーを使用できます。

* ポリシー名の解析を試みます。 
* ポリシー名に age が含まれていない場合は、別のポリシープロバイダー (`DefaultAuthorizationPolicyProvider`など) を呼び出します。

上に示した `IAuthorizationPolicyProvider` の実装例を更新して、コンストラクターでバックアップポリシープロバイダーを作成することによって、`DefaultAuthorizationPolicyProvider` を使用することができます (ポリシー名が予期された ' MinimumAge ' + age) と一致しない場合に使用します)。

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

次に、null を返すのではなく、`BackupPolicyProvider` を使用するように `GetPolicyAsync` メソッドを更新できます。

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a>既定のポリシー

カスタム `IAuthorizationPolicyProvider` は、名前付き承認ポリシーを提供するだけでなく、ポリシー名が指定されていない `[Authorize]` 属性に対して承認ポリシーを提供する `GetDefaultPolicyAsync` を実装する必要があります。

多くの場合、この承認属性には認証されたユーザーのみが必要であるため、`RequireAuthenticatedUser`の呼び出しを使用して必要なポリシーを作成できます。

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

カスタム `IAuthorizationPolicyProvider`のすべての側面と同様に、必要に応じてこれをカスタマイズできます。 場合によっては、フォールバック `IAuthorizationPolicyProvider`から既定のポリシーを取得することが望ましい場合があります。

## <a name="fallback-policy"></a>フォールバックポリシー

カスタム `IAuthorizationPolicyProvider` は、必要に応じて `GetFallbackPolicyAsync` を実装して、ポリシーを[組み合わせる](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine)とき、およびポリシーが指定されていない場合に使用されるポリシーを提供できます。 `GetFallbackPolicyAsync` が null 以外のポリシーを返す場合は、要求にポリシーが指定されていない場合に、返されたポリシーが承認ミドルウェアによって使用されます。

フォールバックポリシーが必要ない場合、プロバイダーは `null` を返すか、フォールバックプロバイダーに遅延させることができます。

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a>カスタム IAuthorizationPolicyProvider を使用する

`IAuthorizationPolicyProvider`からカスタムポリシーを使用するには、次のことを行う必要があります。

* ポリシーベースの承認シナリオと同様に、適切な `AuthorizationHandler` の種類を依存関係の挿入 ([ポリシーベースの承認](xref:security/authorization/policies#authorization-handlers)で説明) に登録します。
* アプリの依存関係挿入サービスコレクション (`Startup.ConfigureServices`) にカスタムの `IAuthorizationPolicyProvider` の種類を登録して、既定のポリシープロバイダーを置き換えます。

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

完全なカスタム `IAuthorizationPolicyProvider` サンプルについては、 [aspnet/AuthSamples GitHub リポジトリ](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)を参照してください。
