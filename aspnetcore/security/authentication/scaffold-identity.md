---
title: ASP.NET Core プロジェクトにおけるIdentityのスキャフォールディング
author: rick-anderson
description: ASP.NET Core プロジェクトで Id をスキャフォールディングする方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/authentication/scaffold-identity
ms.openlocfilehash: f3ae089d344d95ed84c9720ab4ba2c697400901e
ms.sourcegitcommit: dc96d76f6b231de59586fcbb989a7fb5106d26a8
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703772"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="72c2a-103">ASP.NET Core プロジェクトにおけるIdentityのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="72c2a-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="72c2a-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="72c2a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="72c2a-105">ASP.NET Core 2.1 以降では、 [ASP.NET Core id](xref:security/authentication/identity)を[Razor クラスライブラリ](xref:razor-pages/ui-class)として提供します。</span><span class="sxs-lookup"><span data-stu-id="72c2a-105">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="72c2a-106">Id を含むアプリケーションでは、scaffolder を適用して、Id Razor クラスライブラリ (RCL) に含まれているソースコードを選択的に追加することができます。</span><span class="sxs-lookup"><span data-stu-id="72c2a-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="72c2a-107">コードを変更して動作を変更できるように、ソース コードを生成できます。</span><span class="sxs-lookup"><span data-stu-id="72c2a-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="72c2a-108">たとえば、登録で使用するコードを生成するようにスキャフォルダーに指示できます。</span><span class="sxs-lookup"><span data-stu-id="72c2a-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="72c2a-109">生成されたコードは、Identity RCL の同じコードよりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="72c2a-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="72c2a-110">UI を完全に制御し、既定の RCL を使用しないようにするには、「[完全な ID UI ソースの作成](#full)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="72c2a-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="72c2a-111">認証を含ま**ない**アプリケーションでは、scaffolder を適用して Rcl id パッケージを追加できます。</span><span class="sxs-lookup"><span data-stu-id="72c2a-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="72c2a-112">生成される Identity コードの選択オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="72c2a-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="72c2a-113">Scaffolder は、必要なコードの大部分を生成しますが、プロセスを完了するにはプロジェクトを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="72c2a-113">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="72c2a-114">このドキュメントでは、Id スキャフォールディングの更新を完了するために必要な手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="72c2a-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="72c2a-115">Id scaffolder を実行すると、 *ScaffoldingReadme*ファイルがプロジェクトディレクトリに作成されます。</span><span class="sxs-lookup"><span data-stu-id="72c2a-115">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="72c2a-116">*ScaffoldingReadme*ファイルには、id スキャフォールディングの更新を完了するために必要な手順に関する一般的な指示が含まれています。</span><span class="sxs-lookup"><span data-stu-id="72c2a-116">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="72c2a-117">このドキュメントには、 *ScaffoldingReadme*ファイルよりも完全な手順が含まれています。</span><span class="sxs-lookup"><span data-stu-id="72c2a-117">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="72c2a-118">ファイルの違いを示すソース管理システムを使用して、変更を元に戻すことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="72c2a-118">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="72c2a-119">Id scaffolder を実行した後に変更を確認します。</span><span class="sxs-lookup"><span data-stu-id="72c2a-119">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="72c2a-120">サービスは、 [2 要素認証](xref:security/authentication/identity-enable-qrcodes)、アカウントの[確認とパスワードの回復](xref:security/authentication/accconfirm)、および id を持つその他のセキュリティ機能を使用する場合に必要です。</span><span class="sxs-lookup"><span data-stu-id="72c2a-120">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="72c2a-121">サービスまたはサービススタブは、スキャフォールディング Id では生成されません。</span><span class="sxs-lookup"><span data-stu-id="72c2a-121">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="72c2a-122">これらの機能を有効にするサービスは、手動で追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="72c2a-122">Services to enable these features must be added manually.</span></span> <span data-ttu-id="72c2a-123">たとえば、「[電子メールの確認を要求する](xref:security/authentication/accconfirm#require-email-confirmation)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="72c2a-123">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="72c2a-124">空のプロジェクトに対するidentityのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="72c2a-124">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="72c2a-125">次の強調表示されている呼び出しを `Startup` クラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="72c2a-125">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="72c2a-126">既存の承認なしのRazorプロジェクトに対するidentityのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="72c2a-126">Scaffold identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="72c2a-127">Id は、 *Areas/identity/IdentityHostingStartup*で構成されます。</span><span class="sxs-lookup"><span data-stu-id="72c2a-127">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="72c2a-128">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="72c2a-128">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="72c2a-129">マイグレーション、UseAuthentication、およびレイアウト</span><span class="sxs-lookup"><span data-stu-id="72c2a-129">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="72c2a-130">認証の有効化</span><span class="sxs-lookup"><span data-stu-id="72c2a-130">Enable authentication</span></span>

<span data-ttu-id="72c2a-131">@No__t-1 クラスの @no__t 0 メソッドで、`UseStaticFiles` の後に[Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="72c2a-131">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="72c2a-132">レイアウトの変更</span><span class="sxs-lookup"><span data-stu-id="72c2a-132">Layout changes</span></span>

<span data-ttu-id="72c2a-133">省略可能: 次のように、ログイン部分 (`_LoginPartial`) をレイアウトファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="72c2a-133">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="72c2a-134">承認ありの Razor プロジェクトに対する identity のスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="72c2a-134">Scaffold identity into a Razor project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files Account.Register
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
<span data-ttu-id="72c2a-135">一部の Id オプションは、 *Areas/Identity/IdentityHostingStartup*で構成されています。</span><span class="sxs-lookup"><span data-stu-id="72c2a-135">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="72c2a-136">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="72c2a-136">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="72c2a-137">既存の承認なしの MVC プロジェクトに対する identity のスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="72c2a-137">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="72c2a-138">省略可能: *Views/Shared/Layout*ファイルに login 部分 (`_LoginPartial`) を追加します。</span><span class="sxs-lookup"><span data-stu-id="72c2a-138">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="72c2a-139">*Pages/shared/loginfile*を*Views/shared/loginの部分*ファイルに移動します。</span><span class="sxs-lookup"><span data-stu-id="72c2a-139">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="72c2a-140">Id は、 *Areas/identity/IdentityHostingStartup*で構成されます。</span><span class="sxs-lookup"><span data-stu-id="72c2a-140">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="72c2a-141">詳細については、「IHostingStartup」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="72c2a-141">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="72c2a-142">@No__t-1 の後に[Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="72c2a-142">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="72c2a-143">承認ありの MVC プロジェクトに対する identity のスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="72c2a-143">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext --files Account.Register
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="72c2a-144">*ページ/共有*フォルダーとそのフォルダー内のファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="72c2a-144">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="72c2a-145">identity の全ての UI のソースの作成</span><span class="sxs-lookup"><span data-stu-id="72c2a-145">Create full identity UI source</span></span>

<span data-ttu-id="72c2a-146">Id UI の完全な制御を維持するには、Identity scaffolder を実行し、[**すべてのファイルを上書き**する] を選択します。</span><span class="sxs-lookup"><span data-stu-id="72c2a-146">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="72c2a-147">次の強調表示されたコードは、既定の Id UI を ASP.NET Core 2.1 web アプリの Id に置き換える変更を示しています。</span><span class="sxs-lookup"><span data-stu-id="72c2a-147">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="72c2a-148">これは、Id UI を完全に制御するために必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="72c2a-148">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="72c2a-149">既定の Id は、次のコードで置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="72c2a-149">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="72c2a-150">次のコードでは、 [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)、 [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)、および[AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)を設定しています。</span><span class="sxs-lookup"><span data-stu-id="72c2a-150">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="72c2a-151">@No__t 0 の実装を登録します。次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="72c2a-151">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="72c2a-152">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="72c2a-152">Additional resources</span></span>

* [<span data-ttu-id="72c2a-153">ASP.NET Core 2.1 以降に認証コードが変更された</span><span class="sxs-lookup"><span data-stu-id="72c2a-153">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)
