---
title: ASP.NET Core プロジェクト内のスキャフォールディング Id
author: rick-anderson
description: ASP.NET Core プロジェクトで Id をスキャフォールディングする方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/authentication/scaffold-identity
ms.openlocfilehash: ca2046563281efc3c1cd8f4fec73fe4f8d3fbdda
ms.sourcegitcommit: 383017d7060a6d58f6a79cf4d7335d5b4b6c5659
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2019
ms.locfileid: "72816113"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="b62bc-103">ASP.NET Core プロジェクト内のスキャフォールディング Id</span><span class="sxs-lookup"><span data-stu-id="b62bc-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="b62bc-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b62bc-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b62bc-105">ASP.NET Core 2.1 以降では、 [ASP.NET Core id](xref:security/authentication/identity)を[Razor クラスライブラリ](xref:razor-pages/ui-class)として提供します。</span><span class="sxs-lookup"><span data-stu-id="b62bc-105">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="b62bc-106">Id を含むアプリケーションでは、scaffolder を適用して、Id Razor クラスライブラリ (RCL) に含まれているソースコードを選択的に追加することができます。</span><span class="sxs-lookup"><span data-stu-id="b62bc-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="b62bc-107">コードを変更して動作を変更できるように、ソース コードを生成できます。</span><span class="sxs-lookup"><span data-stu-id="b62bc-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="b62bc-108">たとえば、登録で使用するコードを生成するようにスキャフォルダーに指示できます。</span><span class="sxs-lookup"><span data-stu-id="b62bc-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="b62bc-109">生成されたコードは、Identity RCL の同じコードよりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="b62bc-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="b62bc-110">UI を完全に制御し、既定の RCL を使用しないようにするには、「[完全な ID UI ソースの作成](#full)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b62bc-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="b62bc-111">認証を含ま**ない**アプリケーションでは、scaffolder を適用して Rcl id パッケージを追加できます。</span><span class="sxs-lookup"><span data-stu-id="b62bc-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="b62bc-112">生成される Identity コードの選択オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="b62bc-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="b62bc-113">Scaffolder は、必要なコードの大部分を生成しますが、プロセスを完了するにはプロジェクトを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b62bc-113">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="b62bc-114">このドキュメントでは、Id スキャフォールディングの更新を完了するために必要な手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="b62bc-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="b62bc-115">Id scaffolder を実行すると、 *ScaffoldingReadme*ファイルがプロジェクトディレクトリに作成されます。</span><span class="sxs-lookup"><span data-stu-id="b62bc-115">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="b62bc-116">*ScaffoldingReadme*ファイルには、id スキャフォールディングの更新を完了するために必要な手順に関する一般的な指示が含まれています。</span><span class="sxs-lookup"><span data-stu-id="b62bc-116">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="b62bc-117">このドキュメントには、 *ScaffoldingReadme*ファイルよりも完全な手順が含まれています。</span><span class="sxs-lookup"><span data-stu-id="b62bc-117">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="b62bc-118">ファイルの違いを示すソース管理システムを使用して、変更を元に戻すことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="b62bc-118">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="b62bc-119">Id scaffolder を実行した後に変更を確認します。</span><span class="sxs-lookup"><span data-stu-id="b62bc-119">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="b62bc-120">サービスは、 [2 要素認証](xref:security/authentication/identity-enable-qrcodes)、アカウントの[確認とパスワードの回復](xref:security/authentication/accconfirm)、および id を持つその他のセキュリティ機能を使用する場合に必要です。</span><span class="sxs-lookup"><span data-stu-id="b62bc-120">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="b62bc-121">サービスまたはサービススタブは、スキャフォールディング Id では生成されません。</span><span class="sxs-lookup"><span data-stu-id="b62bc-121">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="b62bc-122">これらの機能を有効にするサービスは、手動で追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b62bc-122">Services to enable these features must be added manually.</span></span> <span data-ttu-id="b62bc-123">たとえば、「[電子メールの確認を要求する](xref:security/authentication/accconfirm#require-email-confirmation)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b62bc-123">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="b62bc-124">Id を空のプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="b62bc-124">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="b62bc-125">次の強調表示されている呼び出しを `Startup` クラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="b62bc-125">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="b62bc-126">既存の承認なしでスキャフォールディング id を Razor プロジェクトに</span><span class="sxs-lookup"><span data-stu-id="b62bc-126">Scaffold identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="b62bc-127">Id は、 *Areas/identity/IdentityHostingStartup*で構成されます。</span><span class="sxs-lookup"><span data-stu-id="b62bc-127">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="b62bc-128">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b62bc-128">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="b62bc-129">移行、UseAuthentication、およびレイアウト</span><span class="sxs-lookup"><span data-stu-id="b62bc-129">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="b62bc-130">認証を有効にする</span><span class="sxs-lookup"><span data-stu-id="b62bc-130">Enable authentication</span></span>

<span data-ttu-id="b62bc-131">`Startup` クラスの `Configure` メソッドで、`UseStaticFiles`後に[Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="b62bc-131">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="b62bc-132">レイアウトの変更</span><span class="sxs-lookup"><span data-stu-id="b62bc-132">Layout changes</span></span>

<span data-ttu-id="b62bc-133">省略可能: 次のように、ログイン部分 (`_LoginPartial`) をレイアウトファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="b62bc-133">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="b62bc-134">承認を使用して id を Razor プロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="b62bc-134">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="b62bc-135">一部の Id オプションは、 *Areas/Identity/IdentityHostingStartup*で構成されています。</span><span class="sxs-lookup"><span data-stu-id="b62bc-135">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="b62bc-136">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b62bc-136">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="b62bc-137">既存の承認なしでスキャフォールディング identity を MVC プロジェクトに</span><span class="sxs-lookup"><span data-stu-id="b62bc-137">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="b62bc-138">省略可能: *Views/Shared/Layout*ファイルにログイン部分 (`_LoginPartial`) を追加します。</span><span class="sxs-lookup"><span data-stu-id="b62bc-138">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="b62bc-139">*Pages/shared/loginfile*を*Views/shared/loginの部分*ファイルに移動します。</span><span class="sxs-lookup"><span data-stu-id="b62bc-139">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="b62bc-140">Id は、 *Areas/identity/IdentityHostingStartup*で構成されます。</span><span class="sxs-lookup"><span data-stu-id="b62bc-140">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="b62bc-141">詳細については、「IHostingStartup」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b62bc-141">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="b62bc-142">`UseStaticFiles`後に[Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="b62bc-142">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="b62bc-143">承認を使用して id を MVC プロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="b62bc-143">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="b62bc-144">*ページ/共有*フォルダーとそのフォルダー内のファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="b62bc-144">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="b62bc-145">完全な id UI ソースの作成</span><span class="sxs-lookup"><span data-stu-id="b62bc-145">Create full identity UI source</span></span>

<span data-ttu-id="b62bc-146">Id UI の完全な制御を維持するには、Identity scaffolder を実行し、[**すべてのファイルを上書き**する] を選択します。</span><span class="sxs-lookup"><span data-stu-id="b62bc-146">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="b62bc-147">次の強調表示されたコードは、既定の Id UI を ASP.NET Core 2.1 web アプリの Id に置き換える変更を示しています。</span><span class="sxs-lookup"><span data-stu-id="b62bc-147">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="b62bc-148">これは、Id UI を完全に制御するために必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="b62bc-148">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="b62bc-149">既定の Id は、次のコードで置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="b62bc-149">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="b62bc-150">次のコードでは、 [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)、 [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)、および[AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)を設定しています。</span><span class="sxs-lookup"><span data-stu-id="b62bc-150">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="b62bc-151">`IEmailSender` の実装を登録します。次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="b62bc-151">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="b62bc-152">登録の無効化ページ</span><span class="sxs-lookup"><span data-stu-id="b62bc-152">Disable register page</span></span>

<span data-ttu-id="b62bc-153">ユーザー登録を無効にするには:</span><span class="sxs-lookup"><span data-stu-id="b62bc-153">To disable user registration:</span></span>

* <span data-ttu-id="b62bc-154">スキャフォールディング Id。</span><span class="sxs-lookup"><span data-stu-id="b62bc-154">Scaffold Identity.</span></span> <span data-ttu-id="b62bc-155">Account、Account. Login、および Account. RegisterConfirmation を含めます。</span><span class="sxs-lookup"><span data-stu-id="b62bc-155">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="b62bc-156">(例:</span><span class="sxs-lookup"><span data-stu-id="b62bc-156">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="b62bc-157">ユーザーがこのエンドポイントから登録できないように、*区分/id/ページ/アカウント/登録*を更新します。</span><span class="sxs-lookup"><span data-stu-id="b62bc-157">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="b62bc-158">前の変更との一貫性を保つために、*区分/id/ページ/アカウント/登録*を更新します。</span><span class="sxs-lookup"><span data-stu-id="b62bc-158">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="b62bc-159">*区分/id/ページ/アカウント/ログイン. cshtml*から登録リンクをコメントアウトするか削除します。</span><span class="sxs-lookup"><span data-stu-id="b62bc-159">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="b62bc-160">[*区分/id/ページ/アカウント/RegisterConfirmation 確認*] ページを更新します。</span><span class="sxs-lookup"><span data-stu-id="b62bc-160">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="b62bc-161">コードとリンクを cshtml ファイルから削除します。</span><span class="sxs-lookup"><span data-stu-id="b62bc-161">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="b62bc-162">`PageModel`から確認コードを削除します。</span><span class="sxs-lookup"><span data-stu-id="b62bc-162">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="b62bc-163">別のアプリを使用してユーザーを追加する</span><span class="sxs-lookup"><span data-stu-id="b62bc-163">Use another app to add users</span></span>

<span data-ttu-id="b62bc-164">Web アプリの外部にユーザーを追加するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="b62bc-164">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="b62bc-165">ユーザーを追加するためのオプションは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="b62bc-165">Options to add users include:</span></span>

* <span data-ttu-id="b62bc-166">専用管理者 web アプリ。</span><span class="sxs-lookup"><span data-stu-id="b62bc-166">A dedicated admin web app.</span></span>
* <span data-ttu-id="b62bc-167">コンソールアプリ。</span><span class="sxs-lookup"><span data-stu-id="b62bc-167">A console app.</span></span>

<span data-ttu-id="b62bc-168">次のコードは、ユーザーを追加する方法の概要を示しています。</span><span class="sxs-lookup"><span data-stu-id="b62bc-168">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="b62bc-169">ユーザーの一覧がメモリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="b62bc-169">A list of users is read into memory.</span></span>
* <span data-ttu-id="b62bc-170">ユーザーごとに一意の強力なパスワードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="b62bc-170">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="b62bc-171">ユーザーが Id データベースに追加されます。</span><span class="sxs-lookup"><span data-stu-id="b62bc-171">The user is added to the Identity database.</span></span>
* <span data-ttu-id="b62bc-172">ユーザーに通知され、パスワードを変更するように指示されます。</span><span class="sxs-lookup"><span data-stu-id="b62bc-172">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="b62bc-173">次のコードは、ユーザーの追加の概要を示しています。</span><span class="sxs-lookup"><span data-stu-id="b62bc-173">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="b62bc-174">同様の方法で、運用環境のシナリオにも対応できます。</span><span class="sxs-lookup"><span data-stu-id="b62bc-174">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b62bc-175">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="b62bc-175">Additional resources</span></span>

* [<span data-ttu-id="b62bc-176">ASP.NET Core 2.1 以降に認証コードが変更された</span><span class="sxs-lookup"><span data-stu-id="b62bc-176">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)
