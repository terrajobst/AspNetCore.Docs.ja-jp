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
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78647012"
---
# <a name="access-httpcontext-in-aspnet-core"></a><span data-ttu-id="e8fef-103">ASP.NET Core で HttpContext にアクセスする</span><span class="sxs-lookup"><span data-stu-id="e8fef-103">Access HttpContext in ASP.NET Core</span></span>

<span data-ttu-id="e8fef-104">ASP.NET Core アプリでは、`HttpContext` インターフェイスと、その既定の実装 <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> を介して <xref:Microsoft.AspNetCore.Http.HttpContextAccessor> にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="e8fef-104">ASP.NET Core apps access `HttpContext` through the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> interface and its default implementation <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>.</span></span> <span data-ttu-id="e8fef-105">`IHttpContextAccessor` を使用する必要があるのは、サービス内の `HttpContext` にアクセスする必要がある場合のみです。</span><span class="sxs-lookup"><span data-stu-id="e8fef-105">It's only necessary to use `IHttpContextAccessor` when you need access to the `HttpContext` inside a service.</span></span>

## <a name="use-httpcontext-from-razor-pages"></a><span data-ttu-id="e8fef-106">Razor Pages から HttpContext を使用する</span><span class="sxs-lookup"><span data-stu-id="e8fef-106">Use HttpContext from Razor Pages</span></span>

<span data-ttu-id="e8fef-107">Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> では、<xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> プロパティが公開されます。</span><span class="sxs-lookup"><span data-stu-id="e8fef-107">The Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> exposes the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> property:</span></span>

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

## <a name="use-httpcontext-from-a-razor-view"></a><span data-ttu-id="e8fef-108">Razor ビューから HttpContext を使用する</span><span class="sxs-lookup"><span data-stu-id="e8fef-108">Use HttpContext from a Razor view</span></span>

<span data-ttu-id="e8fef-109">Razor ビューでは、`HttpContext`RazorPage.Context[ プロパティを使用して、ビューに直接 ](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) が公開されます。</span><span class="sxs-lookup"><span data-stu-id="e8fef-109">Razor views expose the `HttpContext` directly via a [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) property on the view.</span></span> <span data-ttu-id="e8fef-110">次の例では、Windows 認証を使用して、イントラネット アプリで現在のユーザー名を取得します。</span><span class="sxs-lookup"><span data-stu-id="e8fef-110">The following example retrieves the current username in an intranet app using Windows Authentication:</span></span>

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a><span data-ttu-id="e8fef-111">コントローラーから HttpContext を使用する</span><span class="sxs-lookup"><span data-stu-id="e8fef-111">Use HttpContext from a controller</span></span>

<span data-ttu-id="e8fef-112">コントローラーでは [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) プロパティが公開されます。</span><span class="sxs-lookup"><span data-stu-id="e8fef-112">Controllers expose the [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) property:</span></span>

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

## <a name="use-httpcontext-from-middleware"></a><span data-ttu-id="e8fef-113">ミドルウェアから HttpContext を使用する</span><span class="sxs-lookup"><span data-stu-id="e8fef-113">Use HttpContext from middleware</span></span>

<span data-ttu-id="e8fef-114">カスタム ミドルウェア コンポーネントを使用する場合、`HttpContext` は `Invoke` メソッドまたは `InvokeAsync` メソッドに渡され、ミドルウェアを構成する際にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="e8fef-114">When working with custom middleware components, `HttpContext` is passed into the `Invoke` or `InvokeAsync` method and can be accessed when the middleware is configured:</span></span>

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a><span data-ttu-id="e8fef-115">カスタム コンポーネントから HttpContext を使用する</span><span class="sxs-lookup"><span data-stu-id="e8fef-115">Use HttpContext from custom components</span></span>

<span data-ttu-id="e8fef-116">`HttpContext` へのアクセスを必要とするその他のフレームワークおよびカスタム コンポーネントに対して推奨される方法は、組み込みの[依存関係の挿入](xref:fundamentals/dependency-injection)コンテナーを使用して依存関係を登録することです。</span><span class="sxs-lookup"><span data-stu-id="e8fef-116">For other framework and custom components that require access to `HttpContext`, the recommended approach is to register a dependency using the built-in [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="e8fef-117">依存関係の挿入コンテナーは、それぞれのコンストラクター内で `IHttpContextAccessor` を依存関係として宣言するすべてのクラスに、これを提供します。</span><span class="sxs-lookup"><span data-stu-id="e8fef-117">The dependency injection container supplies the `IHttpContextAccessor` to any classes that declare it as a dependency in their constructors:</span></span>

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

<span data-ttu-id="e8fef-118">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e8fef-118">In the following example:</span></span>

* <span data-ttu-id="e8fef-119">`UserRepository` は `IHttpContextAccessor` に対する依存関係を宣言します。</span><span class="sxs-lookup"><span data-stu-id="e8fef-119">`UserRepository` declares its dependency on `IHttpContextAccessor`.</span></span>
* <span data-ttu-id="e8fef-120">依存関係の挿入で依存関係のチェーンが解決され、`UserRepository` のインスタンスが作成されると、依存関係が提供されます。</span><span class="sxs-lookup"><span data-stu-id="e8fef-120">The dependency is supplied when dependency injection resolves the dependency chain and creates an instance of `UserRepository`.</span></span>

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

## <a name="httpcontext-access-from-a-background-thread"></a><span data-ttu-id="e8fef-121">バックグラウンド スレッドから HttpContext にアクセスする</span><span class="sxs-lookup"><span data-stu-id="e8fef-121">HttpContext access from a background thread</span></span>

<span data-ttu-id="e8fef-122">`HttpContext` はスレッド セーフではありません。</span><span class="sxs-lookup"><span data-stu-id="e8fef-122">`HttpContext` isn't thread-safe.</span></span> <span data-ttu-id="e8fef-123">要求の処理以外で `HttpContext` のプロパティを読み書きすると、結果的に <xref:System.NullReferenceException> になることがあります。</span><span class="sxs-lookup"><span data-stu-id="e8fef-123">Reading or writing properties of the `HttpContext` outside of processing a request can result in a <xref:System.NullReferenceException>.</span></span>

> [!NOTE]
> <span data-ttu-id="e8fef-124">アプリで `NullReferenceException` エラーが散発的に生成される場合、コードの中で、バックグラウンド処理を開始する部分や要求完了後に処理を続行する部分を見直してください。</span><span class="sxs-lookup"><span data-stu-id="e8fef-124">If your app generates sporadic `NullReferenceException` errors, review parts of the code that start background processing or that continue processing after a request completes.</span></span> <span data-ttu-id="e8fef-125">コントローラー メソッドを `async void` として定義するなどの間違いを探します。</span><span class="sxs-lookup"><span data-stu-id="e8fef-125">Look for mistakes, such as defining a controller method as `async void`.</span></span>

<span data-ttu-id="e8fef-126">`HttpContext` データでバックグラウンド作業を安全に実行するには:</span><span class="sxs-lookup"><span data-stu-id="e8fef-126">To safely perform background work with `HttpContext` data:</span></span>

* <span data-ttu-id="e8fef-127">要求処理中に必要なデータをコピーします。</span><span class="sxs-lookup"><span data-stu-id="e8fef-127">Copy the required data during request processing.</span></span>
* <span data-ttu-id="e8fef-128">コピーしたデータをバックグラウンド タスクに渡します。</span><span class="sxs-lookup"><span data-stu-id="e8fef-128">Pass the copied data to a background task.</span></span>

<span data-ttu-id="e8fef-129">アンセーフ コードを避けるために、バックグラウンド処理を実行しないメソッドには `HttpContext` を決して渡さないでください。</span><span class="sxs-lookup"><span data-stu-id="e8fef-129">To avoid unsafe code, never pass the `HttpContext` into a method that performs background work.</span></span> <span data-ttu-id="e8fef-130">代わりに必要なデータを渡してください。</span><span class="sxs-lookup"><span data-stu-id="e8fef-130">Pass the required data instead.</span></span> <span data-ttu-id="e8fef-131">次の例では、電子メールの送信を開始するために `SendEmailCore` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="e8fef-131">In the following example, `SendEmailCore` is called to start sending an email.</span></span> <span data-ttu-id="e8fef-132">`correlationId` は、`SendEmailCore` ではなく `HttpContext` に渡されます。</span><span class="sxs-lookup"><span data-stu-id="e8fef-132">The `correlationId` is passed to `SendEmailCore`, not the `HttpContext`.</span></span> <span data-ttu-id="e8fef-133">コードの実行では、`SendEmailCore` が完了するのを待機しません。</span><span class="sxs-lookup"><span data-stu-id="e8fef-133">Code execution doesn't wait for `SendEmailCore` to complete:</span></span>

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
