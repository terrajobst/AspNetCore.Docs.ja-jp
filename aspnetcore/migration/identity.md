---
title: 認証と Id を ASP.NET Core に移行する
author: ardalis
description: ASP.NET MVC プロジェクトから ASP.NET Core MVC プロジェクトに認証と id を移行する方法について説明します。
ms.author: riande
ms.date: 10/14/2016
uid: migration/identity
ms.openlocfilehash: f821930dbd36de18db31104cddf34c563009a506
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022268"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core"></a><span data-ttu-id="f9a7e-103">認証と Id を ASP.NET Core に移行する</span><span class="sxs-lookup"><span data-stu-id="f9a7e-103">Migrate Authentication and Identity to ASP.NET Core</span></span>

<span data-ttu-id="f9a7e-104">作成者: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="f9a7e-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="f9a7e-105">前の記事では、 [ASP.NET mvc プロジェクトから ASP.NET CORE mvc に構成を移行](xref:migration/configuration)しています。</span><span class="sxs-lookup"><span data-stu-id="f9a7e-105">In the previous article, we [migrated configuration from an ASP.NET MVC project to ASP.NET Core MVC](xref:migration/configuration).</span></span> <span data-ttu-id="f9a7e-106">この記事では、登録、ログイン、およびユーザー管理機能を移行します。</span><span class="sxs-lookup"><span data-stu-id="f9a7e-106">In this article, we migrate the registration, login, and user management features.</span></span>

## <a name="configure-identity-and-membership"></a><span data-ttu-id="f9a7e-107">Id とメンバーシップを構成する</span><span class="sxs-lookup"><span data-stu-id="f9a7e-107">Configure Identity and Membership</span></span>

<span data-ttu-id="f9a7e-108">ASP.NET MVC では、 *Startup.Auth.cs*および*IdentityConfig.cs*の*App_Start*フォルダーにある ASP.NET Identity を使用して、認証と id の機能が構成されます。</span><span class="sxs-lookup"><span data-stu-id="f9a7e-108">In ASP.NET MVC, authentication and identity features are configured using ASP.NET Identity in *Startup.Auth.cs* and *IdentityConfig.cs*, located in the *App_Start* folder.</span></span> <span data-ttu-id="f9a7e-109">ASP.NET Core MVC では、これらの機能は*Startup.cs*で構成されています。</span><span class="sxs-lookup"><span data-stu-id="f9a7e-109">In ASP.NET Core MVC, these features are configured in *Startup.cs*.</span></span>

<span data-ttu-id="f9a7e-110">`Microsoft.AspNetCore.Identity.EntityFrameworkCore` および`Microsoft.AspNetCore.Authentication.Cookies` NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="f9a7e-110">Install the `Microsoft.AspNetCore.Identity.EntityFrameworkCore` and `Microsoft.AspNetCore.Authentication.Cookies` NuGet packages.</span></span>

<span data-ttu-id="f9a7e-111">次に、 *Startup.cs*を開き、 `Startup.ConfigureServices` Entity Framework および id サービスを使用するようにメソッドを更新します。</span><span class="sxs-lookup"><span data-stu-id="f9a7e-111">Then, open *Startup.cs* and update the `Startup.ConfigureServices` method to use Entity Framework and Identity services:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add EF services to the services container.
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

     services.AddMvc();
}
```

<span data-ttu-id="f9a7e-112">この時点で、上記のコードでは`ApplicationDbContext` 、 `ApplicationUser`ASP.NET MVC プロジェクトからまだ移行していない2つの型が参照されています。</span><span class="sxs-lookup"><span data-stu-id="f9a7e-112">At this point, there are two types referenced in the above code that we haven't yet migrated from the ASP.NET MVC project: `ApplicationDbContext` and `ApplicationUser`.</span></span> <span data-ttu-id="f9a7e-113">ASP.NET Core プロジェクトに新しい*モデル*フォルダーを作成し、これらの型に対応する2つのクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="f9a7e-113">Create a new *Models* folder in the ASP.NET Core project, and add two classes to it corresponding to these types.</span></span> <span data-ttu-id="f9a7e-114">これらのクラスの ASP.NET MVC バージョンは */Models/IdentityModels.cs*にありますが、移行されたプロジェクトのクラスごとに1つのファイルを使用します。これはより明確であるためです。</span><span class="sxs-lookup"><span data-stu-id="f9a7e-114">You will find the ASP.NET MVC versions of these classes in */Models/IdentityModels.cs*, but we will use one file per class in the migrated project since that's more clear.</span></span>

<span data-ttu-id="f9a7e-115">*ApplicationUser.cs*:</span><span class="sxs-lookup"><span data-stu-id="f9a7e-115">*ApplicationUser.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;

namespace NewMvcProject.Models
{
  public class ApplicationUser : IdentityUser
  {
  }
}
```

<span data-ttu-id="f9a7e-116">*ApplicationDbContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="f9a7e-116">*ApplicationDbContext.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;
using Microsoft.Data.Entity;

namespace NewMvcProject.Models
{
    public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }

        protected override void OnModelCreating(ModelBuilder builder)
        {
            base.OnModelCreating(builder);
            // Customize the ASP.NET Core Identity model and override the defaults if needed.
            // For example, you can rename the ASP.NET Core Identity table names and more.
            // Add your customizations after calling base.OnModelCreating(builder);
        }
    }
}
```

<span data-ttu-id="f9a7e-117">ASP.NET Core MVC Starter Web プロジェクトには、ユーザーまたは`ApplicationDbContext`の多くのカスタマイズが含まれていません。</span><span class="sxs-lookup"><span data-stu-id="f9a7e-117">The ASP.NET Core MVC Starter Web project doesn't include much customization of users, or the `ApplicationDbContext`.</span></span> <span data-ttu-id="f9a7e-118">実際のアプリを移行する場合は、アプリのユーザーと`DbContext`クラスのすべてのカスタムプロパティとメソッド、およびアプリが使用するその他のモデルクラスも移行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f9a7e-118">When migrating a real app, you also need to migrate all of the custom properties and methods of your app's user and `DbContext` classes, as well as any other Model classes your app utilizes.</span></span> <span data-ttu-id="f9a7e-119">たとえば、に`DbContext` `DbSet<Album>`がある場合は、 `Album`クラスを移行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f9a7e-119">For example, if your `DbContext` has a `DbSet<Album>`, you need to migrate the `Album` class.</span></span>

<span data-ttu-id="f9a7e-120">これらのファイルを配置したら 、 `using`ステートメントを更新して、Startup.cs ファイルをコンパイルすることができます。</span><span class="sxs-lookup"><span data-stu-id="f9a7e-120">With these files in place, the *Startup.cs* file can be made to compile by updating its `using` statements:</span></span>

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
```

<span data-ttu-id="f9a7e-121">これで、アプリは認証と Id サービスをサポートする準備ができました。</span><span class="sxs-lookup"><span data-stu-id="f9a7e-121">Our app is now ready to support authentication and Identity services.</span></span> <span data-ttu-id="f9a7e-122">これらの機能をユーザーに公開する必要があるだけです。</span><span class="sxs-lookup"><span data-stu-id="f9a7e-122">It just needs to have these features exposed to users.</span></span>

## <a name="migrate-registration-and-login-logic"></a><span data-ttu-id="f9a7e-123">登録とログインのロジックを移行する</span><span class="sxs-lookup"><span data-stu-id="f9a7e-123">Migrate registration and login logic</span></span>

<span data-ttu-id="f9a7e-124">Entity Framework と SQL Server を使用して構成されたアプリとデータアクセス用に Id サービスが構成されているので、登録とアプリへのログインのサポートを追加する準備ができました。</span><span class="sxs-lookup"><span data-stu-id="f9a7e-124">With Identity services configured for the app and data access configured using Entity Framework and SQL Server, we're ready to add support for registration and login to the app.</span></span> <span data-ttu-id="f9a7e-125">[移行プロセスでは、前の手順](xref:migration/mvc#migrate-the-layout-file)で説明したように、( *_B*) の*loginpartial*への参照をコメントアウトしています。</span><span class="sxs-lookup"><span data-stu-id="f9a7e-125">Recall that [earlier in the migration process](xref:migration/mvc#migrate-the-layout-file) we commented out a reference to *_LoginPartial* in *_Layout.cshtml*.</span></span> <span data-ttu-id="f9a7e-126">次に、そのコードに戻り、コメントを解除し、ログイン機能をサポートするために必要なコントローラーとビューを追加します。</span><span class="sxs-lookup"><span data-stu-id="f9a7e-126">Now it's time to return to that code, uncomment it, and add in the necessary controllers and views to support login functionality.</span></span>

<span data-ttu-id="f9a7e-127">レイアウト内`@Html.Partial`の行のコメントを解除します *(_s)* :</span><span class="sxs-lookup"><span data-stu-id="f9a7e-127">Uncomment the `@Html.Partial` line in *_Layout.cshtml*:</span></span>

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

<span data-ttu-id="f9a7e-128">ここで、 *Views/Shared*フォルダーに*loginpartial*という名前の新しい Razor ビューを追加します。</span><span class="sxs-lookup"><span data-stu-id="f9a7e-128">Now, add a new Razor view called *_LoginPartial* to the *Views/Shared* folder:</span></span>

<span data-ttu-id="f9a7e-129">次のコードを使用して*Loginpartial*を更新します (すべての内容を置き換えます)。</span><span class="sxs-lookup"><span data-stu-id="f9a7e-129">Update *_LoginPartial.cshtml* with the following code (replace all of its contents):</span></span>

```cshtml
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager

@if (SignInManager.IsSignedIn(User))
{
    <form asp-area="" asp-controller="Account" asp-action="Logout" method="post" id="logoutForm" class="navbar-right">
        <ul class="nav navbar-nav navbar-right">
            <li>
                <a asp-area="" asp-controller="Manage" asp-action="Index" title="Manage">Hello @UserManager.GetUserName(User)!</a>
            </li>
            <li>
                <button type="submit" class="btn btn-link navbar-btn navbar-link">Log out</button>
            </li>
        </ul>
    </form>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="" asp-controller="Account" asp-action="Register">Register</a></li>
        <li><a asp-area="" asp-controller="Account" asp-action="Login">Log in</a></li>
    </ul>
}
```

<span data-ttu-id="f9a7e-130">この時点で、ブラウザーでサイトを最新の状態に更新できるようになります。</span><span class="sxs-lookup"><span data-stu-id="f9a7e-130">At this point, you should be able to refresh the site in your browser.</span></span>

## <a name="summary"></a><span data-ttu-id="f9a7e-131">まとめ</span><span class="sxs-lookup"><span data-stu-id="f9a7e-131">Summary</span></span>

<span data-ttu-id="f9a7e-132">ASP.NET Core では、ASP.NET Identity 機能の変更について説明します。</span><span class="sxs-lookup"><span data-stu-id="f9a7e-132">ASP.NET Core introduces changes to the ASP.NET Identity features.</span></span> <span data-ttu-id="f9a7e-133">この記事では、ASP.NET Identity の認証およびユーザー管理機能を ASP.NET Core に移行する方法について説明しました。</span><span class="sxs-lookup"><span data-stu-id="f9a7e-133">In this article, you have seen how to migrate the authentication and user management features of ASP.NET Identity to ASP.NET Core.</span></span>
