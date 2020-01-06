---
title: ASP.NET Core Identity の概要
author: rick-anderson
description: ASP.NET Core アプリで Id を使用します。 パスワードの要件 (RequireDigit、RequiredLength、RequiredUniqueChars など) を設定する方法について説明します。
ms.author: riande
ms.date: 12/05/2019
uid: security/authentication/identity
ms.openlocfilehash: 787d39dd7824f912128e6af849fa268c3e8eb908
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75359200"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="2255e-104">ASP.NET Core Identity の概要</span><span class="sxs-lookup"><span data-stu-id="2255e-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2255e-105">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2255e-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2255e-106">ASP.NET Core Id:</span><span class="sxs-lookup"><span data-stu-id="2255e-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="2255e-107">は、ユーザーインターフェイス (UI) のログイン機能をサポートする API です。</span><span class="sxs-lookup"><span data-stu-id="2255e-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="2255e-108">ユーザー、パスワード、プロファイルデータ、ロール、要求、トークン、電子メールの確認などを管理します。</span><span class="sxs-lookup"><span data-stu-id="2255e-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="2255e-109">ユーザーは、Id に格納されているログイン情報を持つアカウントを作成することも、外部ログインプロバイダーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="2255e-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="2255e-110">サポートされている外部ログインプロバイダーには、 [Facebook、Google、Microsoft アカウント、Twitter](xref:security/authentication/social/index)があります。</span><span class="sxs-lookup"><span data-stu-id="2255e-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="2255e-111">[Id ソースコード](https://github.com/aspnet/AspNetCore/tree/master/src/Identity)は、GitHub で入手できます。</span><span class="sxs-lookup"><span data-stu-id="2255e-111">The [Identity source code](https://github.com/aspnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="2255e-112">[スキャフォールディング](xref:security/authentication/scaffold-identity)は、id とのテンプレートの対話を確認するために、生成されたファイルを識別して表示します。</span><span class="sxs-lookup"><span data-stu-id="2255e-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="2255e-113">Id は、通常、ユーザー名、パスワード、およびプロファイルデータを格納するために SQL Server データベースを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="2255e-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="2255e-114">別の永続ストアを使用することもできます (たとえば、Azure Table Storage)。</span><span class="sxs-lookup"><span data-stu-id="2255e-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="2255e-115">このトピックでは、Identity を使用してユーザーを登録、ログイン、ログアウトする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="2255e-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="2255e-116">Id を使用するアプリを作成する方法の詳細については、この記事の最後にある「次のステップ」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2255e-116">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="2255e-117">[Microsoft id プラットフォーム](/azure/active-directory/develop/)は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="2255e-117">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="2255e-118">Azure Active Directory (Azure AD) 開発プラットフォームの進化。</span><span class="sxs-lookup"><span data-stu-id="2255e-118">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="2255e-119">ASP.NET Core Id に関連付けられていません。</span><span class="sxs-lookup"><span data-stu-id="2255e-119">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="2255e-120">サンプルコード ([ダウンロード方法)](xref:index#how-to-download-a-sample)を[表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample)します。</span><span class="sxs-lookup"><span data-stu-id="2255e-120">[View or download the sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="2255e-121">認証を使用して Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="2255e-121">Create a Web app with authentication</span></span>

<span data-ttu-id="2255e-122">個別のユーザー アカウントを使って、ASP.NET Core Web アプリケーション プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="2255e-122">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2255e-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2255e-123">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2255e-124">[**ファイル**>**新しい**>**プロジェクト**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="2255e-124">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="2255e-125">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2255e-125">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="2255e-126">プロジェクトに**WebApp1**という名前を付け、プロジェクトのダウンロードと同じ名前空間にします。</span><span class="sxs-lookup"><span data-stu-id="2255e-126">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="2255e-127">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2255e-127">Click **OK**.</span></span>
* <span data-ttu-id="2255e-128">ASP.NET Core **Web アプリケーション**を選択し、 **[認証の変更]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2255e-128">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="2255e-129">個別のユーザー アカウントを **選択** して **OK** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2255e-129">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="2255e-130">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2255e-130">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="2255e-131">上記のコマンドは、SQLite を使用して Razor web アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="2255e-131">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="2255e-132">LocalDB を使用して web アプリを作成するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2255e-132">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="2255e-133">生成されたプロジェクトは、 [ASP.NET Core Identity](xref:security/authentication/identity)を [Razor クラス ライブラリ](xref:razor-pages/ui-class)として提供します。</span><span class="sxs-lookup"><span data-stu-id="2255e-133">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="2255e-134">Identity の Razor クラス ライブラリは`Identity`エリアでエンドポイントを公開します。</span><span class="sxs-lookup"><span data-stu-id="2255e-134">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="2255e-135">例:</span><span class="sxs-lookup"><span data-stu-id="2255e-135">For example:</span></span>

* <span data-ttu-id="2255e-136">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="2255e-136">/Identity/Account/Login</span></span>
* <span data-ttu-id="2255e-137">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="2255e-137">/Identity/Account/Logout</span></span>
* <span data-ttu-id="2255e-138">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="2255e-138">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="2255e-139">移行を適用する</span><span class="sxs-lookup"><span data-stu-id="2255e-139">Apply migrations</span></span>

<span data-ttu-id="2255e-140">移行を適用してデータベースを初期化します。</span><span class="sxs-lookup"><span data-stu-id="2255e-140">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2255e-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2255e-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2255e-142">パッケージマネージャーコンソール (PMC) で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2255e-142">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="2255e-143">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2255e-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="2255e-144">SQLite を使用する場合、この手順で移行する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="2255e-144">Migrations are not necessary at this step when using SQLite.</span></span> <span data-ttu-id="2255e-145">LocalDB の場合は、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2255e-145">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="2255e-146">テストレジスタとログイン</span><span class="sxs-lookup"><span data-stu-id="2255e-146">Test Register and Login</span></span>

<span data-ttu-id="2255e-147">アプリを実行し、ユーザーを登録します。</span><span class="sxs-lookup"><span data-stu-id="2255e-147">Run the app and register a user.</span></span> <span data-ttu-id="2255e-148">画面サイズによっては、**登録**と**ログイン**のリンクを表示するためにナビゲーションのトグル ボタンを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2255e-148">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="2255e-149">Identity サービスを構成します。</span><span class="sxs-lookup"><span data-stu-id="2255e-149">Configure Identity services</span></span>

<span data-ttu-id="2255e-150">サービスは`ConfigureServices`で追加されます。</span><span class="sxs-lookup"><span data-stu-id="2255e-150">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="2255e-151">一般的なパターンは、すべての `Add{Service}` メソッドを呼び出した後、すべての `services.Configure{Service}` メソッドを呼び出すことです。</span><span class="sxs-lookup"><span data-stu-id="2255e-151">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="2255e-152">上の強調表示されたコードは、既定のオプション値を使用して Id を構成します。</span><span class="sxs-lookup"><span data-stu-id="2255e-152">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="2255e-153">サービスは[依存関係の注入](xref:fundamentals/dependency-injection)を通してアプリで利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="2255e-153">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="2255e-154">Id は <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>を呼び出すことによって有効になります。</span><span class="sxs-lookup"><span data-stu-id="2255e-154">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="2255e-155">`UseAuthentication` は認証[ミドルウェア](xref:fundamentals/middleware/index)を要求パイプラインに追加します。</span><span class="sxs-lookup"><span data-stu-id="2255e-155">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="2255e-156">テンプレートで生成されたアプリは、[承認](xref:security/authorization/secure-data)を使用しません。</span><span class="sxs-lookup"><span data-stu-id="2255e-156">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="2255e-157">アプリが承認を追加する正しい順序で追加されるように `app.UseAuthorization` が含まれています。</span><span class="sxs-lookup"><span data-stu-id="2255e-157">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="2255e-158">`UseRouting`、`UseAuthentication`、`UseAuthorization`、および `UseEndpoints` は、前のコードに示されている順序で呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="2255e-158">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="2255e-159">`IdentityOptions` と `Startup`の詳細については、「<xref:Microsoft.AspNetCore.Identity.IdentityOptions> と[アプリケーションの起動](xref:fundamentals/startup)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2255e-159">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="2255e-160">スキャフォールディング Register、Login、および LogOut</span><span class="sxs-lookup"><span data-stu-id="2255e-160">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2255e-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2255e-161">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2255e-162">Register、Login、LogOut のファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="2255e-162">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="2255e-163">[id の権限を持つ Razor プロジェクトにスキャフォールディング](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)の手順に従って、このセクションで示すコードを生成してください。</span><span class="sxs-lookup"><span data-stu-id="2255e-163">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="2255e-164">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2255e-164">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="2255e-165">**WebApp1**という名前のプロジェクトを作成した場合、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2255e-165">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="2255e-166">それ以外の場合は、`ApplicationDbContext`に正しい名前空間を適用してください :</span><span class="sxs-lookup"><span data-stu-id="2255e-166">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="2255e-167">PowerShell では、コマンドの区切り記号としてセミコロンを使用します。</span><span class="sxs-lookup"><span data-stu-id="2255e-167">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="2255e-168">PowerShell を使用する場合は、前の例に示したように、ファイルリスト内のセミコロンをエスケープするか、ファイルリストを二重引用符で囲みます。</span><span class="sxs-lookup"><span data-stu-id="2255e-168">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="2255e-169">スキャフォールディング Id の詳細については、「[スキャフォールディング identity to a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2255e-169">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="2255e-170">レジスタの確認</span><span class="sxs-lookup"><span data-stu-id="2255e-170">Examine Register</span></span>

<span data-ttu-id="2255e-171">ユーザーが**登録**リンクをクリックすると、`RegisterModel.OnPostAsync`アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2255e-171">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="2255e-172">ユーザーは`_userManager`オブジェクトの[CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)によって作成されます。</span><span class="sxs-lookup"><span data-stu-id="2255e-172">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="2255e-173">`_userManager` は依存関係の挿入によってよって提供されます :</span><span class="sxs-lookup"><span data-stu-id="2255e-173">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="2255e-174">ユーザーが正常に作成された場合、ユーザーは`_signInManager.SignInAsync`の呼び出しによってログインされます。</span><span class="sxs-lookup"><span data-stu-id="2255e-174">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="2255e-175">登録時にすぐにログインできないようにする手順については、「[アカウントの確認](xref:security/authentication/accconfirm#prevent-login-at-registration)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2255e-175">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="2255e-176">ログイン</span><span class="sxs-lookup"><span data-stu-id="2255e-176">Log in</span></span>

<span data-ttu-id="2255e-177">ログイン フォームは次の時に表示されます :</span><span class="sxs-lookup"><span data-stu-id="2255e-177">The Login form is displayed when:</span></span>

* <span data-ttu-id="2255e-178">**ログイン** リンクが選択されたとき。</span><span class="sxs-lookup"><span data-stu-id="2255e-178">The **Log in** link is selected.</span></span>
* <span data-ttu-id="2255e-179">ユーザーがまだシステムに認証されていない**または**アクセスする権限がない状態で、制限されているページにアクセスしようとしたとき。</span><span class="sxs-lookup"><span data-stu-id="2255e-179">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="2255e-180">ログイン ページのフォームが送信されたとき、`OnPostAsync`アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2255e-180">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="2255e-181">`_signInManager`オブジェクト(依存関係の挿入によって提供されます)の`PasswordSignInAsync`が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2255e-181">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="2255e-182">基本 `Controller` クラスは、コントローラーメソッドからアクセスできる `User` プロパティを公開します。</span><span class="sxs-lookup"><span data-stu-id="2255e-182">The base `Controller` class exposes a `User` property that can be accessed from controller methods.</span></span> <span data-ttu-id="2255e-183">たとえば`User.Claims`を列挙して承認の決定を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="2255e-183">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="2255e-184">詳細については、「 <xref:security/authorization/introduction>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2255e-184">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="2255e-185">ログアウト</span><span class="sxs-lookup"><span data-stu-id="2255e-185">Log out</span></span>

<span data-ttu-id="2255e-186">**ログアウト**リンクから`LogoutModel.OnPost`アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2255e-186">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="2255e-187">前のコードでは、ブラウザーが新しい要求を実行し、ユーザーの id が更新されるように、コード `return RedirectToPage();` がリダイレクトである必要があります。</span><span class="sxs-lookup"><span data-stu-id="2255e-187">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="2255e-188">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) はcookie に格納されているユーザー要求をクリアします。</span><span class="sxs-lookup"><span data-stu-id="2255e-188">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="2255e-189">Postは *Pages/Shared/_LoginPartial.cshtml* で指定されています :</span><span class="sxs-lookup"><span data-stu-id="2255e-189">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="2255e-190">Identity のテスト</span><span class="sxs-lookup"><span data-stu-id="2255e-190">Test Identity</span></span>

<span data-ttu-id="2255e-191">既定の web プロジェクト テンプレートは、ホーム ページへの匿名アクセスを許可しています。</span><span class="sxs-lookup"><span data-stu-id="2255e-191">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="2255e-192">Id をテストするには、 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)を追加します。</span><span class="sxs-lookup"><span data-stu-id="2255e-192">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="2255e-193">サインインしている場合は、サインアウトします。アプリを実行し、 **[プライバシー]** リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="2255e-193">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="2255e-194">ログイン ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="2255e-194">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="2255e-195">Identity を調べる</span><span class="sxs-lookup"><span data-stu-id="2255e-195">Explore Identity</span></span>

<span data-ttu-id="2255e-196">Identity についてさらに詳しく調べるには :</span><span class="sxs-lookup"><span data-stu-id="2255e-196">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="2255e-197">完全な Identity の UI のソースを作成します。</span><span class="sxs-lookup"><span data-stu-id="2255e-197">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="2255e-198">各ページのソースを確認し、デバッガーでステップ実行します。</span><span class="sxs-lookup"><span data-stu-id="2255e-198">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="2255e-199">Identity コンポーネント</span><span class="sxs-lookup"><span data-stu-id="2255e-199">Identity Components</span></span>

<span data-ttu-id="2255e-200">すべての Id に依存する NuGet パッケージは、 [ASP.NET Core 共有フレームワーク](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="2255e-200">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="2255e-201">Id のプライマリパッケージは[Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)です。</span><span class="sxs-lookup"><span data-stu-id="2255e-201">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="2255e-202">このパッケージは ASP.NET Core Identity のインターフェイスのコアセットを含んでいて、また`Microsoft.AspNetCore.Identity.EntityFrameworkCore`に含まれています。</span><span class="sxs-lookup"><span data-stu-id="2255e-202">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="2255e-203">ASP.NET Core Id への移行</span><span class="sxs-lookup"><span data-stu-id="2255e-203">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="2255e-204">既存の Identity ストアの移行についての詳細とガイダンスについては、次を参照してください : [移行の認証と Id](xref:migration/identity)。</span><span class="sxs-lookup"><span data-stu-id="2255e-204">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="2255e-205">パスワードの強度を設定する</span><span class="sxs-lookup"><span data-stu-id="2255e-205">Setting password strength</span></span>

<span data-ttu-id="2255e-206">パスワードの最小要件を設定するサンプルについては[構成](#pw)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2255e-206">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="2255e-207">AddDefaultIdentity と AddIdentity</span><span class="sxs-lookup"><span data-stu-id="2255e-207">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="2255e-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> は ASP.NET Core 2.1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="2255e-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="2255e-209">`AddDefaultIdentity`の呼び出しは、次の呼び出しに似ています。</span><span class="sxs-lookup"><span data-stu-id="2255e-209">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="2255e-210">詳細については[AddDefaultIdentity のソース](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2255e-210">See [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2255e-211">次のステップ</span><span class="sxs-lookup"><span data-stu-id="2255e-211">Next Steps</span></span>

* <span data-ttu-id="2255e-212">SQLite を使用して Id を構成する方法については、 [GitHub の問題](https://github.com/aspnet/AspNetCore.Docs/issues/5131)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2255e-212">See [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="2255e-213">Identity の構成</span><span class="sxs-lookup"><span data-stu-id="2255e-213">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2255e-214">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2255e-214">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2255e-215">ASP.NET Core Id は、ASP.NET Core アプリにログイン機能を追加するメンバーシップシステムです。</span><span class="sxs-lookup"><span data-stu-id="2255e-215">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="2255e-216">ユーザーは、Id に格納されているログイン情報を持つアカウントを作成することも、外部ログインプロバイダーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="2255e-216">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="2255e-217">サポートされている外部ログインプロバイダーには、 [Facebook、Google、Microsoft アカウント、Twitter](xref:security/authentication/social/index)があります。</span><span class="sxs-lookup"><span data-stu-id="2255e-217">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="2255e-218">Id は、SQL Server データベースを使用して、ユーザー名、パスワード、およびプロファイルデータを格納するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="2255e-218">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="2255e-219">別の永続ストアを使用することもできます (たとえば、Azure Table Storage)。</span><span class="sxs-lookup"><span data-stu-id="2255e-219">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="2255e-220">サンプルコード ([ダウンロード方法)](xref:index#how-to-download-a-sample)を[表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/)します。</span><span class="sxs-lookup"><span data-stu-id="2255e-220">[View or download the sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="2255e-221">このトピックでは、Identity を使用してユーザーを登録、ログイン、ログアウトする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="2255e-221">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="2255e-222">Id を使用するアプリを作成する方法の詳細については、この記事の最後にある「次のステップ」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2255e-222">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="2255e-223">AddDefaultIdentity と AddIdentity</span><span class="sxs-lookup"><span data-stu-id="2255e-223">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="2255e-224"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> は ASP.NET Core 2.1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="2255e-224"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="2255e-225">`AddDefaultIdentity`の呼び出しは、次の呼び出しに似ています。</span><span class="sxs-lookup"><span data-stu-id="2255e-225">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="2255e-226">詳細については[AddDefaultIdentity のソース](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2255e-226">See [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="2255e-227">認証を使用して Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="2255e-227">Create a Web app with authentication</span></span>

<span data-ttu-id="2255e-228">個別のユーザー アカウントを使って、ASP.NET Core Web アプリケーション プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="2255e-228">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2255e-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2255e-229">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2255e-230">[**ファイル**>**新しい**>**プロジェクト**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="2255e-230">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="2255e-231">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2255e-231">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="2255e-232">プロジェクトに**WebApp1**という名前を付け、プロジェクトのダウンロードと同じ名前空間にします。</span><span class="sxs-lookup"><span data-stu-id="2255e-232">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="2255e-233">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2255e-233">Click **OK**.</span></span>
* <span data-ttu-id="2255e-234">ASP.NET Core **Web アプリケーション**を選択し、 **[認証の変更]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2255e-234">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="2255e-235">個別のユーザー アカウントを **選択** して **OK** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2255e-235">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="2255e-236">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2255e-236">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="2255e-237">生成されたプロジェクトは、 [ASP.NET Core Identity](xref:security/authentication/identity)を [Razor クラス ライブラリ](xref:razor-pages/ui-class)として提供します。</span><span class="sxs-lookup"><span data-stu-id="2255e-237">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="2255e-238">Identity の Razor クラス ライブラリは`Identity`エリアでエンドポイントを公開します。</span><span class="sxs-lookup"><span data-stu-id="2255e-238">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="2255e-239">例:</span><span class="sxs-lookup"><span data-stu-id="2255e-239">For example:</span></span>

* <span data-ttu-id="2255e-240">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="2255e-240">/Identity/Account/Login</span></span>
* <span data-ttu-id="2255e-241">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="2255e-241">/Identity/Account/Logout</span></span>
* <span data-ttu-id="2255e-242">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="2255e-242">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="2255e-243">移行を適用する</span><span class="sxs-lookup"><span data-stu-id="2255e-243">Apply migrations</span></span>

<span data-ttu-id="2255e-244">移行を適用してデータベースを初期化します。</span><span class="sxs-lookup"><span data-stu-id="2255e-244">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2255e-245">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2255e-245">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2255e-246">パッケージマネージャーコンソール (PMC) で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2255e-246">Run the following command in the Package Manager Console (PMC):</span></span>

```PM> Update-Database```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="2255e-247">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2255e-247">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="2255e-248">テストレジスタとログイン</span><span class="sxs-lookup"><span data-stu-id="2255e-248">Test Register and Login</span></span>

<span data-ttu-id="2255e-249">アプリを実行し、ユーザーを登録します。</span><span class="sxs-lookup"><span data-stu-id="2255e-249">Run the app and register a user.</span></span> <span data-ttu-id="2255e-250">画面サイズによっては、**登録**と**ログイン**のリンクを表示するためにナビゲーションのトグル ボタンを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2255e-250">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="2255e-251">Identity サービスを構成します。</span><span class="sxs-lookup"><span data-stu-id="2255e-251">Configure Identity services</span></span>

<span data-ttu-id="2255e-252">サービスは`ConfigureServices`で追加されます。</span><span class="sxs-lookup"><span data-stu-id="2255e-252">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="2255e-253">一般的なパターンは、すべての `Add{Service}` メソッドを呼び出した後、すべての `services.Configure{Service}` メソッドを呼び出すことです。</span><span class="sxs-lookup"><span data-stu-id="2255e-253">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="2255e-254">上記のコードでは、既定のオプション値で Identity を構成します。</span><span class="sxs-lookup"><span data-stu-id="2255e-254">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="2255e-255">サービスは[依存関係の注入](xref:fundamentals/dependency-injection)を通してアプリで利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="2255e-255">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="2255e-256">Identity は[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)を呼び出すことによって有効になります。</span><span class="sxs-lookup"><span data-stu-id="2255e-256">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="2255e-257">`UseAuthentication` は認証[ミドルウェア](xref:fundamentals/middleware/index)を要求パイプラインに追加します。</span><span class="sxs-lookup"><span data-stu-id="2255e-257">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="2255e-258">詳細については、次の [IdentityOptions クラス](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)と[アプリケーションの起動](xref:fundamentals/startup) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2255e-258">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="2255e-259">スキャフォールディング Register、Login、および LogOut</span><span class="sxs-lookup"><span data-stu-id="2255e-259">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="2255e-260">[id の権限を持つ Razor プロジェクトにスキャフォールディング](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)の手順に従って、このセクションで示すコードを生成してください。</span><span class="sxs-lookup"><span data-stu-id="2255e-260">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2255e-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2255e-261">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2255e-262">Register、Login、LogOut のファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="2255e-262">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="2255e-263">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2255e-263">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="2255e-264">**WebApp1**という名前のプロジェクトを作成した場合、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2255e-264">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="2255e-265">それ以外の場合は、`ApplicationDbContext`に正しい名前空間を適用してください :</span><span class="sxs-lookup"><span data-stu-id="2255e-265">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="2255e-266">PowerShell では、コマンドの区切り記号としてセミコロンを使用します。</span><span class="sxs-lookup"><span data-stu-id="2255e-266">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="2255e-267">PowerShell を使用する場合は、前の例に示したように、ファイルリスト内のセミコロンをエスケープするか、ファイルリストを二重引用符で囲みます。</span><span class="sxs-lookup"><span data-stu-id="2255e-267">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="2255e-268">レジスタの確認</span><span class="sxs-lookup"><span data-stu-id="2255e-268">Examine Register</span></span>

<span data-ttu-id="2255e-269">ユーザーが**登録**リンクをクリックすると、`RegisterModel.OnPostAsync`アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2255e-269">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="2255e-270">ユーザーは`_userManager`オブジェクトの[CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)によって作成されます。</span><span class="sxs-lookup"><span data-stu-id="2255e-270">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="2255e-271">`_userManager` は依存関係の挿入によってよって提供されます :</span><span class="sxs-lookup"><span data-stu-id="2255e-271">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="2255e-272">ユーザーが正常に作成された場合、ユーザーは`_signInManager.SignInAsync`の呼び出しによってログインされます。</span><span class="sxs-lookup"><span data-stu-id="2255e-272">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="2255e-273">**注:** 登録時にすぐにログインできないようにする手順については、「[アカウントの確認](xref:security/authentication/accconfirm#prevent-login-at-registration)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2255e-273">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="2255e-274">ログイン</span><span class="sxs-lookup"><span data-stu-id="2255e-274">Log in</span></span>

<span data-ttu-id="2255e-275">ログイン フォームは次の時に表示されます :</span><span class="sxs-lookup"><span data-stu-id="2255e-275">The Login form is displayed when:</span></span>

* <span data-ttu-id="2255e-276">**ログイン** リンクが選択されたとき。</span><span class="sxs-lookup"><span data-stu-id="2255e-276">The **Log in** link is selected.</span></span>
* <span data-ttu-id="2255e-277">ユーザーがまだシステムに認証されていない**または**アクセスする権限がない状態で、制限されているページにアクセスしようとしたとき。</span><span class="sxs-lookup"><span data-stu-id="2255e-277">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="2255e-278">ログイン ページのフォームが送信されたとき、`OnPostAsync`アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2255e-278">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="2255e-279">`_signInManager`オブジェクト(依存関係の挿入によって提供されます)の`PasswordSignInAsync`が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2255e-279">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="2255e-280">ベース`Controller`クラスでは、コントローラー メソッドからアクセスできる`User`プロパティを公開します。</span><span class="sxs-lookup"><span data-stu-id="2255e-280">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="2255e-281">たとえば`User.Claims`を列挙して承認の決定を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="2255e-281">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="2255e-282">詳細については、「 <xref:security/authorization/introduction>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2255e-282">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="2255e-283">ログアウト</span><span class="sxs-lookup"><span data-stu-id="2255e-283">Log out</span></span>

<span data-ttu-id="2255e-284">**ログアウト**リンクから`LogoutModel.OnPost`アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2255e-284">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="2255e-285">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) はcookie に格納されているユーザー要求をクリアします。</span><span class="sxs-lookup"><span data-stu-id="2255e-285">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="2255e-286">Postは *Pages/Shared/_LoginPartial.cshtml* で指定されています :</span><span class="sxs-lookup"><span data-stu-id="2255e-286">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="2255e-287">Identity のテスト</span><span class="sxs-lookup"><span data-stu-id="2255e-287">Test Identity</span></span>

<span data-ttu-id="2255e-288">既定の web プロジェクト テンプレートは、ホーム ページへの匿名アクセスを許可しています。</span><span class="sxs-lookup"><span data-stu-id="2255e-288">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="2255e-289">Identity をテストするために、[ `[Authorize]` ](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute)をPrivacy ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="2255e-289">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="2255e-290">サインインしている場合は、サインアウトします。アプリを実行し、 **[プライバシー]** リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="2255e-290">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="2255e-291">ログイン ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="2255e-291">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="2255e-292">Identity を調べる</span><span class="sxs-lookup"><span data-stu-id="2255e-292">Explore Identity</span></span>

<span data-ttu-id="2255e-293">Identity についてさらに詳しく調べるには :</span><span class="sxs-lookup"><span data-stu-id="2255e-293">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="2255e-294">完全な Identity の UI のソースを作成します。</span><span class="sxs-lookup"><span data-stu-id="2255e-294">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="2255e-295">各ページのソースを確認し、デバッガーでステップ実行します。</span><span class="sxs-lookup"><span data-stu-id="2255e-295">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="2255e-296">Identity コンポーネント</span><span class="sxs-lookup"><span data-stu-id="2255e-296">Identity Components</span></span>

<span data-ttu-id="2255e-297">すべての Id 依存の NuGet パッケージは[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれます。</span><span class="sxs-lookup"><span data-stu-id="2255e-297">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="2255e-298">Id のプライマリパッケージは[Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)です。</span><span class="sxs-lookup"><span data-stu-id="2255e-298">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="2255e-299">このパッケージは ASP.NET Core Identity のインターフェイスのコアセットを含んでいて、また`Microsoft.AspNetCore.Identity.EntityFrameworkCore`に含まれています。</span><span class="sxs-lookup"><span data-stu-id="2255e-299">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="2255e-300">ASP.NET Core Id への移行</span><span class="sxs-lookup"><span data-stu-id="2255e-300">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="2255e-301">既存の Identity ストアの移行についての詳細とガイダンスについては、次を参照してください : [移行の認証と Id](xref:migration/identity)。</span><span class="sxs-lookup"><span data-stu-id="2255e-301">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="2255e-302">パスワードの強度を設定する</span><span class="sxs-lookup"><span data-stu-id="2255e-302">Setting password strength</span></span>

<span data-ttu-id="2255e-303">パスワードの最小要件を設定するサンプルについては[構成](#pw)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2255e-303">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2255e-304">次のステップ</span><span class="sxs-lookup"><span data-stu-id="2255e-304">Next Steps</span></span>

* <span data-ttu-id="2255e-305">SQLite を使用して Id を構成する方法については、 [GitHub の問題](https://github.com/aspnet/AspNetCore.Docs/issues/5131)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2255e-305">See [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="2255e-306">Identity の構成</span><span class="sxs-lookup"><span data-stu-id="2255e-306">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
