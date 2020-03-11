---
title: ASP.NET Core Identity の概要
author: rick-anderson
description: ASP.NET Core アプリで Id を使用します。 パスワードの要件 (RequireDigit、RequiredLength、RequiredUniqueChars など) を設定する方法について説明します。
ms.author: riande
ms.date: 01/15/2020
uid: security/authentication/identity
ms.openlocfilehash: 2e0723d34a09109a034f3375c4e94aedab2a5427
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653156"
---
# <a name="introduction-to-identity-on-aspnet-core"></a>ASP.NET Core Identity の概要

::: moniker range=">= aspnetcore-3.0"

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core Id:

* は、ユーザーインターフェイス (UI) のログイン機能をサポートする API です。
* ユーザー、パスワード、プロファイルデータ、ロール、要求、トークン、電子メールの確認などを管理します。

ユーザーは、Id に格納されているログイン情報を持つアカウントを作成することも、外部ログインプロバイダーを使用することもできます。 サポートされている外部ログインプロバイダーには、 [Facebook、Google、Microsoft アカウント、Twitter](xref:security/authentication/social/index)があります。

[Id ソースコード](https://github.com/dotnet/AspNetCore/tree/master/src/Identity)は、GitHub で入手できます。 [スキャフォールディング](xref:security/authentication/scaffold-identity)は、id とのテンプレートの対話を確認するために、生成されたファイルを識別して表示します。

Id は、通常、ユーザー名、パスワード、およびプロファイルデータを格納するために SQL Server データベースを使用して構成されます。 別の永続ストアを使用することもできます (たとえば、Azure Table Storage)。

このトピックでは、Identity を使用してユーザーを登録、ログイン、ログアウトする方法について説明します。 Id を使用するアプリを作成する方法の詳細については、この記事の最後にある「次のステップ」を参照してください。

[Microsoft id プラットフォーム](/azure/active-directory/develop/)は次のとおりです。

* Azure Active Directory (Azure AD) 開発プラットフォームの進化。
* ASP.NET Core Id に関連付けられていません。

[!INCLUDE[](~/includes/IdentityServer4.md)]

サンプルコード ([ダウンロード方法)](xref:index#how-to-download-a-sample)を[表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample)します。

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a>認証を使用して Web アプリを作成する

個別のユーザー アカウントを使って、ASP.NET Core Web アプリケーション プロジェクトを作成します。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [**ファイル**>**新しい**>**プロジェクト**] を選択します。
* **[ASP.NET Core Web アプリケーション]** を選択します。 プロジェクトに**WebApp1**という名前を付け、プロジェクトのダウンロードと同じ名前空間にします。 **[OK]** をクリックすると、
* ASP.NET Core **Web アプリケーション**を選択し、 **[認証の変更]** を選択します。
* **個々のユーザーアカウント**を選択し、[ **OK]** をクリックします。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

上記のコマンドは、SQLite を使用して Razor web アプリを作成します。 LocalDB を使用して web アプリを作成するには、次のコマンドを実行します。

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

生成されたプロジェクトは、 [ASP.NET Core id](xref:security/authentication/identity)を[Razor クラスライブラリ](xref:razor-pages/ui-class)として提供します。 Identity Razor クラスライブラリは、`Identity` 領域でエンドポイントを公開します。 例 :

* /Identity/Account/Login
* /Identity/Account/Logout
* /Identity/Account/Manage

### <a name="apply-migrations"></a>移行を適用する

移行を適用してデータベースを初期化します。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

パッケージマネージャーコンソール (PMC) で次のコマンドを実行します。

`PM> Update-Database`

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

SQLite を使用する場合、この手順で移行する必要はありません。 LocalDB の場合は、次のコマンドを実行します。

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>テストレジスタとログイン

アプリを実行し、ユーザーを登録します。 画面のサイズによっては、[ナビゲーション] トグルボタンを選択して、**登録**リンクと**ログイン**リンクを表示する必要がある場合があります。

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a>Identity サービスを構成します。

サービスは `ConfigureServices`に追加されます。 一般的なパターンは、すべての `Add{Service}` メソッドを呼び出した後、すべての `services.Configure{Service}` メソッドを呼び出すことです。

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

上の強調表示されたコードは、既定のオプション値を使用して Id を構成します。 サービスは、[依存関係の挿入](xref:fundamentals/dependency-injection)によってアプリで使用できるようになります。

Id は <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>を呼び出すことによって有効になります。 `UseAuthentication` は、認証[ミドルウェア](xref:fundamentals/middleware/index)を要求パイプラインに追加します。

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

テンプレートで生成されたアプリは、[承認](xref:security/authorization/secure-data)を使用しません。 アプリが承認を追加する正しい順序で追加されるように `app.UseAuthorization` が含まれています。 `UseRouting`、`UseAuthentication`、`UseAuthorization`、および `UseEndpoints` は、前のコードに示されている順序で呼び出す必要があります。

`IdentityOptions` と `Startup`の詳細については、「<xref:Microsoft.AspNetCore.Identity.IdentityOptions> と[アプリケーションの起動](xref:fundamentals/startup)」を参照してください。

## <a name="scaffold-register-login-and-logout"></a>スキャフォールディング Register、Login、および LogOut

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Register、Login、LogOut のファイルを追加します。 スキャフォールディング id に従って、このセクションに示されているコードを生成するための[承認命令を含む Razor プロジェクトに](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)従います。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

**WebApp1**という名前のプロジェクトを作成した場合は、次のコマンドを実行します。 それ以外の場合は、`ApplicationDbContext`に適切な名前空間を使用します。

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

PowerShell では、コマンドの区切り記号としてセミコロンを使用します。 PowerShell を使用する場合は、前の例に示したように、ファイルリスト内のセミコロンをエスケープするか、ファイルリストを二重引用符で囲みます。

スキャフォールディング Id の詳細については、「[スキャフォールディング identity to a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)」を参照してください。

---

### <a name="examine-register"></a>レジスタの確認

ユーザーが **[登録]** リンクをクリックすると、`RegisterModel.OnPostAsync` アクションが呼び出されます。 ユーザーは、`_userManager` オブジェクトに対して[Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)によって作成されます。 `_userManager` は、依存関係の挿入によって提供されます)。

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

ユーザーが正常に作成された場合は、`_signInManager.SignInAsync`の呼び出しによってユーザーがログインします。

登録時にすぐにログインできないようにする手順については、「[アカウントの確認](xref:security/authentication/accconfirm#prevent-login-at-registration)」をご覧ください。

### <a name="log-in"></a>ログイン

ログイン フォームは次の時に表示されます :

* **ログイン**リンクが選択されています。
* ユーザーがアクセスを承認されていない、**または**システムによって認証されていない制限付きページにアクセスしようとしています。

ログインページのフォームが送信されると、`OnPostAsync` アクションが呼び出されます。 `PasswordSignInAsync` は、(依存関係の挿入によって提供される) `_signInManager` オブジェクトで呼び出されます。

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

基本 `Controller` クラスは、コントローラーメソッドからアクセスできる `User` プロパティを公開します。 たとえば、`User.Claims` を列挙し、承認の決定を行うことができます。 詳細については、<xref:security/authorization/introduction> を参照してください。

### <a name="log-out"></a>ログアウト

**Log out**リンクは、`LogoutModel.OnPost` アクションを呼び出します。 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

前のコードでは、ブラウザーが新しい要求を実行し、ユーザーの id が更新されるように、コード `return RedirectToPage();` がリダイレクトである必要があります。

[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync)者は、cookie に格納されているユーザーの要求をクリアします。

Post は*Pages/Shared/_LoginPartial*に指定されています。

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a>Identity のテスト

既定の web プロジェクト テンプレートは、ホーム ページへの匿名アクセスを許可しています。 Id をテストするには、 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)を追加します。

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

サインインしている場合は、サインアウトします。アプリを実行し、 **[プライバシー]** リンクを選択します。 ログイン ページにリダイレクトされます。

### <a name="explore-identity"></a>Identity を調べる

Identity についてさらに詳しく調べるには :

* [完全な id UI ソースの作成](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* 各ページのソースを確認し、デバッガーでステップ実行します。

## <a name="identity-components"></a>Identity コンポーネント

すべての Id に依存する NuGet パッケージは、 [ASP.NET Core 共有フレームワーク](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)に含まれています。

Id のプライマリパッケージは[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)です。 このパッケージには ASP.NET Core Id のインターフェイスのコアセットが含まれており、`Microsoft.AspNetCore.Identity.EntityFrameworkCore`によって含まれています。

## <a name="migrating-to-aspnet-core-identity"></a>ASP.NET Core Id への移行

既存の Id ストアを移行する方法の詳細とガイダンスについては、「[認証と id の移行](xref:migration/identity)」を参照してください。

## <a name="setting-password-strength"></a>パスワードの強度を設定する

パスワードの最小要件を設定するサンプルについては、「[構成](#pw)」を参照してください。

## <a name="adddefaultidentity-and-addidentity"></a>AddDefaultIdentity と AddIdentity

<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> は ASP.NET Core 2.1 で導入されました。 `AddDefaultIdentity` の呼び出しは、次の呼び出しに似ています。

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

詳細については、「 [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) 」を参照してください。

## <a name="prevent-publish-of-static-identity-assets"></a>静的 Id 資産の発行を禁止する

静的な Id アセット (Id UI 用のスタイルシートおよび JavaScript ファイル) を web ルートに発行できないようにするには、次の `ResolveStaticWebAssetsInputsDependsOn` プロパティを追加し、アプリのプロジェクトファイルにターゲット `RemoveIdentityAssets` します。

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

## <a name="next-steps"></a>次の手順

* SQLite を使用して Id を構成する方法については、 [GitHub の問題](https://github.com/dotnet/AspNetCore.Docs/issues/5131)を参照してください。
* [Identity の構成](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core Id は、ASP.NET Core アプリにログイン機能を追加するメンバーシップシステムです。 ユーザーは、Id に格納されているログイン情報を持つアカウントを作成することも、外部ログインプロバイダーを使用することもできます。 サポートされている外部ログインプロバイダーには、 [Facebook、Google、Microsoft アカウント、Twitter](xref:security/authentication/social/index)があります。

Id は、SQL Server データベースを使用して、ユーザー名、パスワード、およびプロファイルデータを格納するように構成できます。 別の永続ストアを使用することもできます (たとえば、Azure Table Storage)。

サンプルコード ([ダウンロード方法)](xref:index#how-to-download-a-sample)を[表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/)します。

このトピックでは、Identity を使用してユーザーを登録、ログイン、ログアウトする方法について説明します。 Id を使用するアプリを作成する方法の詳細については、この記事の最後にある「次のステップ」を参照してください。

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a>AddDefaultIdentity と AddIdentity

<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> は ASP.NET Core 2.1 で導入されました。 `AddDefaultIdentity` の呼び出しは、次の呼び出しに似ています。

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

詳細については、「 [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) 」を参照してください。

## <a name="create-a-web-app-with-authentication"></a>認証を使用して Web アプリを作成する

個別のユーザー アカウントを使って、ASP.NET Core Web アプリケーション プロジェクトを作成します。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [**ファイル**>**新しい**>**プロジェクト**] を選択します。
* **[ASP.NET Core Web アプリケーション]** を選択します。 プロジェクトに**WebApp1**という名前を付け、プロジェクトのダウンロードと同じ名前空間にします。 **[OK]** をクリックすると、
* ASP.NET Core **Web アプリケーション**を選択し、 **[認証の変更]** を選択します。
* **個々のユーザーアカウント**を選択し、[ **OK]** をクリックします。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

生成されたプロジェクトは、 [ASP.NET Core id](xref:security/authentication/identity)を[Razor クラスライブラリ](xref:razor-pages/ui-class)として提供します。 Identity Razor クラスライブラリは、`Identity` 領域でエンドポイントを公開します。 例 :

* /Identity/Account/Login
* /Identity/Account/Logout
* /Identity/Account/Manage

### <a name="apply-migrations"></a>移行を適用する

移行を適用してデータベースを初期化します。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

パッケージマネージャーコンソール (PMC) で次のコマンドを実行します。

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>テストレジスタとログイン

アプリを実行し、ユーザーを登録します。 画面のサイズによっては、[ナビゲーション] トグルボタンを選択して、**登録**リンクと**ログイン**リンクを表示する必要がある場合があります。

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a>Identity サービスを構成します。

サービスは `ConfigureServices`に追加されます。 一般的なパターンは、すべての `Add{Service}` メソッドを呼び出した後、すべての `services.Configure{Service}` メソッドを呼び出すことです。

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

上記のコードでは、既定のオプション値で Identity を構成します。 サービスは、[依存関係の挿入](xref:fundamentals/dependency-injection)によってアプリで使用できるようになります。

Id は、 [Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)を呼び出すことによって有効になります。 `UseAuthentication` は、認証[ミドルウェア](xref:fundamentals/middleware/index)を要求パイプラインに追加します。

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

詳細については、「[クラス](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)と[アプリケーションの起動](xref:fundamentals/startup)」を参照してください。

## <a name="scaffold-register-login-and-logout"></a>スキャフォールディング Register、Login、および LogOut

スキャフォールディング id に従って、このセクションに示されているコードを生成するための[承認命令を含む Razor プロジェクトに](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)従います。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Register、Login、LogOut のファイルを追加します。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

**WebApp1**という名前のプロジェクトを作成した場合は、次のコマンドを実行します。 それ以外の場合は、`ApplicationDbContext`に適切な名前空間を使用します。

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

PowerShell では、コマンドの区切り記号としてセミコロンを使用します。 PowerShell を使用する場合は、前の例に示したように、ファイルリスト内のセミコロンをエスケープするか、ファイルリストを二重引用符で囲みます。

---

### <a name="examine-register"></a>レジスタの確認

ユーザーが **[登録]** リンクをクリックすると、`RegisterModel.OnPostAsync` アクションが呼び出されます。 ユーザーは、`_userManager` オブジェクトに対して[Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)によって作成されます。 `_userManager` は、依存関係の挿入によって提供されます)。

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

ユーザーが正常に作成された場合は、`_signInManager.SignInAsync`の呼び出しによってユーザーがログインします。

**注:** 登録時にすぐにログインできないようにする手順については、「[アカウントの確認](xref:security/authentication/accconfirm#prevent-login-at-registration)」をご覧ください。

### <a name="log-in"></a>ログイン

ログイン フォームは次の時に表示されます :

* **ログイン**リンクが選択されています。
* ユーザーがアクセスを承認されていない、**または**システムによって認証されていない制限付きページにアクセスしようとしています。

ログインページのフォームが送信されると、`OnPostAsync` アクションが呼び出されます。 `PasswordSignInAsync` は、(依存関係の挿入によって提供される) `_signInManager` オブジェクトで呼び出されます。

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

基本 `Controller` クラスは、コントローラーメソッドからアクセスできる `User` プロパティを公開します。 たとえば、`User.Claims` を列挙し、承認の決定を行うことができます。 詳細については、<xref:security/authorization/introduction> を参照してください。

### <a name="log-out"></a>ログアウト

**Log out**リンクは、`LogoutModel.OnPost` アクションを呼び出します。 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync)者は、cookie に格納されているユーザーの要求をクリアします。

Post は*Pages/Shared/_LoginPartial*に指定されています。

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a>Identity のテスト

既定の web プロジェクト テンプレートは、ホーム ページへの匿名アクセスを許可しています。 Id をテストするには、[プライバシー] ページに[`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute)を追加します。

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

サインインしている場合は、サインアウトします。アプリを実行し、 **[プライバシー]** リンクを選択します。 ログイン ページにリダイレクトされます。

### <a name="explore-identity"></a>Identity を調べる

Identity についてさらに詳しく調べるには :

* [完全な id UI ソースの作成](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* 各ページのソースを確認し、デバッガーでステップ実行します。

## <a name="identity-components"></a>Identity コンポーネント

すべての Id 依存 NuGet パッケージは、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)に含まれています。

Id のプライマリパッケージは[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)です。 このパッケージには ASP.NET Core Id のインターフェイスのコアセットが含まれており、`Microsoft.AspNetCore.Identity.EntityFrameworkCore`によって含まれています。

## <a name="migrating-to-aspnet-core-identity"></a>ASP.NET Core Id への移行

既存の Id ストアを移行する方法の詳細とガイダンスについては、「[認証と id の移行](xref:migration/identity)」を参照してください。

## <a name="setting-password-strength"></a>パスワードの強度を設定する

パスワードの最小要件を設定するサンプルについては、「[構成](#pw)」を参照してください。

## <a name="next-steps"></a>次の手順

* SQLite を使用して Id を構成する方法については、 [GitHub の問題](https://github.com/dotnet/AspNetCore.Docs/issues/5131)を参照してください。
* [Identity の構成](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
