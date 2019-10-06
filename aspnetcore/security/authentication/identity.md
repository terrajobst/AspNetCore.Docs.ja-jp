---
title: ASP.NET Core Identity の概要
author: rick-anderson
description: ASP.NET Core アプリで Id を使用します。 パスワードの要件 (RequireDigit、RequiredLength、RequiredUniqueChars など) を設定する方法について説明します。
ms.author: riande
ms.date: 03/26/2019
uid: security/authentication/identity
ms.openlocfilehash: 6701eb0ac1b1abb8699a5a529bcc29f295e5c7c9
ms.sourcegitcommit: 4115bf0e850c13d4e655beb5ab5e8ff431173cb6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981907"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="91c63-104">ASP.NET Core Identity の概要</span><span class="sxs-lookup"><span data-stu-id="91c63-104">Introduction to Identity on ASP.NET Core</span></span>

<span data-ttu-id="91c63-105">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="91c63-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="91c63-106">ASP.NET Core Id は、ASP.NET Core アプリにログイン機能を追加するメンバーシップシステムです。</span><span class="sxs-lookup"><span data-stu-id="91c63-106">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="91c63-107">ユーザーは、Id に格納されているログイン情報を持つアカウントを作成することも、外部ログインプロバイダーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="91c63-107">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="91c63-108">サポートされている外部ログインプロバイダーには、 [Facebook、Google、Microsoft アカウント、Twitter](xref:security/authentication/social/index)があります。</span><span class="sxs-lookup"><span data-stu-id="91c63-108">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="91c63-109">Id は、SQL Server データベースを使用して、ユーザー名、パスワード、およびプロファイルデータを格納するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="91c63-109">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="91c63-110">別の永続ストアを使用することもできます (たとえば、Azure Table Storage)。</span><span class="sxs-lookup"><span data-stu-id="91c63-110">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="91c63-111">サンプルコード ([ダウンロード方法)](xref:index#how-to-download-a-sample)を[表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/)します。</span><span class="sxs-lookup"><span data-stu-id="91c63-111">[View or download the sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="91c63-112">このトピックでは、Identity を使用してユーザーを登録、ログイン、ログアウトする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="91c63-112">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="91c63-113">Id を使用するアプリを作成する方法の詳細については、この記事の最後にある「次のステップ」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="91c63-113">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

::: moniker range=">= aspnetcore-2.1"

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="91c63-114">AddDefaultIdentity と AddIdentity</span><span class="sxs-lookup"><span data-stu-id="91c63-114">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="91c63-115">[AddDefaultIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionuiextensions.adddefaultidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionUIExtensions_AddDefaultIdentity__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__)は ASP.NET Core 2.1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="91c63-115">[AddDefaultIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionuiextensions.adddefaultidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionUIExtensions_AddDefaultIdentity__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__) was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="91c63-116">`AddDefaultIdentity`の呼び出しは、次の呼び出しに似ています。</span><span class="sxs-lookup"><span data-stu-id="91c63-116">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* [<span data-ttu-id="91c63-117">AddIdentity</span><span class="sxs-lookup"><span data-stu-id="91c63-117">AddIdentity</span></span>](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_AddIdentity__2_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__)
* [<span data-ttu-id="91c63-118">AddDefaultUI</span><span class="sxs-lookup"><span data-stu-id="91c63-118">AddDefaultUI</span></span>](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderuiextensions.adddefaultui?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderUIExtensions_AddDefaultUI_Microsoft_AspNetCore_Identity_IdentityBuilder_)
* [<span data-ttu-id="91c63-119">AddDefaultTokenProviders</span><span class="sxs-lookup"><span data-stu-id="91c63-119">AddDefaultTokenProviders</span></span>](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderextensions.adddefaulttokenproviders?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderExtensions_AddDefaultTokenProviders_Microsoft_AspNetCore_Identity_IdentityBuilder_)

<span data-ttu-id="91c63-120">詳細については[AddDefaultIdentity のソース](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="91c63-120">See [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

::: moniker-end

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="91c63-121">認証を使用して Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="91c63-121">Create a Web app with authentication</span></span>

<span data-ttu-id="91c63-122">個別のユーザー アカウントを使って、ASP.NET Core Web アプリケーション プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="91c63-122">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="91c63-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91c63-123">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="91c63-124">**[ファイル]**  >  **[新規作成]**  >  **[プロジェクト]** を順に選択します。</span><span class="sxs-lookup"><span data-stu-id="91c63-124">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="91c63-125">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="91c63-125">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="91c63-126">プロジェクトに**WebApp1**という名前を付け、プロジェクトのダウンロードと同じ名前空間にします。</span><span class="sxs-lookup"><span data-stu-id="91c63-126">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="91c63-127">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="91c63-127">Click **OK**.</span></span>
* <span data-ttu-id="91c63-128">ASP.NET Core **Web アプリケーション**を選択し、 **[認証の変更]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="91c63-128">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="91c63-129">個別のユーザー アカウントを **選択** して **OK** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="91c63-129">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="91c63-130">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="91c63-130">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="91c63-131">生成されたプロジェクトは、 [ASP.NET Core Identity](xref:security/authentication/identity)を [Razor クラス ライブラリ](xref:razor-pages/ui-class)として提供します。</span><span class="sxs-lookup"><span data-stu-id="91c63-131">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="91c63-132">Identity の Razor クラス ライブラリは`Identity`エリアでエンドポイントを公開します。</span><span class="sxs-lookup"><span data-stu-id="91c63-132">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="91c63-133">以下に例を示します。</span><span class="sxs-lookup"><span data-stu-id="91c63-133">For example:</span></span>

* <span data-ttu-id="91c63-134">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="91c63-134">/Identity/Account/Login</span></span>
* <span data-ttu-id="91c63-135">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="91c63-135">/Identity/Account/Logout</span></span>
* <span data-ttu-id="91c63-136">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="91c63-136">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="91c63-137">移行を適用する</span><span class="sxs-lookup"><span data-stu-id="91c63-137">Apply migrations</span></span>

<span data-ttu-id="91c63-138">データベースを初期化するために、移行を適用します。</span><span class="sxs-lookup"><span data-stu-id="91c63-138">Apply the migrations to initialise the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="91c63-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91c63-139">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="91c63-140">パッケージマネージャーコンソール (PMC) で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="91c63-140">Run the following command in the Package Manager Console (PMC):</span></span>

```PM> Update-Database```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="91c63-141">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="91c63-141">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="91c63-142">テストレジスタとログイン</span><span class="sxs-lookup"><span data-stu-id="91c63-142">Test Register and Login</span></span>

<span data-ttu-id="91c63-143">アプリを実行し、ユーザーを登録します。</span><span class="sxs-lookup"><span data-stu-id="91c63-143">Run the app and register a user.</span></span> <span data-ttu-id="91c63-144">画面サイズによっては、**登録**と**ログイン**のリンクを表示するためにナビゲーションのトグル ボタンを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="91c63-144">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="91c63-145">Identity サービスを構成します。</span><span class="sxs-lookup"><span data-stu-id="91c63-145">Configure Identity services</span></span>

<span data-ttu-id="91c63-146">サービスは`ConfigureServices`で追加されます。</span><span class="sxs-lookup"><span data-stu-id="91c63-146">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="91c63-147">一般的なパターンでは、すべての `Add{Service}` メソッドを呼び出した後、すべての `services.Configure{Service}` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="91c63-147">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="91c63-148">上記のコードでは、既定のオプション値で Identity を構成します。</span><span class="sxs-lookup"><span data-stu-id="91c63-148">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="91c63-149">サービスは[依存関係の注入](xref:fundamentals/dependency-injection)を通してアプリで利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="91c63-149">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

   <span data-ttu-id="91c63-150">Identity は[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)を呼び出すことによって有効になります。</span><span class="sxs-lookup"><span data-stu-id="91c63-150">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="91c63-151">`UseAuthentication` は認証[ミドルウェア](xref:fundamentals/middleware/index)を要求パイプラインに追加します。</span><span class="sxs-lookup"><span data-stu-id="91c63-151">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

   [!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,11-28,30-42)]

   <span data-ttu-id="91c63-152">サービスは[依存関係の注入](xref:fundamentals/dependency-injection)を通してアプリで利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="91c63-152">Services are made available to the application through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

   <span data-ttu-id="91c63-153">Identity は`Configure`メソッド内で`UseAuthentication`を呼び出すことによって、アプリケーションで有効になります。</span><span class="sxs-lookup"><span data-stu-id="91c63-153">Identity is enabled for the application by calling `UseAuthentication` in the `Configure` method.</span></span> <span data-ttu-id="91c63-154">`UseAuthentication` は認証[ミドルウェア](xref:fundamentals/middleware/index)を要求パイプラインに追加します。</span><span class="sxs-lookup"><span data-stu-id="91c63-154">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configure&highlight=17)]

::: moniker-end

::: moniker range="= aspnetcore-1.1"

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,13-33)]

   <span data-ttu-id="91c63-155">これらのサービスは[依存関係の注入](xref:fundamentals/dependency-injection)を通してアプリケーションで利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="91c63-155">These services are made available to the application through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

   <span data-ttu-id="91c63-156">Identity は`Configure`メソッド内で`UseIdentity`を呼び出すことによって、アプリケーションで利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="91c63-156">Identity is enabled for the application by calling `UseIdentity` in the `Configure` method.</span></span> <span data-ttu-id="91c63-157">`UseIdentity` はcookie ベースの認証[ミドルウェア](xref:fundamentals/middleware/index)を要求パイプラインに追加します。</span><span class="sxs-lookup"><span data-stu-id="91c63-157">`UseIdentity` adds cookie-based authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configure&highlight=21)]

::: moniker-end

<span data-ttu-id="91c63-158">詳細については、次の [IdentityOptions クラス](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)と[アプリケーションの起動](xref:fundamentals/startup) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="91c63-158">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="91c63-159">スキャフォールディング Register、Login、および LogOut</span><span class="sxs-lookup"><span data-stu-id="91c63-159">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="91c63-160">[id の権限を持つ Razor プロジェクトにスキャフォールディング](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)の手順に従って、このセクションで示すコードを生成してください。</span><span class="sxs-lookup"><span data-stu-id="91c63-160">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="91c63-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91c63-161">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="91c63-162">Register、Login、LogOut のファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="91c63-162">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="91c63-163">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="91c63-163">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="91c63-164">**WebApp1**という名前のプロジェクトを作成した場合、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="91c63-164">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="91c63-165">それ以外の場合は、`ApplicationDbContext`に正しい名前空間を適用してください :</span><span class="sxs-lookup"><span data-stu-id="91c63-165">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="91c63-166">PowerShell では、コマンドの区切り記号としてセミコロンを使用します。</span><span class="sxs-lookup"><span data-stu-id="91c63-166">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="91c63-167">PowerShell を使用する場合は、前の例に示したように、ファイルリスト内のセミコロンをエスケープするか、ファイルリストを二重引用符で囲みます。</span><span class="sxs-lookup"><span data-stu-id="91c63-167">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="91c63-168">レジスタの確認</span><span class="sxs-lookup"><span data-stu-id="91c63-168">Examine Register</span></span>

::: moniker range=">= aspnetcore-2.1"

   <span data-ttu-id="91c63-169">ユーザーが**登録**リンクをクリックすると、`RegisterModel.OnPostAsync`アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="91c63-169">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="91c63-170">ユーザーは`_userManager`オブジェクトの[CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)によって作成されます。</span><span class="sxs-lookup"><span data-stu-id="91c63-170">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="91c63-171">`_userManager` は依存関係の挿入によってよって提供されます :</span><span class="sxs-lookup"><span data-stu-id="91c63-171">`_userManager` is provided by dependency injection):</span></span>

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7,22)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   <span data-ttu-id="91c63-172">ユーザーが**登録**リンクをクリックすると、`AccountController`の`Register`アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="91c63-172">When a user clicks the **Register** link, the `Register` action is invoked on `AccountController`.</span></span> <span data-ttu-id="91c63-173">`Register`アクションは`_userManager`オブジェクト (依存関係の挿入によって`AccountController`に提供される)の`CreateAsync`を呼び出してユーザーを作成します。</span><span class="sxs-lookup"><span data-stu-id="91c63-173">The `Register` action creates the user by calling `CreateAsync` on the `_userManager` object (provided to `AccountController` by dependency injection):</span></span>

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_register&highlight=11)]

::: moniker-end

   <span data-ttu-id="91c63-174">ユーザーが正常に作成された場合、ユーザーは`_signInManager.SignInAsync`の呼び出しによってログインされます。</span><span class="sxs-lookup"><span data-stu-id="91c63-174">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

   <span data-ttu-id="91c63-175">**注:** 登録時の即時のログインを防止する手順については[アカウントの確認](xref:security/authentication/accconfirm#prevent-login-at-registration)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="91c63-175">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="91c63-176">ログイン</span><span class="sxs-lookup"><span data-stu-id="91c63-176">Log in</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="91c63-177">ログイン フォームは次の時に表示されます :</span><span class="sxs-lookup"><span data-stu-id="91c63-177">The Login form is displayed when:</span></span>

* <span data-ttu-id="91c63-178">**ログイン** リンクが選択されたとき。</span><span class="sxs-lookup"><span data-stu-id="91c63-178">The **Log in** link is selected.</span></span>
* <span data-ttu-id="91c63-179">ユーザーがまだシステムに認証されていない**または**アクセスする権限がない状態で、制限されているページにアクセスしようとしたとき。</span><span class="sxs-lookup"><span data-stu-id="91c63-179">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="91c63-180">ログイン ページのフォームが送信されたとき、`OnPostAsync`アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="91c63-180">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="91c63-181">`_signInManager`オブジェクト(依存関係の挿入によって提供されます)の`PasswordSignInAsync`が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="91c63-181">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

   <span data-ttu-id="91c63-182">ベース`Controller`クラスでは、コントローラー メソッドからアクセスできる`User`プロパティを公開します。</span><span class="sxs-lookup"><span data-stu-id="91c63-182">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="91c63-183">たとえば`User.Claims`を列挙して承認の決定を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="91c63-183">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="91c63-184">詳細については「ASP.NET Core での承認の概要<xref:security/authorization/introduction> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="91c63-184">For more information, see <xref:security/authorization/introduction>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="91c63-185">ログイン フォームは、ユーザーが**ログイン**リンク選択したとき表示されます。また認証が必要なページにアクセスしたときにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="91c63-185">The Login form is displayed when users select the **Log in** link or are redirected when accessing a page that requires authentication.</span></span> <span data-ttu-id="91c63-186">ユーザーがログイン ページでフォームを送信すると、`AccountController`の`Login`アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="91c63-186">When the user submits the form on the Login page, the `AccountController` `Login` action is called.</span></span>

<span data-ttu-id="91c63-187">`Login`アクションは`_signInManager`オブジェクト (依存関係の挿入によって`AccountController`に提供されます)の`PasswordSignInAsync`を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="91c63-187">The `Login` action calls `PasswordSignInAsync` on the `_signInManager` object (provided to `AccountController` by dependency injection).</span></span>

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_login&highlight=13-14)]

<span data-ttu-id="91c63-188">ベース (`Controller`または`PageModel`) クラスは、`User`プロパティを公開します。</span><span class="sxs-lookup"><span data-stu-id="91c63-188">The base (`Controller` or `PageModel`) class exposes a `User` property.</span></span> <span data-ttu-id="91c63-189">たとえば、`User.Claims`を列挙して承認決定を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="91c63-189">For example, `User.Claims` can be enumerated to make authorization decisions.</span></span>

::: moniker-end

### <a name="log-out"></a><span data-ttu-id="91c63-190">ログアウト</span><span class="sxs-lookup"><span data-stu-id="91c63-190">Log out</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="91c63-191">**ログアウト**リンクから`LogoutModel.OnPost`アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="91c63-191">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="91c63-192">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) はcookie に格納されているユーザー要求をクリアします。</span><span class="sxs-lookup"><span data-stu-id="91c63-192">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="91c63-193">Postは *Pages/Shared/_LoginPartial.cshtml* で指定されています :</span><span class="sxs-lookup"><span data-stu-id="91c63-193">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   <span data-ttu-id="91c63-194">**ログアウト**リンクをクリックすると、`LogOut`アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="91c63-194">Clicking the **Log out** link calls the `LogOut` action.</span></span>

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_logout&highlight=7)]

   <span data-ttu-id="91c63-195">上記のコードは`_signInManager.SignOutAsync`メソッドを呼び出しています。</span><span class="sxs-lookup"><span data-stu-id="91c63-195">The preceding code calls the `_signInManager.SignOutAsync` method.</span></span> <span data-ttu-id="91c63-196">@No__t-0 メソッドは、cookie に格納されているユーザーの要求をクリアします。</span><span class="sxs-lookup"><span data-stu-id="91c63-196">The `SignOutAsync` method clears the user's claims stored in a cookie.</span></span>

::: moniker-end

## <a name="test-identity"></a><span data-ttu-id="91c63-197">Identity のテスト</span><span class="sxs-lookup"><span data-stu-id="91c63-197">Test Identity</span></span>

<span data-ttu-id="91c63-198">既定の web プロジェクト テンプレートは、ホーム ページへの匿名アクセスを許可しています。</span><span class="sxs-lookup"><span data-stu-id="91c63-198">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="91c63-199">Identity をテストするために、[ `[Authorize]` ](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute)をPrivacy ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="91c63-199">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=6)]

<span data-ttu-id="91c63-200">サインイン済みの場合、サインアウトします。アプリを実行し、**プライバシー**リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="91c63-200">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="91c63-201">ログインページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="91c63-201">You are redirected to the login page.</span></span>

::: moniker range=">= aspnetcore-2.1"

### <a name="explore-identity"></a><span data-ttu-id="91c63-202">Identity を調べる</span><span class="sxs-lookup"><span data-stu-id="91c63-202">Explore Identity</span></span>

<span data-ttu-id="91c63-203">Identity についてさらに詳しく調べるには :</span><span class="sxs-lookup"><span data-stu-id="91c63-203">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="91c63-204">完全な Identity の UI のソースを作成します。</span><span class="sxs-lookup"><span data-stu-id="91c63-204">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="91c63-205">各ページのソースを確認し、デバッガーでステップ実行します。</span><span class="sxs-lookup"><span data-stu-id="91c63-205">Examine the source of each page and step through the debugger.</span></span>

::: moniker-end

## <a name="identity-components"></a><span data-ttu-id="91c63-206">Identity コンポーネント</span><span class="sxs-lookup"><span data-stu-id="91c63-206">Identity Components</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="91c63-207">すべての Identity 依存の NuGet パッケージは[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれます。</span><span class="sxs-lookup"><span data-stu-id="91c63-207">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="91c63-208">Identity のプライマリパッケージは[Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)です。</span><span class="sxs-lookup"><span data-stu-id="91c63-208">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="91c63-209">このパッケージは ASP.NET Core Identity のインターフェイスのコアセットを含んでいて、また`Microsoft.AspNetCore.Identity.EntityFrameworkCore`に含まれています。</span><span class="sxs-lookup"><span data-stu-id="91c63-209">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="91c63-210">ASP.NET Core Id への移行</span><span class="sxs-lookup"><span data-stu-id="91c63-210">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="91c63-211">既存の Identity ストアの移行についての詳細とガイダンスについては、次を参照してください : [移行の認証と Id](xref:migration/identity)。</span><span class="sxs-lookup"><span data-stu-id="91c63-211">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="91c63-212">パスワードの強度を設定する</span><span class="sxs-lookup"><span data-stu-id="91c63-212">Setting password strength</span></span>

<span data-ttu-id="91c63-213">パスワードの最小要件を設定するサンプルについては[構成](#pw)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="91c63-213">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="91c63-214">次の手順</span><span class="sxs-lookup"><span data-stu-id="91c63-214">Next Steps</span></span>

* [<span data-ttu-id="91c63-215">Identity の構成</span><span class="sxs-lookup"><span data-stu-id="91c63-215">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>
