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
# <a name="scaffold-identity-in-aspnet-core-projects"></a>ASP.NET Core プロジェクトにおけるIdentityのスキャフォールディング

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core 2.1 以降では[ASP.NET Core Identity](xref:security/authentication/identity)として、 [Razor クラス ライブラリ](xref:razor-pages/ui-class)します。 Id を含むアプリケーションでは、選択的に Identity Razor クラス ライブラリ (RCL) に含まれているソース コードを追加する scaffolder を適用できます。 コードを変更して動作を変更できるように、ソース コードを生成できます。 たとえば、登録で使用するコードを生成するようにスキャフォルダーに指示できます。 生成されたコードは、Identity RCL の同じコードよりも優先されます。 UI のフル コントロールを取得し、既定 RCL を使用しない、セクションをご覧ください。 [UI のソースの完全な id 作成](#full)です。

実行するアプリケーション**いない**含める認証 RCL Identity パッケージを追加する scaffolder を適用できます。 生成される Identity コードの選択オプションがあります。

スキャフォルダーは、必要なコードの大部分を生成するが、プロセスを完了するプロジェクトを更新する必要があります。 このドキュメントでは、Id のスキャフォールディング更新の完了に必要な手順について説明します。

Identity scaffolder が実行される、 *ScaffoldingReadme.txt*プロジェクト ディレクトリにファイルが作成されます。 *ScaffoldingReadme.txt*ファイルには、一般的な手順について Id スキャフォールディングの更新の完了に必要なものが含まれています。 このドキュメントより詳細な手順が含まれています、 *ScaffoldingReadme.txt*ファイル。

ファイルの相違点を示し、変更をバックアップすることができますをソース管理システムを使用することをお勧めします。 Identity scaffolder を実行した後、変更を検査します。

> [!NOTE]
> 使用する場合に必要なサービス[2 要素認証](xref:security/authentication/identity-enable-qrcodes)、[アカウントの確認とパスワードの回復](xref:security/authentication/accconfirm)、および Id を持つその他のセキュリティ機能。 Identity をスキャフォールディングするときにサービスまたはサービスのスタブは生成されません。 これらの機能を有効にするサービスを手動で追加する必要があります。 たとえばを参照してください[確認の電子メールを必要と](xref:security/authentication/accconfirm#require-email-confirmation)します。

## <a name="scaffold-identity-into-an-empty-project"></a>空のプロジェクトに対するidentityのスキャフォールディング

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

次の強調表示されている呼び出しを追加、`Startup`クラス。

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>既存の承認なしのRazorプロジェクトに対するidentityのスキャフォールディング

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

Id が構成されて*Areas/Identity/IdentityHostingStartup.cs*します。 詳細については、次を参照してください。 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)します。

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>マイグレーション、UseAuthentication、およびレイアウト

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>認証の有効化

`Configure`のメソッド、`Startup`クラスを呼び出す[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)後`UseStaticFiles`:

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>レイアウトの変更

省略可能:部分的なログインを追加 (`_LoginPartial`)、レイアウト ファイルに。

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>承認ありの Razor プロジェクトに対する identity のスキャフォールディング

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
いくつかの Id オプションが構成されている*Areas/Identity/IdentityHostingStartup.cs*します。 詳細については、次を参照してください。 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)します。

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>既存の承認なしの MVC プロジェクトに対する identity のスキャフォールディング

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

省略可能:部分的なログインを追加 (`_LoginPartial`) に、 *Views/Shared/_Layout.cshtml*ファイル。

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* 移動、 *Pages/Shared/_LoginPartial.cshtml*ファイルを*Views/Shared/_LoginPartial.cshtml*

Id が構成されて*Areas/Identity/IdentityHostingStartup.cs*します。 詳細については、IHostingStartup を参照してください。

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

呼び出す[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)後`UseStaticFiles`:

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>承認ありの MVC プロジェクトに対する identity のスキャフォールディング

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext --files Account.Register
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

削除、*ページ/共有*フォルダーとそのフォルダー内のファイル。

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>identity の全ての UI のソースの作成

Identity UI を完全に制御を維持するために、Identity scaffolder を実行して選択して**すべてのファイルを上書き**します。

次の強調表示されたコードは、ASP.NET Core 2.1 の web アプリで Id を持つ既定の Identity の UI を置換する変更を示しています。 これは、Identity UI の完全に制御をする可能性があります。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

既定の Id は、次のコードに置き換えられます。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

次のコード セット、 [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)、 [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)、および[AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

登録、`IEmailSender`例については、実装します。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

## <a name="additional-resources"></a>その他の技術情報

* [ASP.NET Core 2.1 以降の認証コードの変更](xref:migration/20_21#changes-to-authentication-code)
