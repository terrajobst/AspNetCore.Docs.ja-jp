---
title: ASP.NET Core での要求ベースの承認
author: rick-anderson
description: ASP.NET Core アプリで承認の要求チェックを追加する方法について説明します。
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/claims
ms.openlocfilehash: e289851aafcbc7e3b3f60ab9fbe4b182a78bdf8a
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652970"
---
# <a name="claims-based-authorization-in-aspnet-core"></a>ASP.NET Core での要求ベースの承認

<a name="security-authorization-claims-based"></a>

Id が作成されると、信頼されたパーティによって発行された1つまたは複数の要求が割り当てられる場合があります。 クレームは、サブジェクトの内容を表す名前と値のペアであり、サブジェクトが実行できる内容ではありません。 たとえば、ローカル運転免許証機関によって発行された運転免許証があるとします。 運転免許証には、お客様のドライバーのライセンスがあります。 この場合、要求名は `DateOfBirth`になります。要求の値は生の日付になります。たとえば `8th June 1970` と発行者は運転ライセンス機関になります。 クレームベースの承認では、最も単純にクレームの値を確認し、その値に基づいてリソースへのアクセスを許可します。 たとえば、夜のクラブへのアクセスが必要な場合、承認プロセスは次のようになります。

ドアのセキュリティ担当者は、お客様がアクセス権を付与する前に、誕生日の請求日の値と、発行者 (運転免許機関) を信頼するかどうかを評価します。

Id には複数の値を持つ複数の要求を含めることができ、同じ種類の複数の要求を含めることができます。

## <a name="adding-claims-checks"></a>要求チェックの追加

クレームベースの承認チェックは宣言型です。開発者は、コントローラーまたはコントローラー内のアクションに対してコード内にそれらを埋め込み、現在のユーザーが所有している必要がある要求を指定します。また、必要に応じて、要求が要求されたリソース。 要求要件はポリシーベースです。開発者は、要求要件を表すポリシーを作成して登録する必要があります。

最も単純な種類の要求ポリシーでは、要求の存在を検索し、値を確認しません。

まず、ポリシーをビルドして登録する必要があります。 これは承認サービス構成の一部として行われ、通常は*Startup.cs*ファイルの `ConfigureServices()` に含まれます。

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

この場合、`EmployeeOnly` ポリシーは、現在の id に `EmployeeNumber` 要求が存在するかどうかを確認します。

次に、`AuthorizeAttribute` 属性の `Policy` プロパティを使用してポリシーを適用し、ポリシー名を指定します。

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

`AuthorizeAttribute` 属性はコントローラー全体に適用できます。このインスタンスでは、ポリシーに一致する id のみが、コントローラー上の任意のアクションへのアクセスを許可されます。

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

`AuthorizeAttribute` 属性によって保護されているコントローラーがあり、特定のアクションへの匿名アクセスを許可する場合は、`AllowAnonymousAttribute` 属性を適用します。

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

ほとんどの要求には値が含まれます。 ポリシーを作成するときに、許可される値の一覧を指定できます。 次の例は、従業員番号が1、2、3、4、または5である従業員に対してのみ成功します。

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
### <a name="add-a-generic-claim-check"></a>汎用要求チェックの追加

要求値が単一の値ではない場合、または変換が必要な場合は、 [Requireassertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion)を使用します。 詳細については、「 [func を使用してポリシーを満たす](xref:security/authorization/policies#using-a-func-to-fulfill-a-policy)」を参照してください。

## <a name="multiple-policy-evaluation"></a>複数のポリシーの評価

複数のポリシーをコントローラーまたはアクションに適用する場合は、アクセスが許可される前にすべてのポリシーを渡す必要があります。 例 :

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

上の例では、`EmployeeOnly` ポリシーを満たす id は、コントローラーでポリシーが適用されているため、`Payslip` アクションにアクセスできます。 ただし、`UpdateSalary` アクションを呼び出すには、id が `EmployeeOnly` ポリシーと `HumanResources` ポリシーの*両方*を満たしている必要があります。

誕生日の請求書を取得するなど、より複雑なポリシーが必要な場合は、年数を計算し、その年齢を確認してから、[カスタムポリシーハンドラー](xref:security/authorization/policies)を記述する必要があります。
