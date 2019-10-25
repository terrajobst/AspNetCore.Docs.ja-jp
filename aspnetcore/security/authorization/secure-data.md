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
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="0a2f4-104">承認によって保護されたユーザーデータを含む ASP.NET Core アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="0a2f4-104">Create an ASP.NET Core app with user data protected by authorization</span></span>

<span data-ttu-id="0a2f4-105">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) および [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="0a2f4-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="0a2f4-106">ASP.NET Core MVC バージョンについては、[この PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-106">See [this PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core MVC version.</span></span> <span data-ttu-id="0a2f4-107">このチュートリアルの ASP.NET Core 1.1 バージョンは、[この](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data)フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-107">The ASP.NET Core 1.1 version of this tutorial is in [this](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) folder.</span></span> <span data-ttu-id="0a2f4-108">1\.1 ASP.NET Core サンプルは、「」[のサンプルに](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2)含まれています。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-108">The 1.1 ASP.NET Core sample is in the [samples](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="0a2f4-109">[次の pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)を参照</span><span class="sxs-lookup"><span data-stu-id="0a2f4-109">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0a2f4-110">このチュートリアルでは、承認によって保護されたユーザーデータを使用して ASP.NET Core web アプリを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-110">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="0a2f4-111">認証済み (登録済み) のユーザーが作成した連絡先の一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-111">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="0a2f4-112">セキュリティグループには次の3つがあります。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-112">There are three security groups:</span></span>

* <span data-ttu-id="0a2f4-113">**登録されているユーザー**は、すべての承認済みデータを表示したり、自分のデータを編集または削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-113">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="0a2f4-114">**管理者**は、連絡先データを承認または拒否できます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-114">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="0a2f4-115">承認された連絡先だけがユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-115">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="0a2f4-116">**管理者**は、任意のデータを承認/拒否したり、編集/削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-116">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="0a2f4-117">このドキュメントの画像は、最新のテンプレートと完全には一致しません。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-117">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="0a2f4-118">次の図では、user Rick (`rick@example.com`) がサインインしています。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-118">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="0a2f4-119">Rick は、承認された連絡先を表示して**編集**/**削除**/、連絡先の新しいリンクを**作成**することのみができます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-119">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="0a2f4-120">Rick によって作成された最後のレコードにのみ、**編集**および**削除**のリンクが表示されます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-120">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="0a2f4-121">他のユーザーには、マネージャーまたは管理者が状態を "承認済み" に変更するまで、最後のレコードは表示されません。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-121">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Rick がサインインしていることを示すスクリーンショット](secure-data/_static/rick.png)

<span data-ttu-id="0a2f4-123">次の図では、`manager@contoso.com` がサインインし、マネージャーのロールに含まれています。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-123">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![サインイン manager@contoso.com を示すスクリーンショット](secure-data/_static/manager1.png)

<span data-ttu-id="0a2f4-125">次の図は、連絡先のマネージャーの詳細ビューを示しています。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-125">The following image shows the managers details view of a contact:</span></span>

![連絡先のマネージャーの表示](secure-data/_static/manager.png)

<span data-ttu-id="0a2f4-127">**[承認]** ボタンと **[却下]** ボタンは、マネージャーと管理者に対してのみ表示されます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-127">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="0a2f4-128">次の図では、`admin@contoso.com` がサインインし、管理者のロールに含まれています。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-128">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![サインイン admin@contoso.com を示すスクリーンショット](secure-data/_static/admin.png)

<span data-ttu-id="0a2f4-130">管理者には、すべての特権があります。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-130">The administrator has all privileges.</span></span> <span data-ttu-id="0a2f4-131">連絡先の読み取り、編集、削除、および連絡先の状態の変更を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-131">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="0a2f4-132">アプリは、次の `Contact` モデルを[スキャフォールディング](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model)することによって作成されました。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-132">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="0a2f4-133">このサンプルには、次の承認ハンドラーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-133">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="0a2f4-134">`ContactIsOwnerAuthorizationHandler`: ユーザーがデータを編集できるようになります。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-134">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="0a2f4-135">`ContactManagerAuthorizationHandler`: 管理者が連絡先を承認または拒否できるようにします。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-135">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="0a2f4-136">`ContactAdministratorsAuthorizationHandler`: 管理者は、連絡先を承認または拒否したり、連絡先を編集または削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-136">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0a2f4-137">必要条件</span><span class="sxs-lookup"><span data-stu-id="0a2f4-137">Prerequisites</span></span>

<span data-ttu-id="0a2f4-138">このチュートリアルは高度です。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-138">This tutorial is advanced.</span></span> <span data-ttu-id="0a2f4-139">次のことを理解している必要があります。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-139">You should be familiar with:</span></span>

* [<span data-ttu-id="0a2f4-140">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0a2f4-140">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="0a2f4-141">認証</span><span class="sxs-lookup"><span data-stu-id="0a2f4-141">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="0a2f4-142">アカウントの確認とパスワードの回復</span><span class="sxs-lookup"><span data-stu-id="0a2f4-142">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="0a2f4-143">承認</span><span class="sxs-lookup"><span data-stu-id="0a2f4-143">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="0a2f4-144">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="0a2f4-144">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="0a2f4-145">スターターおよび完成したアプリ</span><span class="sxs-lookup"><span data-stu-id="0a2f4-145">The starter and completed app</span></span>

<span data-ttu-id="0a2f4-146">完成し[た](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples)アプリを[ダウンロード](xref:index#how-to-download-a-sample)します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-146">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="0a2f4-147">完成したアプリを[テスト](#test-the-completed-app)して、セキュリティ機能に慣れるようにします。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-147">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="0a2f4-148">スターターアプリ</span><span class="sxs-lookup"><span data-stu-id="0a2f4-148">The starter app</span></span>

<span data-ttu-id="0a2f4-149">[スターター](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/)アプリを[ダウンロード](xref:index#how-to-download-a-sample)します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-149">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="0a2f4-150">アプリを実行し、 **[Contactmanager]** リンクをタップして、連絡先を作成、編集、および削除できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-150">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="0a2f4-151">ユーザーデータのセキュリティ保護</span><span class="sxs-lookup"><span data-stu-id="0a2f4-151">Secure user data</span></span>

<span data-ttu-id="0a2f4-152">以下のセクションでは、セキュリティで保護されたユーザーデータアプリを作成するための主要な手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-152">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="0a2f4-153">完成したプロジェクトを参照した方が便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-153">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="0a2f4-154">連絡先データをユーザーに関連付ける</span><span class="sxs-lookup"><span data-stu-id="0a2f4-154">Tie the contact data to the user</span></span>

<span data-ttu-id="0a2f4-155">ASP.NET [id](xref:security/authentication/identity)ユーザー id を使用すると、ユーザーがデータを編集できるようになりますが、他のユーザーデータは編集できません。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-155">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="0a2f4-156">`OwnerID` と `ContactStatus` を `Contact` モデルに追加します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-156">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="0a2f4-157">`OwnerID` は、 [id](xref:security/authentication/identity)データベースの `AspNetUser` テーブルのユーザー ID です。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-157">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="0a2f4-158">[`Status`] フィールドによって、連絡先が一般ユーザーに表示されるかどうかが決まります。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-158">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="0a2f4-159">新しい移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-159">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="0a2f4-160">Id への役割サービスの追加</span><span class="sxs-lookup"><span data-stu-id="0a2f4-160">Add Role services to Identity</span></span>

<span data-ttu-id="0a2f4-161">役割サービスを追加するには、 [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1)を追加します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-161">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a><span data-ttu-id="0a2f4-162">認証されたユーザーが必要</span><span class="sxs-lookup"><span data-stu-id="0a2f4-162">Require authenticated users</span></span>

<span data-ttu-id="0a2f4-163">ユーザーが認証されるようにするには、既定の認証ポリシーを設定します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-163">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 <span data-ttu-id="0a2f4-164">Razor ページ、コントローラー、またはアクションメソッドレベルで、`[AllowAnonymous]` 属性を使用して認証をオプトアウトできます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-164">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="0a2f4-165">既定の認証ポリシーを設定すると、ユーザーの認証が必要になり、新しく追加された Razor Pages とコントローラーは保護されます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-165">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="0a2f4-166">既定で認証が必要になるのは、新しいコントローラーに依存していて、Razor Pages `[Authorize]` 属性を含めるよりも安全です。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-166">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="0a2f4-167">匿名ユーザーが登録前にサイトに関する情報を取得できるように、 [Allowanonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute)をインデックスおよびプライバシーページに追加します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-167">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index and Privacy pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="0a2f4-168">テストアカウントを構成する</span><span class="sxs-lookup"><span data-stu-id="0a2f4-168">Configure the test account</span></span>

<span data-ttu-id="0a2f4-169">`SeedData` クラスは、管理者とマネージャーの2つのアカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-169">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="0a2f4-170">これらのアカウントのパスワードを設定するには、[シークレットマネージャーツール](xref:security/app-secrets)を使用します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-170">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="0a2f4-171">プロジェクトディレクトリ ( *Program.cs*を含むディレクトリ) のパスワードを設定します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-171">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="0a2f4-172">強力なパスワードが指定されていない場合、`SeedData.Initialize` が呼び出されると例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-172">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="0a2f4-173">テストパスワードを使用するように `Main` を更新します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-173">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="0a2f4-174">テストアカウントを作成し、連絡先を更新する</span><span class="sxs-lookup"><span data-stu-id="0a2f4-174">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="0a2f4-175">`SeedData` クラスの `Initialize` メソッドを更新して、テストアカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-175">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="0a2f4-176">管理者のユーザー ID と `ContactStatus` を連絡先に追加します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-176">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="0a2f4-177">いずれかの連絡先を "送信済み" にして、"拒否" するようにします。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-177">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="0a2f4-178">すべての連絡先にユーザー ID と状態を追加します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-178">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="0a2f4-179">1つの連絡先のみが表示されます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-179">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="0a2f4-180">所有者、マネージャー、および管理者の承認ハンドラーを作成する</span><span class="sxs-lookup"><span data-stu-id="0a2f4-180">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="0a2f4-181">*承認*フォルダーに `ContactIsOwnerAuthorizationHandler` クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-181">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="0a2f4-182">`ContactIsOwnerAuthorizationHandler` は、リソースに対して動作しているユーザーがリソースを所有していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-182">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="0a2f4-183">`ContactIsOwnerAuthorizationHandler` は[コンテキストを呼び出します。](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)現在の認証済みユーザーが連絡先の所有者である場合は成功します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-183">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="0a2f4-184">通常、承認ハンドラーは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-184">Authorization handlers generally:</span></span>

* <span data-ttu-id="0a2f4-185">要件が満たされた場合に `context.Succeed` を返します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-185">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="0a2f4-186">要件を満たしていない場合は `Task.CompletedTask` を返します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-186">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="0a2f4-187">`Task.CompletedTask` は成功でも失敗でも&mdash;他の承認ハンドラーの実行を許可します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-187">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="0a2f4-188">明示的に失敗する必要がある場合は、コンテキストを返し[ます。失敗](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-188">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="0a2f4-189">アプリを使用すると、連絡先所有者は自分のデータを編集/削除/作成できます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-189">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="0a2f4-190">`ContactIsOwnerAuthorizationHandler` は、要件パラメーターで渡された操作を確認する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-190">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="0a2f4-191">マネージャーの承認ハンドラーを作成する</span><span class="sxs-lookup"><span data-stu-id="0a2f4-191">Create a manager authorization handler</span></span>

<span data-ttu-id="0a2f4-192">*承認*フォルダーに `ContactManagerAuthorizationHandler` クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-192">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="0a2f4-193">`ContactManagerAuthorizationHandler` は、リソースに対して動作しているユーザーがマネージャーであることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-193">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="0a2f4-194">コンテンツの変更を承認または拒否できるのは、管理者のみです (新規または変更)。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-194">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="0a2f4-195">管理者の承認ハンドラーを作成する</span><span class="sxs-lookup"><span data-stu-id="0a2f4-195">Create an administrator authorization handler</span></span>

<span data-ttu-id="0a2f4-196">*承認*フォルダーに `ContactAdministratorsAuthorizationHandler` クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-196">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="0a2f4-197">`ContactAdministratorsAuthorizationHandler` は、リソースに対して動作しているユーザーが管理者であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-197">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="0a2f4-198">管理者は、すべての操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-198">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="0a2f4-199">認証ハンドラーを登録する</span><span class="sxs-lookup"><span data-stu-id="0a2f4-199">Register the authorization handlers</span></span>

<span data-ttu-id="0a2f4-200">Entity Framework Core を使用するサービスは、 [Addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)を使用して[依存関係の挿入](xref:fundamentals/dependency-injection)に登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-200">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="0a2f4-201">`ContactIsOwnerAuthorizationHandler` は Entity Framework Core 上に構築された ASP.NET Core [id](xref:security/authentication/identity)を使用します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-201">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="0a2f4-202">サービスコレクションにハンドラーを登録して、[依存関係の挿入](xref:fundamentals/dependency-injection)によって `ContactsController` で使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-202">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0a2f4-203">`ConfigureServices`の末尾に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-203">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="0a2f4-204">`ContactAdministratorsAuthorizationHandler` と `ContactManagerAuthorizationHandler` はシングルトンとして追加されます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-204">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="0a2f4-205">EF を使用せず、必要なすべての情報が `HandleRequirementAsync` メソッドの `Context` パラメーターに含まれているため、これらはシングルトンです。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-205">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="0a2f4-206">認証のサポート</span><span class="sxs-lookup"><span data-stu-id="0a2f4-206">Support authorization</span></span>

<span data-ttu-id="0a2f4-207">このセクションでは、Razor Pages を更新し、操作要件クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-207">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="0a2f4-208">Contact operation の要件クラスを確認する</span><span class="sxs-lookup"><span data-stu-id="0a2f4-208">Review the contact operations requirements class</span></span>

<span data-ttu-id="0a2f4-209">`ContactOperations` クラスを確認します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-209">Review the `ContactOperations` class.</span></span> <span data-ttu-id="0a2f4-210">このクラスには、アプリがサポートする要件が含まれています。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-210">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="0a2f4-211">連絡先の基本クラスを作成 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="0a2f4-211">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="0a2f4-212">連絡先 Razor Pages で使用されるサービスを含む基本クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-212">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="0a2f4-213">基本クラスは、初期化コードを1つの場所に配置します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-213">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="0a2f4-214">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-214">The preceding code:</span></span>

* <span data-ttu-id="0a2f4-215">承認ハンドラーにアクセスするための `IAuthorizationService` サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-215">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="0a2f4-216">Id `UserManager` サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-216">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="0a2f4-217">`ApplicationDbContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-217">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="0a2f4-218">CreateModel を更新する</span><span class="sxs-lookup"><span data-stu-id="0a2f4-218">Update the CreateModel</span></span>

<span data-ttu-id="0a2f4-219">Create page model コンストラクターを更新して、`DI_BasePageModel` 基底クラスを使用するようにします。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-219">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="0a2f4-220">`CreateModel.OnPostAsync` メソッドを次のように更新します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-220">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="0a2f4-221">`Contact` モデルにユーザー ID を追加します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-221">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="0a2f4-222">承認ハンドラーを呼び出して、ユーザーが連絡先を作成するアクセス許可を持っていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-222">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="0a2f4-223">IndexModel を更新する</span><span class="sxs-lookup"><span data-stu-id="0a2f4-223">Update the IndexModel</span></span>

<span data-ttu-id="0a2f4-224">`OnGetAsync` メソッドを更新して、承認された連絡先だけが一般ユーザーに表示されるようにします。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-224">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="0a2f4-225">EditModel を更新する</span><span class="sxs-lookup"><span data-stu-id="0a2f4-225">Update the EditModel</span></span>

<span data-ttu-id="0a2f4-226">ユーザーが連絡先を所有していることを確認するための承認ハンドラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-226">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="0a2f4-227">リソース承認が検証されているため、`[Authorize]` 属性では不十分です。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-227">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="0a2f4-228">属性が評価された場合、アプリにはリソースへのアクセス権がありません。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-228">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="0a2f4-229">リソースベースの承認は、必須である必要があります。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-229">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="0a2f4-230">アプリケーションがリソースにアクセスできるようになったら、そのアプリをページモデルに読み込むか、ハンドラー自体に読み込むことによって、チェックを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-230">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="0a2f4-231">リソースキーを渡すことによって、リソースに頻繁にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-231">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="0a2f4-232">DeleteModel を更新する</span><span class="sxs-lookup"><span data-stu-id="0a2f4-232">Update the DeleteModel</span></span>

<span data-ttu-id="0a2f4-233">承認ハンドラーを使用するように delete ページモデルを更新して、ユーザーが連絡先に対する delete 権限を持っていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-233">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="0a2f4-234">ビューに承認サービスを挿入する</span><span class="sxs-lookup"><span data-stu-id="0a2f4-234">Inject the authorization service into the views</span></span>

<span data-ttu-id="0a2f4-235">現在、UI には、ユーザーが変更できない連絡先の編集と削除のリンクが表示されています。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-235">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="0a2f4-236">*Pages/_ViewImports*ファイルに承認サービスを挿入して、すべてのビューで使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-236">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="0a2f4-237">上記のマークアップでは、いくつかの `using` ステートメントが追加されます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-237">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="0a2f4-238">*Pages/Contacts/Index. cshtml*の**編集**と**削除**のリンクを更新して、適切なアクセス許可を持つユーザーにのみ表示されるようにします。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-238">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="0a2f4-239">データを変更するアクセス許可がないユーザーからのリンクを非表示にしても、アプリはセキュリティで保護されません。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-239">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="0a2f4-240">リンクを非表示にすると、有効なリンクのみが表示されるため、アプリのユーザーがわかりやすくなります。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-240">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="0a2f4-241">ユーザーは、生成された Url をハッキングして、所有していないデータに対する編集操作と削除操作を呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-241">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="0a2f4-242">Razor ページまたはコントローラーは、データをセキュリティで保護するためにアクセスチェックを強制する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-242">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="0a2f4-243">更新プログラムの詳細</span><span class="sxs-lookup"><span data-stu-id="0a2f4-243">Update Details</span></span>

<span data-ttu-id="0a2f4-244">上司が連絡先を承認または拒否できるように、詳細ビューを更新します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-244">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="0a2f4-245">詳細ページモデルを更新します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-245">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="0a2f4-246">ロールに対するユーザーの追加または削除</span><span class="sxs-lookup"><span data-stu-id="0a2f4-246">Add or remove a user to a role</span></span>

<span data-ttu-id="0a2f4-247">次の情報については、[この問題](https://github.com/aspnet/AspNetCore.Docs/issues/8502)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-247">See [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="0a2f4-248">ユーザーから特権を削除しています。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-248">Removing privileges from a user.</span></span> <span data-ttu-id="0a2f4-249">たとえば、チャットアプリでユーザーのミュートを行います。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-249">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="0a2f4-250">ユーザーへの特権の追加。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-250">Adding privileges to a user.</span></span>

## <a name="differences-between-challenge-vs-forbid"></a><span data-ttu-id="0a2f4-251">チャレンジと禁止の違い</span><span class="sxs-lookup"><span data-stu-id="0a2f4-251">Differences between Challenge vs Forbid</span></span>

<span data-ttu-id="0a2f4-252">このアプリでは、認証された[ユーザーを要求](#require-authenticated-users)する既定のポリシーを設定します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-252">This app sets the default policy to [require authenticated users](#require-authenticated-users).</span></span> <span data-ttu-id="0a2f4-253">次のコードでは、匿名ユーザーを許可します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-253">The following code allows anonymous users.</span></span> <span data-ttu-id="0a2f4-254">匿名ユーザーは、チャレンジと禁止の違いを示すことができます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-254">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="0a2f4-255">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-255">In the preceding code:</span></span>

* <span data-ttu-id="0a2f4-256">ユーザーが認証**されていない**場合は、`ChallengeResult` が返されます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-256">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="0a2f4-257">`ChallengeResult` が返されると、ユーザーはサインインページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-257">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="0a2f4-258">ユーザーが認証されているが承認されていない場合、`ForbidResult` が返されます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-258">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="0a2f4-259">`ForbidResult` が返されると、ユーザーは [アクセス拒否] ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-259">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="0a2f4-260">完成したアプリをテストする</span><span class="sxs-lookup"><span data-stu-id="0a2f4-260">Test the completed app</span></span>

<span data-ttu-id="0a2f4-261">シードされたユーザーアカウントのパスワードをまだ設定していない場合は、[シークレットマネージャーツール](xref:security/app-secrets#secret-manager)を使用してパスワードを設定します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-261">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="0a2f4-262">強力なパスワードを選択する: 8 個以上の文字と、少なくとも1つの大文字、数字、および記号を使用します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-262">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="0a2f4-263">たとえば、`Passw0rd!` は強力なパスワードの要件を満たしています。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-263">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="0a2f4-264">プロジェクトのフォルダーから次のコマンドを実行します。 `<PW>` はパスワードです。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-264">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="0a2f4-265">アプリに連絡先がある場合:</span><span class="sxs-lookup"><span data-stu-id="0a2f4-265">If the app has contacts:</span></span>

* <span data-ttu-id="0a2f4-266">`Contact` テーブル内のすべてのレコードを削除します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-266">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="0a2f4-267">アプリケーションを再起動してデータベースをシード処理します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-267">Restart the app to seed the database.</span></span>

<span data-ttu-id="0a2f4-268">完成したアプリをテストする簡単な方法は、3つの異なるブラウザー (または incognito/InPrivate セッション) を起動することです。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-268">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="0a2f4-269">1つのブラウザーで、新しいユーザーを登録します (たとえば、`test@example.com`)。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-269">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="0a2f4-270">別のユーザーを使用して各ブラウザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-270">Sign in to each browser with a different user.</span></span> <span data-ttu-id="0a2f4-271">次の操作を確認します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-271">Verify the following operations:</span></span>

* <span data-ttu-id="0a2f4-272">登録済みのユーザーは、すべての承認済み連絡先データを表示できます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-272">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="0a2f4-273">登録されているユーザーは、自分のデータを編集または削除できます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-273">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="0a2f4-274">マネージャーは、連絡先データを承認/拒否することができます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-274">Managers can approve/reject contact data.</span></span> <span data-ttu-id="0a2f4-275">`Details` ビューには、 **[承認]** ボタンと **[却下]** ボタンが表示されます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-275">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="0a2f4-276">管理者は、すべてのデータを承認/拒否し、編集/削除することができます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-276">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="0a2f4-277">ユーザー</span><span class="sxs-lookup"><span data-stu-id="0a2f4-277">User</span></span>                | <span data-ttu-id="0a2f4-278">アプリによるシード処理</span><span class="sxs-lookup"><span data-stu-id="0a2f4-278">Seeded by the app</span></span> | <span data-ttu-id="0a2f4-279">オプション</span><span class="sxs-lookup"><span data-stu-id="0a2f4-279">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="0a2f4-280">Ｘ</span><span class="sxs-lookup"><span data-stu-id="0a2f4-280">No</span></span>                | <span data-ttu-id="0a2f4-281">独自のデータを編集または削除します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-281">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="0a2f4-282">[はい]</span><span class="sxs-lookup"><span data-stu-id="0a2f4-282">Yes</span></span>               | <span data-ttu-id="0a2f4-283">自分のデータを承認/拒否し、編集/削除します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-283">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="0a2f4-284">[はい]</span><span class="sxs-lookup"><span data-stu-id="0a2f4-284">Yes</span></span>               | <span data-ttu-id="0a2f4-285">すべてのデータを承認/拒否し、編集/削除します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-285">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="0a2f4-286">管理者のブラウザーで連絡先を作成します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-286">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="0a2f4-287">管理者の連絡先から削除と編集のための URL をコピーします。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-287">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="0a2f4-288">これらのリンクをテストユーザーのブラウザーに貼り付けて、テストユーザーがこれらの操作を実行できないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-288">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="0a2f4-289">スターターアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="0a2f4-289">Create the starter app</span></span>

* <span data-ttu-id="0a2f4-290">"ContactManager" という名前の Razor Pages アプリを作成します</span><span class="sxs-lookup"><span data-stu-id="0a2f4-290">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="0a2f4-291">**個々のユーザーアカウント**を使用してアプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-291">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="0a2f4-292">名前空間がサンプルで使用される名前空間と一致するように、"ContactManager" という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-292">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="0a2f4-293">`-uld` は SQLite ではなく LocalDB を指定します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-293">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="0a2f4-294">*モデル/Contact を追加します。*</span><span class="sxs-lookup"><span data-stu-id="0a2f4-294">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="0a2f4-295">`Contact` モデルをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-295">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="0a2f4-296">初期移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-296">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="0a2f4-297">`dotnet aspnet-codegenerator razorpage` コマンドでバグが発生した場合は、 [GitHub の問題](https://github.com/aspnet/Scaffolding/issues/984)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-297">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="0a2f4-298">*Pages/Shared/Layout*ファイルで**contactmanager**アンカーを更新します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-298">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="0a2f4-299">連絡先の作成、編集、および削除によるアプリのテスト</span><span class="sxs-lookup"><span data-stu-id="0a2f4-299">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="0a2f4-300">データベースのシード</span><span class="sxs-lookup"><span data-stu-id="0a2f4-300">Seed the database</span></span>

<span data-ttu-id="0a2f4-301">[SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs)クラスを*Data*フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-301">Add the [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="0a2f4-302">`Main`から `SeedData.Initialize` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-302">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="0a2f4-303">アプリによってデータベースがシード処理されたことをテストします。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-303">Test that the app seeded the database.</span></span> <span data-ttu-id="0a2f4-304">Contact DB に行がある場合、seed メソッドは実行されません。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-304">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="0a2f4-305">このチュートリアルでは、承認によって保護されたユーザーデータを使用して ASP.NET Core web アプリを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-305">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="0a2f4-306">認証済み (登録済み) のユーザーが作成した連絡先の一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-306">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="0a2f4-307">セキュリティグループには次の3つがあります。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-307">There are three security groups:</span></span>

* <span data-ttu-id="0a2f4-308">**登録されているユーザー**は、すべての承認済みデータを表示したり、自分のデータを編集または削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-308">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="0a2f4-309">**管理者**は、連絡先データを承認または拒否できます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-309">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="0a2f4-310">承認された連絡先だけがユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-310">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="0a2f4-311">**管理者**は、任意のデータを承認/拒否したり、編集/削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-311">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="0a2f4-312">次の図では、user Rick (`rick@example.com`) がサインインしています。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-312">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="0a2f4-313">Rick は、承認された連絡先を表示して**編集**/**削除**/、連絡先の新しいリンクを**作成**することのみができます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-313">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="0a2f4-314">Rick によって作成された最後のレコードにのみ、**編集**および**削除**のリンクが表示されます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-314">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="0a2f4-315">他のユーザーには、マネージャーまたは管理者が状態を "承認済み" に変更するまで、最後のレコードは表示されません。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-315">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Rick がサインインしていることを示すスクリーンショット](secure-data/_static/rick.png)

<span data-ttu-id="0a2f4-317">次の図では、`manager@contoso.com` がサインインし、マネージャーのロールに含まれています。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-317">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![サインイン manager@contoso.com を示すスクリーンショット](secure-data/_static/manager1.png)

<span data-ttu-id="0a2f4-319">次の図は、連絡先のマネージャーの詳細ビューを示しています。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-319">The following image shows the managers details view of a contact:</span></span>

![連絡先のマネージャーの表示](secure-data/_static/manager.png)

<span data-ttu-id="0a2f4-321">**[承認]** ボタンと **[却下]** ボタンは、マネージャーと管理者に対してのみ表示されます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-321">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="0a2f4-322">次の図では、`admin@contoso.com` がサインインし、管理者のロールに含まれています。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-322">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![サインイン admin@contoso.com を示すスクリーンショット](secure-data/_static/admin.png)

<span data-ttu-id="0a2f4-324">管理者には、すべての特権があります。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-324">The administrator has all privileges.</span></span> <span data-ttu-id="0a2f4-325">連絡先の読み取り、編集、削除、および連絡先の状態の変更を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-325">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="0a2f4-326">アプリは、次の `Contact` モデルを[スキャフォールディング](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model)することによって作成されました。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-326">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="0a2f4-327">このサンプルには、次の承認ハンドラーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-327">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="0a2f4-328">`ContactIsOwnerAuthorizationHandler`: ユーザーがデータを編集できるようになります。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-328">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="0a2f4-329">`ContactManagerAuthorizationHandler`: 管理者が連絡先を承認または拒否できるようにします。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-329">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="0a2f4-330">`ContactAdministratorsAuthorizationHandler`: 管理者は、連絡先を承認または拒否したり、連絡先を編集または削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-330">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0a2f4-331">必要条件</span><span class="sxs-lookup"><span data-stu-id="0a2f4-331">Prerequisites</span></span>

<span data-ttu-id="0a2f4-332">このチュートリアルは高度です。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-332">This tutorial is advanced.</span></span> <span data-ttu-id="0a2f4-333">次のことを理解している必要があります。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-333">You should be familiar with:</span></span>

* [<span data-ttu-id="0a2f4-334">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0a2f4-334">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="0a2f4-335">認証</span><span class="sxs-lookup"><span data-stu-id="0a2f4-335">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="0a2f4-336">アカウントの確認とパスワードの回復</span><span class="sxs-lookup"><span data-stu-id="0a2f4-336">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="0a2f4-337">承認</span><span class="sxs-lookup"><span data-stu-id="0a2f4-337">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="0a2f4-338">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="0a2f4-338">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="0a2f4-339">スターターおよび完成したアプリ</span><span class="sxs-lookup"><span data-stu-id="0a2f4-339">The starter and completed app</span></span>

<span data-ttu-id="0a2f4-340">完成し[た](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples)アプリを[ダウンロード](xref:index#how-to-download-a-sample)します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-340">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="0a2f4-341">完成したアプリを[テスト](#test-the-completed-app)して、セキュリティ機能に慣れるようにします。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-341">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="0a2f4-342">スターターアプリ</span><span class="sxs-lookup"><span data-stu-id="0a2f4-342">The starter app</span></span>

<span data-ttu-id="0a2f4-343">[スターター](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/)アプリを[ダウンロード](xref:index#how-to-download-a-sample)します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-343">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="0a2f4-344">アプリを実行し、 **[Contactmanager]** リンクをタップして、連絡先を作成、編集、および削除できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-344">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="0a2f4-345">ユーザーデータのセキュリティ保護</span><span class="sxs-lookup"><span data-stu-id="0a2f4-345">Secure user data</span></span>

<span data-ttu-id="0a2f4-346">以下のセクションでは、セキュリティで保護されたユーザーデータアプリを作成するための主要な手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-346">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="0a2f4-347">完成したプロジェクトを参照した方が便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-347">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="0a2f4-348">連絡先データをユーザーに関連付ける</span><span class="sxs-lookup"><span data-stu-id="0a2f4-348">Tie the contact data to the user</span></span>

<span data-ttu-id="0a2f4-349">ASP.NET [id](xref:security/authentication/identity)ユーザー id を使用すると、ユーザーがデータを編集できるようになりますが、他のユーザーデータは編集できません。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-349">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="0a2f4-350">`OwnerID` と `ContactStatus` を `Contact` モデルに追加します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-350">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="0a2f4-351">`OwnerID` は、 [id](xref:security/authentication/identity)データベースの `AspNetUser` テーブルのユーザー ID です。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-351">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="0a2f4-352">[`Status`] フィールドによって、連絡先が一般ユーザーに表示されるかどうかが決まります。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-352">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="0a2f4-353">新しい移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-353">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="0a2f4-354">Id への役割サービスの追加</span><span class="sxs-lookup"><span data-stu-id="0a2f4-354">Add Role services to Identity</span></span>

<span data-ttu-id="0a2f4-355">役割サービスを追加するには、 [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1)を追加します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-355">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a><span data-ttu-id="0a2f4-356">認証されたユーザーが必要</span><span class="sxs-lookup"><span data-stu-id="0a2f4-356">Require authenticated users</span></span>

<span data-ttu-id="0a2f4-357">ユーザーが認証されるようにするには、既定の認証ポリシーを設定します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-357">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="0a2f4-358">Razor ページ、コントローラー、またはアクションメソッドレベルで、`[AllowAnonymous]` 属性を使用して認証をオプトアウトできます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-358">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="0a2f4-359">既定の認証ポリシーを設定すると、ユーザーの認証が必要になり、新しく追加された Razor Pages とコントローラーは保護されます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-359">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="0a2f4-360">既定で認証が必要になるのは、新しいコントローラーに依存していて、Razor Pages `[Authorize]` 属性を含めるよりも安全です。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-360">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="0a2f4-361">匿名ユーザーが登録前にサイトに関する情報を取得できるように、 [Allowanonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute)を Index、About、および Contact の各ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-361">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="0a2f4-362">テストアカウントを構成する</span><span class="sxs-lookup"><span data-stu-id="0a2f4-362">Configure the test account</span></span>

<span data-ttu-id="0a2f4-363">`SeedData` クラスは、管理者とマネージャーの2つのアカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-363">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="0a2f4-364">これらのアカウントのパスワードを設定するには、[シークレットマネージャーツール](xref:security/app-secrets)を使用します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-364">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="0a2f4-365">プロジェクトディレクトリ ( *Program.cs*を含むディレクトリ) のパスワードを設定します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-365">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="0a2f4-366">強力なパスワードが指定されていない場合、`SeedData.Initialize` が呼び出されると例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-366">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="0a2f4-367">テストパスワードを使用するように `Main` を更新します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-367">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="0a2f4-368">テストアカウントを作成し、連絡先を更新する</span><span class="sxs-lookup"><span data-stu-id="0a2f4-368">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="0a2f4-369">`SeedData` クラスの `Initialize` メソッドを更新して、テストアカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-369">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="0a2f4-370">管理者のユーザー ID と `ContactStatus` を連絡先に追加します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-370">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="0a2f4-371">いずれかの連絡先を "送信済み" にして、"拒否" するようにします。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-371">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="0a2f4-372">すべての連絡先にユーザー ID と状態を追加します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-372">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="0a2f4-373">1つの連絡先のみが表示されます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-373">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="0a2f4-374">所有者、マネージャー、および管理者の承認ハンドラーを作成する</span><span class="sxs-lookup"><span data-stu-id="0a2f4-374">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="0a2f4-375">*承認*フォルダーを作成し、その中に `ContactIsOwnerAuthorizationHandler` クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-375">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="0a2f4-376">`ContactIsOwnerAuthorizationHandler` は、リソースに対して動作しているユーザーがリソースを所有していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-376">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="0a2f4-377">`ContactIsOwnerAuthorizationHandler` は[コンテキストを呼び出します。](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)現在の認証済みユーザーが連絡先の所有者である場合は成功します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-377">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="0a2f4-378">通常、承認ハンドラーは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-378">Authorization handlers generally:</span></span>

* <span data-ttu-id="0a2f4-379">要件が満たされた場合に `context.Succeed` を返します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-379">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="0a2f4-380">要件を満たしていない場合は `Task.CompletedTask` を返します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-380">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="0a2f4-381">`Task.CompletedTask` は成功でも失敗でも&mdash;他の承認ハンドラーの実行を許可します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-381">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="0a2f4-382">明示的に失敗する必要がある場合は、コンテキストを返し[ます。失敗](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-382">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="0a2f4-383">アプリを使用すると、連絡先所有者は自分のデータを編集/削除/作成できます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-383">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="0a2f4-384">`ContactIsOwnerAuthorizationHandler` は、要件パラメーターで渡された操作を確認する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-384">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="0a2f4-385">マネージャーの承認ハンドラーを作成する</span><span class="sxs-lookup"><span data-stu-id="0a2f4-385">Create a manager authorization handler</span></span>

<span data-ttu-id="0a2f4-386">*承認*フォルダーに `ContactManagerAuthorizationHandler` クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-386">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="0a2f4-387">`ContactManagerAuthorizationHandler` は、リソースに対して動作しているユーザーがマネージャーであることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-387">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="0a2f4-388">コンテンツの変更を承認または拒否できるのは、管理者のみです (新規または変更)。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-388">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="0a2f4-389">管理者の承認ハンドラーを作成する</span><span class="sxs-lookup"><span data-stu-id="0a2f4-389">Create an administrator authorization handler</span></span>

<span data-ttu-id="0a2f4-390">*承認*フォルダーに `ContactAdministratorsAuthorizationHandler` クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-390">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="0a2f4-391">`ContactAdministratorsAuthorizationHandler` は、リソースに対して動作しているユーザーが管理者であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-391">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="0a2f4-392">管理者は、すべての操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-392">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="0a2f4-393">認証ハンドラーを登録する</span><span class="sxs-lookup"><span data-stu-id="0a2f4-393">Register the authorization handlers</span></span>

<span data-ttu-id="0a2f4-394">Entity Framework Core を使用するサービスは、 [Addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)を使用して[依存関係の挿入](xref:fundamentals/dependency-injection)に登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-394">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="0a2f4-395">`ContactIsOwnerAuthorizationHandler` は Entity Framework Core 上に構築された ASP.NET Core [id](xref:security/authentication/identity)を使用します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-395">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="0a2f4-396">サービスコレクションにハンドラーを登録して、[依存関係の挿入](xref:fundamentals/dependency-injection)によって `ContactsController` で使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-396">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0a2f4-397">`ConfigureServices`の末尾に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-397">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="0a2f4-398">`ContactAdministratorsAuthorizationHandler` と `ContactManagerAuthorizationHandler` はシングルトンとして追加されます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-398">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="0a2f4-399">EF を使用せず、必要なすべての情報が `HandleRequirementAsync` メソッドの `Context` パラメーターに含まれているため、これらはシングルトンです。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-399">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="0a2f4-400">認証のサポート</span><span class="sxs-lookup"><span data-stu-id="0a2f4-400">Support authorization</span></span>

<span data-ttu-id="0a2f4-401">このセクションでは、Razor Pages を更新し、操作要件クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-401">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="0a2f4-402">Contact operation の要件クラスを確認する</span><span class="sxs-lookup"><span data-stu-id="0a2f4-402">Review the contact operations requirements class</span></span>

<span data-ttu-id="0a2f4-403">`ContactOperations` クラスを確認します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-403">Review the `ContactOperations` class.</span></span> <span data-ttu-id="0a2f4-404">このクラスには、アプリがサポートする要件が含まれています。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-404">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="0a2f4-405">連絡先の基本クラスを作成 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="0a2f4-405">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="0a2f4-406">連絡先 Razor Pages で使用されるサービスを含む基本クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-406">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="0a2f4-407">基本クラスは、初期化コードを1つの場所に配置します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-407">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="0a2f4-408">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-408">The preceding code:</span></span>

* <span data-ttu-id="0a2f4-409">承認ハンドラーにアクセスするための `IAuthorizationService` サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-409">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="0a2f4-410">Id `UserManager` サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-410">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="0a2f4-411">`ApplicationDbContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-411">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="0a2f4-412">CreateModel を更新する</span><span class="sxs-lookup"><span data-stu-id="0a2f4-412">Update the CreateModel</span></span>

<span data-ttu-id="0a2f4-413">Create page model コンストラクターを更新して、`DI_BasePageModel` 基底クラスを使用するようにします。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-413">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="0a2f4-414">`CreateModel.OnPostAsync` メソッドを次のように更新します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-414">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="0a2f4-415">`Contact` モデルにユーザー ID を追加します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-415">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="0a2f4-416">承認ハンドラーを呼び出して、ユーザーが連絡先を作成するアクセス許可を持っていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-416">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="0a2f4-417">IndexModel を更新する</span><span class="sxs-lookup"><span data-stu-id="0a2f4-417">Update the IndexModel</span></span>

<span data-ttu-id="0a2f4-418">`OnGetAsync` メソッドを更新して、承認された連絡先だけが一般ユーザーに表示されるようにします。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-418">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="0a2f4-419">EditModel を更新する</span><span class="sxs-lookup"><span data-stu-id="0a2f4-419">Update the EditModel</span></span>

<span data-ttu-id="0a2f4-420">ユーザーが連絡先を所有していることを確認するための承認ハンドラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-420">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="0a2f4-421">リソース承認が検証されているため、`[Authorize]` 属性では不十分です。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-421">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="0a2f4-422">属性が評価された場合、アプリにはリソースへのアクセス権がありません。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-422">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="0a2f4-423">リソースベースの承認は、必須である必要があります。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-423">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="0a2f4-424">アプリケーションがリソースにアクセスできるようになったら、そのアプリをページモデルに読み込むか、ハンドラー自体に読み込むことによって、チェックを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-424">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="0a2f4-425">リソースキーを渡すことによって、リソースに頻繁にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-425">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="0a2f4-426">DeleteModel を更新する</span><span class="sxs-lookup"><span data-stu-id="0a2f4-426">Update the DeleteModel</span></span>

<span data-ttu-id="0a2f4-427">承認ハンドラーを使用するように delete ページモデルを更新して、ユーザーが連絡先に対する delete 権限を持っていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-427">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="0a2f4-428">ビューに承認サービスを挿入する</span><span class="sxs-lookup"><span data-stu-id="0a2f4-428">Inject the authorization service into the views</span></span>

<span data-ttu-id="0a2f4-429">現在、UI には、ユーザーが変更できない連絡先の編集と削除のリンクが表示されています。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-429">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="0a2f4-430">*Views/_ViewImports*ファイルに承認サービスを挿入して、すべてのビューで使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-430">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="0a2f4-431">上記のマークアップでは、いくつかの `using` ステートメントが追加されます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-431">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="0a2f4-432">*Pages/Contacts/Index. cshtml*の**編集**と**削除**のリンクを更新して、適切なアクセス許可を持つユーザーにのみ表示されるようにします。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-432">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="0a2f4-433">データを変更するアクセス許可がないユーザーからのリンクを非表示にしても、アプリはセキュリティで保護されません。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-433">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="0a2f4-434">リンクを非表示にすると、有効なリンクのみが表示されるため、アプリのユーザーがわかりやすくなります。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-434">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="0a2f4-435">ユーザーは、生成された Url をハッキングして、所有していないデータに対する編集操作と削除操作を呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-435">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="0a2f4-436">Razor ページまたはコントローラーは、データをセキュリティで保護するためにアクセスチェックを強制する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-436">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="0a2f4-437">更新プログラムの詳細</span><span class="sxs-lookup"><span data-stu-id="0a2f4-437">Update Details</span></span>

<span data-ttu-id="0a2f4-438">上司が連絡先を承認または拒否できるように、詳細ビューを更新します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-438">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="0a2f4-439">詳細ページモデルを更新します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-439">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="0a2f4-440">ロールに対するユーザーの追加または削除</span><span class="sxs-lookup"><span data-stu-id="0a2f4-440">Add or remove a user to a role</span></span>

<span data-ttu-id="0a2f4-441">次の情報については、[この問題](https://github.com/aspnet/AspNetCore.Docs/issues/8502)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-441">See [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="0a2f4-442">ユーザーから特権を削除しています。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-442">Removing privileges from a user.</span></span> <span data-ttu-id="0a2f4-443">たとえば、チャットアプリでユーザーのミュートを行います。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-443">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="0a2f4-444">ユーザーへの特権の追加。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-444">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="0a2f4-445">完成したアプリをテストする</span><span class="sxs-lookup"><span data-stu-id="0a2f4-445">Test the completed app</span></span>

<span data-ttu-id="0a2f4-446">シードされたユーザーアカウントのパスワードをまだ設定していない場合は、[シークレットマネージャーツール](xref:security/app-secrets#secret-manager)を使用してパスワードを設定します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-446">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="0a2f4-447">強力なパスワードを選択する: 8 個以上の文字と、少なくとも1つの大文字、数字、および記号を使用します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-447">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="0a2f4-448">たとえば、`Passw0rd!` は強力なパスワードの要件を満たしています。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-448">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="0a2f4-449">プロジェクトのフォルダーから次のコマンドを実行します。 `<PW>` はパスワードです。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-449">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="0a2f4-450">データベースを削除して更新する</span><span class="sxs-lookup"><span data-stu-id="0a2f4-450">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="0a2f4-451">アプリケーションを再起動してデータベースをシード処理します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-451">Restart the app to seed the database.</span></span>

<span data-ttu-id="0a2f4-452">完成したアプリをテストする簡単な方法は、3つの異なるブラウザー (または incognito/InPrivate セッション) を起動することです。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-452">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="0a2f4-453">1つのブラウザーで、新しいユーザーを登録します (たとえば、`test@example.com`)。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-453">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="0a2f4-454">別のユーザーを使用して各ブラウザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-454">Sign in to each browser with a different user.</span></span> <span data-ttu-id="0a2f4-455">次の操作を確認します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-455">Verify the following operations:</span></span>

* <span data-ttu-id="0a2f4-456">登録済みのユーザーは、すべての承認済み連絡先データを表示できます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-456">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="0a2f4-457">登録されているユーザーは、自分のデータを編集または削除できます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-457">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="0a2f4-458">マネージャーは、連絡先データを承認/拒否することができます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-458">Managers can approve/reject contact data.</span></span> <span data-ttu-id="0a2f4-459">`Details` ビューには、 **[承認]** ボタンと **[却下]** ボタンが表示されます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-459">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="0a2f4-460">管理者は、すべてのデータを承認/拒否し、編集/削除することができます。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-460">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="0a2f4-461">ユーザー</span><span class="sxs-lookup"><span data-stu-id="0a2f4-461">User</span></span>                | <span data-ttu-id="0a2f4-462">アプリによるシード処理</span><span class="sxs-lookup"><span data-stu-id="0a2f4-462">Seeded by the app</span></span> | <span data-ttu-id="0a2f4-463">オプション</span><span class="sxs-lookup"><span data-stu-id="0a2f4-463">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="0a2f4-464">Ｘ</span><span class="sxs-lookup"><span data-stu-id="0a2f4-464">No</span></span>                | <span data-ttu-id="0a2f4-465">独自のデータを編集または削除します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-465">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="0a2f4-466">[はい]</span><span class="sxs-lookup"><span data-stu-id="0a2f4-466">Yes</span></span>               | <span data-ttu-id="0a2f4-467">自分のデータを承認/拒否し、編集/削除します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-467">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="0a2f4-468">[はい]</span><span class="sxs-lookup"><span data-stu-id="0a2f4-468">Yes</span></span>               | <span data-ttu-id="0a2f4-469">すべてのデータを承認/拒否し、編集/削除します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-469">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="0a2f4-470">管理者のブラウザーで連絡先を作成します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-470">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="0a2f4-471">管理者の連絡先から削除と編集のための URL をコピーします。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-471">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="0a2f4-472">これらのリンクをテストユーザーのブラウザーに貼り付けて、テストユーザーがこれらの操作を実行できないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-472">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="0a2f4-473">スターターアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="0a2f4-473">Create the starter app</span></span>

* <span data-ttu-id="0a2f4-474">"ContactManager" という名前の Razor Pages アプリを作成します</span><span class="sxs-lookup"><span data-stu-id="0a2f4-474">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="0a2f4-475">**個々のユーザーアカウント**を使用してアプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-475">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="0a2f4-476">名前空間がサンプルで使用される名前空間と一致するように、"ContactManager" という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-476">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="0a2f4-477">`-uld` は SQLite ではなく LocalDB を指定します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-477">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="0a2f4-478">*モデル/Contact を追加します。*</span><span class="sxs-lookup"><span data-stu-id="0a2f4-478">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="0a2f4-479">`Contact` モデルをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-479">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="0a2f4-480">初期移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-480">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="0a2f4-481">*Pages/Layout*ファイルで**contactmanager**アンカーを更新します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-481">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="0a2f4-482">連絡先の作成、編集、および削除によるアプリのテスト</span><span class="sxs-lookup"><span data-stu-id="0a2f4-482">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="0a2f4-483">データベースのシード</span><span class="sxs-lookup"><span data-stu-id="0a2f4-483">Seed the database</span></span>

<span data-ttu-id="0a2f4-484">[SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs)クラスを*Data*フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-484">Add the [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="0a2f4-485">`Main`から `SeedData.Initialize` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-485">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="0a2f4-486">アプリによってデータベースがシード処理されたことをテストします。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-486">Test that the app seeded the database.</span></span> <span data-ttu-id="0a2f4-487">Contact DB に行がある場合、seed メソッドは実行されません。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-487">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="0a2f4-488">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="0a2f4-488">Additional resources</span></span>

* [<span data-ttu-id="0a2f4-489">Azure App Service で .NET Core と SQL Database web アプリを構築する</span><span class="sxs-lookup"><span data-stu-id="0a2f4-489">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="0a2f4-490">[ASP.NET Core の承認ラボ](https://github.com/blowdart/AspNetAuthorizationWorkshop)。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-490">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="0a2f4-491">このチュートリアルで紹介するセキュリティ機能の詳細については、このラボを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0a2f4-491">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="0a2f4-492">カスタム ポリシー ベースの承認</span><span class="sxs-lookup"><span data-stu-id="0a2f4-492">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
