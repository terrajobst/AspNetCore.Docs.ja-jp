---
title: ASP.NET Core プロジェクトにおけるIdentityのスキャフォールディング
author: rick-anderson
description: ASP.NET Core プロジェクトで Id をスキャフォールディングする方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/15/2020
uid: security/authentication/scaffold-identity
ms.openlocfilehash: b3e077aeac11e62d9e992884100476f7be35b59a
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653714"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a>ASP.NET Core プロジェクトにおけるIdentityのスキャフォールディング

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core は、 [ASP.NET Core id](xref:security/authentication/identity)を[Razor クラスライブラリ](xref:razor-pages/ui-class)として提供します。 Id を含むアプリケーションでは、scaffolder を適用して、Id Razor クラスライブラリ (RCL) に含まれているソースコードを選択的に追加することができます。 コードを変更して動作を変更できるように、ソース コードを生成できます。 たとえば、登録で使用するコードを生成するようにスキャフォルダーに指示できます。 生成されたコードは、Identity RCL の同じコードよりも優先されます。 UI を完全に制御し、既定の RCL を使用しないようにするには、「[完全な ID UI ソースの作成](#full)」セクションを参照してください。

認証を含ま**ない**アプリケーションでは、scaffolder を適用して Rcl id パッケージを追加できます。 生成される Identity コードの選択オプションがあります。

Scaffolder は、必要なコードの大部分を生成しますが、プロセスを完了するにはプロジェクトを更新する必要があります。 このドキュメントでは、Id スキャフォールディングの更新を完了するために必要な手順について説明します。

ファイルの違いを示すソース管理システムを使用して、変更を元に戻すことをお勧めします。 Id scaffolder を実行した後に変更を確認します。

サービスは、 [2 要素認証](xref:security/authentication/identity-enable-qrcodes)、アカウントの[確認とパスワードの回復](xref:security/authentication/accconfirm)、および id を持つその他のセキュリティ機能を使用する場合に必要です。 サービスまたはサービススタブは、スキャフォールディング Id では生成されません。 これらの機能を有効にするサービスは、手動で追加する必要があります。 たとえば、「[電子メールの確認を要求する](xref:security/authentication/accconfirm#require-email-confirmation)」を参照してください。

このドキュメントには、scaffolder の実行時に生成される*ScaffoldingReadme*ファイルよりも完全な手順が含まれています。

## <a name="scaffold-identity-into-an-empty-project"></a>空のプロジェクトに対するidentityのスキャフォールディング

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

次のようなコードを使用して、`Startup` クラスを更新します。

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>既存の承認なしのRazorプロジェクトに対するidentityのスキャフォールディング

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add CreateIdentitySchema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Id は、 *Areas/identity/IdentityHostingStartup*で構成されます。 詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>マイグレーション、UseAuthentication、およびレイアウト

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>認証を有効にする

次のようなコードを使用して、`Startup` クラスを更新します。

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>レイアウトの変更

省略可能: 次のように、ログイン部分 (`_LoginPartial`) をレイアウトファイルに追加します。

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>承認ありの Razor プロジェクトに対する identity のスキャフォールディング

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
一部の Id オプションは、 *Areas/Identity/IdentityHostingStartup*で構成されています。 詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>既存の承認なしの MVC プロジェクトに対する identity のスキャフォールディング

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

省略可能: *Views/Shared/_Layout cshtml*ファイルにログイン部分 (`_LoginPartial`) を追加します。

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* *Pages/shared/_LoginPartial cshtml*ファイルを*Views/shared/_LoginPartial*に移動します。

Id は、 *Areas/identity/IdentityHostingStartup*で構成されます。 詳細については、「IHostingStartup」を参照してください。

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

次のようなコードを使用して、`Startup` クラスを更新します。

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>承認ありの MVC プロジェクトに対する identity のスキャフォールディング

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>identity の全ての UI のソースの作成

Id UI の完全な制御を維持するには、Identity scaffolder を実行し、[**すべてのファイルを上書き**する] を選択します。

次の強調表示されたコードは、既定の Id UI を ASP.NET Core 2.1 web アプリの Id に置き換える変更を示しています。 これは、Id UI を完全に制御するために必要になる場合があります。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

既定の Id は、次のコードで置き換えられます。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

次のコードでは、 [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)、 [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)、および[AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)を設定しています。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

`IEmailSender` の実装を登録します。次に例を示します。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a>登録の無効化ページ

ユーザー登録を無効にするには:

* スキャフォールディング Id。 Account、Account. Login、および Account. RegisterConfirmation を含めます。 例 :

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* ユーザーがこのエンドポイントから登録できないように、*区分/id/ページ/アカウント/登録*を更新します。

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* 前の変更との一貫性を保つために、*区分/id/ページ/アカウント/登録*を更新します。

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* *区分/id/ページ/アカウント/ログイン. cshtml*から登録リンクをコメントアウトするか削除します。

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* [*区分/id/ページ/アカウント/RegisterConfirmation 確認*] ページを更新します。

  * コードとリンクを cshtml ファイルから削除します。
  * `PageModel`から確認コードを削除します。

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a>別のアプリを使用してユーザーを追加する

Web アプリの外部にユーザーを追加するためのメカニズムを提供します。 ユーザーを追加するためのオプションは次のとおりです。

* 専用管理者 web アプリ。
* コンソールアプリ。

次のコードは、ユーザーを追加する方法の概要を示しています。

* ユーザーの一覧がメモリに読み込まれます。
* ユーザーごとに一意の強力なパスワードが生成されます。
* ユーザーが Id データベースに追加されます。
* ユーザーに通知され、パスワードを変更するように指示されます。

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

次のコードは、ユーザーの追加の概要を示しています。

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

同様の方法で、運用環境のシナリオにも対応できます。

## <a name="prevent-publish-of-static-identity-assets"></a>静的 Id 資産の発行を禁止する

静的な ID 資産が Web ルートに公開されないようにするには、<xref:security/authentication/identity#prevent-publish-of-static-identity-assets> を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [ASP.NET Core 2.1 以降に認証コードが変更された](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 2.1 以降では、 [ASP.NET Core id](xref:security/authentication/identity)を[Razor クラスライブラリ](xref:razor-pages/ui-class)として提供します。 Id を含むアプリケーションでは、scaffolder を適用して、Id Razor クラスライブラリ (RCL) に含まれているソースコードを選択的に追加することができます。 コードを変更して動作を変更できるように、ソース コードを生成できます。 たとえば、登録で使用するコードを生成するようにスキャフォルダーに指示できます。 生成されたコードは、Identity RCL の同じコードよりも優先されます。 UI を完全に制御し、既定の RCL を使用しないようにするには、「[完全な ID UI ソースの作成](#full)」セクションを参照してください。

認証を含ま**ない**アプリケーションでは、scaffolder を適用して Rcl id パッケージを追加できます。 生成される Identity コードの選択オプションがあります。

Scaffolder は、必要なコードの大部分を生成しますが、プロセスを完了するにはプロジェクトを更新する必要があります。 このドキュメントでは、Id スキャフォールディングの更新を完了するために必要な手順について説明します。

Id scaffolder を実行すると、 *ScaffoldingReadme*ファイルがプロジェクトディレクトリに作成されます。 *ScaffoldingReadme*ファイルには、id スキャフォールディングの更新を完了するために必要な手順に関する一般的な指示が含まれています。 このドキュメントには、 *ScaffoldingReadme*ファイルよりも完全な手順が含まれています。

ファイルの違いを示すソース管理システムを使用して、変更を元に戻すことをお勧めします。 Id scaffolder を実行した後に変更を確認します。

> [!NOTE]
> サービスは、 [2 要素認証](xref:security/authentication/identity-enable-qrcodes)、アカウントの[確認とパスワードの回復](xref:security/authentication/accconfirm)、および id を持つその他のセキュリティ機能を使用する場合に必要です。 サービスまたはサービススタブは、スキャフォールディング Id では生成されません。 これらの機能を有効にするサービスは、手動で追加する必要があります。 たとえば、「[電子メールの確認を要求する](xref:security/authentication/accconfirm#require-email-confirmation)」を参照してください。

## <a name="scaffold-identity-into-an-empty-project"></a>空のプロジェクトに対するidentityのスキャフォールディング

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

次の強調表示されている呼び出しを `Startup` クラスに追加します。

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>既存の承認なしのRazorプロジェクトに対するidentityのスキャフォールディング

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Id は、 *Areas/identity/IdentityHostingStartup*で構成されます。 詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>マイグレーション、UseAuthentication、およびレイアウト

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>認証を有効にする

`Startup` クラスの `Configure` メソッドで、`UseStaticFiles`後に[Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)を呼び出します。

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>レイアウトの変更

省略可能: 次のように、ログイン部分 (`_LoginPartial`) をレイアウトファイルに追加します。

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>承認ありの Razor プロジェクトに対する identity のスキャフォールディング

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
一部の Id オプションは、 *Areas/Identity/IdentityHostingStartup*で構成されています。 詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>既存の承認なしの MVC プロジェクトに対する identity のスキャフォールディング

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

省略可能: *Views/Shared/_Layout cshtml*ファイルにログイン部分 (`_LoginPartial`) を追加します。

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* *Pages/shared/_LoginPartial cshtml*ファイルを*Views/shared/_LoginPartial*に移動します。

Id は、 *Areas/identity/IdentityHostingStartup*で構成されます。 詳細については、「IHostingStartup」を参照してください。

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

`UseStaticFiles`後に[Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)を呼び出します。

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>承認ありの MVC プロジェクトに対する identity のスキャフォールディング

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

*ページ/共有*フォルダーとそのフォルダー内のファイルを削除します。

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>identity の全ての UI のソースの作成

Id UI の完全な制御を維持するには、Identity scaffolder を実行し、[**すべてのファイルを上書き**する] を選択します。

次の強調表示されたコードは、既定の Id UI を ASP.NET Core 2.1 web アプリの Id に置き換える変更を示しています。 これは、Id UI を完全に制御するために必要になる場合があります。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

既定の Id は、次のコードで置き換えられます。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

次のコードでは、 [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)、 [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)、および[AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)を設定しています。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

`IEmailSender` の実装を登録します。次に例を示します。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a>登録の無効化ページ

ユーザー登録を無効にするには:

* スキャフォールディング Id。 Account、Account. Login、および Account. RegisterConfirmation を含めます。 例 :

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* ユーザーがこのエンドポイントから登録できないように、*区分/id/ページ/アカウント/登録*を更新します。

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* 前の変更との一貫性を保つために、*区分/id/ページ/アカウント/登録*を更新します。

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* *区分/id/ページ/アカウント/ログイン. cshtml*から登録リンクをコメントアウトするか削除します。

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* [*区分/id/ページ/アカウント/RegisterConfirmation 確認*] ページを更新します。

  * コードとリンクを cshtml ファイルから削除します。
  * `PageModel`から確認コードを削除します。

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a>別のアプリを使用してユーザーを追加する

Web アプリの外部にユーザーを追加するためのメカニズムを提供します。 ユーザーを追加するためのオプションは次のとおりです。

* 専用管理者 web アプリ。
* コンソールアプリ。

次のコードは、ユーザーを追加する方法の概要を示しています。

* ユーザーの一覧がメモリに読み込まれます。
* ユーザーごとに一意の強力なパスワードが生成されます。
* ユーザーが Id データベースに追加されます。
* ユーザーに通知され、パスワードを変更するように指示されます。

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

次のコードは、ユーザーの追加の概要を示しています。

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

同様の方法で、運用環境のシナリオにも対応できます。

## <a name="additional-resources"></a>その他のリソース

* [ASP.NET Core 2.1 以降に認証コードが変更された](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end