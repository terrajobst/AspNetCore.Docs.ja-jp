---
title: ASP.NET Core で HttpContext にアクセスする
author: coderandhiker
description: ASP.NET Core で HttpContext にアクセスする方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
uid: fundamentals/httpcontext
ms.openlocfilehash: 8a7ee180380c42ea745c91b8e6a18c1baa820220
ms.sourcegitcommit: 5974e3e66dab3398ecf2324fbb82a9c5636f70de
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74778740"
---
# <a name="access-httpcontext-in-aspnet-core"></a>ASP.NET Core で HttpContext にアクセスする

ASP.NET Core アプリでは、<xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> インターフェイスと、その既定の実装 <xref:Microsoft.AspNetCore.Http.HttpContextAccessor> を介して `HttpContext` にアクセスします。 `IHttpContextAccessor` を使用する必要があるのは、サービス内の `HttpContext` にアクセスする必要がある場合のみです。

## <a name="use-httpcontext-from-razor-pages"></a>Razor Pages から HttpContext を使用する

Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> では、<xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> プロパティが公開されます。

```csharp
public class AboutModel : PageModel
{
    public string Message { get; set; }

    public void OnGet()
    {
        Message = HttpContext.Request.PathBase;
    }
}
```

## <a name="use-httpcontext-from-a-razor-view"></a>Razor ビューから HttpContext を使用する

Razor ビューでは、[RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) プロパティを使用して、ビューに直接 `HttpContext` が公開されます。 次の例では、Windows 認証を使用して、イントラネット アプリで現在のユーザー名を取得します。

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a>コントローラーから HttpContext を使用する

コントローラーでは [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) プロパティが公開されます。

```csharp
public class HomeController : Controller
{
    public IActionResult About()
    {
        var pathBase = HttpContext.Request.PathBase;

        ...

        return View();
    }
}
```

## <a name="use-httpcontext-from-middleware"></a>ミドルウェアから HttpContext を使用する

カスタム ミドルウェア コンポーネントを使用する場合、`HttpContext` は `Invoke` メソッドまたは `InvokeAsync` メソッドに渡され、ミドルウェアを構成する際にアクセスできます。

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a>カスタム コンポーネントから HttpContext を使用する

`HttpContext` へのアクセスを必要とするその他のフレームワークおよびカスタム コンポーネントに対して推奨される方法は、組み込みの[依存関係の挿入](xref:fundamentals/dependency-injection)コンテナーを使用して依存関係を登録することです。 依存関係の挿入コンテナーは、それぞれのコンストラクター内で `IHttpContextAccessor` を依存関係として宣言するすべてのクラスに、これを提供します。

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddControllersWithViews();
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddMvc()
         .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

次に例を示します。

* `UserRepository` は `IHttpContextAccessor` に対する依存関係を宣言します。
* 依存関係の挿入で依存関係のチェーンが解決され、`UserRepository` のインスタンスが作成されると、依存関係が提供されます。

```csharp
public class UserRepository : IUserRepository
{
    private readonly IHttpContextAccessor _httpContextAccessor;

    public UserRepository(IHttpContextAccessor httpContextAccessor)
    {
        _httpContextAccessor = httpContextAccessor;
    }

    public void LogCurrentUser()
    {
        var username = _httpContextAccessor.HttpContext.User.Identity.Name;
        service.LogAccessRequest(username);
    }
}
```

## <a name="httpcontext-access-from-a-background-thread"></a>バックグラウンド スレッドから HttpContext にアクセスする

`HttpContext` はスレッド セーフではありません。 要求の処理以外で `HttpContext` のプロパティを読み書きすると、結果的に <xref:System.NullReferenceException> になることがあります。

> [!NOTE]
> アプリで `NullReferenceException` エラーが散発的に生成される場合、コードの中で、バックグラウンド処理を開始する部分や要求完了後に処理を続行する部分を見直してください。 コントローラー メソッドを `async void` として定義するなどの間違いを探します。

`HttpContext` データでバックグラウンド作業を安全に実行するには:

* 要求処理中に必要なデータをコピーします。
* コピーしたデータをバックグラウンド タスクに渡します。

アンセーフ コードを避けるために、バックグラウンド処理を実行しないメソッドには `HttpContext` を決して渡さないでください。 代わりに必要なデータを渡してください。 次の例では、電子メールの送信を開始するために `SendEmailCore` が呼び出されます。 `correlationId` は、`HttpContext` ではなく `SendEmailCore` に渡されます。 コードの実行では、`SendEmailCore` が完了するのを待機しません。

```csharp
public class EmailController : Controller
{
    public IActionResult SendEmail(string email)
    {
        var correlationId = HttpContext.Request.Headers["x-correlation-id"].ToString();

        _ = SendEmailCore(correlationId);

        return View();
    }

    private async Task SendEmailCore(string correlationId)
    {
        ...
    }
}
