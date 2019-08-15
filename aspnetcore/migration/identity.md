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
# <a name="migrate-authentication-and-identity-to-aspnet-core"></a>認証と Id を ASP.NET Core に移行する

作成者: [Steve Smith](https://ardalis.com/)

前の記事では、 [ASP.NET mvc プロジェクトから ASP.NET CORE mvc に構成を移行](xref:migration/configuration)しています。 この記事では、登録、ログイン、およびユーザー管理機能を移行します。

## <a name="configure-identity-and-membership"></a>Id とメンバーシップを構成する

ASP.NET MVC では、 *Startup.Auth.cs*および*IdentityConfig.cs*の*App_Start*フォルダーにある ASP.NET Identity を使用して、認証と id の機能が構成されます。 ASP.NET Core MVC では、これらの機能は*Startup.cs*で構成されています。

`Microsoft.AspNetCore.Identity.EntityFrameworkCore` および`Microsoft.AspNetCore.Authentication.Cookies` NuGet パッケージをインストールします。

次に、 *Startup.cs*を開き、 `Startup.ConfigureServices` Entity Framework および id サービスを使用するようにメソッドを更新します。

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

この時点で、上記のコードでは`ApplicationDbContext` 、 `ApplicationUser`ASP.NET MVC プロジェクトからまだ移行していない2つの型が参照されています。 ASP.NET Core プロジェクトに新しい*モデル*フォルダーを作成し、これらの型に対応する2つのクラスを追加します。 これらのクラスの ASP.NET MVC バージョンは */Models/IdentityModels.cs*にありますが、移行されたプロジェクトのクラスごとに1つのファイルを使用します。これはより明確であるためです。

*ApplicationUser.cs*:

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;

namespace NewMvcProject.Models
{
  public class ApplicationUser : IdentityUser
  {
  }
}
```

*ApplicationDbContext.cs*:

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

ASP.NET Core MVC Starter Web プロジェクトには、ユーザーまたは`ApplicationDbContext`の多くのカスタマイズが含まれていません。 実際のアプリを移行する場合は、アプリのユーザーと`DbContext`クラスのすべてのカスタムプロパティとメソッド、およびアプリが使用するその他のモデルクラスも移行する必要があります。 たとえば、に`DbContext` `DbSet<Album>`がある場合は、 `Album`クラスを移行する必要があります。

これらのファイルを配置したら 、 `using`ステートメントを更新して、Startup.cs ファイルをコンパイルすることができます。

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
```

これで、アプリは認証と Id サービスをサポートする準備ができました。 これらの機能をユーザーに公開する必要があるだけです。

## <a name="migrate-registration-and-login-logic"></a>登録とログインのロジックを移行する

Entity Framework と SQL Server を使用して構成されたアプリとデータアクセス用に Id サービスが構成されているので、登録とアプリへのログインのサポートを追加する準備ができました。 [移行プロセスでは、前の手順](xref:migration/mvc#migrate-the-layout-file)で説明したように、( *_B*) の*loginpartial*への参照をコメントアウトしています。 次に、そのコードに戻り、コメントを解除し、ログイン機能をサポートするために必要なコントローラーとビューを追加します。

レイアウト内`@Html.Partial`の行のコメントを解除します *(_s)* :

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

ここで、 *Views/Shared*フォルダーに*loginpartial*という名前の新しい Razor ビューを追加します。

次のコードを使用して*Loginpartial*を更新します (すべての内容を置き換えます)。

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

この時点で、ブラウザーでサイトを最新の状態に更新できるようになります。

## <a name="summary"></a>まとめ

ASP.NET Core では、ASP.NET Identity 機能の変更について説明します。 この記事では、ASP.NET Identity の認証およびユーザー管理機能を ASP.NET Core に移行する方法について説明しました。
