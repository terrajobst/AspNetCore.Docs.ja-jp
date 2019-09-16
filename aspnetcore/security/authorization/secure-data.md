---
title: 承認によって保護されたユーザー データと ASP.NET Core アプリを作成します。
author: rick-anderson
description: 承認によって保護されたユーザー データと Razor ページ アプリを作成する方法について説明します。 HTTPS、認証、セキュリティ、ASP.NET Core Identity が含まれています。
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
uid: security/authorization/secure-data
ms.openlocfilehash: d95f44394d6ecc3c3896b45c5bebc73fa2d92445
ms.sourcegitcommit: dc5b293e08336dc236de66ed1834f7ef78359531
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2019
ms.locfileid: "71011195"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="0d788-104">承認によって保護されたユーザー データと ASP.NET Core アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="0d788-104">Create an ASP.NET Core app with user data protected by authorization</span></span>

<span data-ttu-id="0d788-105">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) および [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="0d788-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="0d788-106">参照してください[この PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) ASP.NET Core MVC のバージョン。</span><span class="sxs-lookup"><span data-stu-id="0d788-106">See [this PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core MVC version.</span></span> <span data-ttu-id="0d788-107">このチュートリアルの ASP.NET Core 1.1 バージョンは[この](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data)フォルダー。</span><span class="sxs-lookup"><span data-stu-id="0d788-107">The ASP.NET Core 1.1 version of this tutorial is in [this](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) folder.</span></span> <span data-ttu-id="0d788-108">ASP.NET Core サンプルについては、1.1、[サンプル](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2)します。</span><span class="sxs-lookup"><span data-stu-id="0d788-108">The 1.1 ASP.NET Core sample is in the [samples](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="0d788-109">参照してください[この pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="0d788-109">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0d788-110">このチュートリアルでは、承認によって保護されたユーザー データと ASP.NET Core web アプリを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="0d788-110">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="0d788-111">認証済み (登録済み) のユーザーの連絡先の一覧を表示を作成します。</span><span class="sxs-lookup"><span data-stu-id="0d788-111">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="0d788-112">これには 3 つのセキュリティ グループがあります。</span><span class="sxs-lookup"><span data-stu-id="0d788-112">There are three security groups:</span></span>

* <span data-ttu-id="0d788-113">**ユーザーが登録されている**承認済みのすべてのデータを表示することができ、独自のデータ編集、または削除できます。</span><span class="sxs-lookup"><span data-stu-id="0d788-113">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="0d788-114">**マネージャー**を承認または連絡先データを拒否します。</span><span class="sxs-lookup"><span data-stu-id="0d788-114">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="0d788-115">承認されたメンバーのみがユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="0d788-115">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="0d788-116">**管理者**承認または却下と編集/削除のすべてのデータをことができます。</span><span class="sxs-lookup"><span data-stu-id="0d788-116">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="0d788-117">このドキュメントの画像は、最新のテンプレートと完全には一致しません。</span><span class="sxs-lookup"><span data-stu-id="0d788-117">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="0d788-118">次の図では、ユーザー Rick の (`rick@example.com`) がサインインしています。</span><span class="sxs-lookup"><span data-stu-id="0d788-118">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="0d788-119">Rick は許可されている連絡先のみを表示し、**編集**/**削除**/**新規作成**彼の連絡先へのリンク。</span><span class="sxs-lookup"><span data-stu-id="0d788-119">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="0d788-120">最後のレコードのみが Rick、表示によって作成された**編集**と**削除**リンク。</span><span class="sxs-lookup"><span data-stu-id="0d788-120">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="0d788-121">他のユーザーは、管理者は、状態を"Approved"に変わるまで、最後のレコードを表示されません。</span><span class="sxs-lookup"><span data-stu-id="0d788-121">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![サインインして Rick を示すスクリーン ショット](secure-data/_static/rick.png)

<span data-ttu-id="0d788-123">次の図では`manager@contoso.com` 、がサインインし、マネージャーのロールに含まれています。</span><span class="sxs-lookup"><span data-stu-id="0d788-123">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![スクリーン ショットmanager@contoso.comサインイン](secure-data/_static/manager1.png)

<span data-ttu-id="0d788-125">次の図は、連絡先の詳細ビューをマネージャーには。</span><span class="sxs-lookup"><span data-stu-id="0d788-125">The following image shows the managers details view of a contact:</span></span>

![連絡先のマネージャーの表示](secure-data/_static/manager.png)

<span data-ttu-id="0d788-127">**承認**と**拒否**ボタンは、管理者と管理者のみ表示されます。</span><span class="sxs-lookup"><span data-stu-id="0d788-127">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="0d788-128">次の図では`admin@contoso.com` 、がサインインし、管理者のロールに含まれています。</span><span class="sxs-lookup"><span data-stu-id="0d788-128">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![スクリーン ショットadmin@contoso.comサインイン](secure-data/_static/admin.png)

<span data-ttu-id="0d788-130">管理者は、すべての特権を持ちます。</span><span class="sxs-lookup"><span data-stu-id="0d788-130">The administrator has all privileges.</span></span> <span data-ttu-id="0d788-131">彼女は、読み取り/編集/削除のいずれかの連絡先をことができ、連絡先の状態を変更します。</span><span class="sxs-lookup"><span data-stu-id="0d788-131">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="0d788-132">によって、アプリが作成された[スキャフォールディング](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model)次`Contact`モデル。</span><span class="sxs-lookup"><span data-stu-id="0d788-132">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="0d788-133">サンプルには、次の承認ハンドラーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0d788-133">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="0d788-134">`ContactIsOwnerAuthorizationHandler`:ユーザーがデータを編集できるようにします。</span><span class="sxs-lookup"><span data-stu-id="0d788-134">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="0d788-135">`ContactManagerAuthorizationHandler`:マネージャーが連絡先を承認または拒否できるようにします。</span><span class="sxs-lookup"><span data-stu-id="0d788-135">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="0d788-136">`ContactAdministratorsAuthorizationHandler`:管理者は、連絡先を承認または拒否したり、連絡先を編集または削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="0d788-136">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0d788-137">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="0d788-137">Prerequisites</span></span>

<span data-ttu-id="0d788-138">このチュートリアルは、高度なです。</span><span class="sxs-lookup"><span data-stu-id="0d788-138">This tutorial is advanced.</span></span> <span data-ttu-id="0d788-139">理解しておく必要があります。</span><span class="sxs-lookup"><span data-stu-id="0d788-139">You should be familiar with:</span></span>

* [<span data-ttu-id="0d788-140">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0d788-140">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="0d788-141">認証</span><span class="sxs-lookup"><span data-stu-id="0d788-141">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="0d788-142">アカウントの確認とパスワードの回復</span><span class="sxs-lookup"><span data-stu-id="0d788-142">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="0d788-143">承認</span><span class="sxs-lookup"><span data-stu-id="0d788-143">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="0d788-144">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="0d788-144">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="0d788-145">Starter および完成したアプリ</span><span class="sxs-lookup"><span data-stu-id="0d788-145">The starter and completed app</span></span>

<span data-ttu-id="0d788-146">[ダウンロード](xref:index#how-to-download-a-sample)、[完了](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples)アプリ。</span><span class="sxs-lookup"><span data-stu-id="0d788-146">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="0d788-147">[テスト](#test-the-completed-app)完成したアプリのセキュリティ機能を理解するようにします。</span><span class="sxs-lookup"><span data-stu-id="0d788-147">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="0d788-148">スターター アプリ</span><span class="sxs-lookup"><span data-stu-id="0d788-148">The starter app</span></span>

<span data-ttu-id="0d788-149">[ダウンロード](xref:index#how-to-download-a-sample)、[スターター](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/)アプリ。</span><span class="sxs-lookup"><span data-stu-id="0d788-149">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="0d788-150">アプリを実行し、タップ、 **ContactManager**リンク、および作成、編集、および連絡先を削除することを確認します。</span><span class="sxs-lookup"><span data-stu-id="0d788-150">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="0d788-151">セキュリティで保護されたユーザー データ</span><span class="sxs-lookup"><span data-stu-id="0d788-151">Secure user data</span></span>

<span data-ttu-id="0d788-152">次のセクションでは、セキュリティで保護されたユーザー データ アプリを作成するすべての主要な手順があります。</span><span class="sxs-lookup"><span data-stu-id="0d788-152">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="0d788-153">完成したプロジェクトを参照すると便利です。</span><span class="sxs-lookup"><span data-stu-id="0d788-153">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="0d788-154">ユーザーに連絡先データを関連付ける</span><span class="sxs-lookup"><span data-stu-id="0d788-154">Tie the contact data to the user</span></span>

<span data-ttu-id="0d788-155">ASP.NET を使用して、 [Identity](xref:security/authentication/identity)データが他のユーザー データではなく、ユーザーのユーザー ID を編集できます。</span><span class="sxs-lookup"><span data-stu-id="0d788-155">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="0d788-156">追加`OwnerID`と`ContactStatus`を`Contact`モデル。</span><span class="sxs-lookup"><span data-stu-id="0d788-156">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="0d788-157">`OwnerID` ユーザーの id、`AspNetUser`テーブルに、 [Identity](xref:security/authentication/identity)データベース。</span><span class="sxs-lookup"><span data-stu-id="0d788-157">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="0d788-158">`Status`連絡先が一般ユーザーが表示可能なかどうかにフィールドが決定します。</span><span class="sxs-lookup"><span data-stu-id="0d788-158">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="0d788-159">新しい移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="0d788-159">Create a new migration and update the database:</span></span>

```console
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="0d788-160">役割サービスの Id を追加します。</span><span class="sxs-lookup"><span data-stu-id="0d788-160">Add Role services to Identity</span></span>

<span data-ttu-id="0d788-161">追加[AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1)役割サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="0d788-161">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a><span data-ttu-id="0d788-162">認証されたユーザーが必要です。</span><span class="sxs-lookup"><span data-stu-id="0d788-162">Require authenticated users</span></span>

<span data-ttu-id="0d788-163">ユーザー認証を必要とする既定の認証ポリシーを設定します。</span><span class="sxs-lookup"><span data-stu-id="0d788-163">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 <span data-ttu-id="0d788-164">Razor ページ、コントローラ、またはアクション メソッド レベルでの認証をオプトアウトすることができます、`[AllowAnonymous]`属性。</span><span class="sxs-lookup"><span data-stu-id="0d788-164">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="0d788-165">ユーザー認証を必要とする既定の認証ポリシーの設定は、新しく追加された Razor ページとコント ローラーを保護します。</span><span class="sxs-lookup"><span data-stu-id="0d788-165">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="0d788-166">既定で必要な認証は、新しいコント ローラーと Razor ページで証明書利用者より安全ですが、`[Authorize]`属性。</span><span class="sxs-lookup"><span data-stu-id="0d788-166">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="0d788-167">匿名ユーザーが登録前にサイトに関する情報を取得できるように、 [Allowanonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute)をインデックスおよびプライバシーページに追加します。</span><span class="sxs-lookup"><span data-stu-id="0d788-167">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index and Privacy pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="0d788-168">テスト アカウントを構成します。</span><span class="sxs-lookup"><span data-stu-id="0d788-168">Configure the test account</span></span>

<span data-ttu-id="0d788-169">`SeedData`クラスは、2 つのアカウントを作成します。 管理者とマネージャー。</span><span class="sxs-lookup"><span data-stu-id="0d788-169">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="0d788-170">使用して、 [Secret Manager ツール](xref:security/app-secrets)これらのアカウントのパスワードを設定します。</span><span class="sxs-lookup"><span data-stu-id="0d788-170">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="0d788-171">プロジェクト ディレクトリから、パスワードの設定 (含まれるディレクトリ*Program.cs*)。</span><span class="sxs-lookup"><span data-stu-id="0d788-171">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```console
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="0d788-172">例外がときにスローされる場合は、強力なパスワードが指定されていない`SeedData.Initialize`が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="0d788-172">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="0d788-173">Update`Main`テストのパスワードを使用します。</span><span class="sxs-lookup"><span data-stu-id="0d788-173">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="0d788-174">テスト アカウントを作成し、連絡先の更新</span><span class="sxs-lookup"><span data-stu-id="0d788-174">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="0d788-175">更新プログラム、`Initialize`メソッドで、`SeedData`テスト アカウントを作成するクラス。</span><span class="sxs-lookup"><span data-stu-id="0d788-175">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="0d788-176">管理者のユーザー ID を追加し、`ContactStatus`連絡先にします。</span><span class="sxs-lookup"><span data-stu-id="0d788-176">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="0d788-177">「送信済み」と「拒否」の 1 つの連絡先を作成します。</span><span class="sxs-lookup"><span data-stu-id="0d788-177">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="0d788-178">すべての連絡先に、ユーザー ID と状態を追加します。</span><span class="sxs-lookup"><span data-stu-id="0d788-178">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="0d788-179">1 人だけが表示されます。</span><span class="sxs-lookup"><span data-stu-id="0d788-179">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="0d788-180">所有者、マネージャー、および管理者の承認ハンドラーを作成します。</span><span class="sxs-lookup"><span data-stu-id="0d788-180">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="0d788-181">作成、`ContactIsOwnerAuthorizationHandler`クラス、*承認*フォルダー。</span><span class="sxs-lookup"><span data-stu-id="0d788-181">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="0d788-182">`ContactIsOwnerAuthorizationHandler`リソースで動作しているユーザーがリソースを所有していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0d788-182">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="0d788-183">`ContactIsOwnerAuthorizationHandler`呼び出し[コンテキスト。成功](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)現在認証済みユーザーがメンバーの所有者である場合。</span><span class="sxs-lookup"><span data-stu-id="0d788-183">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="0d788-184">承認ハンドラー通常。</span><span class="sxs-lookup"><span data-stu-id="0d788-184">Authorization handlers generally:</span></span>

* <span data-ttu-id="0d788-185">返す`context.Succeed`要件を満たしている場合。</span><span class="sxs-lookup"><span data-stu-id="0d788-185">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="0d788-186">返す`Task.CompletedTask`要件が満たされていません。</span><span class="sxs-lookup"><span data-stu-id="0d788-186">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="0d788-187">`Task.CompletedTask`が成功または失敗&mdash;ではない場合は、他の承認ハンドラーの実行を許可します。</span><span class="sxs-lookup"><span data-stu-id="0d788-187">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="0d788-188">明示的に失敗する場合は、返す[コンテキスト。失敗](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)します。</span><span class="sxs-lookup"><span data-stu-id="0d788-188">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="0d788-189">アプリは、独自のデータを編集/削除/作成所有者が連絡先にできます。</span><span class="sxs-lookup"><span data-stu-id="0d788-189">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="0d788-190">`ContactIsOwnerAuthorizationHandler` 要求パラメーターで渡された操作を確認する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="0d788-190">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="0d788-191">マネージャーの承認ハンドラーを作成します。</span><span class="sxs-lookup"><span data-stu-id="0d788-191">Create a manager authorization handler</span></span>

<span data-ttu-id="0d788-192">作成、`ContactManagerAuthorizationHandler`クラス、*承認*フォルダー。</span><span class="sxs-lookup"><span data-stu-id="0d788-192">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="0d788-193">`ContactManagerAuthorizationHandler`リソースで動作しているユーザーが、マネージャーであることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0d788-193">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="0d788-194">管理者だけでは、承認したり、(新規または変更された) コンテンツの変更を拒否することができます。</span><span class="sxs-lookup"><span data-stu-id="0d788-194">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="0d788-195">管理者の承認ハンドラーを作成します。</span><span class="sxs-lookup"><span data-stu-id="0d788-195">Create an administrator authorization handler</span></span>

<span data-ttu-id="0d788-196">作成、`ContactAdministratorsAuthorizationHandler`クラス、*承認*フォルダー。</span><span class="sxs-lookup"><span data-stu-id="0d788-196">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="0d788-197">`ContactAdministratorsAuthorizationHandler`リソースで動作しているユーザーが管理者であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0d788-197">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="0d788-198">管理者は、すべての操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="0d788-198">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="0d788-199">承認ハンドラーを登録します。</span><span class="sxs-lookup"><span data-stu-id="0d788-199">Register the authorization handlers</span></span>

<span data-ttu-id="0d788-200">Entity Framework Core を使用してサービスを登録する必要があります[依存関係の注入](xref:fundamentals/dependency-injection)を使用して[AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)します。</span><span class="sxs-lookup"><span data-stu-id="0d788-200">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="0d788-201">`ContactIsOwnerAuthorizationHandler` ASP.NET Core を使用して[Identity](xref:security/authentication/identity)、これは Entity Framework Core 上に構築します。</span><span class="sxs-lookup"><span data-stu-id="0d788-201">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="0d788-202">使用するため、サービスのコレクションを使用してハンドラーを登録、`ContactsController`を通じて[依存関係の注入](xref:fundamentals/dependency-injection)します。</span><span class="sxs-lookup"><span data-stu-id="0d788-202">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0d788-203">末尾に次のコードを追加`ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="0d788-203">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="0d788-204">`ContactAdministratorsAuthorizationHandler` `ContactManagerAuthorizationHandler`シングルトンとして追加されます。</span><span class="sxs-lookup"><span data-stu-id="0d788-204">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="0d788-205">シングルトンになるため、EF を使用していないし、必要なすべての情報は、`Context`のパラメーター、`HandleRequirementAsync`メソッド。</span><span class="sxs-lookup"><span data-stu-id="0d788-205">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="0d788-206">承認をサポートします。</span><span class="sxs-lookup"><span data-stu-id="0d788-206">Support authorization</span></span>

<span data-ttu-id="0d788-207">このセクションでは、Razor ページを更新し、操作の要件クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="0d788-207">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="0d788-208">連絡先の操作の要件のクラスを確認してください。</span><span class="sxs-lookup"><span data-stu-id="0d788-208">Review the contact operations requirements class</span></span>

<span data-ttu-id="0d788-209">レビュー、`ContactOperations`クラス。</span><span class="sxs-lookup"><span data-stu-id="0d788-209">Review the `ContactOperations` class.</span></span> <span data-ttu-id="0d788-210">このクラスには、要件が含まれています。 アプリがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="0d788-210">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="0d788-211">連絡先の Razor ページの基本クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="0d788-211">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="0d788-212">連絡先の Razor ページで使用されるサービスを含む基本クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="0d788-212">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="0d788-213">基底クラスでは、1 つの場所に、初期化コードが配置します。</span><span class="sxs-lookup"><span data-stu-id="0d788-213">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="0d788-214">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="0d788-214">The preceding code:</span></span>

* <span data-ttu-id="0d788-215">追加、`IAuthorizationService`承認ハンドラーへのアクセスをサービスします。</span><span class="sxs-lookup"><span data-stu-id="0d788-215">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="0d788-216">Id を追加します。`UserManager`サービス。</span><span class="sxs-lookup"><span data-stu-id="0d788-216">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="0d788-217">`ApplicationDbContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="0d788-217">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="0d788-218">CreateModel の更新します。</span><span class="sxs-lookup"><span data-stu-id="0d788-218">Update the CreateModel</span></span>

<span data-ttu-id="0d788-219">作成のページ モデルを使用するコンストラクターを更新、`DI_BasePageModel`基本クラス。</span><span class="sxs-lookup"><span data-stu-id="0d788-219">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="0d788-220">更新プログラム、`CreateModel.OnPostAsync`メソッド。</span><span class="sxs-lookup"><span data-stu-id="0d788-220">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="0d788-221">ユーザー ID を追加、`Contact`モデル。</span><span class="sxs-lookup"><span data-stu-id="0d788-221">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="0d788-222">ユーザーが連絡先を作成するためのアクセス許可を確認する認証ハンドラーを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0d788-222">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="0d788-223">更新プログラム、IndexModel</span><span class="sxs-lookup"><span data-stu-id="0d788-223">Update the IndexModel</span></span>

<span data-ttu-id="0d788-224">更新プログラム、`OnGetAsync`メソッドのため、一般ユーザーにのみ許可されている連絡先が表示されます。</span><span class="sxs-lookup"><span data-stu-id="0d788-224">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="0d788-225">更新プログラム、EditModel</span><span class="sxs-lookup"><span data-stu-id="0d788-225">Update the EditModel</span></span>

<span data-ttu-id="0d788-226">ユーザーが連絡先を所有していることを確認するための承認ハンドラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="0d788-226">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="0d788-227">リソース承認が検証されているため、`[Authorize]`属性が十分ではありません。</span><span class="sxs-lookup"><span data-stu-id="0d788-227">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="0d788-228">アプリは、属性が評価されたときに、リソースへのアクセスにすることがありません。</span><span class="sxs-lookup"><span data-stu-id="0d788-228">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="0d788-229">リソース ベースの承認は、命令型である必要があります。</span><span class="sxs-lookup"><span data-stu-id="0d788-229">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="0d788-230">ページ モデルに読み込んで、または読み込みハンドラー自体内で、アプリが、リソースへのアクセスを持つ、チェックを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0d788-230">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="0d788-231">リソース キーを渡すことで、リソースを頻繁にアクセスするとします。</span><span class="sxs-lookup"><span data-stu-id="0d788-231">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="0d788-232">更新プログラム、DeleteModel</span><span class="sxs-lookup"><span data-stu-id="0d788-232">Update the DeleteModel</span></span>

<span data-ttu-id="0d788-233">承認ハンドラーを使用して、ユーザーが連絡先に削除するアクセス許可を持ってことを確認する delete ページ モデルを更新します。</span><span class="sxs-lookup"><span data-stu-id="0d788-233">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="0d788-234">承認サービスをビューに挿入します。</span><span class="sxs-lookup"><span data-stu-id="0d788-234">Inject the authorization service into the views</span></span>

<span data-ttu-id="0d788-235">現時点では、UI の表示では、編集し、ユーザーが変更できない連絡先へのリンクを削除します。</span><span class="sxs-lookup"><span data-stu-id="0d788-235">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="0d788-236">*Pages/_ViewImports*ファイルに承認サービスを挿入して、すべてのビューで使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="0d788-236">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="0d788-237">上記のマークアップにいくつか追加`using`ステートメント。</span><span class="sxs-lookup"><span data-stu-id="0d788-237">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="0d788-238">更新プログラム、**編集**と**削除**でリンク*Pages/Contacts/Index.cshtml*適切なアクセス許可を持つユーザーのみレンダリングするように。</span><span class="sxs-lookup"><span data-stu-id="0d788-238">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="0d788-239">変更データへのアクセス許可がないユーザーからのリンクを非表示と、アプリをセキュリティで保護しません。</span><span class="sxs-lookup"><span data-stu-id="0d788-239">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="0d788-240">リンクを非表示アプリよりユーザー フレンドリな唯一の有効なリンクを表示することで。</span><span class="sxs-lookup"><span data-stu-id="0d788-240">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="0d788-241">ユーザーは、編集を呼び出すし、所有していないデータの操作を削除するには、生成された Url を切断できます。</span><span class="sxs-lookup"><span data-stu-id="0d788-241">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="0d788-242">Razor ページまたはコント ローラーは、データを保護するアクセス チェックを適用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0d788-242">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="0d788-243">更新プログラムの詳細</span><span class="sxs-lookup"><span data-stu-id="0d788-243">Update Details</span></span>

<span data-ttu-id="0d788-244">マネージャーが承認または連絡先を拒否するために、詳細ビューを更新します。</span><span class="sxs-lookup"><span data-stu-id="0d788-244">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="0d788-245">詳細のページ モデルを更新します。</span><span class="sxs-lookup"><span data-stu-id="0d788-245">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="0d788-246">追加またはロールにユーザーを削除します。</span><span class="sxs-lookup"><span data-stu-id="0d788-246">Add or remove a user to a role</span></span>

<span data-ttu-id="0d788-247">参照してください[今月](https://github.com/aspnet/AspNetCore.Docs/issues/8502)について。</span><span class="sxs-lookup"><span data-stu-id="0d788-247">See [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="0d788-248">ユーザーから権限を削除しています。</span><span class="sxs-lookup"><span data-stu-id="0d788-248">Removing privileges from a user.</span></span> <span data-ttu-id="0d788-249">たとえば、チャットアプリでユーザーのミュートを行います。</span><span class="sxs-lookup"><span data-stu-id="0d788-249">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="0d788-250">ユーザーに特権を追加します。</span><span class="sxs-lookup"><span data-stu-id="0d788-250">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="0d788-251">完成したアプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="0d788-251">Test the completed app</span></span>

<span data-ttu-id="0d788-252">シード処理されたユーザー アカウントのパスワードを設定していない場合は、使用、 [Secret Manager ツール](xref:security/app-secrets#secret-manager)パスワードを設定します。</span><span class="sxs-lookup"><span data-stu-id="0d788-252">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="0d788-253">強力なパスワードを選択してください:8個以上の文字と、少なくとも1つの大文字の文字、数字、および記号を使用します。</span><span class="sxs-lookup"><span data-stu-id="0d788-253">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="0d788-254">たとえば、`Passw0rd!`強力なパスワード要件を満たしています。</span><span class="sxs-lookup"><span data-stu-id="0d788-254">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="0d788-255">プロジェクトのフォルダーから次のコマンドを実行、`<PW>`パスワードです。</span><span class="sxs-lookup"><span data-stu-id="0d788-255">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```console
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="0d788-256">場合は、アプリでは、連絡先があります。</span><span class="sxs-lookup"><span data-stu-id="0d788-256">If the app has contacts:</span></span>

* <span data-ttu-id="0d788-257">すべてのレコードの削除、`Contact`テーブル。</span><span class="sxs-lookup"><span data-stu-id="0d788-257">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="0d788-258">データベースをシードするアプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="0d788-258">Restart the app to seed the database.</span></span>

<span data-ttu-id="0d788-259">完成したアプリをテストする簡単な方法では、次の 3 つの異なるブラウザー (または incognito/inprivate ブラウズ セッション) を起動します。</span><span class="sxs-lookup"><span data-stu-id="0d788-259">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="0d788-260">1 つのブラウザーで新しいユーザーを登録します (たとえば、 `test@example.com`)。</span><span class="sxs-lookup"><span data-stu-id="0d788-260">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="0d788-261">ブラウザーごとに別のユーザーでサインインします。</span><span class="sxs-lookup"><span data-stu-id="0d788-261">Sign in to each browser with a different user.</span></span> <span data-ttu-id="0d788-262">次の操作を確認します。</span><span class="sxs-lookup"><span data-stu-id="0d788-262">Verify the following operations:</span></span>

* <span data-ttu-id="0d788-263">登録済みユーザーには、すべての承認済みの連絡先データを表示できます。</span><span class="sxs-lookup"><span data-stu-id="0d788-263">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="0d788-264">登録済みユーザーは編集/削除が自分のデータ。</span><span class="sxs-lookup"><span data-stu-id="0d788-264">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="0d788-265">管理者では、連絡先データの承認または却下をします。</span><span class="sxs-lookup"><span data-stu-id="0d788-265">Managers can approve/reject contact data.</span></span> <span data-ttu-id="0d788-266">`Details`ビューには、**承認**と**拒否**ボタン。</span><span class="sxs-lookup"><span data-stu-id="0d788-266">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="0d788-267">管理者承認または却下して編集/削除のすべてのデータ。</span><span class="sxs-lookup"><span data-stu-id="0d788-267">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="0d788-268">ユーザー</span><span class="sxs-lookup"><span data-stu-id="0d788-268">User</span></span>                | <span data-ttu-id="0d788-269">アプリによってシード処理</span><span class="sxs-lookup"><span data-stu-id="0d788-269">Seeded by the app</span></span> | <span data-ttu-id="0d788-270">オプション</span><span class="sxs-lookup"><span data-stu-id="0d788-270">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="0d788-271">いいえ</span><span class="sxs-lookup"><span data-stu-id="0d788-271">No</span></span>                | <span data-ttu-id="0d788-272">独自のデータを編集/削除します。</span><span class="sxs-lookup"><span data-stu-id="0d788-272">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="0d788-273">[はい]</span><span class="sxs-lookup"><span data-stu-id="0d788-273">Yes</span></span>               | <span data-ttu-id="0d788-274">承認または却下と編集/削除は、データを所有します。</span><span class="sxs-lookup"><span data-stu-id="0d788-274">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="0d788-275">[はい]</span><span class="sxs-lookup"><span data-stu-id="0d788-275">Yes</span></span>               | <span data-ttu-id="0d788-276">承認または却下し、すべてのデータの編集/削除します。</span><span class="sxs-lookup"><span data-stu-id="0d788-276">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="0d788-277">管理者のブラウザーで連絡先を作成します。</span><span class="sxs-lookup"><span data-stu-id="0d788-277">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="0d788-278">削除の URL をコピーし、管理者の連絡先から管理者を編集します。</span><span class="sxs-lookup"><span data-stu-id="0d788-278">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="0d788-279">テスト ユーザーのブラウザー テスト ユーザーがこれらの操作を実行できないことを確認するには、これらのリンクを貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="0d788-279">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="0d788-280">スターター アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="0d788-280">Create the starter app</span></span>

* <span data-ttu-id="0d788-281">「ContactManager」という名前の Razor ページ アプリの作成</span><span class="sxs-lookup"><span data-stu-id="0d788-281">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="0d788-282">使用してアプリを作成する**個々 のユーザー アカウント**します。</span><span class="sxs-lookup"><span data-stu-id="0d788-282">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="0d788-283">ファイル名「ContactManager」名前空間サンプルで使用する名前空間が一致するようにします。</span><span class="sxs-lookup"><span data-stu-id="0d788-283">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="0d788-284">`-uld` SQLite の代わりに LocalDB を指定します</span><span class="sxs-lookup"><span data-stu-id="0d788-284">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```console
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="0d788-285">*モデル/Contact を追加します。*</span><span class="sxs-lookup"><span data-stu-id="0d788-285">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="0d788-286">スキャフォールディング、`Contact`モデル。</span><span class="sxs-lookup"><span data-stu-id="0d788-286">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="0d788-287">最初の移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="0d788-287">Create initial migration and update the database:</span></span>

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
  ```

<span data-ttu-id="0d788-288">`dotnet aspnet-codegenerator razorpage`コマンドでバグが発生した場合は、 [GitHub の問題](https://github.com/aspnet/Scaffolding/issues/984)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0d788-288">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="0d788-289">*Pages/Shared/Layout*ファイルで**contactmanager**アンカーを更新します。</span><span class="sxs-lookup"><span data-stu-id="0d788-289">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="0d788-290">作成、編集、および連絡先を削除して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="0d788-290">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="0d788-291">データベースのシード</span><span class="sxs-lookup"><span data-stu-id="0d788-291">Seed the database</span></span>

<span data-ttu-id="0d788-292">[SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs)クラスを*Data*フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="0d788-292">Add the [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="0d788-293">呼び出す`SeedData.Initialize`から`Main`:</span><span class="sxs-lookup"><span data-stu-id="0d788-293">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="0d788-294">アプリが、データベースをシード処理をテストします。</span><span class="sxs-lookup"><span data-stu-id="0d788-294">Test that the app seeded the database.</span></span> <span data-ttu-id="0d788-295">DB の連絡先に行がある場合は、seed メソッドは実行されません。</span><span class="sxs-lookup"><span data-stu-id="0d788-295">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="0d788-296">このチュートリアルでは、承認によって保護されたユーザー データと ASP.NET Core web アプリを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="0d788-296">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="0d788-297">認証済み (登録済み) のユーザーの連絡先の一覧を表示を作成します。</span><span class="sxs-lookup"><span data-stu-id="0d788-297">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="0d788-298">これには 3 つのセキュリティ グループがあります。</span><span class="sxs-lookup"><span data-stu-id="0d788-298">There are three security groups:</span></span>

* <span data-ttu-id="0d788-299">**ユーザーが登録されている**承認済みのすべてのデータを表示することができ、独自のデータ編集、または削除できます。</span><span class="sxs-lookup"><span data-stu-id="0d788-299">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="0d788-300">**マネージャー**を承認または連絡先データを拒否します。</span><span class="sxs-lookup"><span data-stu-id="0d788-300">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="0d788-301">承認されたメンバーのみがユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="0d788-301">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="0d788-302">**管理者**承認または却下と編集/削除のすべてのデータをことができます。</span><span class="sxs-lookup"><span data-stu-id="0d788-302">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="0d788-303">次の図では、ユーザー Rick の (`rick@example.com`) がサインインしています。</span><span class="sxs-lookup"><span data-stu-id="0d788-303">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="0d788-304">Rick は許可されている連絡先のみを表示し、**編集**/**削除**/**新規作成**彼の連絡先へのリンク。</span><span class="sxs-lookup"><span data-stu-id="0d788-304">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="0d788-305">最後のレコードのみが Rick、表示によって作成された**編集**と**削除**リンク。</span><span class="sxs-lookup"><span data-stu-id="0d788-305">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="0d788-306">他のユーザーは、管理者は、状態を"Approved"に変わるまで、最後のレコードを表示されません。</span><span class="sxs-lookup"><span data-stu-id="0d788-306">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![サインインして Rick を示すスクリーン ショット](secure-data/_static/rick.png)

<span data-ttu-id="0d788-308">次の図では`manager@contoso.com` 、がサインインし、マネージャーのロールに含まれています。</span><span class="sxs-lookup"><span data-stu-id="0d788-308">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![スクリーン ショットmanager@contoso.comサインイン](secure-data/_static/manager1.png)

<span data-ttu-id="0d788-310">次の図は、連絡先の詳細ビューをマネージャーには。</span><span class="sxs-lookup"><span data-stu-id="0d788-310">The following image shows the managers details view of a contact:</span></span>

![連絡先のマネージャーの表示](secure-data/_static/manager.png)

<span data-ttu-id="0d788-312">**承認**と**拒否**ボタンは、管理者と管理者のみ表示されます。</span><span class="sxs-lookup"><span data-stu-id="0d788-312">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="0d788-313">次の図では`admin@contoso.com` 、がサインインし、管理者のロールに含まれています。</span><span class="sxs-lookup"><span data-stu-id="0d788-313">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![スクリーン ショットadmin@contoso.comサインイン](secure-data/_static/admin.png)

<span data-ttu-id="0d788-315">管理者は、すべての特権を持ちます。</span><span class="sxs-lookup"><span data-stu-id="0d788-315">The administrator has all privileges.</span></span> <span data-ttu-id="0d788-316">彼女は、読み取り/編集/削除のいずれかの連絡先をことができ、連絡先の状態を変更します。</span><span class="sxs-lookup"><span data-stu-id="0d788-316">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="0d788-317">によって、アプリが作成された[スキャフォールディング](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model)次`Contact`モデル。</span><span class="sxs-lookup"><span data-stu-id="0d788-317">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="0d788-318">サンプルには、次の承認ハンドラーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0d788-318">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="0d788-319">`ContactIsOwnerAuthorizationHandler`:ユーザーがデータを編集できるようにします。</span><span class="sxs-lookup"><span data-stu-id="0d788-319">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="0d788-320">`ContactManagerAuthorizationHandler`:マネージャーが連絡先を承認または拒否できるようにします。</span><span class="sxs-lookup"><span data-stu-id="0d788-320">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="0d788-321">`ContactAdministratorsAuthorizationHandler`:管理者は、連絡先を承認または拒否したり、連絡先を編集または削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="0d788-321">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0d788-322">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="0d788-322">Prerequisites</span></span>

<span data-ttu-id="0d788-323">このチュートリアルは、高度なです。</span><span class="sxs-lookup"><span data-stu-id="0d788-323">This tutorial is advanced.</span></span> <span data-ttu-id="0d788-324">理解しておく必要があります。</span><span class="sxs-lookup"><span data-stu-id="0d788-324">You should be familiar with:</span></span>

* [<span data-ttu-id="0d788-325">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0d788-325">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="0d788-326">認証</span><span class="sxs-lookup"><span data-stu-id="0d788-326">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="0d788-327">アカウントの確認とパスワードの回復</span><span class="sxs-lookup"><span data-stu-id="0d788-327">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="0d788-328">承認</span><span class="sxs-lookup"><span data-stu-id="0d788-328">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="0d788-329">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="0d788-329">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="0d788-330">Starter および完成したアプリ</span><span class="sxs-lookup"><span data-stu-id="0d788-330">The starter and completed app</span></span>

<span data-ttu-id="0d788-331">[ダウンロード](xref:index#how-to-download-a-sample)、[完了](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples)アプリ。</span><span class="sxs-lookup"><span data-stu-id="0d788-331">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="0d788-332">[テスト](#test-the-completed-app)完成したアプリのセキュリティ機能を理解するようにします。</span><span class="sxs-lookup"><span data-stu-id="0d788-332">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="0d788-333">スターター アプリ</span><span class="sxs-lookup"><span data-stu-id="0d788-333">The starter app</span></span>

<span data-ttu-id="0d788-334">[ダウンロード](xref:index#how-to-download-a-sample)、[スターター](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/)アプリ。</span><span class="sxs-lookup"><span data-stu-id="0d788-334">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="0d788-335">アプリを実行し、タップ、 **ContactManager**リンク、および作成、編集、および連絡先を削除することを確認します。</span><span class="sxs-lookup"><span data-stu-id="0d788-335">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="0d788-336">セキュリティで保護されたユーザー データ</span><span class="sxs-lookup"><span data-stu-id="0d788-336">Secure user data</span></span>

<span data-ttu-id="0d788-337">次のセクションでは、セキュリティで保護されたユーザー データ アプリを作成するすべての主要な手順があります。</span><span class="sxs-lookup"><span data-stu-id="0d788-337">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="0d788-338">完成したプロジェクトを参照すると便利です。</span><span class="sxs-lookup"><span data-stu-id="0d788-338">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="0d788-339">ユーザーに連絡先データを関連付ける</span><span class="sxs-lookup"><span data-stu-id="0d788-339">Tie the contact data to the user</span></span>

<span data-ttu-id="0d788-340">ASP.NET を使用して、 [Identity](xref:security/authentication/identity)データが他のユーザー データではなく、ユーザーのユーザー ID を編集できます。</span><span class="sxs-lookup"><span data-stu-id="0d788-340">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="0d788-341">追加`OwnerID`と`ContactStatus`を`Contact`モデル。</span><span class="sxs-lookup"><span data-stu-id="0d788-341">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="0d788-342">`OwnerID` ユーザーの id、`AspNetUser`テーブルに、 [Identity](xref:security/authentication/identity)データベース。</span><span class="sxs-lookup"><span data-stu-id="0d788-342">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="0d788-343">`Status`連絡先が一般ユーザーが表示可能なかどうかにフィールドが決定します。</span><span class="sxs-lookup"><span data-stu-id="0d788-343">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="0d788-344">新しい移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="0d788-344">Create a new migration and update the database:</span></span>

```console
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="0d788-345">役割サービスの Id を追加します。</span><span class="sxs-lookup"><span data-stu-id="0d788-345">Add Role services to Identity</span></span>

<span data-ttu-id="0d788-346">追加[AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1)役割サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="0d788-346">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a><span data-ttu-id="0d788-347">認証されたユーザーが必要です。</span><span class="sxs-lookup"><span data-stu-id="0d788-347">Require authenticated users</span></span>

<span data-ttu-id="0d788-348">ユーザー認証を必要とする既定の認証ポリシーを設定します。</span><span class="sxs-lookup"><span data-stu-id="0d788-348">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="0d788-349">Razor ページ、コントローラ、またはアクション メソッド レベルでの認証をオプトアウトすることができます、`[AllowAnonymous]`属性。</span><span class="sxs-lookup"><span data-stu-id="0d788-349">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="0d788-350">ユーザー認証を必要とする既定の認証ポリシーの設定は、新しく追加された Razor ページとコント ローラーを保護します。</span><span class="sxs-lookup"><span data-stu-id="0d788-350">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="0d788-351">既定で必要な認証は、新しいコント ローラーと Razor ページで証明書利用者より安全ですが、`[Authorize]`属性。</span><span class="sxs-lookup"><span data-stu-id="0d788-351">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="0d788-352">追加[AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute)インデックス、および連絡先について、ページ匿名ユーザーは登録前に、サイトに関する情報を取得できるようにします。</span><span class="sxs-lookup"><span data-stu-id="0d788-352">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="0d788-353">テスト アカウントを構成します。</span><span class="sxs-lookup"><span data-stu-id="0d788-353">Configure the test account</span></span>

<span data-ttu-id="0d788-354">`SeedData`クラスは、2 つのアカウントを作成します。 管理者とマネージャー。</span><span class="sxs-lookup"><span data-stu-id="0d788-354">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="0d788-355">使用して、 [Secret Manager ツール](xref:security/app-secrets)これらのアカウントのパスワードを設定します。</span><span class="sxs-lookup"><span data-stu-id="0d788-355">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="0d788-356">プロジェクト ディレクトリから、パスワードの設定 (含まれるディレクトリ*Program.cs*)。</span><span class="sxs-lookup"><span data-stu-id="0d788-356">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```console
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="0d788-357">例外がときにスローされる場合は、強力なパスワードが指定されていない`SeedData.Initialize`が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="0d788-357">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="0d788-358">Update`Main`テストのパスワードを使用します。</span><span class="sxs-lookup"><span data-stu-id="0d788-358">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="0d788-359">テスト アカウントを作成し、連絡先の更新</span><span class="sxs-lookup"><span data-stu-id="0d788-359">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="0d788-360">更新プログラム、`Initialize`メソッドで、`SeedData`テスト アカウントを作成するクラス。</span><span class="sxs-lookup"><span data-stu-id="0d788-360">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="0d788-361">管理者のユーザー ID を追加し、`ContactStatus`連絡先にします。</span><span class="sxs-lookup"><span data-stu-id="0d788-361">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="0d788-362">「送信済み」と「拒否」の 1 つの連絡先を作成します。</span><span class="sxs-lookup"><span data-stu-id="0d788-362">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="0d788-363">すべての連絡先に、ユーザー ID と状態を追加します。</span><span class="sxs-lookup"><span data-stu-id="0d788-363">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="0d788-364">1 人だけが表示されます。</span><span class="sxs-lookup"><span data-stu-id="0d788-364">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="0d788-365">所有者、マネージャー、および管理者の承認ハンドラーを作成します。</span><span class="sxs-lookup"><span data-stu-id="0d788-365">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="0d788-366">*承認*フォルダーを作成し、そこ`ContactIsOwnerAuthorizationHandler`にクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="0d788-366">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="0d788-367">`ContactIsOwnerAuthorizationHandler`リソースで動作しているユーザーがリソースを所有していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0d788-367">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="0d788-368">`ContactIsOwnerAuthorizationHandler`呼び出し[コンテキスト。成功](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)現在認証済みユーザーがメンバーの所有者である場合。</span><span class="sxs-lookup"><span data-stu-id="0d788-368">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="0d788-369">承認ハンドラー通常。</span><span class="sxs-lookup"><span data-stu-id="0d788-369">Authorization handlers generally:</span></span>

* <span data-ttu-id="0d788-370">返す`context.Succeed`要件を満たしている場合。</span><span class="sxs-lookup"><span data-stu-id="0d788-370">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="0d788-371">返す`Task.CompletedTask`要件が満たされていません。</span><span class="sxs-lookup"><span data-stu-id="0d788-371">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="0d788-372">`Task.CompletedTask`が成功または失敗&mdash;ではない場合は、他の承認ハンドラーの実行を許可します。</span><span class="sxs-lookup"><span data-stu-id="0d788-372">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="0d788-373">明示的に失敗する場合は、返す[コンテキスト。失敗](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)します。</span><span class="sxs-lookup"><span data-stu-id="0d788-373">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="0d788-374">アプリは、独自のデータを編集/削除/作成所有者が連絡先にできます。</span><span class="sxs-lookup"><span data-stu-id="0d788-374">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="0d788-375">`ContactIsOwnerAuthorizationHandler` 要求パラメーターで渡された操作を確認する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="0d788-375">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="0d788-376">マネージャーの承認ハンドラーを作成します。</span><span class="sxs-lookup"><span data-stu-id="0d788-376">Create a manager authorization handler</span></span>

<span data-ttu-id="0d788-377">作成、`ContactManagerAuthorizationHandler`クラス、*承認*フォルダー。</span><span class="sxs-lookup"><span data-stu-id="0d788-377">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="0d788-378">`ContactManagerAuthorizationHandler`リソースで動作しているユーザーが、マネージャーであることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0d788-378">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="0d788-379">管理者だけでは、承認したり、(新規または変更された) コンテンツの変更を拒否することができます。</span><span class="sxs-lookup"><span data-stu-id="0d788-379">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="0d788-380">管理者の承認ハンドラーを作成します。</span><span class="sxs-lookup"><span data-stu-id="0d788-380">Create an administrator authorization handler</span></span>

<span data-ttu-id="0d788-381">作成、`ContactAdministratorsAuthorizationHandler`クラス、*承認*フォルダー。</span><span class="sxs-lookup"><span data-stu-id="0d788-381">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="0d788-382">`ContactAdministratorsAuthorizationHandler`リソースで動作しているユーザーが管理者であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0d788-382">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="0d788-383">管理者は、すべての操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="0d788-383">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="0d788-384">承認ハンドラーを登録します。</span><span class="sxs-lookup"><span data-stu-id="0d788-384">Register the authorization handlers</span></span>

<span data-ttu-id="0d788-385">Entity Framework Core を使用してサービスを登録する必要があります[依存関係の注入](xref:fundamentals/dependency-injection)を使用して[AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)します。</span><span class="sxs-lookup"><span data-stu-id="0d788-385">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="0d788-386">`ContactIsOwnerAuthorizationHandler` ASP.NET Core を使用して[Identity](xref:security/authentication/identity)、これは Entity Framework Core 上に構築します。</span><span class="sxs-lookup"><span data-stu-id="0d788-386">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="0d788-387">使用するため、サービスのコレクションを使用してハンドラーを登録、`ContactsController`を通じて[依存関係の注入](xref:fundamentals/dependency-injection)します。</span><span class="sxs-lookup"><span data-stu-id="0d788-387">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0d788-388">末尾に次のコードを追加`ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="0d788-388">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="0d788-389">`ContactAdministratorsAuthorizationHandler` `ContactManagerAuthorizationHandler`シングルトンとして追加されます。</span><span class="sxs-lookup"><span data-stu-id="0d788-389">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="0d788-390">シングルトンになるため、EF を使用していないし、必要なすべての情報は、`Context`のパラメーター、`HandleRequirementAsync`メソッド。</span><span class="sxs-lookup"><span data-stu-id="0d788-390">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="0d788-391">承認をサポートします。</span><span class="sxs-lookup"><span data-stu-id="0d788-391">Support authorization</span></span>

<span data-ttu-id="0d788-392">このセクションでは、Razor ページを更新し、操作の要件クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="0d788-392">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="0d788-393">連絡先の操作の要件のクラスを確認してください。</span><span class="sxs-lookup"><span data-stu-id="0d788-393">Review the contact operations requirements class</span></span>

<span data-ttu-id="0d788-394">レビュー、`ContactOperations`クラス。</span><span class="sxs-lookup"><span data-stu-id="0d788-394">Review the `ContactOperations` class.</span></span> <span data-ttu-id="0d788-395">このクラスには、要件が含まれています。 アプリがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="0d788-395">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="0d788-396">連絡先の Razor ページの基本クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="0d788-396">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="0d788-397">連絡先の Razor ページで使用されるサービスを含む基本クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="0d788-397">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="0d788-398">基底クラスでは、1 つの場所に、初期化コードが配置します。</span><span class="sxs-lookup"><span data-stu-id="0d788-398">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="0d788-399">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="0d788-399">The preceding code:</span></span>

* <span data-ttu-id="0d788-400">追加、`IAuthorizationService`承認ハンドラーへのアクセスをサービスします。</span><span class="sxs-lookup"><span data-stu-id="0d788-400">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="0d788-401">Id を追加します。`UserManager`サービス。</span><span class="sxs-lookup"><span data-stu-id="0d788-401">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="0d788-402">`ApplicationDbContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="0d788-402">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="0d788-403">CreateModel の更新します。</span><span class="sxs-lookup"><span data-stu-id="0d788-403">Update the CreateModel</span></span>

<span data-ttu-id="0d788-404">作成のページ モデルを使用するコンストラクターを更新、`DI_BasePageModel`基本クラス。</span><span class="sxs-lookup"><span data-stu-id="0d788-404">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="0d788-405">更新プログラム、`CreateModel.OnPostAsync`メソッド。</span><span class="sxs-lookup"><span data-stu-id="0d788-405">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="0d788-406">ユーザー ID を追加、`Contact`モデル。</span><span class="sxs-lookup"><span data-stu-id="0d788-406">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="0d788-407">ユーザーが連絡先を作成するためのアクセス許可を確認する認証ハンドラーを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0d788-407">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="0d788-408">更新プログラム、IndexModel</span><span class="sxs-lookup"><span data-stu-id="0d788-408">Update the IndexModel</span></span>

<span data-ttu-id="0d788-409">更新プログラム、`OnGetAsync`メソッドのため、一般ユーザーにのみ許可されている連絡先が表示されます。</span><span class="sxs-lookup"><span data-stu-id="0d788-409">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="0d788-410">更新プログラム、EditModel</span><span class="sxs-lookup"><span data-stu-id="0d788-410">Update the EditModel</span></span>

<span data-ttu-id="0d788-411">ユーザーが連絡先を所有していることを確認するための承認ハンドラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="0d788-411">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="0d788-412">リソース承認が検証されているため、`[Authorize]`属性が十分ではありません。</span><span class="sxs-lookup"><span data-stu-id="0d788-412">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="0d788-413">アプリは、属性が評価されたときに、リソースへのアクセスにすることがありません。</span><span class="sxs-lookup"><span data-stu-id="0d788-413">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="0d788-414">リソース ベースの承認は、命令型である必要があります。</span><span class="sxs-lookup"><span data-stu-id="0d788-414">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="0d788-415">ページ モデルに読み込んで、または読み込みハンドラー自体内で、アプリが、リソースへのアクセスを持つ、チェックを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0d788-415">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="0d788-416">リソース キーを渡すことで、リソースを頻繁にアクセスするとします。</span><span class="sxs-lookup"><span data-stu-id="0d788-416">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="0d788-417">更新プログラム、DeleteModel</span><span class="sxs-lookup"><span data-stu-id="0d788-417">Update the DeleteModel</span></span>

<span data-ttu-id="0d788-418">承認ハンドラーを使用して、ユーザーが連絡先に削除するアクセス許可を持ってことを確認する delete ページ モデルを更新します。</span><span class="sxs-lookup"><span data-stu-id="0d788-418">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="0d788-419">承認サービスをビューに挿入します。</span><span class="sxs-lookup"><span data-stu-id="0d788-419">Inject the authorization service into the views</span></span>

<span data-ttu-id="0d788-420">現時点では、UI の表示では、編集し、ユーザーが変更できない連絡先へのリンクを削除します。</span><span class="sxs-lookup"><span data-stu-id="0d788-420">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="0d788-421">承認サービスを挿入、 *Views/_ViewImports.cshtml*ファイルのすべてのビューに使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="0d788-421">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="0d788-422">上記のマークアップにいくつか追加`using`ステートメント。</span><span class="sxs-lookup"><span data-stu-id="0d788-422">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="0d788-423">更新プログラム、**編集**と**削除**でリンク*Pages/Contacts/Index.cshtml*適切なアクセス許可を持つユーザーのみレンダリングするように。</span><span class="sxs-lookup"><span data-stu-id="0d788-423">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="0d788-424">変更データへのアクセス許可がないユーザーからのリンクを非表示と、アプリをセキュリティで保護しません。</span><span class="sxs-lookup"><span data-stu-id="0d788-424">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="0d788-425">リンクを非表示アプリよりユーザー フレンドリな唯一の有効なリンクを表示することで。</span><span class="sxs-lookup"><span data-stu-id="0d788-425">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="0d788-426">ユーザーは、編集を呼び出すし、所有していないデータの操作を削除するには、生成された Url を切断できます。</span><span class="sxs-lookup"><span data-stu-id="0d788-426">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="0d788-427">Razor ページまたはコント ローラーは、データを保護するアクセス チェックを適用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0d788-427">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="0d788-428">更新プログラムの詳細</span><span class="sxs-lookup"><span data-stu-id="0d788-428">Update Details</span></span>

<span data-ttu-id="0d788-429">マネージャーが承認または連絡先を拒否するために、詳細ビューを更新します。</span><span class="sxs-lookup"><span data-stu-id="0d788-429">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="0d788-430">詳細のページ モデルを更新します。</span><span class="sxs-lookup"><span data-stu-id="0d788-430">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="0d788-431">追加またはロールにユーザーを削除します。</span><span class="sxs-lookup"><span data-stu-id="0d788-431">Add or remove a user to a role</span></span>

<span data-ttu-id="0d788-432">参照してください[今月](https://github.com/aspnet/AspNetCore.Docs/issues/8502)について。</span><span class="sxs-lookup"><span data-stu-id="0d788-432">See [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="0d788-433">ユーザーから権限を削除しています。</span><span class="sxs-lookup"><span data-stu-id="0d788-433">Removing privileges from a user.</span></span> <span data-ttu-id="0d788-434">たとえば、チャットアプリでユーザーのミュートを行います。</span><span class="sxs-lookup"><span data-stu-id="0d788-434">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="0d788-435">ユーザーに特権を追加します。</span><span class="sxs-lookup"><span data-stu-id="0d788-435">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="0d788-436">完成したアプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="0d788-436">Test the completed app</span></span>

<span data-ttu-id="0d788-437">シード処理されたユーザー アカウントのパスワードを設定していない場合は、使用、 [Secret Manager ツール](xref:security/app-secrets#secret-manager)パスワードを設定します。</span><span class="sxs-lookup"><span data-stu-id="0d788-437">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="0d788-438">強力なパスワードを選択してください:8個以上の文字と、少なくとも1つの大文字の文字、数字、および記号を使用します。</span><span class="sxs-lookup"><span data-stu-id="0d788-438">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="0d788-439">たとえば、`Passw0rd!`強力なパスワード要件を満たしています。</span><span class="sxs-lookup"><span data-stu-id="0d788-439">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="0d788-440">プロジェクトのフォルダーから次のコマンドを実行、`<PW>`パスワードです。</span><span class="sxs-lookup"><span data-stu-id="0d788-440">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```console
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="0d788-441">データベースを削除して更新する</span><span class="sxs-lookup"><span data-stu-id="0d788-441">Drop and update the Database</span></span>

    ```console
     dotnet ef database drop -f
     dotnet ef database update  
     ```

* <span data-ttu-id="0d788-442">データベースをシードするアプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="0d788-442">Restart the app to seed the database.</span></span>

<span data-ttu-id="0d788-443">完成したアプリをテストする簡単な方法では、次の 3 つの異なるブラウザー (または incognito/inprivate ブラウズ セッション) を起動します。</span><span class="sxs-lookup"><span data-stu-id="0d788-443">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="0d788-444">1 つのブラウザーで新しいユーザーを登録します (たとえば、 `test@example.com`)。</span><span class="sxs-lookup"><span data-stu-id="0d788-444">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="0d788-445">ブラウザーごとに別のユーザーでサインインします。</span><span class="sxs-lookup"><span data-stu-id="0d788-445">Sign in to each browser with a different user.</span></span> <span data-ttu-id="0d788-446">次の操作を確認します。</span><span class="sxs-lookup"><span data-stu-id="0d788-446">Verify the following operations:</span></span>

* <span data-ttu-id="0d788-447">登録済みユーザーには、すべての承認済みの連絡先データを表示できます。</span><span class="sxs-lookup"><span data-stu-id="0d788-447">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="0d788-448">登録済みユーザーは編集/削除が自分のデータ。</span><span class="sxs-lookup"><span data-stu-id="0d788-448">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="0d788-449">管理者では、連絡先データの承認または却下をします。</span><span class="sxs-lookup"><span data-stu-id="0d788-449">Managers can approve/reject contact data.</span></span> <span data-ttu-id="0d788-450">`Details`ビューには、**承認**と**拒否**ボタン。</span><span class="sxs-lookup"><span data-stu-id="0d788-450">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="0d788-451">管理者承認または却下して編集/削除のすべてのデータ。</span><span class="sxs-lookup"><span data-stu-id="0d788-451">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="0d788-452">ユーザー</span><span class="sxs-lookup"><span data-stu-id="0d788-452">User</span></span>                | <span data-ttu-id="0d788-453">アプリによってシード処理</span><span class="sxs-lookup"><span data-stu-id="0d788-453">Seeded by the app</span></span> | <span data-ttu-id="0d788-454">オプション</span><span class="sxs-lookup"><span data-stu-id="0d788-454">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="0d788-455">いいえ</span><span class="sxs-lookup"><span data-stu-id="0d788-455">No</span></span>                | <span data-ttu-id="0d788-456">独自のデータを編集/削除します。</span><span class="sxs-lookup"><span data-stu-id="0d788-456">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="0d788-457">[はい]</span><span class="sxs-lookup"><span data-stu-id="0d788-457">Yes</span></span>               | <span data-ttu-id="0d788-458">承認または却下と編集/削除は、データを所有します。</span><span class="sxs-lookup"><span data-stu-id="0d788-458">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="0d788-459">[はい]</span><span class="sxs-lookup"><span data-stu-id="0d788-459">Yes</span></span>               | <span data-ttu-id="0d788-460">承認または却下し、すべてのデータの編集/削除します。</span><span class="sxs-lookup"><span data-stu-id="0d788-460">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="0d788-461">管理者のブラウザーで連絡先を作成します。</span><span class="sxs-lookup"><span data-stu-id="0d788-461">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="0d788-462">削除の URL をコピーし、管理者の連絡先から管理者を編集します。</span><span class="sxs-lookup"><span data-stu-id="0d788-462">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="0d788-463">テスト ユーザーのブラウザー テスト ユーザーがこれらの操作を実行できないことを確認するには、これらのリンクを貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="0d788-463">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="0d788-464">スターター アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="0d788-464">Create the starter app</span></span>

* <span data-ttu-id="0d788-465">「ContactManager」という名前の Razor ページ アプリの作成</span><span class="sxs-lookup"><span data-stu-id="0d788-465">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="0d788-466">使用してアプリを作成する**個々 のユーザー アカウント**します。</span><span class="sxs-lookup"><span data-stu-id="0d788-466">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="0d788-467">ファイル名「ContactManager」名前空間サンプルで使用する名前空間が一致するようにします。</span><span class="sxs-lookup"><span data-stu-id="0d788-467">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="0d788-468">`-uld` SQLite の代わりに LocalDB を指定します</span><span class="sxs-lookup"><span data-stu-id="0d788-468">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```console
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="0d788-469">*モデル/Contact を追加します。*</span><span class="sxs-lookup"><span data-stu-id="0d788-469">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="0d788-470">スキャフォールディング、`Contact`モデル。</span><span class="sxs-lookup"><span data-stu-id="0d788-470">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="0d788-471">最初の移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="0d788-471">Create initial migration and update the database:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="0d788-472">更新プログラム、 **ContactManager**で固定、 *Pages/_Layout.cshtml*ファイル。</span><span class="sxs-lookup"><span data-stu-id="0d788-472">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="0d788-473">作成、編集、および連絡先を削除して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="0d788-473">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="0d788-474">データベースのシード</span><span class="sxs-lookup"><span data-stu-id="0d788-474">Seed the database</span></span>

<span data-ttu-id="0d788-475">追加、 [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs)クラスを*データ*フォルダー。</span><span class="sxs-lookup"><span data-stu-id="0d788-475">Add the [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="0d788-476">呼び出す`SeedData.Initialize`から`Main`:</span><span class="sxs-lookup"><span data-stu-id="0d788-476">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="0d788-477">アプリが、データベースをシード処理をテストします。</span><span class="sxs-lookup"><span data-stu-id="0d788-477">Test that the app seeded the database.</span></span> <span data-ttu-id="0d788-478">DB の連絡先に行がある場合は、seed メソッドは実行されません。</span><span class="sxs-lookup"><span data-stu-id="0d788-478">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="0d788-479">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="0d788-479">Additional resources</span></span>

* [<span data-ttu-id="0d788-480">Azure App Service で .NET Core および SQL Database web アプリを構築します。</span><span class="sxs-lookup"><span data-stu-id="0d788-480">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="0d788-481">[ASP.NET Core 承認ラボ](https://github.com/blowdart/AspNetAuthorizationWorkshop)します。</span><span class="sxs-lookup"><span data-stu-id="0d788-481">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="0d788-482">このラボでは、このチュートリアルで導入されたセキュリティ機能の詳細に移動します。</span><span class="sxs-lookup"><span data-stu-id="0d788-482">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="0d788-483">カスタム ポリシー ベースの承認</span><span class="sxs-lookup"><span data-stu-id="0d788-483">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
