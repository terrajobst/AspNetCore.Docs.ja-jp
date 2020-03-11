---
title: 認証と Id を ASP.NET Core に移行する
author: ardalis
description: ASP.NET MVC プロジェクトから ASP.NET Core MVC プロジェクトに認証と id を移行する方法について説明します。
ms.author: riande
ms.date: 10/14/2016
uid: migration/identity
ms.openlocfilehash: f821930dbd36de18db31104cddf34c563009a506
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653012"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core"></a>認証と Id を ASP.NET Core に移行する

作成者: [Steve Smith](https://ardalis.com/)

前の記事では、 [ASP.NET mvc プロジェクトから ASP.NET CORE mvc に構成を移行](xref:migration/configuration)しています。 この記事では、登録、ログイン、およびユーザー管理機能を移行します。

## <a name="configure-identity-and-membership"></a>Id とメンバーシップを構成する

ASP.NET MVC では、 *App_Start*フォルダーにある*Startup.Auth.cs*と*IdentityConfig.cs*の ASP.NET Identity を使用して認証と id の機能が構成されます。 ASP.NET Core MVC では、これらの機能は*Startup.cs*で構成されています。

`Microsoft.AspNetCore.Identity.EntityFrameworkCore` と `Microsoft.AspNetCore.Authentication.Cookies` NuGet パッケージをインストールします。

次に、 *Startup.cs*を開き、Entity Framework と id サービスを使用するように `Startup.ConfigureServices` メソッドを更新します。

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

この時点で、上記のコードで参照されている2つの型は、ASP.NET MVC プロジェクトからまだ移行していません。 `ApplicationDbContext` と `ApplicationUser`です。 ASP.NET Core プロジェクトに新しい*モデル*フォルダーを作成し、これらの型に対応する2つのクラスを追加します。 これらのクラスの ASP.NET MVC バージョンは */Models/IdentityModels.cs*にありますが、移行されたプロジェクトのクラスごとに1つのファイルを使用します。これはより明確であるためです。

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

ASP.NET Core MVC Starter Web プロジェクトには、ユーザーの多くのカスタマイズや `ApplicationDbContext`は含まれていません。 実際のアプリを移行する場合は、アプリのユーザーと `DbContext` クラスのすべてのカスタムプロパティとメソッド、およびアプリが使用するその他のモデルクラスも移行する必要があります。 たとえば、`DbContext` に `DbSet<Album>`がある場合、`Album` クラスを移行する必要があります。

これらのファイルが配置されているので、`using` ステートメントを更新することにより、 *Startup.cs*ファイルをコンパイルできるようになります。

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

Entity Framework と SQL Server を使用して構成されたアプリとデータアクセス用に Id サービスが構成されているので、登録とアプリへのログインのサポートを追加する準備ができました。 [移行プロセスの前半](xref:migration/mvc#migrate-the-layout-file)で、 *_Layout*の *_LoginPartial*への参照がコメントアウトされていることを思い出してください。 次に、そのコードに戻り、コメントを解除し、ログイン機能をサポートするために必要なコントローラーとビューを追加します。

_Layout の `@Html.Partial` 行のコメントを解除*します*。

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

ここで、 *_LoginPartial*という名前の新しい Razor ビューを*ビュー/共有*フォルダーに追加します。

次のコードを使用して _LoginPartial を更新*します*(すべての内容を置き換えます)。

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

## <a name="summary"></a>要約

ASP.NET Core では、ASP.NET Identity 機能の変更について説明します。 この記事では、ASP.NET Identity の認証およびユーザー管理機能を ASP.NET Core に移行する方法について説明しました。
