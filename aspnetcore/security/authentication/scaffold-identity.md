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
ms.sourcegitcommit: 990a4c2e623c202a27f60bdf3902f250359c13be
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2020
ms.locfileid: "76972042"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="6738b-103">ASP.NET Core プロジェクトにおけるIdentityのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="6738b-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="6738b-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6738b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6738b-105">ASP.NET Core は、 [ASP.NET Core id](xref:security/authentication/identity)を[Razor クラスライブラリ](xref:razor-pages/ui-class)として提供します。</span><span class="sxs-lookup"><span data-stu-id="6738b-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="6738b-106">Id を含むアプリケーションでは、scaffolder を適用して、Id Razor クラスライブラリ (RCL) に含まれているソースコードを選択的に追加することができます。</span><span class="sxs-lookup"><span data-stu-id="6738b-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="6738b-107">コードを変更して動作を変更できるように、ソース コードを生成できます。</span><span class="sxs-lookup"><span data-stu-id="6738b-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="6738b-108">たとえば、登録で使用するコードを生成するようにスキャフォルダーに指示できます。</span><span class="sxs-lookup"><span data-stu-id="6738b-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="6738b-109">生成されたコードは、Identity RCL の同じコードよりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="6738b-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="6738b-110">UI を完全に制御し、既定の RCL を使用しないようにするには、「[完全な ID UI ソースの作成](#full)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6738b-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="6738b-111">認証を含ま**ない**アプリケーションでは、scaffolder を適用して Rcl id パッケージを追加できます。</span><span class="sxs-lookup"><span data-stu-id="6738b-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="6738b-112">生成される Identity コードの選択オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="6738b-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="6738b-113">Scaffolder は、必要なコードの大部分を生成しますが、プロセスを完了するにはプロジェクトを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6738b-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="6738b-114">このドキュメントでは、Id スキャフォールディングの更新を完了するために必要な手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="6738b-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="6738b-115">ファイルの違いを示すソース管理システムを使用して、変更を元に戻すことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="6738b-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="6738b-116">Id scaffolder を実行した後に変更を確認します。</span><span class="sxs-lookup"><span data-stu-id="6738b-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="6738b-117">サービスは、 [2 要素認証](xref:security/authentication/identity-enable-qrcodes)、アカウントの[確認とパスワードの回復](xref:security/authentication/accconfirm)、および id を持つその他のセキュリティ機能を使用する場合に必要です。</span><span class="sxs-lookup"><span data-stu-id="6738b-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="6738b-118">サービスまたはサービススタブは、スキャフォールディング Id では生成されません。</span><span class="sxs-lookup"><span data-stu-id="6738b-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="6738b-119">これらの機能を有効にするサービスは、手動で追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6738b-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="6738b-120">たとえば、「[電子メールの確認を要求する](xref:security/authentication/accconfirm#require-email-confirmation)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6738b-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="6738b-121">このドキュメントには、scaffolder の実行時に生成される*ScaffoldingReadme*ファイルよりも完全な手順が含まれています。</span><span class="sxs-lookup"><span data-stu-id="6738b-121">This document contains more complete instructions than the *ScaffoldingReadme.txt* file which is generated when running the scaffolder.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="6738b-122">空のプロジェクトに対するidentityのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="6738b-122">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="6738b-123">次のようなコードを使用して、`Startup` クラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="6738b-123">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="6738b-124">既存の承認なしのRazorプロジェクトに対するidentityのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="6738b-124">Scaffold identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="6738b-125">Id は、 *Areas/identity/IdentityHostingStartup*で構成されます。</span><span class="sxs-lookup"><span data-stu-id="6738b-125">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="6738b-126">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6738b-126">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="6738b-127">マイグレーション、UseAuthentication、およびレイアウト</span><span class="sxs-lookup"><span data-stu-id="6738b-127">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="6738b-128">認証の有効化</span><span class="sxs-lookup"><span data-stu-id="6738b-128">Enable authentication</span></span>

<span data-ttu-id="6738b-129">次のようなコードを使用して、`Startup` クラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="6738b-129">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="6738b-130">レイアウトの変更</span><span class="sxs-lookup"><span data-stu-id="6738b-130">Layout changes</span></span>

<span data-ttu-id="6738b-131">省略可能: 次のように、ログイン部分 (`_LoginPartial`) をレイアウトファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="6738b-131">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="6738b-132">承認ありの Razor プロジェクトに対する identity のスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="6738b-132">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="6738b-133">一部の Id オプションは、 *Areas/Identity/IdentityHostingStartup*で構成されています。</span><span class="sxs-lookup"><span data-stu-id="6738b-133">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="6738b-134">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6738b-134">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="6738b-135">既存の承認なしの MVC プロジェクトに対する identity のスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="6738b-135">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="6738b-136">省略可能: *Views/Shared/_Layout cshtml*ファイルにログイン部分 (`_LoginPartial`) を追加します。</span><span class="sxs-lookup"><span data-stu-id="6738b-136">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="6738b-137">*Pages/shared/_LoginPartial cshtml*ファイルを*Views/shared/_LoginPartial*に移動します。</span><span class="sxs-lookup"><span data-stu-id="6738b-137">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="6738b-138">Id は、 *Areas/identity/IdentityHostingStartup*で構成されます。</span><span class="sxs-lookup"><span data-stu-id="6738b-138">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="6738b-139">詳細については、「IHostingStartup」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6738b-139">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="6738b-140">次のようなコードを使用して、`Startup` クラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="6738b-140">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="6738b-141">承認ありの MVC プロジェクトに対する identity のスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="6738b-141">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="6738b-142">identity の全ての UI のソースの作成</span><span class="sxs-lookup"><span data-stu-id="6738b-142">Create full identity UI source</span></span>

<span data-ttu-id="6738b-143">Id UI の完全な制御を維持するには、Identity scaffolder を実行し、[**すべてのファイルを上書き**する] を選択します。</span><span class="sxs-lookup"><span data-stu-id="6738b-143">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="6738b-144">次の強調表示されたコードは、既定の Id UI を ASP.NET Core 2.1 web アプリの Id に置き換える変更を示しています。</span><span class="sxs-lookup"><span data-stu-id="6738b-144">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="6738b-145">これは、Id UI を完全に制御するために必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="6738b-145">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="6738b-146">既定の Id は、次のコードで置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="6738b-146">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="6738b-147">次のコードでは、 [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)、 [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)、および[AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)を設定しています。</span><span class="sxs-lookup"><span data-stu-id="6738b-147">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="6738b-148">`IEmailSender` の実装を登録します。次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="6738b-148">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="6738b-149">登録の無効化ページ</span><span class="sxs-lookup"><span data-stu-id="6738b-149">Disable register page</span></span>

<span data-ttu-id="6738b-150">ユーザー登録を無効にするには:</span><span class="sxs-lookup"><span data-stu-id="6738b-150">To disable user registration:</span></span>

* <span data-ttu-id="6738b-151">スキャフォールディング Id。</span><span class="sxs-lookup"><span data-stu-id="6738b-151">Scaffold Identity.</span></span> <span data-ttu-id="6738b-152">Account、Account. Login、および Account. RegisterConfirmation を含めます。</span><span class="sxs-lookup"><span data-stu-id="6738b-152">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="6738b-153">例:</span><span class="sxs-lookup"><span data-stu-id="6738b-153">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="6738b-154">ユーザーがこのエンドポイントから登録できないように、*区分/id/ページ/アカウント/登録*を更新します。</span><span class="sxs-lookup"><span data-stu-id="6738b-154">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="6738b-155">前の変更との一貫性を保つために、*区分/id/ページ/アカウント/登録*を更新します。</span><span class="sxs-lookup"><span data-stu-id="6738b-155">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="6738b-156">*区分/id/ページ/アカウント/ログイン. cshtml*から登録リンクをコメントアウトするか削除します。</span><span class="sxs-lookup"><span data-stu-id="6738b-156">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="6738b-157">[*区分/id/ページ/アカウント/RegisterConfirmation 確認*] ページを更新します。</span><span class="sxs-lookup"><span data-stu-id="6738b-157">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="6738b-158">コードとリンクを cshtml ファイルから削除します。</span><span class="sxs-lookup"><span data-stu-id="6738b-158">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="6738b-159">`PageModel`から確認コードを削除します。</span><span class="sxs-lookup"><span data-stu-id="6738b-159">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="6738b-160">別のアプリを使用してユーザーを追加する</span><span class="sxs-lookup"><span data-stu-id="6738b-160">Use another app to add users</span></span>

<span data-ttu-id="6738b-161">Web アプリの外部にユーザーを追加するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="6738b-161">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="6738b-162">ユーザーを追加するためのオプションは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="6738b-162">Options to add users include:</span></span>

* <span data-ttu-id="6738b-163">専用管理者 web アプリ。</span><span class="sxs-lookup"><span data-stu-id="6738b-163">A dedicated admin web app.</span></span>
* <span data-ttu-id="6738b-164">コンソールアプリ。</span><span class="sxs-lookup"><span data-stu-id="6738b-164">A console app.</span></span>

<span data-ttu-id="6738b-165">次のコードは、ユーザーを追加する方法の概要を示しています。</span><span class="sxs-lookup"><span data-stu-id="6738b-165">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="6738b-166">ユーザーの一覧がメモリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="6738b-166">A list of users is read into memory.</span></span>
* <span data-ttu-id="6738b-167">ユーザーごとに一意の強力なパスワードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="6738b-167">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="6738b-168">ユーザーが Id データベースに追加されます。</span><span class="sxs-lookup"><span data-stu-id="6738b-168">The user is added to the Identity database.</span></span>
* <span data-ttu-id="6738b-169">ユーザーに通知され、パスワードを変更するように指示されます。</span><span class="sxs-lookup"><span data-stu-id="6738b-169">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="6738b-170">次のコードは、ユーザーの追加の概要を示しています。</span><span class="sxs-lookup"><span data-stu-id="6738b-170">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="6738b-171">同様の方法で、運用環境のシナリオにも対応できます。</span><span class="sxs-lookup"><span data-stu-id="6738b-171">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="6738b-172">静的 Id 資産の発行を禁止する</span><span class="sxs-lookup"><span data-stu-id="6738b-172">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="6738b-173">静的な ID 資産が Web ルートに公開されないようにするには、<xref:security/authentication/identity#prevent-publish-of-static-identity-assets> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6738b-173">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6738b-174">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="6738b-174">Additional resources</span></span>

* [<span data-ttu-id="6738b-175">ASP.NET Core 2.1 以降に認証コードが変更された</span><span class="sxs-lookup"><span data-stu-id="6738b-175">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6738b-176">ASP.NET Core 2.1 以降では、 [ASP.NET Core id](xref:security/authentication/identity)を[Razor クラスライブラリ](xref:razor-pages/ui-class)として提供します。</span><span class="sxs-lookup"><span data-stu-id="6738b-176">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="6738b-177">Id を含むアプリケーションでは、scaffolder を適用して、Id Razor クラスライブラリ (RCL) に含まれているソースコードを選択的に追加することができます。</span><span class="sxs-lookup"><span data-stu-id="6738b-177">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="6738b-178">コードを変更して動作を変更できるように、ソース コードを生成できます。</span><span class="sxs-lookup"><span data-stu-id="6738b-178">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="6738b-179">たとえば、登録で使用するコードを生成するようにスキャフォルダーに指示できます。</span><span class="sxs-lookup"><span data-stu-id="6738b-179">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="6738b-180">生成されたコードは、Identity RCL の同じコードよりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="6738b-180">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="6738b-181">UI を完全に制御し、既定の RCL を使用しないようにするには、「[完全な ID UI ソースの作成](#full)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6738b-181">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="6738b-182">認証を含ま**ない**アプリケーションでは、scaffolder を適用して Rcl id パッケージを追加できます。</span><span class="sxs-lookup"><span data-stu-id="6738b-182">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="6738b-183">生成される Identity コードの選択オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="6738b-183">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="6738b-184">Scaffolder は、必要なコードの大部分を生成しますが、プロセスを完了するにはプロジェクトを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6738b-184">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="6738b-185">このドキュメントでは、Id スキャフォールディングの更新を完了するために必要な手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="6738b-185">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="6738b-186">Id scaffolder を実行すると、 *ScaffoldingReadme*ファイルがプロジェクトディレクトリに作成されます。</span><span class="sxs-lookup"><span data-stu-id="6738b-186">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="6738b-187">*ScaffoldingReadme*ファイルには、id スキャフォールディングの更新を完了するために必要な手順に関する一般的な指示が含まれています。</span><span class="sxs-lookup"><span data-stu-id="6738b-187">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="6738b-188">このドキュメントには、 *ScaffoldingReadme*ファイルよりも完全な手順が含まれています。</span><span class="sxs-lookup"><span data-stu-id="6738b-188">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="6738b-189">ファイルの違いを示すソース管理システムを使用して、変更を元に戻すことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="6738b-189">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="6738b-190">Id scaffolder を実行した後に変更を確認します。</span><span class="sxs-lookup"><span data-stu-id="6738b-190">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="6738b-191">サービスは、 [2 要素認証](xref:security/authentication/identity-enable-qrcodes)、アカウントの[確認とパスワードの回復](xref:security/authentication/accconfirm)、および id を持つその他のセキュリティ機能を使用する場合に必要です。</span><span class="sxs-lookup"><span data-stu-id="6738b-191">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="6738b-192">サービスまたはサービススタブは、スキャフォールディング Id では生成されません。</span><span class="sxs-lookup"><span data-stu-id="6738b-192">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="6738b-193">これらの機能を有効にするサービスは、手動で追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6738b-193">Services to enable these features must be added manually.</span></span> <span data-ttu-id="6738b-194">たとえば、「[電子メールの確認を要求する](xref:security/authentication/accconfirm#require-email-confirmation)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6738b-194">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="6738b-195">空のプロジェクトに対するidentityのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="6738b-195">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="6738b-196">次の強調表示されている呼び出しを `Startup` クラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="6738b-196">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="6738b-197">既存の承認なしのRazorプロジェクトに対するidentityのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="6738b-197">Scaffold identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="6738b-198">Id は、 *Areas/identity/IdentityHostingStartup*で構成されます。</span><span class="sxs-lookup"><span data-stu-id="6738b-198">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="6738b-199">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6738b-199">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="6738b-200">マイグレーション、UseAuthentication、およびレイアウト</span><span class="sxs-lookup"><span data-stu-id="6738b-200">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="6738b-201">認証の有効化</span><span class="sxs-lookup"><span data-stu-id="6738b-201">Enable authentication</span></span>

<span data-ttu-id="6738b-202">`Startup` クラスの `Configure` メソッドで、`UseStaticFiles`後に[Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="6738b-202">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="6738b-203">レイアウトの変更</span><span class="sxs-lookup"><span data-stu-id="6738b-203">Layout changes</span></span>

<span data-ttu-id="6738b-204">省略可能: 次のように、ログイン部分 (`_LoginPartial`) をレイアウトファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="6738b-204">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="6738b-205">承認ありの Razor プロジェクトに対する identity のスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="6738b-205">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="6738b-206">一部の Id オプションは、 *Areas/Identity/IdentityHostingStartup*で構成されています。</span><span class="sxs-lookup"><span data-stu-id="6738b-206">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="6738b-207">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6738b-207">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="6738b-208">既存の承認なしの MVC プロジェクトに対する identity のスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="6738b-208">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="6738b-209">省略可能: *Views/Shared/_Layout cshtml*ファイルにログイン部分 (`_LoginPartial`) を追加します。</span><span class="sxs-lookup"><span data-stu-id="6738b-209">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="6738b-210">*Pages/shared/_LoginPartial cshtml*ファイルを*Views/shared/_LoginPartial*に移動します。</span><span class="sxs-lookup"><span data-stu-id="6738b-210">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="6738b-211">Id は、 *Areas/identity/IdentityHostingStartup*で構成されます。</span><span class="sxs-lookup"><span data-stu-id="6738b-211">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="6738b-212">詳細については、「IHostingStartup」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6738b-212">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="6738b-213">`UseStaticFiles`後に[Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="6738b-213">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="6738b-214">承認ありの MVC プロジェクトに対する identity のスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="6738b-214">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="6738b-215">*ページ/共有*フォルダーとそのフォルダー内のファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="6738b-215">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="6738b-216">identity の全ての UI のソースの作成</span><span class="sxs-lookup"><span data-stu-id="6738b-216">Create full identity UI source</span></span>

<span data-ttu-id="6738b-217">Id UI の完全な制御を維持するには、Identity scaffolder を実行し、[**すべてのファイルを上書き**する] を選択します。</span><span class="sxs-lookup"><span data-stu-id="6738b-217">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="6738b-218">次の強調表示されたコードは、既定の Id UI を ASP.NET Core 2.1 web アプリの Id に置き換える変更を示しています。</span><span class="sxs-lookup"><span data-stu-id="6738b-218">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="6738b-219">これは、Id UI を完全に制御するために必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="6738b-219">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="6738b-220">既定の Id は、次のコードで置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="6738b-220">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="6738b-221">次のコードでは、 [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)、 [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)、および[AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)を設定しています。</span><span class="sxs-lookup"><span data-stu-id="6738b-221">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="6738b-222">`IEmailSender` の実装を登録します。次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="6738b-222">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="6738b-223">登録の無効化ページ</span><span class="sxs-lookup"><span data-stu-id="6738b-223">Disable register page</span></span>

<span data-ttu-id="6738b-224">ユーザー登録を無効にするには:</span><span class="sxs-lookup"><span data-stu-id="6738b-224">To disable user registration:</span></span>

* <span data-ttu-id="6738b-225">スキャフォールディング Id。</span><span class="sxs-lookup"><span data-stu-id="6738b-225">Scaffold Identity.</span></span> <span data-ttu-id="6738b-226">Account、Account. Login、および Account. RegisterConfirmation を含めます。</span><span class="sxs-lookup"><span data-stu-id="6738b-226">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="6738b-227">例:</span><span class="sxs-lookup"><span data-stu-id="6738b-227">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="6738b-228">ユーザーがこのエンドポイントから登録できないように、*区分/id/ページ/アカウント/登録*を更新します。</span><span class="sxs-lookup"><span data-stu-id="6738b-228">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="6738b-229">前の変更との一貫性を保つために、*区分/id/ページ/アカウント/登録*を更新します。</span><span class="sxs-lookup"><span data-stu-id="6738b-229">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="6738b-230">*区分/id/ページ/アカウント/ログイン. cshtml*から登録リンクをコメントアウトするか削除します。</span><span class="sxs-lookup"><span data-stu-id="6738b-230">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="6738b-231">[*区分/id/ページ/アカウント/RegisterConfirmation 確認*] ページを更新します。</span><span class="sxs-lookup"><span data-stu-id="6738b-231">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="6738b-232">コードとリンクを cshtml ファイルから削除します。</span><span class="sxs-lookup"><span data-stu-id="6738b-232">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="6738b-233">`PageModel`から確認コードを削除します。</span><span class="sxs-lookup"><span data-stu-id="6738b-233">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="6738b-234">別のアプリを使用してユーザーを追加する</span><span class="sxs-lookup"><span data-stu-id="6738b-234">Use another app to add users</span></span>

<span data-ttu-id="6738b-235">Web アプリの外部にユーザーを追加するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="6738b-235">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="6738b-236">ユーザーを追加するためのオプションは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="6738b-236">Options to add users include:</span></span>

* <span data-ttu-id="6738b-237">専用管理者 web アプリ。</span><span class="sxs-lookup"><span data-stu-id="6738b-237">A dedicated admin web app.</span></span>
* <span data-ttu-id="6738b-238">コンソールアプリ。</span><span class="sxs-lookup"><span data-stu-id="6738b-238">A console app.</span></span>

<span data-ttu-id="6738b-239">次のコードは、ユーザーを追加する方法の概要を示しています。</span><span class="sxs-lookup"><span data-stu-id="6738b-239">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="6738b-240">ユーザーの一覧がメモリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="6738b-240">A list of users is read into memory.</span></span>
* <span data-ttu-id="6738b-241">ユーザーごとに一意の強力なパスワードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="6738b-241">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="6738b-242">ユーザーが Id データベースに追加されます。</span><span class="sxs-lookup"><span data-stu-id="6738b-242">The user is added to the Identity database.</span></span>
* <span data-ttu-id="6738b-243">ユーザーに通知され、パスワードを変更するように指示されます。</span><span class="sxs-lookup"><span data-stu-id="6738b-243">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="6738b-244">次のコードは、ユーザーの追加の概要を示しています。</span><span class="sxs-lookup"><span data-stu-id="6738b-244">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="6738b-245">同様の方法で、運用環境のシナリオにも対応できます。</span><span class="sxs-lookup"><span data-stu-id="6738b-245">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6738b-246">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="6738b-246">Additional resources</span></span>

* [<span data-ttu-id="6738b-247">ASP.NET Core 2.1 以降に認証コードが変更された</span><span class="sxs-lookup"><span data-stu-id="6738b-247">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end