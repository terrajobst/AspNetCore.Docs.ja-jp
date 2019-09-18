---
title: ASP.NET Core Identity の概要
author: rick-anderson
description: ASP.NET Core アプリでは、Identity を使用します。 パスワードの要件 (RequireDigit、RequiredLength、RequiredUniqueChars など) を設定する方法について説明します。
ms.author: riande
ms.date: 03/26/2019
uid: security/authentication/identity
ms.openlocfilehash: 979681cfc196aca9fb5097583d99a086e1c597ba
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082458"
---
# <a name="introduction-to-identity-on-aspnet-core"></a>ASP.NET Core Identity の概要

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core Identity は、ASP.NET Core アプリにログイン機能を追加するメンバーシップ システムです。 ユーザーがIdentity にログイン情報を格納するようにアカウントを作成することもできますし、外部のログインプロバイダーを利用することもできます。 サポートされている外部ログイン プロバイダーには、 [Facebook、Google、Microsoft アカウント、および Twitter](xref:security/authentication/social/index)があります。

Identity は、ユーザー名、パスワード、およびプロファイル データを格納するために SQL Server データベースを使用するように構成できます。 そのほかにも、たとえば Azure Table Storage のような別の永続的なストアを使用することもできます。

[サンプルコードの表示とダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/)([ダウンロードする方法)](xref:index#how-to-download-a-sample))。

このトピックでは、Identityを使って登録、ログイン、ログアウトする方法について説明します。 Identity を使用するアプリを作成する詳細な手順については、この記事の最後の「次の手順」を参照してください。

::: moniker range=">= aspnetcore-2.1"

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a>AddDefaultIdentity と AddIdentity

[AddDefaultIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionuiextensions.adddefaultidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionUIExtensions_AddDefaultIdentity__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__)は ASP.NET Core 2.1 で導入されました。 `AddDefaultIdentity`の呼び出しは、次の呼び出しに似ています。

* [AddIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_AddIdentity__2_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__)
* [AddDefaultUI](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderuiextensions.adddefaultui?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderUIExtensions_AddDefaultUI_Microsoft_AspNetCore_Identity_IdentityBuilder_)
* [AddDefaultTokenProviders](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderextensions.adddefaulttokenproviders?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderExtensions_AddDefaultTokenProviders_Microsoft_AspNetCore_Identity_IdentityBuilder_)

詳細については[AddDefaultIdentity のソース](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63)を参照してください。

::: moniker-end

## <a name="create-a-web-app-with-authentication"></a>認証を使用して Web アプリを作成する

個別のユーザー アカウントを使って、ASP.NET Core Web アプリケーション プロジェクトを作成します。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* **[ファイル]**  >  **[新規作成]**  >  **[プロジェクト]** を順に選択します。
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

データベースを初期化するためのマイグレーションを適用します。

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

サービスは`ConfigureServices`で追加されます。 一般的なパターンでは、すべての `Add{Service}` メソッドを呼び出した後、すべての `services.Configure{Service}` メソッドを呼び出します。

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

上記のコードでは、既定のオプション値で Identity を構成します。 サービスは[依存関係の注入](xref:fundamentals/dependency-injection)を通してアプリで利用できるようになります。

   Identity は[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)を呼び出すことによって有効になります。 `UseAuthentication` は認証[ミドルウェア](xref:fundamentals/middleware/index)を要求パイプラインに追加します。

   [!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,11-28,30-42)]

   サービスは[依存関係の注入](xref:fundamentals/dependency-injection)を通してアプリで利用できるようになります。

   Identity は`Configure`メソッド内で`UseAuthentication`を呼び出すことによって、アプリケーションで有効になります。 `UseAuthentication` は認証[ミドルウェア](xref:fundamentals/middleware/index)を要求パイプラインに追加します。

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configure&highlight=17)]

::: moniker-end

::: moniker range="= aspnetcore-1.1"

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,13-33)]

   これらのサービスは[依存関係の注入](xref:fundamentals/dependency-injection)を通してアプリケーションで利用できるようになります。

   Identity は`Configure`メソッド内で`UseIdentity`を呼び出すことによって、アプリケーションで利用できるようになります。 `UseIdentity` はcookie ベースの認証[ミドルウェア](xref:fundamentals/middleware/index)を要求パイプラインに追加します。

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configure&highlight=21)]

::: moniker-end

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

::: moniker range=">= aspnetcore-2.1"

   ユーザーが**登録**リンクをクリックすると、`RegisterModel.OnPostAsync`アクションが呼び出されます。 ユーザーは`_userManager`オブジェクトの[CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)によって作成されます。 `_userManager` は依存関係の挿入によってよって提供されます :

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7,22)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   ユーザーが**登録**リンクをクリックすると、`AccountController`の`Register`アクションが呼び出されます。 `Register`アクションは`_userManager`オブジェクト (依存関係の挿入によって`AccountController`に提供される)の`CreateAsync`を呼び出してユーザーを作成します。

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_register&highlight=11)]

::: moniker-end

   ユーザーが正常に作成された場合、ユーザーは`_signInManager.SignInAsync`の呼び出しによってログインされます。

   **注:** 登録時の即時のログインを防止する手順については[アカウントの確認](xref:security/authentication/accconfirm#prevent-login-at-registration)を参照してください。

### <a name="log-in"></a>ログイン

::: moniker range=">= aspnetcore-2.1"

ログイン フォームは次の時に表示されます :

* **ログイン** リンクが選択されたとき。
* ユーザーがまだシステムに認証されていない**または**アクセスする権限がない状態で、制限されているページにアクセスしようとしたとき。

ログイン ページのフォームが送信されたとき、`OnPostAsync`アクションが呼び出されます。 `_signInManager`オブジェクト(依存関係の挿入によって提供されます)の`PasswordSignInAsync`が呼び出されます。

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

   ベース`Controller`クラスでは、コントローラー メソッドからアクセスできる`User`プロパティを公開します。 たとえば`User.Claims`を列挙して承認の決定を行うことができます。 詳細については「ASP.NET Core での承認の概要<xref:security/authorization/introduction> 」を参照してください。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

ログイン フォームは、ユーザーが**ログイン**リンク選択したとき表示されます。また認証が必要なページにアクセスしたときにリダイレクトされます。 ユーザーがログイン ページでフォームを送信すると、`AccountController`の`Login`アクションが呼び出されます。

`Login`アクションは`_signInManager`オブジェクト (依存関係の挿入によって`AccountController`に提供されます)の`PasswordSignInAsync`を呼び出します。

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_login&highlight=13-14)]

ベース (`Controller`または`PageModel`) クラスは、`User`プロパティを公開します。 たとえば、`User.Claims`を列挙して承認決定を行うことができます。

::: moniker-end

### <a name="log-out"></a>ログアウト

::: moniker range=">= aspnetcore-2.1"

**ログアウト**リンクから`LogoutModel.OnPost`アクションが呼び出されます。 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) はcookie に格納されているユーザー要求をクリアします。

Postは *Pages/Shared/_LoginPartial.cshtml* で指定されています :

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   **ログアウト**リンクをクリックすると、`LogOut`アクションが呼び出されます。

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_logout&highlight=7)]

   上記のコードは`_signInManager.SignOutAsync`メソッドを呼び出しています。 メソッド`SignOutAsync`は、cookie に格納されているユーザーの要求をクリアします。

::: moniker-end

## <a name="test-identity"></a>Identity のテスト

既定の web プロジェクト テンプレートは、ホーム ページへの匿名アクセスを許可しています。 Identity をテストするために、[ `[Authorize]` ](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute)をPrivacy ページに追加します。

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=6)]

サインイン済みの場合、サインアウトします。アプリを実行し、**プライバシー**リンクを選択します。 ログインページにリダイレクトされます。

::: moniker range=">= aspnetcore-2.1"

### <a name="explore-identity"></a>Identity を調べる

Identity についてさらに詳しく調べるには :

* [完全な Identity の UI のソースを作成します。](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* 各ページのソースを確認し、デバッガーでステップ実行します。

::: moniker-end

## <a name="identity-components"></a>Identity コンポーネント

::: moniker range=">= aspnetcore-2.1"

すべての Identity 依存の NuGet パッケージは[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれます。

::: moniker-end

Identity のプライマリパッケージは[Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)です。 このパッケージは ASP.NET Core Identity のインターフェイスのコアセットを含んでいて、また`Microsoft.AspNetCore.Identity.EntityFrameworkCore`に含まれています。

## <a name="migrating-to-aspnet-core-identity"></a>ASP.NET Core Id への移行

既存の Identity ストアの移行についての詳細とガイダンスについては、次を参照してください : [移行の認証と Id](xref:migration/identity)。

## <a name="setting-password-strength"></a>パスワードの強度を設定する

パスワードの最小要件を設定するサンプルについては[構成](#pw)を参照してください。

## <a name="next-steps"></a>次の手順

* [Identity の構成](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>
