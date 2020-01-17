---
title: ASP.NET Core Identity の概要
author: rick-anderson
description: ASP.NET Core アプリで Id を使用します。 パスワードの要件 (RequireDigit、RequiredLength、RequiredUniqueChars など) を設定する方法について説明します。
ms.author: riande
ms.date: 01/15/2020
uid: security/authentication/identity
ms.openlocfilehash: 98fee261a741a20eed181ca5b9a4ebb693deeb63
ms.sourcegitcommit: cbd30479f42cbb3385000ef834d9c7d021fd218d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2020
ms.locfileid: "76146512"
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

サンプルコード ([ダウンロード方法)](xref:index#how-to-download-a-sample)を[表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample)します。

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a>認証を使用して Web アプリを作成する

個別のユーザー アカウントを使って、ASP.NET Core Web アプリケーション プロジェクトを作成します。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* [**ファイル**>**新しい**>**プロジェクト**] を選択します。
* **[ASP.NET Core Web アプリケーション]** を選択します。 プロジェクトに**WebApp1**という名前を付け、プロジェクトのダウンロードと同じ名前空間にします。 **[OK]** をクリックします。
* ASP.NET Core **Web アプリケーション**を選択し、 **[認証の変更]** を選択します。
* 個別のユーザー アカウントを **選択** して **OK** をクリックします。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

上記のコマンドは、SQLite を使用して Razor web アプリを作成します。 LocalDB を使用して web アプリを作成するには、次のコマンドを実行します。

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

生成されたプロジェクトは、 [ASP.NET Core Identity](xref:security/authentication/identity)を [Razor クラス ライブラリ](xref:razor-pages/ui-class)として提供します。 Identity の Razor クラス ライブラリは`Identity`エリアでエンドポイントを公開します。 例:

* /Identity/Account/Login
* /Identity/Account/Logout
* /Identity/Account/Manage

### <a name="apply-migrations"></a>移行を適用する

移行を適用してデータベースを初期化します。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

パッケージマネージャーコンソール (PMC) で次のコマンドを実行します。

`PM> Update-Database`

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

SQLite を使用する場合、この手順で移行する必要はありません。 LocalDB の場合は、次のコマンドを実行します。

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>テストレジスタとログイン

アプリを実行し、ユーザーを登録します。 画面サイズによっては、**登録**と**ログイン**のリンクを表示するためにナビゲーションのトグル ボタンを選択する必要があります。

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a>Identity サービスを構成します。

サービスは`ConfigureServices`で追加されます。 一般的なパターンは、すべての `Add{Service}` メソッドを呼び出した後、すべての `services.Configure{Service}` メソッドを呼び出すことです。

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

上の強調表示されたコードは、既定のオプション値を使用して Id を構成します。 サービスは[依存関係の注入](xref:fundamentals/dependency-injection)を通してアプリで利用できるようになります。

Id は <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>を呼び出すことによって有効になります。 `UseAuthentication` は認証[ミドルウェア](xref:fundamentals/middleware/index)を要求パイプラインに追加します。

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

テンプレートで生成されたアプリは、[承認](xref:security/authorization/secure-data)を使用しません。 アプリが承認を追加する正しい順序で追加されるように `app.UseAuthorization` が含まれています。 `UseRouting`、`UseAuthentication`、`UseAuthorization`、および `UseEndpoints` は、前のコードに示されている順序で呼び出す必要があります。

`IdentityOptions` と `Startup`の詳細については、「<xref:Microsoft.AspNetCore.Identity.IdentityOptions> と[アプリケーションの起動](xref:fundamentals/startup)」を参照してください。

## <a name="scaffold-register-login-and-logout"></a>スキャフォールディング Register、Login、および LogOut

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Register、Login、LogOut のファイルを追加します。 [id の権限を持つ Razor プロジェクトにスキャフォールディング](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)の手順に従って、このセクションで示すコードを生成してください。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

**WebApp1**という名前のプロジェクトを作成した場合、次のコマンドを実行します。 それ以外の場合は、`ApplicationDbContext`に正しい名前空間を適用してください :

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

PowerShell では、コマンドの区切り記号としてセミコロンを使用します。 PowerShell を使用する場合は、前の例に示したように、ファイルリスト内のセミコロンをエスケープするか、ファイルリストを二重引用符で囲みます。

スキャフォールディング Id の詳細については、「[スキャフォールディング identity to a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)」を参照してください。

---

### <a name="examine-register"></a>レジスタの確認

ユーザーが**登録**リンクをクリックすると、`RegisterModel.OnPostAsync`アクションが呼び出されます。 ユーザーは`_userManager`オブジェクトの[CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)によって作成されます。 `_userManager` は依存関係の挿入によってよって提供されます :

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

ユーザーが正常に作成された場合、ユーザーは`_signInManager.SignInAsync`の呼び出しによってログインされます。

登録時にすぐにログインできないようにする手順については、「[アカウントの確認](xref:security/authentication/accconfirm#prevent-login-at-registration)」をご覧ください。

### <a name="log-in"></a>ログイン

ログイン フォームは次の時に表示されます :

* **ログイン** リンクが選択されたとき。
* ユーザーがまだシステムに認証されていない**または**アクセスする権限がない状態で、制限されているページにアクセスしようとしたとき。

ログイン ページのフォームが送信されたとき、`OnPostAsync`アクションが呼び出されます。 `_signInManager`オブジェクト(依存関係の挿入によって提供されます)の`PasswordSignInAsync`が呼び出されます。

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

基本 `Controller` クラスは、コントローラーメソッドからアクセスできる `User` プロパティを公開します。 たとえば`User.Claims`を列挙して承認の決定を行うことができます。 詳細については、「 <xref:security/authorization/introduction>」を参照してください。

### <a name="log-out"></a>ログアウト

**ログアウト**リンクから`LogoutModel.OnPost`アクションが呼び出されます。 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

前のコードでは、ブラウザーが新しい要求を実行し、ユーザーの id が更新されるように、コード `return RedirectToPage();` がリダイレクトである必要があります。

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) はcookie に格納されているユーザー要求をクリアします。

Postは *Pages/Shared/_LoginPartial.cshtml* で指定されています :

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a>Identity のテスト

既定の web プロジェクト テンプレートは、ホーム ページへの匿名アクセスを許可しています。 Id をテストするには、 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)を追加します。

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

サインインしている場合は、サインアウトします。アプリを実行し、 **[プライバシー]** リンクを選択します。 ログイン ページにリダイレクトされます。

### <a name="explore-identity"></a>Identity を調べる

Identity についてさらに詳しく調べるには :

* [完全な Identity の UI のソースを作成します。](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* 各ページのソースを確認し、デバッガーでステップ実行します。

## <a name="identity-components"></a>Identity コンポーネント

すべての Id に依存する NuGet パッケージは、 [ASP.NET Core 共有フレームワーク](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)に含まれています。

Id のプライマリパッケージは[Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)です。 このパッケージは ASP.NET Core Identity のインターフェイスのコアセットを含んでいて、また`Microsoft.AspNetCore.Identity.EntityFrameworkCore`に含まれています。

## <a name="migrating-to-aspnet-core-identity"></a>ASP.NET Core Id への移行

既存の Identity ストアの移行についての詳細とガイダンスについては、次を参照してください : [移行の認証と Id](xref:migration/identity)。

## <a name="setting-password-strength"></a>パスワードの強度を設定する

パスワードの最小要件を設定するサンプルについては[構成](#pw)を参照してください。

## <a name="adddefaultidentity-and-addidentity"></a>AddDefaultIdentity と AddIdentity

<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> は ASP.NET Core 2.1 で導入されました。 `AddDefaultIdentity`の呼び出しは、次の呼び出しに似ています。

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

詳細については[AddDefaultIdentity のソース](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63)を参照してください。

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

## <a name="next-steps"></a>次のステップ

* SQLite を使用して Id を構成する方法については、 [GitHub の問題](https://github.com/aspnet/AspNetCore.Docs/issues/5131)を参照してください。
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

サンプルコード ([ダウンロード方法)](xref:index#how-to-download-a-sample)を[表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/)します。

このトピックでは、Identity を使用してユーザーを登録、ログイン、ログアウトする方法について説明します。 Id を使用するアプリを作成する方法の詳細については、この記事の最後にある「次のステップ」を参照してください。

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a>AddDefaultIdentity と AddIdentity

<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> は ASP.NET Core 2.1 で導入されました。 `AddDefaultIdentity`の呼び出しは、次の呼び出しに似ています。

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

詳細については[AddDefaultIdentity のソース](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63)を参照してください。

## <a name="create-a-web-app-with-authentication"></a>認証を使用して Web アプリを作成する

個別のユーザー アカウントを使って、ASP.NET Core Web アプリケーション プロジェクトを作成します。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* [**ファイル**>**新しい**>**プロジェクト**] を選択します。
* **[ASP.NET Core Web アプリケーション]** を選択します。 プロジェクトに**WebApp1**という名前を付け、プロジェクトのダウンロードと同じ名前空間にします。 **[OK]** をクリックします。
* ASP.NET Core **Web アプリケーション**を選択し、 **[認証の変更]** を選択します。
* 個別のユーザー アカウントを **選択** して **OK** をクリックします。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

生成されたプロジェクトは、 [ASP.NET Core Identity](xref:security/authentication/identity)を [Razor クラス ライブラリ](xref:razor-pages/ui-class)として提供します。 Identity の Razor クラス ライブラリは`Identity`エリアでエンドポイントを公開します。 例:

* /Identity/Account/Login
* /Identity/Account/Logout
* /Identity/Account/Manage

### <a name="apply-migrations"></a>移行を適用する

移行を適用してデータベースを初期化します。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

パッケージマネージャーコンソール (PMC) で次のコマンドを実行します。

```PM> Update-Database```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>テストレジスタとログイン

アプリを実行し、ユーザーを登録します。 画面サイズによっては、**登録**と**ログイン**のリンクを表示するためにナビゲーションのトグル ボタンを選択する必要があります。

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a>Identity サービスを構成します。

サービスは`ConfigureServices`で追加されます。 一般的なパターンは、すべての `Add{Service}` メソッドを呼び出した後、すべての `services.Configure{Service}` メソッドを呼び出すことです。

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

上記のコードでは、既定のオプション値で Identity を構成します。 サービスは[依存関係の注入](xref:fundamentals/dependency-injection)を通してアプリで利用できるようになります。

Identity は[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)を呼び出すことによって有効になります。 `UseAuthentication` は認証[ミドルウェア](xref:fundamentals/middleware/index)を要求パイプラインに追加します。

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

詳細については、次の [IdentityOptions クラス](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)と[アプリケーションの起動](xref:fundamentals/startup) を参照してください。

## <a name="scaffold-register-login-and-logout"></a>スキャフォールディング Register、Login、および LogOut

[id の権限を持つ Razor プロジェクトにスキャフォールディング](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)の手順に従って、このセクションで示すコードを生成してください。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Register、Login、LogOut のファイルを追加します。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

**WebApp1**という名前のプロジェクトを作成した場合、次のコマンドを実行します。 それ以外の場合は、`ApplicationDbContext`に正しい名前空間を適用してください :

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

PowerShell では、コマンドの区切り記号としてセミコロンを使用します。 PowerShell を使用する場合は、前の例に示したように、ファイルリスト内のセミコロンをエスケープするか、ファイルリストを二重引用符で囲みます。

---

### <a name="examine-register"></a>レジスタの確認

ユーザーが**登録**リンクをクリックすると、`RegisterModel.OnPostAsync`アクションが呼び出されます。 ユーザーは`_userManager`オブジェクトの[CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)によって作成されます。 `_userManager` は依存関係の挿入によってよって提供されます :

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

ユーザーが正常に作成された場合、ユーザーは`_signInManager.SignInAsync`の呼び出しによってログインされます。

**注:** 登録時にすぐにログインできないようにする手順については、「[アカウントの確認](xref:security/authentication/accconfirm#prevent-login-at-registration)」をご覧ください。

### <a name="log-in"></a>ログイン

ログイン フォームは次の時に表示されます :

* **ログイン** リンクが選択されたとき。
* ユーザーがまだシステムに認証されていない**または**アクセスする権限がない状態で、制限されているページにアクセスしようとしたとき。

ログイン ページのフォームが送信されたとき、`OnPostAsync`アクションが呼び出されます。 `_signInManager`オブジェクト(依存関係の挿入によって提供されます)の`PasswordSignInAsync`が呼び出されます。

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

ベース`Controller`クラスでは、コントローラー メソッドからアクセスできる`User`プロパティを公開します。 たとえば`User.Claims`を列挙して承認の決定を行うことができます。 詳細については、「 <xref:security/authorization/introduction>」を参照してください。

### <a name="log-out"></a>ログアウト

**ログアウト**リンクから`LogoutModel.OnPost`アクションが呼び出されます。 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) はcookie に格納されているユーザー要求をクリアします。

Postは *Pages/Shared/_LoginPartial.cshtml* で指定されています :

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a>Identity のテスト

既定の web プロジェクト テンプレートは、ホーム ページへの匿名アクセスを許可しています。 Identity をテストするために、[ `[Authorize]` ](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute)をPrivacy ページに追加します。

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

サインインしている場合は、サインアウトします。アプリを実行し、 **[プライバシー]** リンクを選択します。 ログイン ページにリダイレクトされます。

### <a name="explore-identity"></a>Identity を調べる

Identity についてさらに詳しく調べるには :

* [完全な Identity の UI のソースを作成します。](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* 各ページのソースを確認し、デバッガーでステップ実行します。

## <a name="identity-components"></a>Identity コンポーネント

すべての Id 依存の NuGet パッケージは[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれます。

Id のプライマリパッケージは[Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)です。 このパッケージは ASP.NET Core Identity のインターフェイスのコアセットを含んでいて、また`Microsoft.AspNetCore.Identity.EntityFrameworkCore`に含まれています。

## <a name="migrating-to-aspnet-core-identity"></a>ASP.NET Core Id への移行

既存の Identity ストアの移行についての詳細とガイダンスについては、次を参照してください : [移行の認証と Id](xref:migration/identity)。

## <a name="setting-password-strength"></a>パスワードの強度を設定する

パスワードの最小要件を設定するサンプルについては[構成](#pw)を参照してください。

## <a name="next-steps"></a>次のステップ

* SQLite を使用して Id を構成する方法については、 [GitHub の問題](https://github.com/aspnet/AspNetCore.Docs/issues/5131)を参照してください。
* [Identity の構成](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
