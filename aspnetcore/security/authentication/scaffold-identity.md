---
title: ASP.NET Core プロジェクトにおけるIdentityのスキャフォールディング
author: rick-anderson
description: ASP.NET Core プロジェクトでの Id をスキャフォールディングする方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 9e784864ee78bdfb76dab7748f3fb81728b9c289
ms.sourcegitcommit: 91cc1f07ef178ab709ea42f8b3a10399c970496e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67622764"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="5d40f-103">ASP.NET Core プロジェクトにおけるIdentityのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="5d40f-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="5d40f-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5d40f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5d40f-105">ASP.NET Core 2.1 以降では[ASP.NET Core Identity](xref:security/authentication/identity)として、 [Razor クラス ライブラリ](xref:razor-pages/ui-class)します。</span><span class="sxs-lookup"><span data-stu-id="5d40f-105">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="5d40f-106">Id を含むアプリケーションでは、選択的に Identity Razor クラス ライブラリ (RCL) に含まれているソース コードを追加する scaffolder を適用できます。</span><span class="sxs-lookup"><span data-stu-id="5d40f-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="5d40f-107">コードを変更して動作を変更できるように、ソース コードを生成できます。</span><span class="sxs-lookup"><span data-stu-id="5d40f-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="5d40f-108">たとえば、登録で使用するコードを生成するようにスキャフォルダーに指示できます。</span><span class="sxs-lookup"><span data-stu-id="5d40f-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="5d40f-109">生成されたコードは、Identity RCL の同じコードよりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="5d40f-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="5d40f-110">UI のフル コントロールを取得し、既定 RCL を使用しない、セクションをご覧ください。 [UI のソースの完全な id 作成](#full)です。</span><span class="sxs-lookup"><span data-stu-id="5d40f-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="5d40f-111">実行するアプリケーション**いない**含める認証 RCL Identity パッケージを追加する scaffolder を適用できます。</span><span class="sxs-lookup"><span data-stu-id="5d40f-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="5d40f-112">生成される Identity コードの選択オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="5d40f-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="5d40f-113">スキャフォルダーは、必要なコードの大部分を生成するが、プロセスを完了するプロジェクトを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5d40f-113">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="5d40f-114">このドキュメントでは、Id のスキャフォールディング更新の完了に必要な手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="5d40f-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="5d40f-115">Identity scaffolder が実行される、 *ScaffoldingReadme.txt*プロジェクト ディレクトリにファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="5d40f-115">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="5d40f-116">*ScaffoldingReadme.txt*ファイルには、一般的な手順について Id スキャフォールディングの更新の完了に必要なものが含まれています。</span><span class="sxs-lookup"><span data-stu-id="5d40f-116">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="5d40f-117">このドキュメントより詳細な手順が含まれています、 *ScaffoldingReadme.txt*ファイル。</span><span class="sxs-lookup"><span data-stu-id="5d40f-117">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="5d40f-118">ファイルの相違点を示し、変更をバックアップすることができますをソース管理システムを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="5d40f-118">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="5d40f-119">Identity scaffolder を実行した後、変更を検査します。</span><span class="sxs-lookup"><span data-stu-id="5d40f-119">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="5d40f-120">使用する場合に必要なサービス[2 要素認証](xref:security/authentication/identity-enable-qrcodes)、[アカウントの確認とパスワードの回復](xref:security/authentication/accconfirm)、および Id を持つその他のセキュリティ機能。</span><span class="sxs-lookup"><span data-stu-id="5d40f-120">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="5d40f-121">Identity をスキャフォールディングするときにサービスまたはサービスのスタブは生成されません。</span><span class="sxs-lookup"><span data-stu-id="5d40f-121">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="5d40f-122">これらの機能を有効にするサービスを手動で追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5d40f-122">Services to enable these features must be added manually.</span></span> <span data-ttu-id="5d40f-123">たとえばを参照してください[確認の電子メールを必要と](xref:security/authentication/accconfirm#require-email-confirmation)します。</span><span class="sxs-lookup"><span data-stu-id="5d40f-123">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="5d40f-124">空のプロジェクトに対するidentityのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="5d40f-124">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="5d40f-125">次の強調表示されている呼び出しを追加、`Startup`クラス。</span><span class="sxs-lookup"><span data-stu-id="5d40f-125">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="5d40f-126">既存の承認なしのRazorプロジェクトに対するidentityのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="5d40f-126">Scaffold identity into a Razor project without existing authorization</span></span>

<!--
set projNam=RPnoAuth
set projType=webapp
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

<span data-ttu-id="5d40f-127">Id が構成されて*Areas/Identity/IdentityHostingStartup.cs*します。</span><span class="sxs-lookup"><span data-stu-id="5d40f-127">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="5d40f-128">詳細については、次を参照してください。 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)します。</span><span class="sxs-lookup"><span data-stu-id="5d40f-128">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="5d40f-129">マイグレーション、UseAuthentication、およびレイアウト</span><span class="sxs-lookup"><span data-stu-id="5d40f-129">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="5d40f-130">認証の有効化</span><span class="sxs-lookup"><span data-stu-id="5d40f-130">Enable authentication</span></span>

<span data-ttu-id="5d40f-131">`Configure`のメソッド、`Startup`クラスを呼び出す[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)後`UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="5d40f-131">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="5d40f-132">レイアウトの変更</span><span class="sxs-lookup"><span data-stu-id="5d40f-132">Layout changes</span></span>

<span data-ttu-id="5d40f-133">省略可能:部分的なログインを追加 (`_LoginPartial`)、レイアウト ファイルに。</span><span class="sxs-lookup"><span data-stu-id="5d40f-133">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="5d40f-134">承認ありの Razor プロジェクトに対する identity のスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="5d40f-134">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="5d40f-135">いくつかの Id オプションが構成されている*Areas/Identity/IdentityHostingStartup.cs*します。</span><span class="sxs-lookup"><span data-stu-id="5d40f-135">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="5d40f-136">詳細については、次を参照してください。 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)します。</span><span class="sxs-lookup"><span data-stu-id="5d40f-136">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="5d40f-137">既存の承認なしの MVC プロジェクトに対する identity のスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="5d40f-137">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="5d40f-138">省略可能:部分的なログインを追加 (`_LoginPartial`) に、 *Views/Shared/_Layout.cshtml*ファイル。</span><span class="sxs-lookup"><span data-stu-id="5d40f-138">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="5d40f-139">移動、 *Pages/Shared/_LoginPartial.cshtml*ファイルを*Views/Shared/_LoginPartial.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5d40f-139">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="5d40f-140">Id が構成されて*Areas/Identity/IdentityHostingStartup.cs*します。</span><span class="sxs-lookup"><span data-stu-id="5d40f-140">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="5d40f-141">詳細については、IHostingStartup を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5d40f-141">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="5d40f-142">呼び出す[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)後`UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="5d40f-142">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="5d40f-143">承認ありの MVC プロジェクトに対する identity のスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="5d40f-143">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext --files Account.Register
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="5d40f-144">削除、*ページ/共有*フォルダーとそのフォルダー内のファイル。</span><span class="sxs-lookup"><span data-stu-id="5d40f-144">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="5d40f-145">identity の全ての UI のソースの作成</span><span class="sxs-lookup"><span data-stu-id="5d40f-145">Create full identity UI source</span></span>

<span data-ttu-id="5d40f-146">Identity UI を完全に制御を維持するために、Identity scaffolder を実行して選択して**すべてのファイルを上書き**します。</span><span class="sxs-lookup"><span data-stu-id="5d40f-146">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="5d40f-147">次の強調表示されたコードは、ASP.NET Core 2.1 の web アプリで Id を持つ既定の Identity の UI を置換する変更を示しています。</span><span class="sxs-lookup"><span data-stu-id="5d40f-147">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="5d40f-148">これは、Identity UI の完全に制御をする可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5d40f-148">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="5d40f-149">既定の Id は、次のコードに置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="5d40f-149">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="5d40f-150">次のコード セット、 [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)、 [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)、および[AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="5d40f-150">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="5d40f-151">登録、`IEmailSender`例については、実装します。</span><span class="sxs-lookup"><span data-stu-id="5d40f-151">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="5d40f-152">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="5d40f-152">Additional resources</span></span>

* [<span data-ttu-id="5d40f-153">ASP.NET Core 2.1 以降の認証コードの変更</span><span class="sxs-lookup"><span data-stu-id="5d40f-153">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)
