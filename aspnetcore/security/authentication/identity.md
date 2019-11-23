---
title: ASP.NET Core Identity の概要
author: rick-anderson
description: ASP.NET Core アプリで Id を使用します。 パスワードの要件 (RequireDigit、RequiredLength、RequiredUniqueChars など) を設定する方法について説明します。
ms.author: riande
ms.date: 10/15/2019
uid: security/authentication/identity
ms.openlocfilehash: 8da13ca5f74a9c829eb8137d33af0684ff88266d
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333551"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="5d3e7-104">ASP.NET Core Identity の概要</span><span class="sxs-lookup"><span data-stu-id="5d3e7-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5d3e7-105">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5d3e7-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5d3e7-106">ASP.NET Core Id は、ユーザーインターフェイス (UI) のログイン機能をサポートするメンバーシップシステムです。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-106">ASP.NET Core Identity is a membership system that supports user interface (UI) login functionality.</span></span> <span data-ttu-id="5d3e7-107">ユーザーは、Id に格納されているログイン情報を持つアカウントを作成することも、外部ログインプロバイダーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-107">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="5d3e7-108">サポートされている外部ログインプロバイダーには、 [Facebook、Google、Microsoft アカウント、Twitter](xref:security/authentication/social/index)があります。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-108">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="5d3e7-109">Id は、SQL Server データベースを使用して、ユーザー名、パスワード、およびプロファイルデータを格納するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-109">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="5d3e7-110">別の永続ストアを使用することもできます (たとえば、Azure Table Storage)。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-110">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="5d3e7-111">このトピックでは、Identity を使用してユーザーを登録、ログイン、ログアウトする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-111">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="5d3e7-112">Id を使用するアプリを作成する方法の詳細については、この記事の最後にある「次のステップ」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-112">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="5d3e7-113">サンプルコード ([ダウンロード方法)](xref:index#how-to-download-a-sample)を[表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample)します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-113">[View or download the sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="5d3e7-114">認証を使用して Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="5d3e7-114">Create a Web app with authentication</span></span>

<span data-ttu-id="5d3e7-115">個別のユーザー アカウントを使って、ASP.NET Core Web アプリケーション プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-115">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5d3e7-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d3e7-116">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5d3e7-117">[**ファイル**>**新しい**>**プロジェクト**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-117">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="5d3e7-118">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-118">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="5d3e7-119">プロジェクトに**WebApp1**という名前を付け、プロジェクトのダウンロードと同じ名前空間にします。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-119">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="5d3e7-120">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-120">Click **OK**.</span></span>
* <span data-ttu-id="5d3e7-121">ASP.NET Core **Web アプリケーション**を選択し、 **[認証の変更]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-121">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="5d3e7-122">個別のユーザー アカウントを **選択** して **OK** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-122">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="5d3e7-123">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5d3e7-123">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="5d3e7-124">上記のコマンドは、SQLite を使用して Razor web アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-124">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="5d3e7-125">LocalDB を使用して web アプリを作成するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-125">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="5d3e7-126">生成されたプロジェクトは、 [ASP.NET Core Identity](xref:security/authentication/identity)を [Razor クラス ライブラリ](xref:razor-pages/ui-class)として提供します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-126">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="5d3e7-127">Identity の Razor クラス ライブラリは`Identity`エリアでエンドポイントを公開します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-127">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="5d3e7-128">例 :</span><span class="sxs-lookup"><span data-stu-id="5d3e7-128">For example:</span></span>

* <span data-ttu-id="5d3e7-129">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="5d3e7-129">/Identity/Account/Login</span></span>
* <span data-ttu-id="5d3e7-130">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="5d3e7-130">/Identity/Account/Logout</span></span>
* <span data-ttu-id="5d3e7-131">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="5d3e7-131">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="5d3e7-132">移行を適用する</span><span class="sxs-lookup"><span data-stu-id="5d3e7-132">Apply migrations</span></span>

<span data-ttu-id="5d3e7-133">移行を適用してデータベースを初期化します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-133">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5d3e7-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d3e7-134">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5d3e7-135">パッケージマネージャーコンソール (PMC) で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-135">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="5d3e7-136">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5d3e7-136">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5d3e7-137">SQLite を使用する場合、この手順で移行する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-137">Migrations are not necessary at this step when using SQLite.</span></span> <span data-ttu-id="5d3e7-138">LocalDB の場合は、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-138">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="5d3e7-139">テストレジスタとログイン</span><span class="sxs-lookup"><span data-stu-id="5d3e7-139">Test Register and Login</span></span>

<span data-ttu-id="5d3e7-140">アプリを実行し、ユーザーを登録します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-140">Run the app and register a user.</span></span> <span data-ttu-id="5d3e7-141">画面サイズによっては、**登録**と**ログイン**のリンクを表示するためにナビゲーションのトグル ボタンを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-141">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="5d3e7-142">Identity サービスを構成します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-142">Configure Identity services</span></span>

<span data-ttu-id="5d3e7-143">サービスは`ConfigureServices`で追加されます。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-143">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="5d3e7-144">一般的なパターンは、すべての `Add{Service}` メソッドを呼び出した後、すべての `services.Configure{Service}` メソッドを呼び出すことです。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-144">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="5d3e7-145">上の強調表示されたコードは、既定のオプション値を使用して Id を構成します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-145">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="5d3e7-146">サービスは[依存関係の注入](xref:fundamentals/dependency-injection)を通してアプリで利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-146">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="5d3e7-147">Id は <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>を呼び出すことによって有効になります。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-147">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="5d3e7-148">`UseAuthentication` は認証[ミドルウェア](xref:fundamentals/middleware/index)を要求パイプラインに追加します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-148">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="5d3e7-149">テンプレートで生成されたアプリは、[承認](xref:security/authorization/secure-data)を使用しません。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-149">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="5d3e7-150">アプリが承認を追加する正しい順序で追加されるように `app.UseAuthorization` が含まれています。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-150">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="5d3e7-151">`UseRouting`、`UseAuthentication`、`UseAuthorization`、および `UseEndpoints` は、前のコードに示されている順序で呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-151">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="5d3e7-152">`IdentityOptions` と `Startup`の詳細については、「<xref:Microsoft.AspNetCore.Identity.IdentityOptions> と[アプリケーションの起動](xref:fundamentals/startup)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-152">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="5d3e7-153">スキャフォールディング Register、Login、および LogOut</span><span class="sxs-lookup"><span data-stu-id="5d3e7-153">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5d3e7-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d3e7-154">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5d3e7-155">Register、Login、LogOut のファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-155">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="5d3e7-156">[id の権限を持つ Razor プロジェクトにスキャフォールディング](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)の手順に従って、このセクションで示すコードを生成してください。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-156">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="5d3e7-157">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5d3e7-157">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5d3e7-158">**WebApp1**という名前のプロジェクトを作成した場合、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-158">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="5d3e7-159">それ以外の場合は、`ApplicationDbContext`に正しい名前空間を適用してください :</span><span class="sxs-lookup"><span data-stu-id="5d3e7-159">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="5d3e7-160">PowerShell では、コマンドの区切り記号としてセミコロンを使用します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-160">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="5d3e7-161">PowerShell を使用する場合は、前の例に示したように、ファイルリスト内のセミコロンをエスケープするか、ファイルリストを二重引用符で囲みます。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-161">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="5d3e7-162">スキャフォールディング Id の詳細については、「[スキャフォールディング identity to a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-162">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="5d3e7-163">レジスタの確認</span><span class="sxs-lookup"><span data-stu-id="5d3e7-163">Examine Register</span></span>

<span data-ttu-id="5d3e7-164">ユーザーが**登録**リンクをクリックすると、`RegisterModel.OnPostAsync`アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-164">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="5d3e7-165">ユーザーは[オブジェクトの](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)CreateAsync`_userManager`によって作成されます。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-165">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="5d3e7-166">`_userManager` は依存関係の挿入によってよって提供されます :</span><span class="sxs-lookup"><span data-stu-id="5d3e7-166">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="5d3e7-167">ユーザーが正常に作成された場合、ユーザーは`_signInManager.SignInAsync`の呼び出しによってログインされます。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-167">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="5d3e7-168">登録時の即時のログインを防止する手順については[アカウントの確認](xref:security/authentication/accconfirm#prevent-login-at-registration)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-168">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="5d3e7-169">ログイン</span><span class="sxs-lookup"><span data-stu-id="5d3e7-169">Log in</span></span>

<span data-ttu-id="5d3e7-170">ログイン フォームは次の時に表示されます :</span><span class="sxs-lookup"><span data-stu-id="5d3e7-170">The Login form is displayed when:</span></span>

* <span data-ttu-id="5d3e7-171">**ログイン** リンクが選択されたとき。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-171">The **Log in** link is selected.</span></span>
* <span data-ttu-id="5d3e7-172">ユーザーがまだシステムに認証されていない**または**アクセスする権限がない状態で、制限されているページにアクセスしようとしたとき。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-172">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="5d3e7-173">ログイン ページのフォームが送信されたとき、`OnPostAsync`アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-173">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="5d3e7-174">`PasswordSignInAsync`オブジェクト(依存関係の挿入によって提供されます)の`_signInManager`が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-174">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="5d3e7-175">基本 `Controller` クラスは、コントローラーメソッドからアクセスできる `User` プロパティを公開します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-175">The base `Controller` class exposes a `User` property that can be accessed from controller methods.</span></span> <span data-ttu-id="5d3e7-176">たとえば`User.Claims`を列挙して承認の決定を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-176">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="5d3e7-177">詳細については、「 <xref:security/authorization/introduction>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-177">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="5d3e7-178">ログアウト</span><span class="sxs-lookup"><span data-stu-id="5d3e7-178">Log out</span></span>

<span data-ttu-id="5d3e7-179">**ログアウト**リンクから`LogoutModel.OnPost`アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-179">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="5d3e7-180">前のコードでは、ブラウザーが新しい要求を実行し、ユーザーの id が更新されるように、コード `return RedirectToPage();` がリダイレクトである必要があります。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-180">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="5d3e7-181">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) はcookie に格納されているユーザー要求をクリアします。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-181">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="5d3e7-182">Postは *Pages/Shared/_LoginPartial.cshtml* で指定されています :</span><span class="sxs-lookup"><span data-stu-id="5d3e7-182">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="5d3e7-183">Identity のテスト</span><span class="sxs-lookup"><span data-stu-id="5d3e7-183">Test Identity</span></span>

<span data-ttu-id="5d3e7-184">既定の web プロジェクト テンプレートは、ホーム ページへの匿名アクセスを許可しています。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-184">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="5d3e7-185">Id をテストするには、 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)を追加します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-185">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="5d3e7-186">サインインしている場合は、サインアウトします。アプリを実行し、 **[プライバシー]** リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-186">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="5d3e7-187">ログインページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-187">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="5d3e7-188">Identity を調べる</span><span class="sxs-lookup"><span data-stu-id="5d3e7-188">Explore Identity</span></span>

<span data-ttu-id="5d3e7-189">Identity についてさらに詳しく調べるには :</span><span class="sxs-lookup"><span data-stu-id="5d3e7-189">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="5d3e7-190">完全な Identity の UI のソースを作成します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-190">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="5d3e7-191">各ページのソースを確認し、デバッガーでステップ実行します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-191">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="5d3e7-192">Identity コンポーネント</span><span class="sxs-lookup"><span data-stu-id="5d3e7-192">Identity Components</span></span>

<span data-ttu-id="5d3e7-193">すべての Id に依存する NuGet パッケージは、 [ASP.NET Core 共有フレームワーク](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-193">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="5d3e7-194">Identity のプライマリパッケージは[Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)です。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-194">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="5d3e7-195">このパッケージは ASP.NET Core Identity のインターフェイスのコアセットを含んでいて、また`Microsoft.AspNetCore.Identity.EntityFrameworkCore`に含まれています。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-195">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="5d3e7-196">ASP.NET Core Id への移行</span><span class="sxs-lookup"><span data-stu-id="5d3e7-196">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="5d3e7-197">既存の Identity ストアの移行についての詳細とガイダンスについては、次を参照してください : [移行の認証と Id](xref:migration/identity)。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-197">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="5d3e7-198">パスワードの強度を設定する</span><span class="sxs-lookup"><span data-stu-id="5d3e7-198">Setting password strength</span></span>

<span data-ttu-id="5d3e7-199">パスワードの最小要件を設定するサンプルについては[構成](#pw)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-199">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="5d3e7-200">AddDefaultIdentity と AddIdentity</span><span class="sxs-lookup"><span data-stu-id="5d3e7-200">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="5d3e7-201"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> は ASP.NET Core 2.1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-201"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="5d3e7-202">`AddDefaultIdentity`の呼び出しは、次の呼び出しに似ています。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-202">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="5d3e7-203">詳細については[AddDefaultIdentity のソース](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-203">See [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="next-steps"></a><span data-ttu-id="5d3e7-204">次の手順</span><span class="sxs-lookup"><span data-stu-id="5d3e7-204">Next Steps</span></span>

* [<span data-ttu-id="5d3e7-205">Identity の構成</span><span class="sxs-lookup"><span data-stu-id="5d3e7-205">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5d3e7-206">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5d3e7-206">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5d3e7-207">ASP.NET Core Id は、ASP.NET Core アプリにログイン機能を追加するメンバーシップシステムです。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-207">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="5d3e7-208">ユーザーは、Id に格納されているログイン情報を持つアカウントを作成することも、外部ログインプロバイダーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-208">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="5d3e7-209">サポートされている外部ログインプロバイダーには、 [Facebook、Google、Microsoft アカウント、Twitter](xref:security/authentication/social/index)があります。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-209">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="5d3e7-210">Id は、SQL Server データベースを使用して、ユーザー名、パスワード、およびプロファイルデータを格納するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-210">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="5d3e7-211">別の永続ストアを使用することもできます (たとえば、Azure Table Storage)。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-211">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="5d3e7-212">サンプルコード ([ダウンロード方法)](xref:index#how-to-download-a-sample)を[表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/)します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-212">[View or download the sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="5d3e7-213">このトピックでは、Identity を使用してユーザーを登録、ログイン、ログアウトする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-213">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="5d3e7-214">Id を使用するアプリを作成する方法の詳細については、この記事の最後にある「次のステップ」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-214">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="5d3e7-215">AddDefaultIdentity と AddIdentity</span><span class="sxs-lookup"><span data-stu-id="5d3e7-215">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="5d3e7-216"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> は ASP.NET Core 2.1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-216"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="5d3e7-217">`AddDefaultIdentity`の呼び出しは、次の呼び出しに似ています。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-217">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="5d3e7-218">詳細については[AddDefaultIdentity のソース](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-218">See [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="5d3e7-219">認証を使用して Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="5d3e7-219">Create a Web app with authentication</span></span>

<span data-ttu-id="5d3e7-220">個別のユーザー アカウントを使って、ASP.NET Core Web アプリケーション プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-220">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5d3e7-221">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d3e7-221">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5d3e7-222">[**ファイル**>**新しい**>**プロジェクト**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-222">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="5d3e7-223">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-223">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="5d3e7-224">プロジェクトに**WebApp1**という名前を付け、プロジェクトのダウンロードと同じ名前空間にします。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-224">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="5d3e7-225">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-225">Click **OK**.</span></span>
* <span data-ttu-id="5d3e7-226">ASP.NET Core **Web アプリケーション**を選択し、 **[認証の変更]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-226">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="5d3e7-227">個別のユーザー アカウントを **選択** して **OK** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-227">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="5d3e7-228">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5d3e7-228">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="5d3e7-229">生成されたプロジェクトは、 [ASP.NET Core Identity](xref:security/authentication/identity)を [Razor クラス ライブラリ](xref:razor-pages/ui-class)として提供します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-229">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="5d3e7-230">Identity の Razor クラス ライブラリは`Identity`エリアでエンドポイントを公開します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-230">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="5d3e7-231">例 :</span><span class="sxs-lookup"><span data-stu-id="5d3e7-231">For example:</span></span>

* <span data-ttu-id="5d3e7-232">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="5d3e7-232">/Identity/Account/Login</span></span>
* <span data-ttu-id="5d3e7-233">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="5d3e7-233">/Identity/Account/Logout</span></span>
* <span data-ttu-id="5d3e7-234">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="5d3e7-234">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="5d3e7-235">移行を適用する</span><span class="sxs-lookup"><span data-stu-id="5d3e7-235">Apply migrations</span></span>

<span data-ttu-id="5d3e7-236">移行を適用してデータベースを初期化します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-236">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5d3e7-237">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d3e7-237">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5d3e7-238">パッケージマネージャーコンソール (PMC) で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-238">Run the following command in the Package Manager Console (PMC):</span></span>

```PM> Update-Database```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="5d3e7-239">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5d3e7-239">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="5d3e7-240">テストレジスタとログイン</span><span class="sxs-lookup"><span data-stu-id="5d3e7-240">Test Register and Login</span></span>

<span data-ttu-id="5d3e7-241">アプリを実行し、ユーザーを登録します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-241">Run the app and register a user.</span></span> <span data-ttu-id="5d3e7-242">画面サイズによっては、**登録**と**ログイン**のリンクを表示するためにナビゲーションのトグル ボタンを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-242">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="5d3e7-243">Identity サービスを構成します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-243">Configure Identity services</span></span>

<span data-ttu-id="5d3e7-244">サービスは`ConfigureServices`で追加されます。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-244">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="5d3e7-245">一般的なパターンは、すべての `Add{Service}` メソッドを呼び出した後、すべての `services.Configure{Service}` メソッドを呼び出すことです。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-245">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="5d3e7-246">上記のコードでは、既定のオプション値で Identity を構成します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-246">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="5d3e7-247">サービスは[依存関係の注入](xref:fundamentals/dependency-injection)を通してアプリで利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-247">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="5d3e7-248">Identity は[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)を呼び出すことによって有効になります。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-248">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="5d3e7-249">`UseAuthentication` は認証[ミドルウェア](xref:fundamentals/middleware/index)を要求パイプラインに追加します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-249">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="5d3e7-250">詳細については、次の [IdentityOptions クラス](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)と[アプリケーションの起動](xref:fundamentals/startup) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-250">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="5d3e7-251">スキャフォールディング Register、Login、および LogOut</span><span class="sxs-lookup"><span data-stu-id="5d3e7-251">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="5d3e7-252">[id の権限を持つ Razor プロジェクトにスキャフォールディング](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)の手順に従って、このセクションで示すコードを生成してください。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-252">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5d3e7-253">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d3e7-253">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5d3e7-254">Register、Login、LogOut のファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-254">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="5d3e7-255">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5d3e7-255">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5d3e7-256">**WebApp1**という名前のプロジェクトを作成した場合、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-256">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="5d3e7-257">それ以外の場合は、`ApplicationDbContext`に正しい名前空間を適用してください :</span><span class="sxs-lookup"><span data-stu-id="5d3e7-257">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="5d3e7-258">PowerShell では、コマンドの区切り記号としてセミコロンを使用します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-258">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="5d3e7-259">PowerShell を使用する場合は、前の例に示したように、ファイルリスト内のセミコロンをエスケープするか、ファイルリストを二重引用符で囲みます。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-259">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="5d3e7-260">レジスタの確認</span><span class="sxs-lookup"><span data-stu-id="5d3e7-260">Examine Register</span></span>

<span data-ttu-id="5d3e7-261">ユーザーが**登録**リンクをクリックすると、`RegisterModel.OnPostAsync`アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-261">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="5d3e7-262">ユーザーは[オブジェクトの](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)CreateAsync`_userManager`によって作成されます。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-262">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="5d3e7-263">`_userManager` は依存関係の挿入によってよって提供されます :</span><span class="sxs-lookup"><span data-stu-id="5d3e7-263">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="5d3e7-264">ユーザーが正常に作成された場合、ユーザーは`_signInManager.SignInAsync`の呼び出しによってログインされます。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-264">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="5d3e7-265">**注:** 登録時にすぐにログインできないようにする手順については、「[アカウントの確認](xref:security/authentication/accconfirm#prevent-login-at-registration)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-265">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="5d3e7-266">ログイン</span><span class="sxs-lookup"><span data-stu-id="5d3e7-266">Log in</span></span>

<span data-ttu-id="5d3e7-267">ログイン フォームは次の時に表示されます :</span><span class="sxs-lookup"><span data-stu-id="5d3e7-267">The Login form is displayed when:</span></span>

* <span data-ttu-id="5d3e7-268">**ログイン** リンクが選択されたとき。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-268">The **Log in** link is selected.</span></span>
* <span data-ttu-id="5d3e7-269">ユーザーがまだシステムに認証されていない**または**アクセスする権限がない状態で、制限されているページにアクセスしようとしたとき。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-269">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="5d3e7-270">ログイン ページのフォームが送信されたとき、`OnPostAsync`アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-270">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="5d3e7-271">`PasswordSignInAsync`オブジェクト(依存関係の挿入によって提供されます)の`_signInManager`が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-271">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="5d3e7-272">ベース`Controller`クラスでは、コントローラー メソッドからアクセスできる`User`プロパティを公開します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-272">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="5d3e7-273">たとえば`User.Claims`を列挙して承認の決定を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-273">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="5d3e7-274">詳細については、「 <xref:security/authorization/introduction>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-274">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="5d3e7-275">ログアウト</span><span class="sxs-lookup"><span data-stu-id="5d3e7-275">Log out</span></span>

<span data-ttu-id="5d3e7-276">**ログアウト**リンクから`LogoutModel.OnPost`アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-276">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="5d3e7-277">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) はcookie に格納されているユーザー要求をクリアします。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-277">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="5d3e7-278">Postは *Pages/Shared/_LoginPartial.cshtml* で指定されています :</span><span class="sxs-lookup"><span data-stu-id="5d3e7-278">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="5d3e7-279">Identity のテスト</span><span class="sxs-lookup"><span data-stu-id="5d3e7-279">Test Identity</span></span>

<span data-ttu-id="5d3e7-280">既定の web プロジェクト テンプレートは、ホーム ページへの匿名アクセスを許可しています。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-280">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="5d3e7-281">Identity をテストするために、[ `[Authorize]` ](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute)をPrivacy ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-281">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="5d3e7-282">サインインしている場合は、サインアウトします。アプリを実行し、 **[プライバシー]** リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-282">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="5d3e7-283">ログインページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-283">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="5d3e7-284">Identity を調べる</span><span class="sxs-lookup"><span data-stu-id="5d3e7-284">Explore Identity</span></span>

<span data-ttu-id="5d3e7-285">Identity についてさらに詳しく調べるには :</span><span class="sxs-lookup"><span data-stu-id="5d3e7-285">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="5d3e7-286">完全な Identity の UI のソースを作成します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-286">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="5d3e7-287">各ページのソースを確認し、デバッガーでステップ実行します。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-287">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="5d3e7-288">Identity コンポーネント</span><span class="sxs-lookup"><span data-stu-id="5d3e7-288">Identity Components</span></span>

<span data-ttu-id="5d3e7-289">すべての Identity 依存の NuGet パッケージは[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれます。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-289">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="5d3e7-290">Identity のプライマリパッケージは[Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)です。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-290">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="5d3e7-291">このパッケージは ASP.NET Core Identity のインターフェイスのコアセットを含んでいて、また`Microsoft.AspNetCore.Identity.EntityFrameworkCore`に含まれています。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-291">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="5d3e7-292">ASP.NET Core Id への移行</span><span class="sxs-lookup"><span data-stu-id="5d3e7-292">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="5d3e7-293">既存の Identity ストアの移行についての詳細とガイダンスについては、次を参照してください : [移行の認証と Id](xref:migration/identity)。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-293">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="5d3e7-294">パスワードの強度を設定する</span><span class="sxs-lookup"><span data-stu-id="5d3e7-294">Setting password strength</span></span>

<span data-ttu-id="5d3e7-295">パスワードの最小要件を設定するサンプルについては[構成](#pw)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5d3e7-295">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="5d3e7-296">次の手順</span><span class="sxs-lookup"><span data-stu-id="5d3e7-296">Next Steps</span></span>

* [<span data-ttu-id="5d3e7-297">Identity の構成</span><span class="sxs-lookup"><span data-stu-id="5d3e7-297">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
