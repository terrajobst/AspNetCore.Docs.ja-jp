---
title: 承認によって保護されたユーザー データと ASP.NET Core アプリを作成します。
author: rick-anderson
description: 承認によって保護されたユーザー データと Razor ページ アプリを作成する方法について説明します。 HTTPS、認証、セキュリティ、ASP.NET Core Identity が含まれています。
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
uid: security/authorization/secure-data
ms.openlocfilehash: 222ae1d6212b838e5c70f831960fa23a9924a0ae
ms.sourcegitcommit: 7a40c56bf6a6aaa63a7ee83a2cac9b3a1d77555e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67856142"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a>承認によって保護されたユーザー データと ASP.NET Core アプリを作成します。

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) および [Joe Audette](https://twitter.com/joeaudette)

::: moniker range="<= aspnetcore-1.1"

参照してください[この PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) ASP.NET Core MVC のバージョン。 このチュートリアルの ASP.NET Core 1.1 バージョンは[この](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data)フォルダー。 ASP.NET Core サンプルについては、1.1、[サンプル](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2)します。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

参照してください[この pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

このチュートリアルでは、承認によって保護されたユーザー データと ASP.NET Core web アプリを作成する方法を示します。 認証済み (登録済み) のユーザーの連絡先の一覧を表示を作成します。 これには 3 つのセキュリティ グループがあります。

* **ユーザーが登録されている**承認済みのすべてのデータを表示することができ、独自のデータ編集、または削除できます。
* **マネージャー**を承認または連絡先データを拒否します。 承認されたメンバーのみがユーザーに表示されます。
* **管理者**承認または却下と編集/削除のすべてのデータをことができます。

このドキュメント内のイメージは、最新のテンプレート正確に一致しません。

次の図では、ユーザー Rick の (`rick@example.com`) がサインインしています。 Rick は許可されている連絡先のみを表示し、**編集**/**削除**/**新規作成**彼の連絡先へのリンク。 最後のレコードのみが Rick、表示によって作成された**編集**と**削除**リンク。 他のユーザーは、管理者は、状態を"Approved"に変わるまで、最後のレコードを表示されません。

![サインインして Rick を示すスクリーン ショット](secure-data/_static/rick.png)

次の図の`manager@contoso.com`では、マネージャーの役割では署名します。

![スクリーン ショットmanager@contoso.comサインイン](secure-data/_static/manager1.png)

次の図は、連絡先の詳細ビューをマネージャーには。

![連絡先のマネージャーの表示](secure-data/_static/manager.png)

**承認**と**拒否**ボタンは、管理者と管理者のみ表示されます。

次の図の`admin@contoso.com`では、管理者の役割では署名します。

![スクリーン ショットadmin@contoso.comサインイン](secure-data/_static/admin.png)

管理者は、すべての特権を持ちます。 彼女は、読み取り/編集/削除のいずれかの連絡先をことができ、連絡先の状態を変更します。

によって、アプリが作成された[スキャフォールディング](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model)次`Contact`モデル。

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

サンプルには、次の承認ハンドラーが含まれています。

* `ContactIsOwnerAuthorizationHandler`:により、ユーザーは、そのデータにのみ編集できます。
* `ContactManagerAuthorizationHandler`:承認または拒否の連絡先のマネージャーを使用します。
* `ContactAdministratorsAuthorizationHandler`:管理者を承認または拒否の連絡先と連絡先の編集/削除に使用できます。

## <a name="prerequisites"></a>必須コンポーネント

このチュートリアルは、高度なです。 理解しておく必要があります。

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [認証](xref:security/authentication/identity)
* [アカウントの確認とパスワードの回復](xref:security/authentication/accconfirm)
* [承認](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>Starter および完成したアプリ

[ダウンロード](xref:index#how-to-download-a-sample)、[完了](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples)アプリ。 [テスト](#test-the-completed-app)完成したアプリのセキュリティ機能を理解するようにします。

### <a name="the-starter-app"></a>スターター アプリ

[ダウンロード](xref:index#how-to-download-a-sample)、[スターター](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/)アプリ。

アプリを実行し、タップ、 **ContactManager**リンク、および作成、編集、および連絡先を削除することを確認します。

## <a name="secure-user-data"></a>セキュリティで保護されたユーザー データ

次のセクションでは、セキュリティで保護されたユーザー データ アプリを作成するすべての主要な手順があります。 完成したプロジェクトを参照すると便利です。

### <a name="tie-the-contact-data-to-the-user"></a>ユーザーに連絡先データを関連付ける

ASP.NET を使用して、 [Identity](xref:security/authentication/identity)データが他のユーザー データではなく、ユーザーのユーザー ID を編集できます。 追加`OwnerID`と`ContactStatus`を`Contact`モデル。

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID` ユーザーの id、`AspNetUser`テーブルに、 [Identity](xref:security/authentication/identity)データベース。 `Status`連絡先が一般ユーザーが表示可能なかどうかにフィールドが決定します。

新しい移行を作成し、データベースを更新します。

```console
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a>役割サービスの Id を追加します。

追加[AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1)役割サービスを追加します。

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a>認証されたユーザーが必要です。

ユーザー認証を必要とする既定の認証ポリシーを設定します。

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 Razor ページ、コントローラ、またはアクション メソッド レベルでの認証をオプトアウトすることができます、`[AllowAnonymous]`属性。 ユーザー認証を必要とする既定の認証ポリシーの設定は、新しく追加された Razor ページとコント ローラーを保護します。 既定で必要な認証は、新しいコント ローラーと Razor ページで証明書利用者より安全ですが、`[Authorize]`属性。

追加[AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute)インデックスとプライバシー ページ匿名ユーザーは登録前に、サイトに関する情報を取得できるようにします。

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a>テスト アカウントを構成します。

`SeedData`クラスは、2 つのアカウントを作成します。 管理者とマネージャー。 使用して、 [Secret Manager ツール](xref:security/app-secrets)これらのアカウントのパスワードを設定します。 プロジェクト ディレクトリから、パスワードの設定 (含まれるディレクトリ*Program.cs*)。

```console
dotnet user-secrets set SeedUserPW <PW>
```

例外がときにスローされる場合は、強力なパスワードが指定されていない`SeedData.Initialize`が呼び出されます。

Update`Main`テストのパスワードを使用します。

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>テスト アカウントを作成し、連絡先の更新

更新プログラム、`Initialize`メソッドで、`SeedData`テスト アカウントを作成するクラス。

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

管理者のユーザー ID を追加し、`ContactStatus`連絡先にします。 「送信済み」と「拒否」の 1 つの連絡先を作成します。 すべての連絡先に、ユーザー ID と状態を追加します。 1 人だけが表示されます。

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>所有者、マネージャー、および管理者の承認ハンドラーを作成します。

作成、`ContactIsOwnerAuthorizationHandler`クラス、*承認*フォルダー。 `ContactIsOwnerAuthorizationHandler`リソースで動作しているユーザーがリソースを所有していることを確認します。

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

`ContactIsOwnerAuthorizationHandler`呼び出し[コンテキスト。成功](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)現在認証済みユーザーがメンバーの所有者である場合。 承認ハンドラー通常。

* 返す`context.Succeed`要件を満たしている場合。
* 返す`Task.CompletedTask`要件が満たされていません。 `Task.CompletedTask` 成功または失敗のない&mdash;他の承認ハンドラーを実行することができます。

明示的に失敗する場合は、返す[コンテキスト。失敗](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)します。

アプリは、独自のデータを編集/削除/作成所有者が連絡先にできます。 `ContactIsOwnerAuthorizationHandler` 要求パラメーターで渡された操作を確認する必要はありません。

### <a name="create-a-manager-authorization-handler"></a>マネージャーの承認ハンドラーを作成します。

作成、`ContactManagerAuthorizationHandler`クラス、*承認*フォルダー。 `ContactManagerAuthorizationHandler`リソースで動作しているユーザーが、マネージャーであることを確認します。 管理者だけでは、承認したり、(新規または変更された) コンテンツの変更を拒否することができます。

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>管理者の承認ハンドラーを作成します。

作成、`ContactAdministratorsAuthorizationHandler`クラス、*承認*フォルダー。 `ContactAdministratorsAuthorizationHandler`リソースで動作しているユーザーが管理者であることを確認します。 管理者は、すべての操作を実行できます。

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>承認ハンドラーを登録します。

Entity Framework Core を使用してサービスを登録する必要があります[依存関係の注入](xref:fundamentals/dependency-injection)を使用して[AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)します。 `ContactIsOwnerAuthorizationHandler` ASP.NET Core を使用して[Identity](xref:security/authentication/identity)、これは Entity Framework Core 上に構築します。 使用するため、サービスのコレクションを使用してハンドラーを登録、`ContactsController`を通じて[依存関係の注入](xref:fundamentals/dependency-injection)します。 末尾に次のコードを追加`ConfigureServices`:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

`ContactAdministratorsAuthorizationHandler` `ContactManagerAuthorizationHandler`シングルトンとして追加されます。 シングルトンになるため、EF を使用していないし、必要なすべての情報は、`Context`のパラメーター、`HandleRequirementAsync`メソッド。

## <a name="support-authorization"></a>承認をサポートします。

このセクションでは、Razor ページを更新し、操作の要件クラスを追加します。

### <a name="review-the-contact-operations-requirements-class"></a>連絡先の操作の要件のクラスを確認してください。

レビュー、`ContactOperations`クラス。 このクラスには、要件が含まれています。 アプリがサポートされます。

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a>連絡先の Razor ページの基本クラスを作成します。

連絡先の Razor ページで使用されるサービスを含む基本クラスを作成します。 基底クラスでは、1 つの場所に、初期化コードが配置します。

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

上のコードでは以下の操作が行われます。

* 追加、`IAuthorizationService`承認ハンドラーへのアクセスをサービスします。
* Id を追加します。`UserManager`サービス。
* `ApplicationDbContext` を追加します。

### <a name="update-the-createmodel"></a>CreateModel の更新します。

作成のページ モデルを使用するコンストラクターを更新、`DI_BasePageModel`基本クラス。

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

更新プログラム、`CreateModel.OnPostAsync`メソッド。

* ユーザー ID を追加、`Contact`モデル。
* ユーザーが連絡先を作成するためのアクセス許可を確認する認証ハンドラーを呼び出します。

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>更新プログラム、IndexModel

更新プログラム、`OnGetAsync`メソッドのため、一般ユーザーにのみ許可されている連絡先が表示されます。

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>更新プログラム、EditModel

ユーザーが連絡先を所有していることを確認するための承認ハンドラーを追加します。 リソース承認が検証されているため、`[Authorize]`属性が十分ではありません。 アプリは、属性が評価されたときに、リソースへのアクセスにすることがありません。 リソース ベースの承認は、命令型である必要があります。 ページ モデルに読み込んで、または読み込みハンドラー自体内で、アプリが、リソースへのアクセスを持つ、チェックを実行する必要があります。 リソース キーを渡すことで、リソースを頻繁にアクセスするとします。

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>更新プログラム、DeleteModel

承認ハンドラーを使用して、ユーザーが連絡先に削除するアクセス許可を持ってことを確認する delete ページ モデルを更新します。

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>承認サービスをビューに挿入します。

現時点では、UI の表示では、編集し、ユーザーが変更できない連絡先へのリンクを削除します。

承認サービスを挿入、 *Pages/_ViewImports.cshtml*ファイルのすべてのビューに使用できるようにします。

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

上記のマークアップにいくつか追加`using`ステートメント。

更新プログラム、**編集**と**削除**でリンク*Pages/Contacts/Index.cshtml*適切なアクセス許可を持つユーザーのみレンダリングするように。

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> 変更データへのアクセス許可がないユーザーからのリンクを非表示と、アプリをセキュリティで保護しません。 リンクを非表示アプリよりユーザー フレンドリな唯一の有効なリンクを表示することで。 ユーザーは、編集を呼び出すし、所有していないデータの操作を削除するには、生成された Url を切断できます。 Razor ページまたはコント ローラーは、データを保護するアクセス チェックを適用する必要があります。

### <a name="update-details"></a>更新プログラムの詳細

マネージャーが承認または連絡先を拒否するために、詳細ビューを更新します。

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

詳細のページ モデルを更新します。

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>追加またはロールにユーザーを削除します。

参照してください[今月](https://github.com/aspnet/AspNetCore.Docs/issues/8502)について。

* ユーザーから権限を削除しています。 たとえばのチャット アプリケーションのユーザーをミュートします。
* ユーザーに特権を追加します。

## <a name="test-the-completed-app"></a>完成したアプリをテストします。

シード処理されたユーザー アカウントのパスワードを設定していない場合は、使用、 [Secret Manager ツール](xref:security/app-secrets#secret-manager)パスワードを設定します。

* 強力なパスワードを選択します。以上の文字し、少なくとも 1 つの大文字の文字数、およびシンボルまたは 8 を使用しています。 たとえば、`Passw0rd!`強力なパスワード要件を満たしています。
* プロジェクトのフォルダーから次のコマンドを実行、`<PW>`パスワードです。

  ```console
  dotnet user-secrets set SeedUserPW <PW>
  ```

場合は、アプリでは、連絡先があります。

* すべてのレコードの削除、`Contact`テーブル。
* データベースをシードするアプリを再起動します。

完成したアプリをテストする簡単な方法では、次の 3 つの異なるブラウザー (または incognito/inprivate ブラウズ セッション) を起動します。 1 つのブラウザーで新しいユーザーを登録します (たとえば、 `test@example.com`)。 ブラウザーごとに別のユーザーでサインインします。 次の操作を確認します。

* 登録済みユーザーには、すべての承認済みの連絡先データを表示できます。
* 登録済みユーザーは編集/削除が自分のデータ。
* 管理者では、連絡先データの承認または却下をします。 `Details`ビューには、**承認**と**拒否**ボタン。
* 管理者承認または却下して編集/削除のすべてのデータ。

| ユーザー                | アプリによってシード処理 | オプション                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | いいえ                | 独自のデータを編集/削除します。                |
| manager@contoso.com | [はい]               | 承認または却下と編集/削除は、データを所有します。 |
| admin@contoso.com   | [はい]               | 承認または却下し、すべてのデータの編集/削除します。 |

管理者のブラウザーで連絡先を作成します。 削除の URL をコピーし、管理者の連絡先から管理者を編集します。 テスト ユーザーのブラウザー テスト ユーザーがこれらの操作を実行できないことを確認するには、これらのリンクを貼り付けます。

## <a name="create-the-starter-app"></a>スターター アプリを作成します。

* 「ContactManager」という名前の Razor ページ アプリの作成
  * 使用してアプリを作成する**個々 のユーザー アカウント**します。
  * ファイル名「ContactManager」名前空間サンプルで使用する名前空間が一致するようにします。
  * `-uld` SQLite の代わりに LocalDB を指定します

  ```console
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* 追加*Models/Contact.cs*:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* スキャフォールディング、`Contact`モデル。
* 最初の移行を作成し、データベースを更新します。

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
  ```

バグが発生した場合、`dotnet aspnet-codegenerator razorpage`コマンドを参照してください[この GitHub の問題](https://github.com/aspnet/Scaffolding/issues/984)します。

* 更新プログラム、 **ContactManager**で固定、 *Pages/Shared/_Layout.cshtml*ファイル。

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* 作成、編集、および連絡先を削除して、アプリをテストします。

### <a name="seed-the-database"></a>データベースのシード

追加、 [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs)クラスを*データ*フォルダー。

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

呼び出す`SeedData.Initialize`から`Main`:

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

アプリが、データベースをシード処理をテストします。 DB の連絡先に行がある場合は、seed メソッドは実行されません。

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

このチュートリアルでは、承認によって保護されたユーザー データと ASP.NET Core web アプリを作成する方法を示します。 認証済み (登録済み) のユーザーの連絡先の一覧を表示を作成します。 これには 3 つのセキュリティ グループがあります。

* **ユーザーが登録されている**承認済みのすべてのデータを表示することができ、独自のデータ編集、または削除できます。
* **マネージャー**を承認または連絡先データを拒否します。 承認されたメンバーのみがユーザーに表示されます。
* **管理者**承認または却下と編集/削除のすべてのデータをことができます。

次の図では、ユーザー Rick の (`rick@example.com`) がサインインしています。 Rick は許可されている連絡先のみを表示し、**編集**/**削除**/**新規作成**彼の連絡先へのリンク。 最後のレコードのみが Rick、表示によって作成された**編集**と**削除**リンク。 他のユーザーは、管理者は、状態を"Approved"に変わるまで、最後のレコードを表示されません。

![サインインして Rick を示すスクリーン ショット](secure-data/_static/rick.png)

次の図の`manager@contoso.com`では、マネージャーの役割では署名します。

![スクリーン ショットmanager@contoso.comサインイン](secure-data/_static/manager1.png)

次の図は、連絡先の詳細ビューをマネージャーには。

![連絡先のマネージャーの表示](secure-data/_static/manager.png)

**承認**と**拒否**ボタンは、管理者と管理者のみ表示されます。

次の図の`admin@contoso.com`では、管理者の役割では署名します。

![スクリーン ショットadmin@contoso.comサインイン](secure-data/_static/admin.png)

管理者は、すべての特権を持ちます。 彼女は、読み取り/編集/削除のいずれかの連絡先をことができ、連絡先の状態を変更します。

によって、アプリが作成された[スキャフォールディング](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model)次`Contact`モデル。

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

サンプルには、次の承認ハンドラーが含まれています。

* `ContactIsOwnerAuthorizationHandler`:により、ユーザーは、そのデータにのみ編集できます。
* `ContactManagerAuthorizationHandler`:承認または拒否の連絡先のマネージャーを使用します。
* `ContactAdministratorsAuthorizationHandler`:管理者を承認または拒否の連絡先と連絡先の編集/削除に使用できます。

## <a name="prerequisites"></a>必須コンポーネント

このチュートリアルは、高度なです。 理解しておく必要があります。

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [認証](xref:security/authentication/identity)
* [アカウントの確認とパスワードの回復](xref:security/authentication/accconfirm)
* [承認](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>Starter および完成したアプリ

[ダウンロード](xref:index#how-to-download-a-sample)、[完了](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples)アプリ。 [テスト](#test-the-completed-app)完成したアプリのセキュリティ機能を理解するようにします。

### <a name="the-starter-app"></a>スターター アプリ

[ダウンロード](xref:index#how-to-download-a-sample)、[スターター](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/)アプリ。

アプリを実行し、タップ、 **ContactManager**リンク、および作成、編集、および連絡先を削除することを確認します。

## <a name="secure-user-data"></a>セキュリティで保護されたユーザー データ

次のセクションでは、セキュリティで保護されたユーザー データ アプリを作成するすべての主要な手順があります。 完成したプロジェクトを参照すると便利です。

### <a name="tie-the-contact-data-to-the-user"></a>ユーザーに連絡先データを関連付ける

ASP.NET を使用して、 [Identity](xref:security/authentication/identity)データが他のユーザー データではなく、ユーザーのユーザー ID を編集できます。 追加`OwnerID`と`ContactStatus`を`Contact`モデル。

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID` ユーザーの id、`AspNetUser`テーブルに、 [Identity](xref:security/authentication/identity)データベース。 `Status`連絡先が一般ユーザーが表示可能なかどうかにフィールドが決定します。

新しい移行を作成し、データベースを更新します。

```console
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a>役割サービスの Id を追加します。

追加[AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1)役割サービスを追加します。

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a>認証されたユーザーが必要です。

ユーザー認証を必要とする既定の認証ポリシーを設定します。

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 Razor ページ、コントローラ、またはアクション メソッド レベルでの認証をオプトアウトすることができます、`[AllowAnonymous]`属性。 ユーザー認証を必要とする既定の認証ポリシーの設定は、新しく追加された Razor ページとコント ローラーを保護します。 既定で必要な認証は、新しいコント ローラーと Razor ページで証明書利用者より安全ですが、`[Authorize]`属性。

追加[AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute)インデックス、および連絡先について、ページ匿名ユーザーは登録前に、サイトに関する情報を取得できるようにします。

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a>テスト アカウントを構成します。

`SeedData`クラスは、2 つのアカウントを作成します。 管理者とマネージャー。 使用して、 [Secret Manager ツール](xref:security/app-secrets)これらのアカウントのパスワードを設定します。 プロジェクト ディレクトリから、パスワードの設定 (含まれるディレクトリ*Program.cs*)。

```console
dotnet user-secrets set SeedUserPW <PW>
```

例外がときにスローされる場合は、強力なパスワードが指定されていない`SeedData.Initialize`が呼び出されます。

Update`Main`テストのパスワードを使用します。

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>テスト アカウントを作成し、連絡先の更新

更新プログラム、`Initialize`メソッドで、`SeedData`テスト アカウントを作成するクラス。

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

管理者のユーザー ID を追加し、`ContactStatus`連絡先にします。 「送信済み」と「拒否」の 1 つの連絡先を作成します。 すべての連絡先に、ユーザー ID と状態を追加します。 1 人だけが表示されます。

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>所有者、マネージャー、および管理者の承認ハンドラーを作成します。

作成、`ContactIsOwnerAuthorizationHandler`クラス、*承認*フォルダー。 `ContactIsOwnerAuthorizationHandler`リソースで動作しているユーザーがリソースを所有していることを確認します。

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

`ContactIsOwnerAuthorizationHandler`呼び出し[コンテキスト。成功](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)現在認証済みユーザーがメンバーの所有者である場合。 承認ハンドラー通常。

* 返す`context.Succeed`要件を満たしている場合。
* 返す`Task.CompletedTask`要件が満たされていません。 `Task.CompletedTask` 成功または失敗のない&mdash;他の承認ハンドラーを実行することができます。

明示的に失敗する場合は、返す[コンテキスト。失敗](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)します。

アプリは、独自のデータを編集/削除/作成所有者が連絡先にできます。 `ContactIsOwnerAuthorizationHandler` 要求パラメーターで渡された操作を確認する必要はありません。

### <a name="create-a-manager-authorization-handler"></a>マネージャーの承認ハンドラーを作成します。

作成、`ContactManagerAuthorizationHandler`クラス、*承認*フォルダー。 `ContactManagerAuthorizationHandler`リソースで動作しているユーザーが、マネージャーであることを確認します。 管理者だけでは、承認したり、(新規または変更された) コンテンツの変更を拒否することができます。

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>管理者の承認ハンドラーを作成します。

作成、`ContactAdministratorsAuthorizationHandler`クラス、*承認*フォルダー。 `ContactAdministratorsAuthorizationHandler`リソースで動作しているユーザーが管理者であることを確認します。 管理者は、すべての操作を実行できます。

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>承認ハンドラーを登録します。

Entity Framework Core を使用してサービスを登録する必要があります[依存関係の注入](xref:fundamentals/dependency-injection)を使用して[AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)します。 `ContactIsOwnerAuthorizationHandler` ASP.NET Core を使用して[Identity](xref:security/authentication/identity)、これは Entity Framework Core 上に構築します。 使用するため、サービスのコレクションを使用してハンドラーを登録、`ContactsController`を通じて[依存関係の注入](xref:fundamentals/dependency-injection)します。 末尾に次のコードを追加`ConfigureServices`:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

`ContactAdministratorsAuthorizationHandler` `ContactManagerAuthorizationHandler`シングルトンとして追加されます。 シングルトンになるため、EF を使用していないし、必要なすべての情報は、`Context`のパラメーター、`HandleRequirementAsync`メソッド。

## <a name="support-authorization"></a>承認をサポートします。

このセクションでは、Razor ページを更新し、操作の要件クラスを追加します。

### <a name="review-the-contact-operations-requirements-class"></a>連絡先の操作の要件のクラスを確認してください。

レビュー、`ContactOperations`クラス。 このクラスには、要件が含まれています。 アプリがサポートされます。

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a>連絡先の Razor ページの基本クラスを作成します。

連絡先の Razor ページで使用されるサービスを含む基本クラスを作成します。 基底クラスでは、1 つの場所に、初期化コードが配置します。

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

上のコードでは以下の操作が行われます。

* 追加、`IAuthorizationService`承認ハンドラーへのアクセスをサービスします。
* Id を追加します。`UserManager`サービス。
* `ApplicationDbContext` を追加します。

### <a name="update-the-createmodel"></a>CreateModel の更新します。

作成のページ モデルを使用するコンストラクターを更新、`DI_BasePageModel`基本クラス。

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

更新プログラム、`CreateModel.OnPostAsync`メソッド。

* ユーザー ID を追加、`Contact`モデル。
* ユーザーが連絡先を作成するためのアクセス許可を確認する認証ハンドラーを呼び出します。

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>更新プログラム、IndexModel

更新プログラム、`OnGetAsync`メソッドのため、一般ユーザーにのみ許可されている連絡先が表示されます。

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>更新プログラム、EditModel

ユーザーが連絡先を所有していることを確認するための承認ハンドラーを追加します。 リソース承認が検証されているため、`[Authorize]`属性が十分ではありません。 アプリは、属性が評価されたときに、リソースへのアクセスにすることがありません。 リソース ベースの承認は、命令型である必要があります。 ページ モデルに読み込んで、または読み込みハンドラー自体内で、アプリが、リソースへのアクセスを持つ、チェックを実行する必要があります。 リソース キーを渡すことで、リソースを頻繁にアクセスするとします。

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>更新プログラム、DeleteModel

承認ハンドラーを使用して、ユーザーが連絡先に削除するアクセス許可を持ってことを確認する delete ページ モデルを更新します。

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>承認サービスをビューに挿入します。

現時点では、UI の表示では、編集し、ユーザーが変更できない連絡先へのリンクを削除します。

承認サービスを挿入、 *Views/_ViewImports.cshtml*ファイルのすべてのビューに使用できるようにします。

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

上記のマークアップにいくつか追加`using`ステートメント。

更新プログラム、**編集**と**削除**でリンク*Pages/Contacts/Index.cshtml*適切なアクセス許可を持つユーザーのみレンダリングするように。

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> 変更データへのアクセス許可がないユーザーからのリンクを非表示と、アプリをセキュリティで保護しません。 リンクを非表示アプリよりユーザー フレンドリな唯一の有効なリンクを表示することで。 ユーザーは、編集を呼び出すし、所有していないデータの操作を削除するには、生成された Url を切断できます。 Razor ページまたはコント ローラーは、データを保護するアクセス チェックを適用する必要があります。

### <a name="update-details"></a>更新プログラムの詳細

マネージャーが承認または連絡先を拒否するために、詳細ビューを更新します。

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

詳細のページ モデルを更新します。

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>追加またはロールにユーザーを削除します。

参照してください[今月](https://github.com/aspnet/AspNetCore.Docs/issues/8502)について。

* ユーザーから権限を削除しています。 たとえばのチャット アプリケーションのユーザーをミュートします。
* ユーザーに特権を追加します。

## <a name="test-the-completed-app"></a>完成したアプリをテストします。

シード処理されたユーザー アカウントのパスワードを設定していない場合は、使用、 [Secret Manager ツール](xref:security/app-secrets#secret-manager)パスワードを設定します。

* 強力なパスワードを選択します。以上の文字し、少なくとも 1 つの大文字の文字数、およびシンボルまたは 8 を使用しています。 たとえば、`Passw0rd!`強力なパスワード要件を満たしています。
* プロジェクトのフォルダーから次のコマンドを実行、`<PW>`パスワードです。

  ```console
  dotnet user-secrets set SeedUserPW <PW>
  ```

* データベースの削除と更新
    ```console
     dotnet ef database drop -f
     dotnet ef database update  
```

* データベースをシードするアプリを再起動します。

完成したアプリをテストする簡単な方法では、次の 3 つの異なるブラウザー (または incognito/inprivate ブラウズ セッション) を起動します。 1 つのブラウザーで新しいユーザーを登録します (たとえば、 `test@example.com`)。 ブラウザーごとに別のユーザーでサインインします。 次の操作を確認します。

* 登録済みユーザーには、すべての承認済みの連絡先データを表示できます。
* 登録済みユーザーは編集/削除が自分のデータ。
* 管理者では、連絡先データの承認または却下をします。 `Details`ビューには、**承認**と**拒否**ボタン。
* 管理者承認または却下して編集/削除のすべてのデータ。

| ユーザー                | アプリによってシード処理 | オプション                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | いいえ                | 独自のデータを編集/削除します。                |
| manager@contoso.com | [はい]               | 承認または却下と編集/削除は、データを所有します。 |
| admin@contoso.com   | [はい]               | 承認または却下し、すべてのデータの編集/削除します。 |

管理者のブラウザーで連絡先を作成します。 削除の URL をコピーし、管理者の連絡先から管理者を編集します。 テスト ユーザーのブラウザー テスト ユーザーがこれらの操作を実行できないことを確認するには、これらのリンクを貼り付けます。

## <a name="create-the-starter-app"></a>スターター アプリを作成します。

* 「ContactManager」という名前の Razor ページ アプリの作成
  * 使用してアプリを作成する**個々 のユーザー アカウント**します。
  * ファイル名「ContactManager」名前空間サンプルで使用する名前空間が一致するようにします。
  * `-uld` SQLite の代わりに LocalDB を指定します

  ```console
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* 追加*Models/Contact.cs*:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* スキャフォールディング、`Contact`モデル。
* 最初の移行を作成し、データベースを更新します。

  ```console
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* 更新プログラム、 **ContactManager**で固定、 *Pages/_Layout.cshtml*ファイル。

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* 作成、編集、および連絡先を削除して、アプリをテストします。

### <a name="seed-the-database"></a>データベースのシード

追加、 [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs)クラスを*データ*フォルダー。

呼び出す`SeedData.Initialize`から`Main`:

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

アプリが、データベースをシード処理をテストします。 DB の連絡先に行がある場合は、seed メソッドは実行されません。

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a>その他の技術情報

* [Azure App Service で .NET Core および SQL Database web アプリを構築します。](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [ASP.NET Core 承認ラボ](https://github.com/blowdart/AspNetAuthorizationWorkshop)します。 このラボでは、このチュートリアルで導入されたセキュリティ機能の詳細に移動します。
* <xref:security/authorization/introduction>
* [カスタム ポリシー ベースの承認](xref:security/authorization/policies)
