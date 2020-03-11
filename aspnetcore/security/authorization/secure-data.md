---
title: 承認によって保護されたユーザー データと ASP.NET Core アプリを作成します。
author: rick-anderson
description: 承認によって保護されたユーザー データと Razor ページ アプリを作成する方法について説明します。 HTTPS、認証、セキュリティ、ASP.NET Core Identity が含まれています。
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
uid: security/authorization/secure-data
ms.openlocfilehash: 7710a8965771db02e601dafb7da752906bcd43e5
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651920"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="6b3eb-104">承認によって保護されたユーザー データと ASP.NET Core アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-104">Create an ASP.NET Core app with user data protected by authorization</span></span>

<span data-ttu-id="6b3eb-105">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) および [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="6b3eb-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="6b3eb-106">ASP.NET Core MVC バージョンについては、[この PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-106">See [this PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core MVC version.</span></span> <span data-ttu-id="6b3eb-107">このチュートリアルの ASP.NET Core 1.1 バージョンは、[この](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data)フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-107">The ASP.NET Core 1.1 version of this tutorial is in [this](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) folder.</span></span> <span data-ttu-id="6b3eb-108">1\.1 ASP.NET Core サンプルは、「」[のサンプルに](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2)含まれています。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-108">The 1.1 ASP.NET Core sample is in the [samples](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="6b3eb-109">[次の pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)を参照</span><span class="sxs-lookup"><span data-stu-id="6b3eb-109">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6b3eb-110">このチュートリアルでは、承認によって保護されたユーザー データと ASP.NET Core web アプリを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-110">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="6b3eb-111">認証済み (登録済み) のユーザーの連絡先の一覧を表示を作成します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-111">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="6b3eb-112">これには 3 つのセキュリティ グループがあります。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-112">There are three security groups:</span></span>

* <span data-ttu-id="6b3eb-113">**登録されているユーザー**は、すべての承認済みデータを表示したり、自分のデータを編集または削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-113">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="6b3eb-114">**管理者**は、連絡先データを承認または拒否できます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-114">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="6b3eb-115">承認されたメンバーのみがユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-115">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="6b3eb-116">**管理者**は、任意のデータを承認/拒否したり、編集/削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-116">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="6b3eb-117">このドキュメントの画像は、最新のテンプレートと完全には一致しません。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-117">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="6b3eb-118">次の図では、user Rick (`rick@example.com`) がサインインしています。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-118">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="6b3eb-119">Rick は、承認された連絡先を表示して**編集**/**削除**/、連絡先の新しいリンクを**作成**することのみができます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-119">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="6b3eb-120">Rick によって作成された最後のレコードにのみ、**編集**および**削除**のリンクが表示されます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-120">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="6b3eb-121">他のユーザーは、管理者は、状態を"Approved"に変わるまで、最後のレコードを表示されません。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-121">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![サインインして Rick を示すスクリーン ショット](secure-data/_static/rick.png)

<span data-ttu-id="6b3eb-123">次の図では、`manager@contoso.com` がサインインし、マネージャーのロールに含まれています。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-123">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![サインイン manager@contoso.com を示すスクリーンショット](secure-data/_static/manager1.png)

<span data-ttu-id="6b3eb-125">次の図は、連絡先の詳細ビューをマネージャーには。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-125">The following image shows the managers details view of a contact:</span></span>

![連絡先のマネージャーの表示](secure-data/_static/manager.png)

<span data-ttu-id="6b3eb-127">**[承認]** ボタンと **[却下]** ボタンは、マネージャーと管理者に対してのみ表示されます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-127">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="6b3eb-128">次の図では、`admin@contoso.com` がサインインし、管理者のロールに含まれています。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-128">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![サインイン admin@contoso.com を示すスクリーンショット](secure-data/_static/admin.png)

<span data-ttu-id="6b3eb-130">管理者は、すべての特権を持ちます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-130">The administrator has all privileges.</span></span> <span data-ttu-id="6b3eb-131">彼女は、読み取り/編集/削除のいずれかの連絡先をことができ、連絡先の状態を変更します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-131">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="6b3eb-132">アプリは、次の `Contact` モデルを[スキャフォールディング](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model)することによって作成されました。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-132">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="6b3eb-133">サンプルには、次の承認ハンドラーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-133">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="6b3eb-134">`ContactIsOwnerAuthorizationHandler`: ユーザーがデータを編集できるようになります。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-134">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="6b3eb-135">`ContactManagerAuthorizationHandler`: 管理者が連絡先を承認または拒否できるようにします。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-135">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="6b3eb-136">`ContactAdministratorsAuthorizationHandler`: 管理者は、連絡先を承認または拒否したり、連絡先を編集または削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-136">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6b3eb-137">前提条件</span><span class="sxs-lookup"><span data-stu-id="6b3eb-137">Prerequisites</span></span>

<span data-ttu-id="6b3eb-138">このチュートリアルは、高度なです。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-138">This tutorial is advanced.</span></span> <span data-ttu-id="6b3eb-139">理解しておく必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-139">You should be familiar with:</span></span>

* [<span data-ttu-id="6b3eb-140">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6b3eb-140">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="6b3eb-141">認証</span><span class="sxs-lookup"><span data-stu-id="6b3eb-141">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="6b3eb-142">アカウントの確認とパスワードの回復</span><span class="sxs-lookup"><span data-stu-id="6b3eb-142">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="6b3eb-143">承認</span><span class="sxs-lookup"><span data-stu-id="6b3eb-143">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="6b3eb-144">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="6b3eb-144">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="6b3eb-145">Starter および完成したアプリ</span><span class="sxs-lookup"><span data-stu-id="6b3eb-145">The starter and completed app</span></span>

<span data-ttu-id="6b3eb-146">完成し[た](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples)アプリを[ダウンロード](xref:index#how-to-download-a-sample)します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-146">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="6b3eb-147">完成したアプリを[テスト](#test-the-completed-app)して、セキュリティ機能に慣れるようにします。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-147">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="6b3eb-148">スターター アプリ</span><span class="sxs-lookup"><span data-stu-id="6b3eb-148">The starter app</span></span>

<span data-ttu-id="6b3eb-149">[スターター](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/)アプリを[ダウンロード](xref:index#how-to-download-a-sample)します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-149">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="6b3eb-150">アプリを実行し、 **[Contactmanager]** リンクをタップして、連絡先を作成、編集、および削除できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-150">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="6b3eb-151">セキュリティで保護されたユーザー データ</span><span class="sxs-lookup"><span data-stu-id="6b3eb-151">Secure user data</span></span>

<span data-ttu-id="6b3eb-152">次のセクションでは、セキュリティで保護されたユーザー データ アプリを作成するすべての主要な手順があります。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-152">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="6b3eb-153">完成したプロジェクトを参照すると便利です。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-153">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="6b3eb-154">ユーザーに連絡先データを関連付ける</span><span class="sxs-lookup"><span data-stu-id="6b3eb-154">Tie the contact data to the user</span></span>

<span data-ttu-id="6b3eb-155">ASP.NET [id](xref:security/authentication/identity)ユーザー id を使用すると、ユーザーがデータを編集できるようになりますが、他のユーザーデータは編集できません。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-155">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="6b3eb-156">`OwnerID` と `ContactStatus` を `Contact` モデルに追加します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-156">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="6b3eb-157">`OwnerID` は、 [id](xref:security/authentication/identity)データベースの `AspNetUser` テーブルのユーザー ID です。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-157">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="6b3eb-158">[`Status`] フィールドによって、連絡先が一般ユーザーに表示されるかどうかが決まります。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-158">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="6b3eb-159">新しい移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-159">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="6b3eb-160">役割サービスの Id を追加します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-160">Add Role services to Identity</span></span>

<span data-ttu-id="6b3eb-161">役割サービスを追加するには、 [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1)を追加します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-161">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a><span data-ttu-id="6b3eb-162">認証されたユーザーが必要です。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-162">Require authenticated users</span></span>

<span data-ttu-id="6b3eb-163">ユーザー認証を必要とする既定の認証ポリシーを設定します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-163">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 <span data-ttu-id="6b3eb-164">Razor ページ、コントローラー、またはアクションメソッドレベルで、`[AllowAnonymous]` 属性を使用して認証をオプトアウトできます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-164">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="6b3eb-165">ユーザー認証を必要とする既定の認証ポリシーの設定は、新しく追加された Razor ページとコント ローラーを保護します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-165">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="6b3eb-166">既定で認証が必要になるのは、新しいコントローラーに依存していて、Razor Pages `[Authorize]` 属性を含めるよりも安全です。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-166">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="6b3eb-167">匿名ユーザーが登録前にサイトに関する情報を取得できるように、 [Allowanonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute)をインデックスおよびプライバシーページに追加します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-167">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index and Privacy pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="6b3eb-168">テスト アカウントを構成します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-168">Configure the test account</span></span>

<span data-ttu-id="6b3eb-169">`SeedData` クラスは、管理者とマネージャーの2つのアカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-169">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="6b3eb-170">これらのアカウントのパスワードを設定するには、[シークレットマネージャーツール](xref:security/app-secrets)を使用します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-170">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="6b3eb-171">プロジェクトディレクトリ ( *Program.cs*を含むディレクトリ) のパスワードを設定します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-171">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="6b3eb-172">強力なパスワードが指定されていない場合、`SeedData.Initialize` が呼び出されると例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-172">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="6b3eb-173">テストパスワードを使用するように `Main` を更新します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-173">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="6b3eb-174">テスト アカウントを作成し、連絡先の更新</span><span class="sxs-lookup"><span data-stu-id="6b3eb-174">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="6b3eb-175">`SeedData` クラスの `Initialize` メソッドを更新して、テストアカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-175">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="6b3eb-176">管理者のユーザー ID と `ContactStatus` を連絡先に追加します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-176">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="6b3eb-177">「送信済み」と「拒否」の 1 つの連絡先を作成します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-177">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="6b3eb-178">すべての連絡先に、ユーザー ID と状態を追加します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-178">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="6b3eb-179">1 人だけが表示されます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-179">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="6b3eb-180">所有者、マネージャー、および管理者の承認ハンドラーを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-180">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="6b3eb-181">*承認*フォルダーに `ContactIsOwnerAuthorizationHandler` クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-181">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="6b3eb-182">`ContactIsOwnerAuthorizationHandler` は、リソースに対して動作しているユーザーがリソースを所有していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-182">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="6b3eb-183">`ContactIsOwnerAuthorizationHandler` は[コンテキストを呼び出します。](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)現在の認証済みユーザーが連絡先の所有者である場合は成功します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-183">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="6b3eb-184">承認ハンドラー通常。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-184">Authorization handlers generally:</span></span>

* <span data-ttu-id="6b3eb-185">要件が満たされた場合に `context.Succeed` を返します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-185">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="6b3eb-186">要件を満たしていない場合は `Task.CompletedTask` を返します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-186">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="6b3eb-187">`Task.CompletedTask` は成功でも失敗でも&mdash;他の承認ハンドラーの実行を許可します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-187">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="6b3eb-188">明示的に失敗する必要がある場合は、コンテキストを返し[ます。失敗](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-188">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="6b3eb-189">アプリは、独自のデータを編集/削除/作成所有者が連絡先にできます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-189">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="6b3eb-190">`ContactIsOwnerAuthorizationHandler` は、要件パラメーターで渡された操作を確認する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-190">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="6b3eb-191">マネージャーの承認ハンドラーを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-191">Create a manager authorization handler</span></span>

<span data-ttu-id="6b3eb-192">*承認*フォルダーに `ContactManagerAuthorizationHandler` クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-192">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="6b3eb-193">`ContactManagerAuthorizationHandler` は、リソースに対して動作しているユーザーがマネージャーであることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-193">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="6b3eb-194">管理者だけでは、承認したり、(新規または変更された) コンテンツの変更を拒否することができます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-194">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="6b3eb-195">管理者の承認ハンドラーを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-195">Create an administrator authorization handler</span></span>

<span data-ttu-id="6b3eb-196">*承認*フォルダーに `ContactAdministratorsAuthorizationHandler` クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-196">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="6b3eb-197">`ContactAdministratorsAuthorizationHandler` は、リソースに対して動作しているユーザーが管理者であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-197">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="6b3eb-198">管理者は、すべての操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-198">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="6b3eb-199">承認ハンドラーを登録します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-199">Register the authorization handlers</span></span>

<span data-ttu-id="6b3eb-200">Entity Framework Core を使用するサービスは、 [Addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)を使用して[依存関係の挿入](xref:fundamentals/dependency-injection)に登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-200">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="6b3eb-201">`ContactIsOwnerAuthorizationHandler` は Entity Framework Core 上に構築された ASP.NET Core [id](xref:security/authentication/identity)を使用します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-201">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="6b3eb-202">サービスコレクションにハンドラーを登録して、[依存関係の挿入](xref:fundamentals/dependency-injection)によって `ContactsController` で使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-202">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6b3eb-203">`ConfigureServices`の末尾に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-203">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="6b3eb-204">`ContactAdministratorsAuthorizationHandler` と `ContactManagerAuthorizationHandler` はシングルトンとして追加されます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-204">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="6b3eb-205">EF を使用せず、必要なすべての情報が `HandleRequirementAsync` メソッドの `Context` パラメーターに含まれているため、これらはシングルトンです。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-205">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="6b3eb-206">承認をサポートします。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-206">Support authorization</span></span>

<span data-ttu-id="6b3eb-207">このセクションでは、Razor ページを更新し、操作の要件クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-207">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="6b3eb-208">連絡先の操作の要件のクラスを確認してください。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-208">Review the contact operations requirements class</span></span>

<span data-ttu-id="6b3eb-209">`ContactOperations` クラスを確認します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-209">Review the `ContactOperations` class.</span></span> <span data-ttu-id="6b3eb-210">このクラスには、要件が含まれています。 アプリがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-210">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="6b3eb-211">連絡先の Razor ページの基本クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-211">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="6b3eb-212">連絡先の Razor ページで使用されるサービスを含む基本クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-212">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="6b3eb-213">基底クラスでは、1 つの場所に、初期化コードが配置します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-213">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="6b3eb-214">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-214">The preceding code:</span></span>

* <span data-ttu-id="6b3eb-215">承認ハンドラーにアクセスするための `IAuthorizationService` サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-215">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="6b3eb-216">Id `UserManager` サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-216">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="6b3eb-217">`ApplicationDbContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-217">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="6b3eb-218">CreateModel の更新します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-218">Update the CreateModel</span></span>

<span data-ttu-id="6b3eb-219">Create page model コンストラクターを更新して、`DI_BasePageModel` 基底クラスを使用するようにします。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-219">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="6b3eb-220">`CreateModel.OnPostAsync` メソッドを次のように更新します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-220">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="6b3eb-221">`Contact` モデルにユーザー ID を追加します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-221">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="6b3eb-222">ユーザーが連絡先を作成するためのアクセス許可を確認する認証ハンドラーを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-222">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="6b3eb-223">更新プログラム、IndexModel</span><span class="sxs-lookup"><span data-stu-id="6b3eb-223">Update the IndexModel</span></span>

<span data-ttu-id="6b3eb-224">`OnGetAsync` メソッドを更新して、承認された連絡先だけが一般ユーザーに表示されるようにします。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-224">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="6b3eb-225">更新プログラム、EditModel</span><span class="sxs-lookup"><span data-stu-id="6b3eb-225">Update the EditModel</span></span>

<span data-ttu-id="6b3eb-226">ユーザーが連絡先を所有していることを確認するための承認ハンドラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-226">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="6b3eb-227">リソース承認が検証されているため、`[Authorize]` 属性では不十分です。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-227">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="6b3eb-228">アプリは、属性が評価されたときに、リソースへのアクセスにすることがありません。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-228">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="6b3eb-229">リソース ベースの承認は、命令型である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-229">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="6b3eb-230">ページ モデルに読み込んで、または読み込みハンドラー自体内で、アプリが、リソースへのアクセスを持つ、チェックを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-230">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="6b3eb-231">リソース キーを渡すことで、リソースを頻繁にアクセスするとします。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-231">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="6b3eb-232">更新プログラム、DeleteModel</span><span class="sxs-lookup"><span data-stu-id="6b3eb-232">Update the DeleteModel</span></span>

<span data-ttu-id="6b3eb-233">承認ハンドラーを使用して、ユーザーが連絡先に削除するアクセス許可を持ってことを確認する delete ページ モデルを更新します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-233">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="6b3eb-234">承認サービスをビューに挿入します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-234">Inject the authorization service into the views</span></span>

<span data-ttu-id="6b3eb-235">現時点では、UI の表示では、編集し、ユーザーが変更できない連絡先へのリンクを削除します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-235">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="6b3eb-236">*ページ/_ViewImports*ファイルに承認サービスを挿入して、すべてのビューで使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-236">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="6b3eb-237">上記のマークアップでは、いくつかの `using` ステートメントが追加されます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-237">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="6b3eb-238">*Pages/Contacts/Index. cshtml*の**編集**と**削除**のリンクを更新して、適切なアクセス許可を持つユーザーにのみ表示されるようにします。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-238">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="6b3eb-239">変更データへのアクセス許可がないユーザーからのリンクを非表示と、アプリをセキュリティで保護しません。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-239">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="6b3eb-240">リンクを非表示アプリよりユーザー フレンドリな唯一の有効なリンクを表示することで。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-240">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="6b3eb-241">ユーザーは、編集を呼び出すし、所有していないデータの操作を削除するには、生成された Url を切断できます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-241">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="6b3eb-242">Razor ページまたはコント ローラーは、データを保護するアクセス チェックを適用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-242">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="6b3eb-243">更新プログラムの詳細</span><span class="sxs-lookup"><span data-stu-id="6b3eb-243">Update Details</span></span>

<span data-ttu-id="6b3eb-244">マネージャーが承認または連絡先を拒否するために、詳細ビューを更新します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-244">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="6b3eb-245">詳細のページ モデルを更新します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-245">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="6b3eb-246">追加またはロールにユーザーを削除します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-246">Add or remove a user to a role</span></span>

<span data-ttu-id="6b3eb-247">次の情報については、[この問題](https://github.com/dotnet/AspNetCore.Docs/issues/8502)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-247">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="6b3eb-248">ユーザーから権限を削除しています。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-248">Removing privileges from a user.</span></span> <span data-ttu-id="6b3eb-249">たとえば、チャットアプリでユーザーのミュートを行います。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-249">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="6b3eb-250">ユーザーに特権を追加します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-250">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="6b3eb-251">チャレンジと禁止の違い</span><span class="sxs-lookup"><span data-stu-id="6b3eb-251">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="6b3eb-252">このアプリでは、認証された[ユーザーを要求](#require-authenticated-users)する既定のポリシーを設定します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-252">This app sets the default policy to [require authenticated users](#require-authenticated-users).</span></span> <span data-ttu-id="6b3eb-253">次のコードでは、匿名ユーザーを許可します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-253">The following code allows anonymous users.</span></span> <span data-ttu-id="6b3eb-254">匿名ユーザーは、チャレンジと禁止の違いを示すことができます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-254">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="6b3eb-255">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-255">In the preceding code:</span></span>

* <span data-ttu-id="6b3eb-256">ユーザーが認証**されていない**場合は、`ChallengeResult` が返されます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-256">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="6b3eb-257">`ChallengeResult` が返されると、ユーザーはサインインページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-257">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="6b3eb-258">ユーザーが認証されているが承認されていない場合、`ForbidResult` が返されます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-258">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="6b3eb-259">`ForbidResult` が返されると、ユーザーは [アクセス拒否] ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-259">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="6b3eb-260">完成したアプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-260">Test the completed app</span></span>

<span data-ttu-id="6b3eb-261">シードされたユーザーアカウントのパスワードをまだ設定していない場合は、[シークレットマネージャーツール](xref:security/app-secrets#secret-manager)を使用してパスワードを設定します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-261">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="6b3eb-262">強力なパスワードを選択します。 8 を使用して、または詳細文字と少なくとも 1 つの大文字の文字、番号、およびシンボル。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-262">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="6b3eb-263">たとえば、`Passw0rd!` は強力なパスワードの要件を満たしています。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-263">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="6b3eb-264">プロジェクトのフォルダーから次のコマンドを実行します。 `<PW>` はパスワードです。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-264">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="6b3eb-265">場合は、アプリでは、連絡先があります。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-265">If the app has contacts:</span></span>

* <span data-ttu-id="6b3eb-266">`Contact` テーブル内のすべてのレコードを削除します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-266">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="6b3eb-267">データベースをシードするアプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-267">Restart the app to seed the database.</span></span>

<span data-ttu-id="6b3eb-268">完成したアプリをテストする簡単な方法では、次の 3 つの異なるブラウザー (または incognito/inprivate ブラウズ セッション) を起動します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-268">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="6b3eb-269">1つのブラウザーで、新しいユーザーを登録します (たとえば、`test@example.com`)。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-269">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="6b3eb-270">ブラウザーごとに別のユーザーでサインインします。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-270">Sign in to each browser with a different user.</span></span> <span data-ttu-id="6b3eb-271">次の操作を確認します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-271">Verify the following operations:</span></span>

* <span data-ttu-id="6b3eb-272">登録済みユーザーには、すべての承認済みの連絡先データを表示できます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-272">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="6b3eb-273">登録済みユーザーは編集/削除が自分のデータ。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-273">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="6b3eb-274">管理者では、連絡先データの承認または却下をします。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-274">Managers can approve/reject contact data.</span></span> <span data-ttu-id="6b3eb-275">`Details` ビューには、 **[承認]** ボタンと **[却下]** ボタンが表示されます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-275">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="6b3eb-276">管理者承認または却下して編集/削除のすべてのデータ。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-276">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="6b3eb-277">User</span><span class="sxs-lookup"><span data-stu-id="6b3eb-277">User</span></span>                | <span data-ttu-id="6b3eb-278">アプリによってシード処理</span><span class="sxs-lookup"><span data-stu-id="6b3eb-278">Seeded by the app</span></span> | <span data-ttu-id="6b3eb-279">オプション</span><span class="sxs-lookup"><span data-stu-id="6b3eb-279">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="6b3eb-280">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b3eb-280">No</span></span>                | <span data-ttu-id="6b3eb-281">独自のデータを編集/削除します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-281">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="6b3eb-282">はい</span><span class="sxs-lookup"><span data-stu-id="6b3eb-282">Yes</span></span>               | <span data-ttu-id="6b3eb-283">承認または却下と編集/削除は、データを所有します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-283">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="6b3eb-284">はい</span><span class="sxs-lookup"><span data-stu-id="6b3eb-284">Yes</span></span>               | <span data-ttu-id="6b3eb-285">承認または却下し、すべてのデータの編集/削除します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-285">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="6b3eb-286">管理者のブラウザーで連絡先を作成します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-286">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="6b3eb-287">削除の URL をコピーし、管理者の連絡先から管理者を編集します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-287">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="6b3eb-288">テスト ユーザーのブラウザー テスト ユーザーがこれらの操作を実行できないことを確認するには、これらのリンクを貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-288">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="6b3eb-289">スターター アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-289">Create the starter app</span></span>

* <span data-ttu-id="6b3eb-290">「ContactManager」という名前の Razor ページ アプリの作成</span><span class="sxs-lookup"><span data-stu-id="6b3eb-290">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="6b3eb-291">**個々のユーザーアカウント**を使用してアプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-291">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="6b3eb-292">ファイル名「ContactManager」名前空間サンプルで使用する名前空間が一致するようにします。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-292">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="6b3eb-293">`-uld` は SQLite ではなく LocalDB を指定します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-293">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="6b3eb-294">*モデル/Contact を追加します。*</span><span class="sxs-lookup"><span data-stu-id="6b3eb-294">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="6b3eb-295">`Contact` モデルをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-295">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="6b3eb-296">最初の移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-296">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="6b3eb-297">`dotnet aspnet-codegenerator razorpage` コマンドでバグが発生した場合は、 [GitHub の問題](https://github.com/aspnet/Scaffolding/issues/984)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-297">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="6b3eb-298">*Pages/Shared/_Layout cshtml*ファイルで**contactmanager**アンカーを更新します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-298">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="6b3eb-299">作成、編集、および連絡先を削除して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-299">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="6b3eb-300">データベースのシード</span><span class="sxs-lookup"><span data-stu-id="6b3eb-300">Seed the database</span></span>

<span data-ttu-id="6b3eb-301">[SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs)クラスを*Data*フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-301">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="6b3eb-302">`Main`から `SeedData.Initialize` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-302">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="6b3eb-303">アプリが、データベースをシード処理をテストします。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-303">Test that the app seeded the database.</span></span> <span data-ttu-id="6b3eb-304">DB の連絡先に行がある場合は、seed メソッドは実行されません。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-304">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="6b3eb-305">このチュートリアルでは、承認によって保護されたユーザー データと ASP.NET Core web アプリを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-305">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="6b3eb-306">認証済み (登録済み) のユーザーの連絡先の一覧を表示を作成します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-306">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="6b3eb-307">これには 3 つのセキュリティ グループがあります。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-307">There are three security groups:</span></span>

* <span data-ttu-id="6b3eb-308">**登録されているユーザー**は、すべての承認済みデータを表示したり、自分のデータを編集または削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-308">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="6b3eb-309">**管理者**は、連絡先データを承認または拒否できます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-309">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="6b3eb-310">承認されたメンバーのみがユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-310">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="6b3eb-311">**管理者**は、任意のデータを承認/拒否したり、編集/削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-311">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="6b3eb-312">次の図では、user Rick (`rick@example.com`) がサインインしています。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-312">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="6b3eb-313">Rick は、承認された連絡先を表示して**編集**/**削除**/、連絡先の新しいリンクを**作成**することのみができます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-313">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="6b3eb-314">Rick によって作成された最後のレコードにのみ、**編集**および**削除**のリンクが表示されます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-314">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="6b3eb-315">他のユーザーは、管理者は、状態を"Approved"に変わるまで、最後のレコードを表示されません。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-315">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![サインインして Rick を示すスクリーン ショット](secure-data/_static/rick.png)

<span data-ttu-id="6b3eb-317">次の図では、`manager@contoso.com` がサインインし、マネージャーのロールに含まれています。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-317">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![サインイン manager@contoso.com を示すスクリーンショット](secure-data/_static/manager1.png)

<span data-ttu-id="6b3eb-319">次の図は、連絡先の詳細ビューをマネージャーには。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-319">The following image shows the managers details view of a contact:</span></span>

![連絡先のマネージャーの表示](secure-data/_static/manager.png)

<span data-ttu-id="6b3eb-321">**[承認]** ボタンと **[却下]** ボタンは、マネージャーと管理者に対してのみ表示されます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-321">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="6b3eb-322">次の図では、`admin@contoso.com` がサインインし、管理者のロールに含まれています。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-322">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![サインイン admin@contoso.com を示すスクリーンショット](secure-data/_static/admin.png)

<span data-ttu-id="6b3eb-324">管理者は、すべての特権を持ちます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-324">The administrator has all privileges.</span></span> <span data-ttu-id="6b3eb-325">彼女は、読み取り/編集/削除のいずれかの連絡先をことができ、連絡先の状態を変更します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-325">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="6b3eb-326">アプリは、次の `Contact` モデルを[スキャフォールディング](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model)することによって作成されました。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-326">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="6b3eb-327">サンプルには、次の承認ハンドラーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-327">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="6b3eb-328">`ContactIsOwnerAuthorizationHandler`: ユーザーがデータを編集できるようになります。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-328">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="6b3eb-329">`ContactManagerAuthorizationHandler`: 管理者が連絡先を承認または拒否できるようにします。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-329">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="6b3eb-330">`ContactAdministratorsAuthorizationHandler`: 管理者は、連絡先を承認または拒否したり、連絡先を編集または削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-330">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6b3eb-331">前提条件</span><span class="sxs-lookup"><span data-stu-id="6b3eb-331">Prerequisites</span></span>

<span data-ttu-id="6b3eb-332">このチュートリアルは、高度なです。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-332">This tutorial is advanced.</span></span> <span data-ttu-id="6b3eb-333">理解しておく必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-333">You should be familiar with:</span></span>

* [<span data-ttu-id="6b3eb-334">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6b3eb-334">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="6b3eb-335">認証</span><span class="sxs-lookup"><span data-stu-id="6b3eb-335">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="6b3eb-336">アカウントの確認とパスワードの回復</span><span class="sxs-lookup"><span data-stu-id="6b3eb-336">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="6b3eb-337">承認</span><span class="sxs-lookup"><span data-stu-id="6b3eb-337">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="6b3eb-338">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="6b3eb-338">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="6b3eb-339">Starter および完成したアプリ</span><span class="sxs-lookup"><span data-stu-id="6b3eb-339">The starter and completed app</span></span>

<span data-ttu-id="6b3eb-340">完成し[た](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples)アプリを[ダウンロード](xref:index#how-to-download-a-sample)します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-340">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="6b3eb-341">完成したアプリを[テスト](#test-the-completed-app)して、セキュリティ機能に慣れるようにします。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-341">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="6b3eb-342">スターター アプリ</span><span class="sxs-lookup"><span data-stu-id="6b3eb-342">The starter app</span></span>

<span data-ttu-id="6b3eb-343">[スターター](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/)アプリを[ダウンロード](xref:index#how-to-download-a-sample)します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-343">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="6b3eb-344">アプリを実行し、 **[Contactmanager]** リンクをタップして、連絡先を作成、編集、および削除できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-344">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="6b3eb-345">セキュリティで保護されたユーザー データ</span><span class="sxs-lookup"><span data-stu-id="6b3eb-345">Secure user data</span></span>

<span data-ttu-id="6b3eb-346">次のセクションでは、セキュリティで保護されたユーザー データ アプリを作成するすべての主要な手順があります。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-346">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="6b3eb-347">完成したプロジェクトを参照すると便利です。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-347">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="6b3eb-348">ユーザーに連絡先データを関連付ける</span><span class="sxs-lookup"><span data-stu-id="6b3eb-348">Tie the contact data to the user</span></span>

<span data-ttu-id="6b3eb-349">ASP.NET [id](xref:security/authentication/identity)ユーザー id を使用すると、ユーザーがデータを編集できるようになりますが、他のユーザーデータは編集できません。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-349">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="6b3eb-350">`OwnerID` と `ContactStatus` を `Contact` モデルに追加します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-350">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="6b3eb-351">`OwnerID` は、 [id](xref:security/authentication/identity)データベースの `AspNetUser` テーブルのユーザー ID です。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-351">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="6b3eb-352">[`Status`] フィールドによって、連絡先が一般ユーザーに表示されるかどうかが決まります。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-352">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="6b3eb-353">新しい移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-353">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="6b3eb-354">役割サービスの Id を追加します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-354">Add Role services to Identity</span></span>

<span data-ttu-id="6b3eb-355">役割サービスを追加するには、 [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1)を追加します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-355">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a><span data-ttu-id="6b3eb-356">認証されたユーザーが必要です。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-356">Require authenticated users</span></span>

<span data-ttu-id="6b3eb-357">ユーザー認証を必要とする既定の認証ポリシーを設定します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-357">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="6b3eb-358">Razor ページ、コントローラー、またはアクションメソッドレベルで、`[AllowAnonymous]` 属性を使用して認証をオプトアウトできます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-358">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="6b3eb-359">ユーザー認証を必要とする既定の認証ポリシーの設定は、新しく追加された Razor ページとコント ローラーを保護します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-359">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="6b3eb-360">既定で認証が必要になるのは、新しいコントローラーに依存していて、Razor Pages `[Authorize]` 属性を含めるよりも安全です。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-360">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="6b3eb-361">匿名ユーザーが登録前にサイトに関する情報を取得できるように、 [Allowanonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute)を Index、About、および Contact の各ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-361">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="6b3eb-362">テスト アカウントを構成します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-362">Configure the test account</span></span>

<span data-ttu-id="6b3eb-363">`SeedData` クラスは、管理者とマネージャーの2つのアカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-363">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="6b3eb-364">これらのアカウントのパスワードを設定するには、[シークレットマネージャーツール](xref:security/app-secrets)を使用します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-364">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="6b3eb-365">プロジェクトディレクトリ ( *Program.cs*を含むディレクトリ) のパスワードを設定します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-365">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="6b3eb-366">強力なパスワードが指定されていない場合、`SeedData.Initialize` が呼び出されると例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-366">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="6b3eb-367">テストパスワードを使用するように `Main` を更新します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-367">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="6b3eb-368">テスト アカウントを作成し、連絡先の更新</span><span class="sxs-lookup"><span data-stu-id="6b3eb-368">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="6b3eb-369">`SeedData` クラスの `Initialize` メソッドを更新して、テストアカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-369">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="6b3eb-370">管理者のユーザー ID と `ContactStatus` を連絡先に追加します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-370">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="6b3eb-371">「送信済み」と「拒否」の 1 つの連絡先を作成します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-371">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="6b3eb-372">すべての連絡先に、ユーザー ID と状態を追加します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-372">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="6b3eb-373">1 人だけが表示されます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-373">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="6b3eb-374">所有者、マネージャー、および管理者の承認ハンドラーを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-374">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="6b3eb-375">*承認*フォルダーを作成し、その中に `ContactIsOwnerAuthorizationHandler` クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-375">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="6b3eb-376">`ContactIsOwnerAuthorizationHandler` は、リソースに対して動作しているユーザーがリソースを所有していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-376">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="6b3eb-377">`ContactIsOwnerAuthorizationHandler` は[コンテキストを呼び出します。](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)現在の認証済みユーザーが連絡先の所有者である場合は成功します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-377">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="6b3eb-378">承認ハンドラー通常。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-378">Authorization handlers generally:</span></span>

* <span data-ttu-id="6b3eb-379">要件が満たされた場合に `context.Succeed` を返します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-379">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="6b3eb-380">要件を満たしていない場合は `Task.CompletedTask` を返します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-380">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="6b3eb-381">`Task.CompletedTask` は成功でも失敗でも&mdash;他の承認ハンドラーの実行を許可します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-381">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="6b3eb-382">明示的に失敗する必要がある場合は、コンテキストを返し[ます。失敗](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-382">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="6b3eb-383">アプリは、独自のデータを編集/削除/作成所有者が連絡先にできます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-383">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="6b3eb-384">`ContactIsOwnerAuthorizationHandler` は、要件パラメーターで渡された操作を確認する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-384">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="6b3eb-385">マネージャーの承認ハンドラーを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-385">Create a manager authorization handler</span></span>

<span data-ttu-id="6b3eb-386">*承認*フォルダーに `ContactManagerAuthorizationHandler` クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-386">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="6b3eb-387">`ContactManagerAuthorizationHandler` は、リソースに対して動作しているユーザーがマネージャーであることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-387">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="6b3eb-388">管理者だけでは、承認したり、(新規または変更された) コンテンツの変更を拒否することができます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-388">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="6b3eb-389">管理者の承認ハンドラーを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-389">Create an administrator authorization handler</span></span>

<span data-ttu-id="6b3eb-390">*承認*フォルダーに `ContactAdministratorsAuthorizationHandler` クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-390">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="6b3eb-391">`ContactAdministratorsAuthorizationHandler` は、リソースに対して動作しているユーザーが管理者であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-391">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="6b3eb-392">管理者は、すべての操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-392">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="6b3eb-393">承認ハンドラーを登録します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-393">Register the authorization handlers</span></span>

<span data-ttu-id="6b3eb-394">Entity Framework Core を使用するサービスは、 [Addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)を使用して[依存関係の挿入](xref:fundamentals/dependency-injection)に登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-394">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="6b3eb-395">`ContactIsOwnerAuthorizationHandler` は Entity Framework Core 上に構築された ASP.NET Core [id](xref:security/authentication/identity)を使用します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-395">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="6b3eb-396">サービスコレクションにハンドラーを登録して、[依存関係の挿入](xref:fundamentals/dependency-injection)によって `ContactsController` で使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-396">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6b3eb-397">`ConfigureServices`の末尾に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-397">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="6b3eb-398">`ContactAdministratorsAuthorizationHandler` と `ContactManagerAuthorizationHandler` はシングルトンとして追加されます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-398">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="6b3eb-399">EF を使用せず、必要なすべての情報が `HandleRequirementAsync` メソッドの `Context` パラメーターに含まれているため、これらはシングルトンです。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-399">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="6b3eb-400">承認をサポートします。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-400">Support authorization</span></span>

<span data-ttu-id="6b3eb-401">このセクションでは、Razor ページを更新し、操作の要件クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-401">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="6b3eb-402">連絡先の操作の要件のクラスを確認してください。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-402">Review the contact operations requirements class</span></span>

<span data-ttu-id="6b3eb-403">`ContactOperations` クラスを確認します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-403">Review the `ContactOperations` class.</span></span> <span data-ttu-id="6b3eb-404">このクラスには、要件が含まれています。 アプリがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-404">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="6b3eb-405">連絡先の Razor ページの基本クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-405">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="6b3eb-406">連絡先の Razor ページで使用されるサービスを含む基本クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-406">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="6b3eb-407">基底クラスでは、1 つの場所に、初期化コードが配置します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-407">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="6b3eb-408">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-408">The preceding code:</span></span>

* <span data-ttu-id="6b3eb-409">承認ハンドラーにアクセスするための `IAuthorizationService` サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-409">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="6b3eb-410">Id `UserManager` サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-410">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="6b3eb-411">`ApplicationDbContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-411">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="6b3eb-412">CreateModel の更新します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-412">Update the CreateModel</span></span>

<span data-ttu-id="6b3eb-413">Create page model コンストラクターを更新して、`DI_BasePageModel` 基底クラスを使用するようにします。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-413">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="6b3eb-414">`CreateModel.OnPostAsync` メソッドを次のように更新します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-414">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="6b3eb-415">`Contact` モデルにユーザー ID を追加します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-415">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="6b3eb-416">ユーザーが連絡先を作成するためのアクセス許可を確認する認証ハンドラーを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-416">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="6b3eb-417">更新プログラム、IndexModel</span><span class="sxs-lookup"><span data-stu-id="6b3eb-417">Update the IndexModel</span></span>

<span data-ttu-id="6b3eb-418">`OnGetAsync` メソッドを更新して、承認された連絡先だけが一般ユーザーに表示されるようにします。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-418">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="6b3eb-419">更新プログラム、EditModel</span><span class="sxs-lookup"><span data-stu-id="6b3eb-419">Update the EditModel</span></span>

<span data-ttu-id="6b3eb-420">ユーザーが連絡先を所有していることを確認するための承認ハンドラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-420">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="6b3eb-421">リソース承認が検証されているため、`[Authorize]` 属性では不十分です。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-421">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="6b3eb-422">アプリは、属性が評価されたときに、リソースへのアクセスにすることがありません。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-422">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="6b3eb-423">リソース ベースの承認は、命令型である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-423">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="6b3eb-424">ページ モデルに読み込んで、または読み込みハンドラー自体内で、アプリが、リソースへのアクセスを持つ、チェックを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-424">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="6b3eb-425">リソース キーを渡すことで、リソースを頻繁にアクセスするとします。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-425">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="6b3eb-426">更新プログラム、DeleteModel</span><span class="sxs-lookup"><span data-stu-id="6b3eb-426">Update the DeleteModel</span></span>

<span data-ttu-id="6b3eb-427">承認ハンドラーを使用して、ユーザーが連絡先に削除するアクセス許可を持ってことを確認する delete ページ モデルを更新します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-427">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="6b3eb-428">承認サービスをビューに挿入します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-428">Inject the authorization service into the views</span></span>

<span data-ttu-id="6b3eb-429">現時点では、UI の表示では、編集し、ユーザーが変更できない連絡先へのリンクを削除します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-429">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="6b3eb-430">すべてのビューで使用できるように、 *views/_ViewImports cshtml*ファイルに承認サービスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-430">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="6b3eb-431">上記のマークアップでは、いくつかの `using` ステートメントが追加されます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-431">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="6b3eb-432">*Pages/Contacts/Index. cshtml*の**編集**と**削除**のリンクを更新して、適切なアクセス許可を持つユーザーにのみ表示されるようにします。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-432">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="6b3eb-433">変更データへのアクセス許可がないユーザーからのリンクを非表示と、アプリをセキュリティで保護しません。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-433">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="6b3eb-434">リンクを非表示アプリよりユーザー フレンドリな唯一の有効なリンクを表示することで。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-434">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="6b3eb-435">ユーザーは、編集を呼び出すし、所有していないデータの操作を削除するには、生成された Url を切断できます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-435">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="6b3eb-436">Razor ページまたはコント ローラーは、データを保護するアクセス チェックを適用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-436">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="6b3eb-437">更新プログラムの詳細</span><span class="sxs-lookup"><span data-stu-id="6b3eb-437">Update Details</span></span>

<span data-ttu-id="6b3eb-438">マネージャーが承認または連絡先を拒否するために、詳細ビューを更新します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-438">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="6b3eb-439">詳細のページ モデルを更新します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-439">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="6b3eb-440">追加またはロールにユーザーを削除します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-440">Add or remove a user to a role</span></span>

<span data-ttu-id="6b3eb-441">次の情報については、[この問題](https://github.com/dotnet/AspNetCore.Docs/issues/8502)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-441">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="6b3eb-442">ユーザーから権限を削除しています。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-442">Removing privileges from a user.</span></span> <span data-ttu-id="6b3eb-443">たとえば、チャットアプリでユーザーのミュートを行います。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-443">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="6b3eb-444">ユーザーに特権を追加します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-444">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="6b3eb-445">完成したアプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-445">Test the completed app</span></span>

<span data-ttu-id="6b3eb-446">シードされたユーザーアカウントのパスワードをまだ設定していない場合は、[シークレットマネージャーツール](xref:security/app-secrets#secret-manager)を使用してパスワードを設定します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-446">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="6b3eb-447">強力なパスワードを選択します。 8 を使用して、または詳細文字と少なくとも 1 つの大文字の文字、番号、およびシンボル。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-447">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="6b3eb-448">たとえば、`Passw0rd!` は強力なパスワードの要件を満たしています。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-448">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="6b3eb-449">プロジェクトのフォルダーから次のコマンドを実行します。 `<PW>` はパスワードです。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-449">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="6b3eb-450">データベースを削除して更新する</span><span class="sxs-lookup"><span data-stu-id="6b3eb-450">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="6b3eb-451">データベースをシードするアプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-451">Restart the app to seed the database.</span></span>

<span data-ttu-id="6b3eb-452">完成したアプリをテストする簡単な方法では、次の 3 つの異なるブラウザー (または incognito/inprivate ブラウズ セッション) を起動します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-452">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="6b3eb-453">1つのブラウザーで、新しいユーザーを登録します (たとえば、`test@example.com`)。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-453">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="6b3eb-454">ブラウザーごとに別のユーザーでサインインします。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-454">Sign in to each browser with a different user.</span></span> <span data-ttu-id="6b3eb-455">次の操作を確認します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-455">Verify the following operations:</span></span>

* <span data-ttu-id="6b3eb-456">登録済みユーザーには、すべての承認済みの連絡先データを表示できます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-456">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="6b3eb-457">登録済みユーザーは編集/削除が自分のデータ。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-457">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="6b3eb-458">管理者では、連絡先データの承認または却下をします。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-458">Managers can approve/reject contact data.</span></span> <span data-ttu-id="6b3eb-459">`Details` ビューには、 **[承認]** ボタンと **[却下]** ボタンが表示されます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-459">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="6b3eb-460">管理者承認または却下して編集/削除のすべてのデータ。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-460">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="6b3eb-461">User</span><span class="sxs-lookup"><span data-stu-id="6b3eb-461">User</span></span>                | <span data-ttu-id="6b3eb-462">アプリによってシード処理</span><span class="sxs-lookup"><span data-stu-id="6b3eb-462">Seeded by the app</span></span> | <span data-ttu-id="6b3eb-463">オプション</span><span class="sxs-lookup"><span data-stu-id="6b3eb-463">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="6b3eb-464">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b3eb-464">No</span></span>                | <span data-ttu-id="6b3eb-465">独自のデータを編集/削除します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-465">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="6b3eb-466">はい</span><span class="sxs-lookup"><span data-stu-id="6b3eb-466">Yes</span></span>               | <span data-ttu-id="6b3eb-467">承認または却下と編集/削除は、データを所有します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-467">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="6b3eb-468">はい</span><span class="sxs-lookup"><span data-stu-id="6b3eb-468">Yes</span></span>               | <span data-ttu-id="6b3eb-469">承認または却下し、すべてのデータの編集/削除します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-469">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="6b3eb-470">管理者のブラウザーで連絡先を作成します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-470">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="6b3eb-471">削除の URL をコピーし、管理者の連絡先から管理者を編集します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-471">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="6b3eb-472">テスト ユーザーのブラウザー テスト ユーザーがこれらの操作を実行できないことを確認するには、これらのリンクを貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-472">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="6b3eb-473">スターター アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-473">Create the starter app</span></span>

* <span data-ttu-id="6b3eb-474">「ContactManager」という名前の Razor ページ アプリの作成</span><span class="sxs-lookup"><span data-stu-id="6b3eb-474">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="6b3eb-475">**個々のユーザーアカウント**を使用してアプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-475">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="6b3eb-476">ファイル名「ContactManager」名前空間サンプルで使用する名前空間が一致するようにします。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-476">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="6b3eb-477">`-uld` は SQLite ではなく LocalDB を指定します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-477">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="6b3eb-478">*モデル/Contact を追加します。*</span><span class="sxs-lookup"><span data-stu-id="6b3eb-478">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="6b3eb-479">`Contact` モデルをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-479">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="6b3eb-480">最初の移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-480">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="6b3eb-481">*Pages/_Layout cshtml*ファイルで**contactmanager**アンカーを更新します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-481">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="6b3eb-482">作成、編集、および連絡先を削除して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-482">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="6b3eb-483">データベースのシード</span><span class="sxs-lookup"><span data-stu-id="6b3eb-483">Seed the database</span></span>

<span data-ttu-id="6b3eb-484">[SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs)クラスを*Data*フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-484">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="6b3eb-485">`Main`から `SeedData.Initialize` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-485">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="6b3eb-486">アプリが、データベースをシード処理をテストします。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-486">Test that the app seeded the database.</span></span> <span data-ttu-id="6b3eb-487">DB の連絡先に行がある場合は、seed メソッドは実行されません。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-487">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="6b3eb-488">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="6b3eb-488">Additional resources</span></span>

* [<span data-ttu-id="6b3eb-489">Azure App Service で .NET Core と SQL Database web アプリを構築する</span><span class="sxs-lookup"><span data-stu-id="6b3eb-489">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="6b3eb-490">[ASP.NET Core の承認ラボ](https://github.com/blowdart/AspNetAuthorizationWorkshop)。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-490">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="6b3eb-491">このラボでは、このチュートリアルで導入されたセキュリティ機能の詳細に移動します。</span><span class="sxs-lookup"><span data-stu-id="6b3eb-491">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="6b3eb-492">カスタム ポリシー ベースの承認</span><span class="sxs-lookup"><span data-stu-id="6b3eb-492">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
