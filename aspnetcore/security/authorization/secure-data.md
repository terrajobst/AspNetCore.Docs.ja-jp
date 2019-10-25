---
title: 承認によって保護されたユーザーデータを含む ASP.NET Core アプリを作成する
author: rick-anderson
description: 承認によって保護されたユーザーデータを使用して Razor Pages アプリを作成する方法について説明します。 HTTPS、認証、セキュリティ、ASP.NET Core Id を含みます。
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
uid: security/authorization/secure-data
ms.openlocfilehash: 6e2f785a6dc014884f105766686f284cb2685530
ms.sourcegitcommit: 383017d7060a6d58f6a79cf4d7335d5b4b6c5659
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2019
ms.locfileid: "72816151"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a>承認によって保護されたユーザーデータを含む ASP.NET Core アプリを作成する

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) および [Joe Audette](https://twitter.com/joeaudette)

::: moniker range="<= aspnetcore-1.1"

ASP.NET Core MVC バージョンについては、[この PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf)を参照してください。 このチュートリアルの ASP.NET Core 1.1 バージョンは、[この](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data)フォルダーにあります。 1\.1 ASP.NET Core サンプルは、「」[のサンプルに](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2)含まれています。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[次の pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)を参照

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

このチュートリアルでは、承認によって保護されたユーザーデータを使用して ASP.NET Core web アプリを作成する方法について説明します。 認証済み (登録済み) のユーザーが作成した連絡先の一覧が表示されます。 セキュリティグループには次の3つがあります。

* **登録されているユーザー**は、すべての承認済みデータを表示したり、自分のデータを編集または削除したりできます。
* **管理者**は、連絡先データを承認または拒否できます。 承認された連絡先だけがユーザーに表示されます。
* **管理者**は、任意のデータを承認/拒否したり、編集/削除したりできます。

このドキュメントの画像は、最新のテンプレートと完全には一致しません。

次の図では、user Rick (`rick@example.com`) がサインインしています。 Rick は、承認された連絡先を表示して**編集**/**削除**/、連絡先の新しいリンクを**作成**することのみができます。 Rick によって作成された最後のレコードにのみ、**編集**および**削除**のリンクが表示されます。 他のユーザーには、マネージャーまたは管理者が状態を "承認済み" に変更するまで、最後のレコードは表示されません。

![Rick がサインインしていることを示すスクリーンショット](secure-data/_static/rick.png)

次の図では、`manager@contoso.com` がサインインし、マネージャーのロールに含まれています。

![サインイン manager@contoso.com を示すスクリーンショット](secure-data/_static/manager1.png)

次の図は、連絡先のマネージャーの詳細ビューを示しています。

![連絡先のマネージャーの表示](secure-data/_static/manager.png)

**[承認]** ボタンと **[却下]** ボタンは、マネージャーと管理者に対してのみ表示されます。

次の図では、`admin@contoso.com` がサインインし、管理者のロールに含まれています。

![サインイン admin@contoso.com を示すスクリーンショット](secure-data/_static/admin.png)

管理者には、すべての特権があります。 連絡先の読み取り、編集、削除、および連絡先の状態の変更を行うことができます。

アプリは、次の `Contact` モデルを[スキャフォールディング](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model)することによって作成されました。

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

このサンプルには、次の承認ハンドラーが含まれています。

* `ContactIsOwnerAuthorizationHandler`: ユーザーがデータを編集できるようになります。
* `ContactManagerAuthorizationHandler`: 管理者が連絡先を承認または拒否できるようにします。
* `ContactAdministratorsAuthorizationHandler`: 管理者は、連絡先を承認または拒否したり、連絡先を編集または削除したりできます。

## <a name="prerequisites"></a>必要条件

このチュートリアルは高度です。 次のことを理解している必要があります。

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [認証](xref:security/authentication/identity)
* [アカウントの確認とパスワードの回復](xref:security/authentication/accconfirm)
* [承認](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>スターターおよび完成したアプリ

完成し[た](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples)アプリを[ダウンロード](xref:index#how-to-download-a-sample)します。 完成したアプリを[テスト](#test-the-completed-app)して、セキュリティ機能に慣れるようにします。

### <a name="the-starter-app"></a>スターターアプリ

[スターター](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/)アプリを[ダウンロード](xref:index#how-to-download-a-sample)します。

アプリを実行し、 **[Contactmanager]** リンクをタップして、連絡先を作成、編集、および削除できることを確認します。

## <a name="secure-user-data"></a>ユーザーデータのセキュリティ保護

以下のセクションでは、セキュリティで保護されたユーザーデータアプリを作成するための主要な手順について説明します。 完成したプロジェクトを参照した方が便利な場合があります。

### <a name="tie-the-contact-data-to-the-user"></a>連絡先データをユーザーに関連付ける

ASP.NET [id](xref:security/authentication/identity)ユーザー id を使用すると、ユーザーがデータを編集できるようになりますが、他のユーザーデータは編集できません。 `OwnerID` と `ContactStatus` を `Contact` モデルに追加します。

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID` は、 [id](xref:security/authentication/identity)データベースの `AspNetUser` テーブルのユーザー ID です。 [`Status`] フィールドによって、連絡先が一般ユーザーに表示されるかどうかが決まります。

新しい移行を作成し、データベースを更新します。

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a>Id への役割サービスの追加

役割サービスを追加するには、 [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1)を追加します。

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a>認証されたユーザーが必要

ユーザーが認証されるようにするには、既定の認証ポリシーを設定します。

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 Razor ページ、コントローラー、またはアクションメソッドレベルで、`[AllowAnonymous]` 属性を使用して認証をオプトアウトできます。 既定の認証ポリシーを設定すると、ユーザーの認証が必要になり、新しく追加された Razor Pages とコントローラーは保護されます。 既定で認証が必要になるのは、新しいコントローラーに依存していて、Razor Pages `[Authorize]` 属性を含めるよりも安全です。

匿名ユーザーが登録前にサイトに関する情報を取得できるように、 [Allowanonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute)をインデックスおよびプライバシーページに追加します。

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a>テストアカウントを構成する

`SeedData` クラスは、管理者とマネージャーの2つのアカウントを作成します。 これらのアカウントのパスワードを設定するには、[シークレットマネージャーツール](xref:security/app-secrets)を使用します。 プロジェクトディレクトリ ( *Program.cs*を含むディレクトリ) のパスワードを設定します。

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

強力なパスワードが指定されていない場合、`SeedData.Initialize` が呼び出されると例外がスローされます。

テストパスワードを使用するように `Main` を更新します。

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>テストアカウントを作成し、連絡先を更新する

`SeedData` クラスの `Initialize` メソッドを更新して、テストアカウントを作成します。

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

管理者のユーザー ID と `ContactStatus` を連絡先に追加します。 いずれかの連絡先を "送信済み" にして、"拒否" するようにします。 すべての連絡先にユーザー ID と状態を追加します。 1つの連絡先のみが表示されます。

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>所有者、マネージャー、および管理者の承認ハンドラーを作成する

*承認*フォルダーに `ContactIsOwnerAuthorizationHandler` クラスを作成します。 `ContactIsOwnerAuthorizationHandler` は、リソースに対して動作しているユーザーがリソースを所有していることを確認します。

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

`ContactIsOwnerAuthorizationHandler` は[コンテキストを呼び出します。](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)現在の認証済みユーザーが連絡先の所有者である場合は成功します。 通常、承認ハンドラーは次のようになります。

* 要件が満たされた場合に `context.Succeed` を返します。
* 要件を満たしていない場合は `Task.CompletedTask` を返します。 `Task.CompletedTask` は成功でも失敗でも&mdash;他の承認ハンドラーの実行を許可します。

明示的に失敗する必要がある場合は、コンテキストを返し[ます。失敗](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)。

アプリを使用すると、連絡先所有者は自分のデータを編集/削除/作成できます。 `ContactIsOwnerAuthorizationHandler` は、要件パラメーターで渡された操作を確認する必要はありません。

### <a name="create-a-manager-authorization-handler"></a>マネージャーの承認ハンドラーを作成する

*承認*フォルダーに `ContactManagerAuthorizationHandler` クラスを作成します。 `ContactManagerAuthorizationHandler` は、リソースに対して動作しているユーザーがマネージャーであることを確認します。 コンテンツの変更を承認または拒否できるのは、管理者のみです (新規または変更)。

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>管理者の承認ハンドラーを作成する

*承認*フォルダーに `ContactAdministratorsAuthorizationHandler` クラスを作成します。 `ContactAdministratorsAuthorizationHandler` は、リソースに対して動作しているユーザーが管理者であることを確認します。 管理者は、すべての操作を実行できます。

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>認証ハンドラーを登録する

Entity Framework Core を使用するサービスは、 [Addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)を使用して[依存関係の挿入](xref:fundamentals/dependency-injection)に登録する必要があります。 `ContactIsOwnerAuthorizationHandler` は Entity Framework Core 上に構築された ASP.NET Core [id](xref:security/authentication/identity)を使用します。 サービスコレクションにハンドラーを登録して、[依存関係の挿入](xref:fundamentals/dependency-injection)によって `ContactsController` で使用できるようにします。 `ConfigureServices`の末尾に次のコードを追加します。

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

`ContactAdministratorsAuthorizationHandler` と `ContactManagerAuthorizationHandler` はシングルトンとして追加されます。 EF を使用せず、必要なすべての情報が `HandleRequirementAsync` メソッドの `Context` パラメーターに含まれているため、これらはシングルトンです。

## <a name="support-authorization"></a>認証のサポート

このセクションでは、Razor Pages を更新し、操作要件クラスを追加します。

### <a name="review-the-contact-operations-requirements-class"></a>Contact operation の要件クラスを確認する

`ContactOperations` クラスを確認します。 このクラスには、アプリがサポートする要件が含まれています。

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a>連絡先の基本クラスを作成 Razor Pages

連絡先 Razor Pages で使用されるサービスを含む基本クラスを作成します。 基本クラスは、初期化コードを1つの場所に配置します。

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

上のコードでは以下の操作が行われます。

* 承認ハンドラーにアクセスするための `IAuthorizationService` サービスを追加します。
* Id `UserManager` サービスを追加します。
* `ApplicationDbContext` を追加します。

### <a name="update-the-createmodel"></a>CreateModel を更新する

Create page model コンストラクターを更新して、`DI_BasePageModel` 基底クラスを使用するようにします。

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

`CreateModel.OnPostAsync` メソッドを次のように更新します。

* `Contact` モデルにユーザー ID を追加します。
* 承認ハンドラーを呼び出して、ユーザーが連絡先を作成するアクセス許可を持っていることを確認します。

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>IndexModel を更新する

`OnGetAsync` メソッドを更新して、承認された連絡先だけが一般ユーザーに表示されるようにします。

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>EditModel を更新する

ユーザーが連絡先を所有していることを確認するための承認ハンドラーを追加します。 リソース承認が検証されているため、`[Authorize]` 属性では不十分です。 属性が評価された場合、アプリにはリソースへのアクセス権がありません。 リソースベースの承認は、必須である必要があります。 アプリケーションがリソースにアクセスできるようになったら、そのアプリをページモデルに読み込むか、ハンドラー自体に読み込むことによって、チェックを実行する必要があります。 リソースキーを渡すことによって、リソースに頻繁にアクセスします。

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>DeleteModel を更新する

承認ハンドラーを使用するように delete ページモデルを更新して、ユーザーが連絡先に対する delete 権限を持っていることを確認します。

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>ビューに承認サービスを挿入する

現在、UI には、ユーザーが変更できない連絡先の編集と削除のリンクが表示されています。

*Pages/_ViewImports*ファイルに承認サービスを挿入して、すべてのビューで使用できるようにします。

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

上記のマークアップでは、いくつかの `using` ステートメントが追加されます。

*Pages/Contacts/Index. cshtml*の**編集**と**削除**のリンクを更新して、適切なアクセス許可を持つユーザーにのみ表示されるようにします。

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> データを変更するアクセス許可がないユーザーからのリンクを非表示にしても、アプリはセキュリティで保護されません。 リンクを非表示にすると、有効なリンクのみが表示されるため、アプリのユーザーがわかりやすくなります。 ユーザーは、生成された Url をハッキングして、所有していないデータに対する編集操作と削除操作を呼び出すことができます。 Razor ページまたはコントローラーは、データをセキュリティで保護するためにアクセスチェックを強制する必要があります。

### <a name="update-details"></a>更新プログラムの詳細

上司が連絡先を承認または拒否できるように、詳細ビューを更新します。

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

詳細ページモデルを更新します。

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>ロールに対するユーザーの追加または削除

次の情報については、[この問題](https://github.com/aspnet/AspNetCore.Docs/issues/8502)を参照してください。

* ユーザーから特権を削除しています。 たとえば、チャットアプリでユーザーのミュートを行います。
* ユーザーへの特権の追加。

## <a name="differences-between-challenge-vs-forbid"></a>チャレンジと禁止の違い

このアプリでは、認証された[ユーザーを要求](#require-authenticated-users)する既定のポリシーを設定します。 次のコードでは、匿名ユーザーを許可します。 匿名ユーザーは、チャレンジと禁止の違いを示すことができます。

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

上のコードでは以下の操作が行われます。

* ユーザーが認証**されていない**場合は、`ChallengeResult` が返されます。 `ChallengeResult` が返されると、ユーザーはサインインページにリダイレクトされます。
* ユーザーが認証されているが承認されていない場合、`ForbidResult` が返されます。 `ForbidResult` が返されると、ユーザーは [アクセス拒否] ページにリダイレクトされます。

## <a name="test-the-completed-app"></a>完成したアプリをテストする

シードされたユーザーアカウントのパスワードをまだ設定していない場合は、[シークレットマネージャーツール](xref:security/app-secrets#secret-manager)を使用してパスワードを設定します。

* 強力なパスワードを選択する: 8 個以上の文字と、少なくとも1つの大文字、数字、および記号を使用します。 たとえば、`Passw0rd!` は強力なパスワードの要件を満たしています。
* プロジェクトのフォルダーから次のコマンドを実行します。 `<PW>` はパスワードです。

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

アプリに連絡先がある場合:

* `Contact` テーブル内のすべてのレコードを削除します。
* アプリケーションを再起動してデータベースをシード処理します。

完成したアプリをテストする簡単な方法は、3つの異なるブラウザー (または incognito/InPrivate セッション) を起動することです。 1つのブラウザーで、新しいユーザーを登録します (たとえば、`test@example.com`)。 別のユーザーを使用して各ブラウザーにサインインします。 次の操作を確認します。

* 登録済みのユーザーは、すべての承認済み連絡先データを表示できます。
* 登録されているユーザーは、自分のデータを編集または削除できます。
* マネージャーは、連絡先データを承認/拒否することができます。 `Details` ビューには、 **[承認]** ボタンと **[却下]** ボタンが表示されます。
* 管理者は、すべてのデータを承認/拒否し、編集/削除することができます。

| ユーザー                | アプリによるシード処理 | オプション                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | Ｘ                | 独自のデータを編集または削除します。                |
| manager@contoso.com | [はい]               | 自分のデータを承認/拒否し、編集/削除します。 |
| admin@contoso.com   | [はい]               | すべてのデータを承認/拒否し、編集/削除します。 |

管理者のブラウザーで連絡先を作成します。 管理者の連絡先から削除と編集のための URL をコピーします。 これらのリンクをテストユーザーのブラウザーに貼り付けて、テストユーザーがこれらの操作を実行できないことを確認します。

## <a name="create-the-starter-app"></a>スターターアプリを作成する

* "ContactManager" という名前の Razor Pages アプリを作成します
  * **個々のユーザーアカウント**を使用してアプリを作成します。
  * 名前空間がサンプルで使用される名前空間と一致するように、"ContactManager" という名前を指定します。
  * `-uld` は SQLite ではなく LocalDB を指定します。

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* *モデル/Contact を追加します。*

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* `Contact` モデルをスキャフォールディングします。
* 初期移行を作成し、データベースを更新します。

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

`dotnet aspnet-codegenerator razorpage` コマンドでバグが発生した場合は、 [GitHub の問題](https://github.com/aspnet/Scaffolding/issues/984)を参照してください。

* *Pages/Shared/Layout*ファイルで**contactmanager**アンカーを更新します。

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* 連絡先の作成、編集、および削除によるアプリのテスト

### <a name="seed-the-database"></a>データベースのシード

[SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs)クラスを*Data*フォルダーに追加します。

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

`Main`から `SeedData.Initialize` を呼び出します。

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

アプリによってデータベースがシード処理されたことをテストします。 Contact DB に行がある場合、seed メソッドは実行されません。

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

このチュートリアルでは、承認によって保護されたユーザーデータを使用して ASP.NET Core web アプリを作成する方法について説明します。 認証済み (登録済み) のユーザーが作成した連絡先の一覧が表示されます。 セキュリティグループには次の3つがあります。

* **登録されているユーザー**は、すべての承認済みデータを表示したり、自分のデータを編集または削除したりできます。
* **管理者**は、連絡先データを承認または拒否できます。 承認された連絡先だけがユーザーに表示されます。
* **管理者**は、任意のデータを承認/拒否したり、編集/削除したりできます。

次の図では、user Rick (`rick@example.com`) がサインインしています。 Rick は、承認された連絡先を表示して**編集**/**削除**/、連絡先の新しいリンクを**作成**することのみができます。 Rick によって作成された最後のレコードにのみ、**編集**および**削除**のリンクが表示されます。 他のユーザーには、マネージャーまたは管理者が状態を "承認済み" に変更するまで、最後のレコードは表示されません。

![Rick がサインインしていることを示すスクリーンショット](secure-data/_static/rick.png)

次の図では、`manager@contoso.com` がサインインし、マネージャーのロールに含まれています。

![サインイン manager@contoso.com を示すスクリーンショット](secure-data/_static/manager1.png)

次の図は、連絡先のマネージャーの詳細ビューを示しています。

![連絡先のマネージャーの表示](secure-data/_static/manager.png)

**[承認]** ボタンと **[却下]** ボタンは、マネージャーと管理者に対してのみ表示されます。

次の図では、`admin@contoso.com` がサインインし、管理者のロールに含まれています。

![サインイン admin@contoso.com を示すスクリーンショット](secure-data/_static/admin.png)

管理者には、すべての特権があります。 連絡先の読み取り、編集、削除、および連絡先の状態の変更を行うことができます。

アプリは、次の `Contact` モデルを[スキャフォールディング](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model)することによって作成されました。

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

このサンプルには、次の承認ハンドラーが含まれています。

* `ContactIsOwnerAuthorizationHandler`: ユーザーがデータを編集できるようになります。
* `ContactManagerAuthorizationHandler`: 管理者が連絡先を承認または拒否できるようにします。
* `ContactAdministratorsAuthorizationHandler`: 管理者は、連絡先を承認または拒否したり、連絡先を編集または削除したりできます。

## <a name="prerequisites"></a>必要条件

このチュートリアルは高度です。 次のことを理解している必要があります。

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [認証](xref:security/authentication/identity)
* [アカウントの確認とパスワードの回復](xref:security/authentication/accconfirm)
* [承認](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>スターターおよび完成したアプリ

完成し[た](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples)アプリを[ダウンロード](xref:index#how-to-download-a-sample)します。 完成したアプリを[テスト](#test-the-completed-app)して、セキュリティ機能に慣れるようにします。

### <a name="the-starter-app"></a>スターターアプリ

[スターター](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/)アプリを[ダウンロード](xref:index#how-to-download-a-sample)します。

アプリを実行し、 **[Contactmanager]** リンクをタップして、連絡先を作成、編集、および削除できることを確認します。

## <a name="secure-user-data"></a>ユーザーデータのセキュリティ保護

以下のセクションでは、セキュリティで保護されたユーザーデータアプリを作成するための主要な手順について説明します。 完成したプロジェクトを参照した方が便利な場合があります。

### <a name="tie-the-contact-data-to-the-user"></a>連絡先データをユーザーに関連付ける

ASP.NET [id](xref:security/authentication/identity)ユーザー id を使用すると、ユーザーがデータを編集できるようになりますが、他のユーザーデータは編集できません。 `OwnerID` と `ContactStatus` を `Contact` モデルに追加します。

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID` は、 [id](xref:security/authentication/identity)データベースの `AspNetUser` テーブルのユーザー ID です。 [`Status`] フィールドによって、連絡先が一般ユーザーに表示されるかどうかが決まります。

新しい移行を作成し、データベースを更新します。

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a>Id への役割サービスの追加

役割サービスを追加するには、 [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1)を追加します。

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a>認証されたユーザーが必要

ユーザーが認証されるようにするには、既定の認証ポリシーを設定します。

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 Razor ページ、コントローラー、またはアクションメソッドレベルで、`[AllowAnonymous]` 属性を使用して認証をオプトアウトできます。 既定の認証ポリシーを設定すると、ユーザーの認証が必要になり、新しく追加された Razor Pages とコントローラーは保護されます。 既定で認証が必要になるのは、新しいコントローラーに依存していて、Razor Pages `[Authorize]` 属性を含めるよりも安全です。

匿名ユーザーが登録前にサイトに関する情報を取得できるように、 [Allowanonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute)を Index、About、および Contact の各ページに追加します。

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a>テストアカウントを構成する

`SeedData` クラスは、管理者とマネージャーの2つのアカウントを作成します。 これらのアカウントのパスワードを設定するには、[シークレットマネージャーツール](xref:security/app-secrets)を使用します。 プロジェクトディレクトリ ( *Program.cs*を含むディレクトリ) のパスワードを設定します。

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

強力なパスワードが指定されていない場合、`SeedData.Initialize` が呼び出されると例外がスローされます。

テストパスワードを使用するように `Main` を更新します。

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>テストアカウントを作成し、連絡先を更新する

`SeedData` クラスの `Initialize` メソッドを更新して、テストアカウントを作成します。

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

管理者のユーザー ID と `ContactStatus` を連絡先に追加します。 いずれかの連絡先を "送信済み" にして、"拒否" するようにします。 すべての連絡先にユーザー ID と状態を追加します。 1つの連絡先のみが表示されます。

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>所有者、マネージャー、および管理者の承認ハンドラーを作成する

*承認*フォルダーを作成し、その中に `ContactIsOwnerAuthorizationHandler` クラスを作成します。 `ContactIsOwnerAuthorizationHandler` は、リソースに対して動作しているユーザーがリソースを所有していることを確認します。

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

`ContactIsOwnerAuthorizationHandler` は[コンテキストを呼び出します。](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)現在の認証済みユーザーが連絡先の所有者である場合は成功します。 通常、承認ハンドラーは次のようになります。

* 要件が満たされた場合に `context.Succeed` を返します。
* 要件を満たしていない場合は `Task.CompletedTask` を返します。 `Task.CompletedTask` は成功でも失敗でも&mdash;他の承認ハンドラーの実行を許可します。

明示的に失敗する必要がある場合は、コンテキストを返し[ます。失敗](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)。

アプリを使用すると、連絡先所有者は自分のデータを編集/削除/作成できます。 `ContactIsOwnerAuthorizationHandler` は、要件パラメーターで渡された操作を確認する必要はありません。

### <a name="create-a-manager-authorization-handler"></a>マネージャーの承認ハンドラーを作成する

*承認*フォルダーに `ContactManagerAuthorizationHandler` クラスを作成します。 `ContactManagerAuthorizationHandler` は、リソースに対して動作しているユーザーがマネージャーであることを確認します。 コンテンツの変更を承認または拒否できるのは、管理者のみです (新規または変更)。

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>管理者の承認ハンドラーを作成する

*承認*フォルダーに `ContactAdministratorsAuthorizationHandler` クラスを作成します。 `ContactAdministratorsAuthorizationHandler` は、リソースに対して動作しているユーザーが管理者であることを確認します。 管理者は、すべての操作を実行できます。

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>認証ハンドラーを登録する

Entity Framework Core を使用するサービスは、 [Addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)を使用して[依存関係の挿入](xref:fundamentals/dependency-injection)に登録する必要があります。 `ContactIsOwnerAuthorizationHandler` は Entity Framework Core 上に構築された ASP.NET Core [id](xref:security/authentication/identity)を使用します。 サービスコレクションにハンドラーを登録して、[依存関係の挿入](xref:fundamentals/dependency-injection)によって `ContactsController` で使用できるようにします。 `ConfigureServices`の末尾に次のコードを追加します。

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

`ContactAdministratorsAuthorizationHandler` と `ContactManagerAuthorizationHandler` はシングルトンとして追加されます。 EF を使用せず、必要なすべての情報が `HandleRequirementAsync` メソッドの `Context` パラメーターに含まれているため、これらはシングルトンです。

## <a name="support-authorization"></a>認証のサポート

このセクションでは、Razor Pages を更新し、操作要件クラスを追加します。

### <a name="review-the-contact-operations-requirements-class"></a>Contact operation の要件クラスを確認する

`ContactOperations` クラスを確認します。 このクラスには、アプリがサポートする要件が含まれています。

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a>連絡先の基本クラスを作成 Razor Pages

連絡先 Razor Pages で使用されるサービスを含む基本クラスを作成します。 基本クラスは、初期化コードを1つの場所に配置します。

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

上のコードでは以下の操作が行われます。

* 承認ハンドラーにアクセスするための `IAuthorizationService` サービスを追加します。
* Id `UserManager` サービスを追加します。
* `ApplicationDbContext` を追加します。

### <a name="update-the-createmodel"></a>CreateModel を更新する

Create page model コンストラクターを更新して、`DI_BasePageModel` 基底クラスを使用するようにします。

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

`CreateModel.OnPostAsync` メソッドを次のように更新します。

* `Contact` モデルにユーザー ID を追加します。
* 承認ハンドラーを呼び出して、ユーザーが連絡先を作成するアクセス許可を持っていることを確認します。

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>IndexModel を更新する

`OnGetAsync` メソッドを更新して、承認された連絡先だけが一般ユーザーに表示されるようにします。

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>EditModel を更新する

ユーザーが連絡先を所有していることを確認するための承認ハンドラーを追加します。 リソース承認が検証されているため、`[Authorize]` 属性では不十分です。 属性が評価された場合、アプリにはリソースへのアクセス権がありません。 リソースベースの承認は、必須である必要があります。 アプリケーションがリソースにアクセスできるようになったら、そのアプリをページモデルに読み込むか、ハンドラー自体に読み込むことによって、チェックを実行する必要があります。 リソースキーを渡すことによって、リソースに頻繁にアクセスします。

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>DeleteModel を更新する

承認ハンドラーを使用するように delete ページモデルを更新して、ユーザーが連絡先に対する delete 権限を持っていることを確認します。

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>ビューに承認サービスを挿入する

現在、UI には、ユーザーが変更できない連絡先の編集と削除のリンクが表示されています。

*Views/_ViewImports*ファイルに承認サービスを挿入して、すべてのビューで使用できるようにします。

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

上記のマークアップでは、いくつかの `using` ステートメントが追加されます。

*Pages/Contacts/Index. cshtml*の**編集**と**削除**のリンクを更新して、適切なアクセス許可を持つユーザーにのみ表示されるようにします。

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> データを変更するアクセス許可がないユーザーからのリンクを非表示にしても、アプリはセキュリティで保護されません。 リンクを非表示にすると、有効なリンクのみが表示されるため、アプリのユーザーがわかりやすくなります。 ユーザーは、生成された Url をハッキングして、所有していないデータに対する編集操作と削除操作を呼び出すことができます。 Razor ページまたはコントローラーは、データをセキュリティで保護するためにアクセスチェックを強制する必要があります。

### <a name="update-details"></a>更新プログラムの詳細

上司が連絡先を承認または拒否できるように、詳細ビューを更新します。

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

詳細ページモデルを更新します。

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>ロールに対するユーザーの追加または削除

次の情報については、[この問題](https://github.com/aspnet/AspNetCore.Docs/issues/8502)を参照してください。

* ユーザーから特権を削除しています。 たとえば、チャットアプリでユーザーのミュートを行います。
* ユーザーへの特権の追加。

## <a name="test-the-completed-app"></a>完成したアプリをテストする

シードされたユーザーアカウントのパスワードをまだ設定していない場合は、[シークレットマネージャーツール](xref:security/app-secrets#secret-manager)を使用してパスワードを設定します。

* 強力なパスワードを選択する: 8 個以上の文字と、少なくとも1つの大文字、数字、および記号を使用します。 たとえば、`Passw0rd!` は強力なパスワードの要件を満たしています。
* プロジェクトのフォルダーから次のコマンドを実行します。 `<PW>` はパスワードです。

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* データベースを削除して更新する

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* アプリケーションを再起動してデータベースをシード処理します。

完成したアプリをテストする簡単な方法は、3つの異なるブラウザー (または incognito/InPrivate セッション) を起動することです。 1つのブラウザーで、新しいユーザーを登録します (たとえば、`test@example.com`)。 別のユーザーを使用して各ブラウザーにサインインします。 次の操作を確認します。

* 登録済みのユーザーは、すべての承認済み連絡先データを表示できます。
* 登録されているユーザーは、自分のデータを編集または削除できます。
* マネージャーは、連絡先データを承認/拒否することができます。 `Details` ビューには、 **[承認]** ボタンと **[却下]** ボタンが表示されます。
* 管理者は、すべてのデータを承認/拒否し、編集/削除することができます。

| ユーザー                | アプリによるシード処理 | オプション                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | Ｘ                | 独自のデータを編集または削除します。                |
| manager@contoso.com | [はい]               | 自分のデータを承認/拒否し、編集/削除します。 |
| admin@contoso.com   | [はい]               | すべてのデータを承認/拒否し、編集/削除します。 |

管理者のブラウザーで連絡先を作成します。 管理者の連絡先から削除と編集のための URL をコピーします。 これらのリンクをテストユーザーのブラウザーに貼り付けて、テストユーザーがこれらの操作を実行できないことを確認します。

## <a name="create-the-starter-app"></a>スターターアプリを作成する

* "ContactManager" という名前の Razor Pages アプリを作成します
  * **個々のユーザーアカウント**を使用してアプリを作成します。
  * 名前空間がサンプルで使用される名前空間と一致するように、"ContactManager" という名前を指定します。
  * `-uld` は SQLite ではなく LocalDB を指定します。

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* *モデル/Contact を追加します。*

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* `Contact` モデルをスキャフォールディングします。
* 初期移行を作成し、データベースを更新します。

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* *Pages/Layout*ファイルで**contactmanager**アンカーを更新します。

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* 連絡先の作成、編集、および削除によるアプリのテスト

### <a name="seed-the-database"></a>データベースのシード

[SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs)クラスを*Data*フォルダーに追加します。

`Main`から `SeedData.Initialize` を呼び出します。

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

アプリによってデータベースがシード処理されたことをテストします。 Contact DB に行がある場合、seed メソッドは実行されません。

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a>その他の技術情報

* [Azure App Service で .NET Core と SQL Database web アプリを構築する](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [ASP.NET Core の承認ラボ](https://github.com/blowdart/AspNetAuthorizationWorkshop)。 このチュートリアルで紹介するセキュリティ機能の詳細については、このラボを参照してください。
* <xref:security/authorization/introduction>
* [カスタム ポリシー ベースの承認](xref:security/authorization/policies)
