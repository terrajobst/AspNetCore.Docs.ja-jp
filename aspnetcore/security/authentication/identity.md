---
title: ASP.NET Core Identity の概要
author: rick-anderson
description: ASP.NET Core アプリで Id を使用します。 パスワードの要件 (RequireDigit、RequiredLength、RequiredUniqueChars など) を設定する方法について説明します。
ms.author: riande
ms.date: 01/15/2020
uid: security/authentication/identity
ms.openlocfilehash: 164ba10c1d1e2a73ebeb8240293a58f158055699
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172529"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="5f304-104">ASP.NET Core Identity の概要</span><span class="sxs-lookup"><span data-stu-id="5f304-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5f304-105">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5f304-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5f304-106">ASP.NET Core Id:</span><span class="sxs-lookup"><span data-stu-id="5f304-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="5f304-107">は、ユーザーインターフェイス (UI) のログイン機能をサポートする API です。</span><span class="sxs-lookup"><span data-stu-id="5f304-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="5f304-108">ユーザー、パスワード、プロファイルデータ、ロール、要求、トークン、電子メールの確認などを管理します。</span><span class="sxs-lookup"><span data-stu-id="5f304-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="5f304-109">ユーザーは、Id に格納されているログイン情報を持つアカウントを作成することも、外部ログインプロバイダーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="5f304-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="5f304-110">サポートされている外部ログインプロバイダーには、 [Facebook、Google、Microsoft アカウント、Twitter](xref:security/authentication/social/index)があります。</span><span class="sxs-lookup"><span data-stu-id="5f304-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="5f304-111">[Id ソースコード](https://github.com/dotnet/AspNetCore/tree/master/src/Identity)は、GitHub で入手できます。</span><span class="sxs-lookup"><span data-stu-id="5f304-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="5f304-112">[スキャフォールディング](xref:security/authentication/scaffold-identity)は、id とのテンプレートの対話を確認するために、生成されたファイルを識別して表示します。</span><span class="sxs-lookup"><span data-stu-id="5f304-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="5f304-113">Id は、通常、ユーザー名、パスワード、およびプロファイルデータを格納するために SQL Server データベースを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="5f304-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="5f304-114">別の永続ストアを使用することもできます (たとえば、Azure Table Storage)。</span><span class="sxs-lookup"><span data-stu-id="5f304-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="5f304-115">このトピックでは、Identity を使用してユーザーを登録、ログイン、ログアウトする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="5f304-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="5f304-116">Id を使用するアプリを作成する方法の詳細については、この記事の最後にある「次のステップ」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f304-116">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="5f304-117">[Microsoft id プラットフォーム](/azure/active-directory/develop/)は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="5f304-117">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="5f304-118">Azure Active Directory (Azure AD) 開発プラットフォームの進化。</span><span class="sxs-lookup"><span data-stu-id="5f304-118">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="5f304-119">ASP.NET Core Id に関連付けられていません。</span><span class="sxs-lookup"><span data-stu-id="5f304-119">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="5f304-120">サンプルコード ([ダウンロード方法)](xref:index#how-to-download-a-sample)を[表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample)します。</span><span class="sxs-lookup"><span data-stu-id="5f304-120">[View or download the sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="5f304-121">認証を使用して Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="5f304-121">Create a Web app with authentication</span></span>

<span data-ttu-id="5f304-122">個別のユーザー アカウントを使って、ASP.NET Core Web アプリケーション プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="5f304-122">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5f304-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5f304-123">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5f304-124">[**ファイル**>**新しい**>**プロジェクト**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="5f304-124">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="5f304-125">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5f304-125">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="5f304-126">プロジェクトに**WebApp1**という名前を付け、プロジェクトのダウンロードと同じ名前空間にします。</span><span class="sxs-lookup"><span data-stu-id="5f304-126">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="5f304-127">**[OK]** をクリックすると、</span><span class="sxs-lookup"><span data-stu-id="5f304-127">Click **OK**.</span></span>
* <span data-ttu-id="5f304-128">ASP.NET Core **Web アプリケーション**を選択し、 **[認証の変更]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5f304-128">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="5f304-129">**個々のユーザーアカウント**を選択し、[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="5f304-129">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="5f304-130">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5f304-130">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="5f304-131">上記のコマンドは、SQLite を使用して Razor web アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="5f304-131">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="5f304-132">LocalDB を使用して web アプリを作成するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5f304-132">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="5f304-133">生成されたプロジェクトは、 [ASP.NET Core id](xref:security/authentication/identity)を[Razor クラスライブラリ](xref:razor-pages/ui-class)として提供します。</span><span class="sxs-lookup"><span data-stu-id="5f304-133">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="5f304-134">Identity Razor クラスライブラリは、`Identity` 領域でエンドポイントを公開します。</span><span class="sxs-lookup"><span data-stu-id="5f304-134">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="5f304-135">例 :</span><span class="sxs-lookup"><span data-stu-id="5f304-135">For example:</span></span>

* <span data-ttu-id="5f304-136">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="5f304-136">/Identity/Account/Login</span></span>
* <span data-ttu-id="5f304-137">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="5f304-137">/Identity/Account/Logout</span></span>
* <span data-ttu-id="5f304-138">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="5f304-138">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="5f304-139">移行を適用する</span><span class="sxs-lookup"><span data-stu-id="5f304-139">Apply migrations</span></span>

<span data-ttu-id="5f304-140">移行を適用してデータベースを初期化します。</span><span class="sxs-lookup"><span data-stu-id="5f304-140">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5f304-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5f304-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5f304-142">パッケージマネージャーコンソール (PMC) で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5f304-142">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="5f304-143">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5f304-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5f304-144">SQLite を使用する場合、この手順で移行する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="5f304-144">Migrations are not necessary at this step when using SQLite.</span></span> <span data-ttu-id="5f304-145">LocalDB の場合は、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5f304-145">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="5f304-146">テストレジスタとログイン</span><span class="sxs-lookup"><span data-stu-id="5f304-146">Test Register and Login</span></span>

<span data-ttu-id="5f304-147">アプリを実行し、ユーザーを登録します。</span><span class="sxs-lookup"><span data-stu-id="5f304-147">Run the app and register a user.</span></span> <span data-ttu-id="5f304-148">画面のサイズによっては、[ナビゲーション] トグルボタンを選択して、**登録**リンクと**ログイン**リンクを表示する必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="5f304-148">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="5f304-149">Identity サービスを構成します。</span><span class="sxs-lookup"><span data-stu-id="5f304-149">Configure Identity services</span></span>

<span data-ttu-id="5f304-150">サービスは `ConfigureServices`に追加されます。</span><span class="sxs-lookup"><span data-stu-id="5f304-150">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="5f304-151">一般的なパターンは、すべての `Add{Service}` メソッドを呼び出した後、すべての `services.Configure{Service}` メソッドを呼び出すことです。</span><span class="sxs-lookup"><span data-stu-id="5f304-151">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="5f304-152">上の強調表示されたコードは、既定のオプション値を使用して Id を構成します。</span><span class="sxs-lookup"><span data-stu-id="5f304-152">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="5f304-153">サービスは、[依存関係の挿入](xref:fundamentals/dependency-injection)によってアプリで使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="5f304-153">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="5f304-154">Id は <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>を呼び出すことによって有効になります。</span><span class="sxs-lookup"><span data-stu-id="5f304-154">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="5f304-155">`UseAuthentication` は、認証[ミドルウェア](xref:fundamentals/middleware/index)を要求パイプラインに追加します。</span><span class="sxs-lookup"><span data-stu-id="5f304-155">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="5f304-156">テンプレートで生成されたアプリは、[承認](xref:security/authorization/secure-data)を使用しません。</span><span class="sxs-lookup"><span data-stu-id="5f304-156">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="5f304-157">アプリが承認を追加する正しい順序で追加されるように `app.UseAuthorization` が含まれています。</span><span class="sxs-lookup"><span data-stu-id="5f304-157">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="5f304-158">`UseRouting`、`UseAuthentication`、`UseAuthorization`、および `UseEndpoints` は、前のコードに示されている順序で呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="5f304-158">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="5f304-159">`IdentityOptions` と `Startup`の詳細については、「<xref:Microsoft.AspNetCore.Identity.IdentityOptions> と[アプリケーションの起動](xref:fundamentals/startup)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f304-159">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="5f304-160">スキャフォールディング Register、Login、および LogOut</span><span class="sxs-lookup"><span data-stu-id="5f304-160">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5f304-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5f304-161">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5f304-162">Register、Login、LogOut のファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="5f304-162">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="5f304-163">スキャフォールディング id に従って、このセクションに示されているコードを生成するための[承認命令を含む Razor プロジェクトに](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)従います。</span><span class="sxs-lookup"><span data-stu-id="5f304-163">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="5f304-164">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5f304-164">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5f304-165">**WebApp1**という名前のプロジェクトを作成した場合は、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5f304-165">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="5f304-166">それ以外の場合は、`ApplicationDbContext`に適切な名前空間を使用します。</span><span class="sxs-lookup"><span data-stu-id="5f304-166">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="5f304-167">PowerShell では、コマンドの区切り記号としてセミコロンを使用します。</span><span class="sxs-lookup"><span data-stu-id="5f304-167">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="5f304-168">PowerShell を使用する場合は、前の例に示したように、ファイルリスト内のセミコロンをエスケープするか、ファイルリストを二重引用符で囲みます。</span><span class="sxs-lookup"><span data-stu-id="5f304-168">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="5f304-169">スキャフォールディング Id の詳細については、「[スキャフォールディング identity to a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f304-169">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="5f304-170">レジスタの確認</span><span class="sxs-lookup"><span data-stu-id="5f304-170">Examine Register</span></span>

<span data-ttu-id="5f304-171">ユーザーが **[登録]** リンクをクリックすると、`RegisterModel.OnPostAsync` アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="5f304-171">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="5f304-172">ユーザーは、`_userManager` オブジェクトに対して[Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)によって作成されます。</span><span class="sxs-lookup"><span data-stu-id="5f304-172">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="5f304-173">`_userManager` は、依存関係の挿入によって提供されます)。</span><span class="sxs-lookup"><span data-stu-id="5f304-173">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="5f304-174">ユーザーが正常に作成された場合は、`_signInManager.SignInAsync`の呼び出しによってユーザーがログインします。</span><span class="sxs-lookup"><span data-stu-id="5f304-174">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="5f304-175">登録時にすぐにログインできないようにする手順については、「[アカウントの確認](xref:security/authentication/accconfirm#prevent-login-at-registration)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="5f304-175">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="5f304-176">ログイン</span><span class="sxs-lookup"><span data-stu-id="5f304-176">Log in</span></span>

<span data-ttu-id="5f304-177">ログイン フォームは次の時に表示されます :</span><span class="sxs-lookup"><span data-stu-id="5f304-177">The Login form is displayed when:</span></span>

* <span data-ttu-id="5f304-178">**ログイン**リンクが選択されています。</span><span class="sxs-lookup"><span data-stu-id="5f304-178">The **Log in** link is selected.</span></span>
* <span data-ttu-id="5f304-179">ユーザーがアクセスを承認されていない、**または**システムによって認証されていない制限付きページにアクセスしようとしています。</span><span class="sxs-lookup"><span data-stu-id="5f304-179">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="5f304-180">ログインページのフォームが送信されると、`OnPostAsync` アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="5f304-180">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="5f304-181">`PasswordSignInAsync` は、(依存関係の挿入によって提供される) `_signInManager` オブジェクトで呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="5f304-181">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="5f304-182">基本 `Controller` クラスは、コントローラーメソッドからアクセスできる `User` プロパティを公開します。</span><span class="sxs-lookup"><span data-stu-id="5f304-182">The base `Controller` class exposes a `User` property that can be accessed from controller methods.</span></span> <span data-ttu-id="5f304-183">たとえば、`User.Claims` を列挙し、承認の決定を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="5f304-183">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="5f304-184">詳細については、「 <xref:security/authorization/introduction>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f304-184">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="5f304-185">ログアウト</span><span class="sxs-lookup"><span data-stu-id="5f304-185">Log out</span></span>

<span data-ttu-id="5f304-186">**Log out**リンクは、`LogoutModel.OnPost` アクションを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5f304-186">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="5f304-187">前のコードでは、ブラウザーが新しい要求を実行し、ユーザーの id が更新されるように、コード `return RedirectToPage();` がリダイレクトである必要があります。</span><span class="sxs-lookup"><span data-stu-id="5f304-187">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="5f304-188">[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync)者は、cookie に格納されているユーザーの要求をクリアします。</span><span class="sxs-lookup"><span data-stu-id="5f304-188">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="5f304-189">Post は*Pages/Shared/_LoginPartial*に指定されています。</span><span class="sxs-lookup"><span data-stu-id="5f304-189">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="5f304-190">Identity のテスト</span><span class="sxs-lookup"><span data-stu-id="5f304-190">Test Identity</span></span>

<span data-ttu-id="5f304-191">既定の web プロジェクト テンプレートは、ホーム ページへの匿名アクセスを許可しています。</span><span class="sxs-lookup"><span data-stu-id="5f304-191">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="5f304-192">Id をテストするには、 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)を追加します。</span><span class="sxs-lookup"><span data-stu-id="5f304-192">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="5f304-193">サインインしている場合は、サインアウトします。アプリを実行し、 **[プライバシー]** リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="5f304-193">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="5f304-194">ログイン ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="5f304-194">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="5f304-195">Identity を調べる</span><span class="sxs-lookup"><span data-stu-id="5f304-195">Explore Identity</span></span>

<span data-ttu-id="5f304-196">Identity についてさらに詳しく調べるには :</span><span class="sxs-lookup"><span data-stu-id="5f304-196">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="5f304-197">完全な id UI ソースの作成</span><span class="sxs-lookup"><span data-stu-id="5f304-197">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="5f304-198">各ページのソースを確認し、デバッガーでステップ実行します。</span><span class="sxs-lookup"><span data-stu-id="5f304-198">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="5f304-199">Identity コンポーネント</span><span class="sxs-lookup"><span data-stu-id="5f304-199">Identity Components</span></span>

<span data-ttu-id="5f304-200">すべての Id に依存する NuGet パッケージは、 [ASP.NET Core 共有フレームワーク](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="5f304-200">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="5f304-201">Id のプライマリパッケージは[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)です。</span><span class="sxs-lookup"><span data-stu-id="5f304-201">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="5f304-202">このパッケージには ASP.NET Core Id のインターフェイスのコアセットが含まれており、`Microsoft.AspNetCore.Identity.EntityFrameworkCore`によって含まれています。</span><span class="sxs-lookup"><span data-stu-id="5f304-202">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="5f304-203">ASP.NET Core Id への移行</span><span class="sxs-lookup"><span data-stu-id="5f304-203">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="5f304-204">既存の Id ストアを移行する方法の詳細とガイダンスについては、「[認証と id の移行](xref:migration/identity)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f304-204">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="5f304-205">パスワードの強度を設定する</span><span class="sxs-lookup"><span data-stu-id="5f304-205">Setting password strength</span></span>

<span data-ttu-id="5f304-206">パスワードの最小要件を設定するサンプルについては、「[構成](#pw)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f304-206">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="5f304-207">AddDefaultIdentity と AddIdentity</span><span class="sxs-lookup"><span data-stu-id="5f304-207">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="5f304-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> は ASP.NET Core 2.1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="5f304-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="5f304-209">`AddDefaultIdentity` の呼び出しは、次の呼び出しに似ています。</span><span class="sxs-lookup"><span data-stu-id="5f304-209">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="5f304-210">詳細については、「 [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f304-210">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="5f304-211">静的 Id 資産の発行を禁止する</span><span class="sxs-lookup"><span data-stu-id="5f304-211">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="5f304-212">静的な Id アセット (Id UI 用のスタイルシートおよび JavaScript ファイル) を web ルートに発行できないようにするには、次の `ResolveStaticWebAssetsInputsDependsOn` プロパティを追加し、アプリのプロジェクトファイルにターゲット `RemoveIdentityAssets` します。</span><span class="sxs-lookup"><span data-stu-id="5f304-212">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>RemoveIdentityAssets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="RemoveIdentityAssets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.Identity.UI'" />
  </ItemGroup>
</Target>
```

## <a name="next-steps"></a><span data-ttu-id="5f304-213">次の手順</span><span class="sxs-lookup"><span data-stu-id="5f304-213">Next Steps</span></span>

* <span data-ttu-id="5f304-214">SQLite を使用して Id を構成する方法については、 [GitHub の問題](https://github.com/aspnet/AspNetCore.Docs/issues/5131)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f304-214">See [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="5f304-215">Identity の構成</span><span class="sxs-lookup"><span data-stu-id="5f304-215">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5f304-216">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5f304-216">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5f304-217">ASP.NET Core Id は、ASP.NET Core アプリにログイン機能を追加するメンバーシップシステムです。</span><span class="sxs-lookup"><span data-stu-id="5f304-217">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="5f304-218">ユーザーは、Id に格納されているログイン情報を持つアカウントを作成することも、外部ログインプロバイダーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="5f304-218">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="5f304-219">サポートされている外部ログインプロバイダーには、 [Facebook、Google、Microsoft アカウント、Twitter](xref:security/authentication/social/index)があります。</span><span class="sxs-lookup"><span data-stu-id="5f304-219">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="5f304-220">Id は、SQL Server データベースを使用して、ユーザー名、パスワード、およびプロファイルデータを格納するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="5f304-220">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="5f304-221">別の永続ストアを使用することもできます (たとえば、Azure Table Storage)。</span><span class="sxs-lookup"><span data-stu-id="5f304-221">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="5f304-222">サンプルコード ([ダウンロード方法)](xref:index#how-to-download-a-sample)を[表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/)します。</span><span class="sxs-lookup"><span data-stu-id="5f304-222">[View or download the sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="5f304-223">このトピックでは、Identity を使用してユーザーを登録、ログイン、ログアウトする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="5f304-223">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="5f304-224">Id を使用するアプリを作成する方法の詳細については、この記事の最後にある「次のステップ」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f304-224">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="5f304-225">AddDefaultIdentity と AddIdentity</span><span class="sxs-lookup"><span data-stu-id="5f304-225">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="5f304-226"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> は ASP.NET Core 2.1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="5f304-226"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="5f304-227">`AddDefaultIdentity` の呼び出しは、次の呼び出しに似ています。</span><span class="sxs-lookup"><span data-stu-id="5f304-227">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="5f304-228">詳細については、「 [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f304-228">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="5f304-229">認証を使用して Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="5f304-229">Create a Web app with authentication</span></span>

<span data-ttu-id="5f304-230">個別のユーザー アカウントを使って、ASP.NET Core Web アプリケーション プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="5f304-230">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5f304-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5f304-231">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5f304-232">[**ファイル**>**新しい**>**プロジェクト**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="5f304-232">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="5f304-233">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5f304-233">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="5f304-234">プロジェクトに**WebApp1**という名前を付け、プロジェクトのダウンロードと同じ名前空間にします。</span><span class="sxs-lookup"><span data-stu-id="5f304-234">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="5f304-235">**[OK]** をクリックすると、</span><span class="sxs-lookup"><span data-stu-id="5f304-235">Click **OK**.</span></span>
* <span data-ttu-id="5f304-236">ASP.NET Core **Web アプリケーション**を選択し、 **[認証の変更]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5f304-236">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="5f304-237">**個々のユーザーアカウント**を選択し、[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="5f304-237">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="5f304-238">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5f304-238">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="5f304-239">生成されたプロジェクトは、 [ASP.NET Core id](xref:security/authentication/identity)を[Razor クラスライブラリ](xref:razor-pages/ui-class)として提供します。</span><span class="sxs-lookup"><span data-stu-id="5f304-239">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="5f304-240">Identity Razor クラスライブラリは、`Identity` 領域でエンドポイントを公開します。</span><span class="sxs-lookup"><span data-stu-id="5f304-240">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="5f304-241">例 :</span><span class="sxs-lookup"><span data-stu-id="5f304-241">For example:</span></span>

* <span data-ttu-id="5f304-242">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="5f304-242">/Identity/Account/Login</span></span>
* <span data-ttu-id="5f304-243">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="5f304-243">/Identity/Account/Logout</span></span>
* <span data-ttu-id="5f304-244">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="5f304-244">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="5f304-245">移行を適用する</span><span class="sxs-lookup"><span data-stu-id="5f304-245">Apply migrations</span></span>

<span data-ttu-id="5f304-246">移行を適用してデータベースを初期化します。</span><span class="sxs-lookup"><span data-stu-id="5f304-246">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5f304-247">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5f304-247">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5f304-248">パッケージマネージャーコンソール (PMC) で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5f304-248">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="5f304-249">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5f304-249">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="5f304-250">テストレジスタとログイン</span><span class="sxs-lookup"><span data-stu-id="5f304-250">Test Register and Login</span></span>

<span data-ttu-id="5f304-251">アプリを実行し、ユーザーを登録します。</span><span class="sxs-lookup"><span data-stu-id="5f304-251">Run the app and register a user.</span></span> <span data-ttu-id="5f304-252">画面のサイズによっては、[ナビゲーション] トグルボタンを選択して、**登録**リンクと**ログイン**リンクを表示する必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="5f304-252">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="5f304-253">Identity サービスを構成します。</span><span class="sxs-lookup"><span data-stu-id="5f304-253">Configure Identity services</span></span>

<span data-ttu-id="5f304-254">サービスは `ConfigureServices`に追加されます。</span><span class="sxs-lookup"><span data-stu-id="5f304-254">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="5f304-255">一般的なパターンは、すべての `Add{Service}` メソッドを呼び出した後、すべての `services.Configure{Service}` メソッドを呼び出すことです。</span><span class="sxs-lookup"><span data-stu-id="5f304-255">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="5f304-256">上記のコードでは、既定のオプション値で Identity を構成します。</span><span class="sxs-lookup"><span data-stu-id="5f304-256">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="5f304-257">サービスは、[依存関係の挿入](xref:fundamentals/dependency-injection)によってアプリで使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="5f304-257">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="5f304-258">Id は、 [Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)を呼び出すことによって有効になります。</span><span class="sxs-lookup"><span data-stu-id="5f304-258">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="5f304-259">`UseAuthentication` は、認証[ミドルウェア](xref:fundamentals/middleware/index)を要求パイプラインに追加します。</span><span class="sxs-lookup"><span data-stu-id="5f304-259">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="5f304-260">詳細については、「[クラス](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)と[アプリケーションの起動](xref:fundamentals/startup)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f304-260">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="5f304-261">スキャフォールディング Register、Login、および LogOut</span><span class="sxs-lookup"><span data-stu-id="5f304-261">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="5f304-262">スキャフォールディング id に従って、このセクションに示されているコードを生成するための[承認命令を含む Razor プロジェクトに](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)従います。</span><span class="sxs-lookup"><span data-stu-id="5f304-262">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5f304-263">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5f304-263">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5f304-264">Register、Login、LogOut のファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="5f304-264">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="5f304-265">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5f304-265">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5f304-266">**WebApp1**という名前のプロジェクトを作成した場合は、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5f304-266">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="5f304-267">それ以外の場合は、`ApplicationDbContext`に適切な名前空間を使用します。</span><span class="sxs-lookup"><span data-stu-id="5f304-267">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="5f304-268">PowerShell では、コマンドの区切り記号としてセミコロンを使用します。</span><span class="sxs-lookup"><span data-stu-id="5f304-268">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="5f304-269">PowerShell を使用する場合は、前の例に示したように、ファイルリスト内のセミコロンをエスケープするか、ファイルリストを二重引用符で囲みます。</span><span class="sxs-lookup"><span data-stu-id="5f304-269">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="5f304-270">レジスタの確認</span><span class="sxs-lookup"><span data-stu-id="5f304-270">Examine Register</span></span>

<span data-ttu-id="5f304-271">ユーザーが **[登録]** リンクをクリックすると、`RegisterModel.OnPostAsync` アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="5f304-271">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="5f304-272">ユーザーは、`_userManager` オブジェクトに対して[Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)によって作成されます。</span><span class="sxs-lookup"><span data-stu-id="5f304-272">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="5f304-273">`_userManager` は、依存関係の挿入によって提供されます)。</span><span class="sxs-lookup"><span data-stu-id="5f304-273">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="5f304-274">ユーザーが正常に作成された場合は、`_signInManager.SignInAsync`の呼び出しによってユーザーがログインします。</span><span class="sxs-lookup"><span data-stu-id="5f304-274">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="5f304-275">**注:** 登録時にすぐにログインできないようにする手順については、「[アカウントの確認](xref:security/authentication/accconfirm#prevent-login-at-registration)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="5f304-275">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="5f304-276">ログイン</span><span class="sxs-lookup"><span data-stu-id="5f304-276">Log in</span></span>

<span data-ttu-id="5f304-277">ログイン フォームは次の時に表示されます :</span><span class="sxs-lookup"><span data-stu-id="5f304-277">The Login form is displayed when:</span></span>

* <span data-ttu-id="5f304-278">**ログイン**リンクが選択されています。</span><span class="sxs-lookup"><span data-stu-id="5f304-278">The **Log in** link is selected.</span></span>
* <span data-ttu-id="5f304-279">ユーザーがアクセスを承認されていない、**または**システムによって認証されていない制限付きページにアクセスしようとしています。</span><span class="sxs-lookup"><span data-stu-id="5f304-279">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="5f304-280">ログインページのフォームが送信されると、`OnPostAsync` アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="5f304-280">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="5f304-281">`PasswordSignInAsync` は、(依存関係の挿入によって提供される) `_signInManager` オブジェクトで呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="5f304-281">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="5f304-282">基本 `Controller` クラスは、コントローラーメソッドからアクセスできる `User` プロパティを公開します。</span><span class="sxs-lookup"><span data-stu-id="5f304-282">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="5f304-283">たとえば、`User.Claims` を列挙し、承認の決定を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="5f304-283">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="5f304-284">詳細については、「 <xref:security/authorization/introduction>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f304-284">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="5f304-285">ログアウト</span><span class="sxs-lookup"><span data-stu-id="5f304-285">Log out</span></span>

<span data-ttu-id="5f304-286">**Log out**リンクは、`LogoutModel.OnPost` アクションを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5f304-286">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="5f304-287">[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync)者は、cookie に格納されているユーザーの要求をクリアします。</span><span class="sxs-lookup"><span data-stu-id="5f304-287">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="5f304-288">Post は*Pages/Shared/_LoginPartial*に指定されています。</span><span class="sxs-lookup"><span data-stu-id="5f304-288">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="5f304-289">Identity のテスト</span><span class="sxs-lookup"><span data-stu-id="5f304-289">Test Identity</span></span>

<span data-ttu-id="5f304-290">既定の web プロジェクト テンプレートは、ホーム ページへの匿名アクセスを許可しています。</span><span class="sxs-lookup"><span data-stu-id="5f304-290">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="5f304-291">Id をテストするには、[プライバシー] ページに[`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute)を追加します。</span><span class="sxs-lookup"><span data-stu-id="5f304-291">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="5f304-292">サインインしている場合は、サインアウトします。アプリを実行し、 **[プライバシー]** リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="5f304-292">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="5f304-293">ログイン ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="5f304-293">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="5f304-294">Identity を調べる</span><span class="sxs-lookup"><span data-stu-id="5f304-294">Explore Identity</span></span>

<span data-ttu-id="5f304-295">Identity についてさらに詳しく調べるには :</span><span class="sxs-lookup"><span data-stu-id="5f304-295">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="5f304-296">完全な id UI ソースの作成</span><span class="sxs-lookup"><span data-stu-id="5f304-296">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="5f304-297">各ページのソースを確認し、デバッガーでステップ実行します。</span><span class="sxs-lookup"><span data-stu-id="5f304-297">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="5f304-298">Identity コンポーネント</span><span class="sxs-lookup"><span data-stu-id="5f304-298">Identity Components</span></span>

<span data-ttu-id="5f304-299">すべての Id 依存 NuGet パッケージは、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="5f304-299">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="5f304-300">Id のプライマリパッケージは[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)です。</span><span class="sxs-lookup"><span data-stu-id="5f304-300">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="5f304-301">このパッケージには ASP.NET Core Id のインターフェイスのコアセットが含まれており、`Microsoft.AspNetCore.Identity.EntityFrameworkCore`によって含まれています。</span><span class="sxs-lookup"><span data-stu-id="5f304-301">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="5f304-302">ASP.NET Core Id への移行</span><span class="sxs-lookup"><span data-stu-id="5f304-302">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="5f304-303">既存の Id ストアを移行する方法の詳細とガイダンスについては、「[認証と id の移行](xref:migration/identity)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f304-303">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="5f304-304">パスワードの強度を設定する</span><span class="sxs-lookup"><span data-stu-id="5f304-304">Setting password strength</span></span>

<span data-ttu-id="5f304-305">パスワードの最小要件を設定するサンプルについては、「[構成](#pw)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f304-305">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="5f304-306">次の手順</span><span class="sxs-lookup"><span data-stu-id="5f304-306">Next Steps</span></span>

* <span data-ttu-id="5f304-307">SQLite を使用して Id を構成する方法については、 [GitHub の問題](https://github.com/aspnet/AspNetCore.Docs/issues/5131)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f304-307">See [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="5f304-308">Identity の構成</span><span class="sxs-lookup"><span data-stu-id="5f304-308">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
