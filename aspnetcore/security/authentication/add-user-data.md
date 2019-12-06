---
title: ASP.NET Core プロジェクトにおける Identity へのカスタムユーザーデータの追加、ダウンロードおよび削除
author: rick-anderson
description: ASP.NET Core プロジェクトにおいて Identity にカスタム ユーザーデータを追加する方法について説明します。 GDPR ごとのデータを削除します。
ms.author: riande
ms.date: 12/05/2019
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: f54df68834cd3e2493e558aaab9851f036f3f01b
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880753"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a><span data-ttu-id="1e845-104">ASP.NET Core プロジェクトにおける Identity へのカスタム ユーザーデータの追加、ダウンロードおよび削除</span><span class="sxs-lookup"><span data-stu-id="1e845-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="1e845-105">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1e845-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1e845-106">この記事では方法。</span><span class="sxs-lookup"><span data-stu-id="1e845-106">This article shows how to:</span></span>

* <span data-ttu-id="1e845-107">ASP.NET Core web アプリにカスタム ユーザー データを追加します。</span><span class="sxs-lookup"><span data-stu-id="1e845-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="1e845-108">カスタムユーザーデータモデルを <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> 属性でマークして、自動的にダウンロードおよび削除できるようにします。</span><span class="sxs-lookup"><span data-stu-id="1e845-108">Mark the custom user data model with the <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="1e845-109">データをダウンロードして、削除することを行うには、満たす助けとなる[GDPR](xref:security/gdpr)要件。</span><span class="sxs-lookup"><span data-stu-id="1e845-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="1e845-110">プロジェクト サンプルは、Razor ページ web アプリから作成されますが、手順は ASP.NET Core MVC web アプリと同様。</span><span class="sxs-lookup"><span data-stu-id="1e845-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="1e845-111">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="1e845-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1e845-112">必要条件</span><span class="sxs-lookup"><span data-stu-id="1e845-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a><span data-ttu-id="1e845-113">Razor Web アプリの作成</span><span class="sxs-lookup"><span data-stu-id="1e845-113">Create a Razor web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1e845-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e845-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="1e845-115">Visual Studio の **[ファイル]** メニューから、 **[新規作成]**  >  **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="1e845-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="1e845-116">プロジェクトに名前を**WebApp1**にする場合の名前空間と一致、[サンプルをダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)コード。</span><span class="sxs-lookup"><span data-stu-id="1e845-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="1e845-117">**ASP.NET Core Web アプリケーション**の選択 > **OK**</span><span class="sxs-lookup"><span data-stu-id="1e845-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="1e845-118">ドロップダウンで**ASP.NET Core 3.0**を選択します。</span><span class="sxs-lookup"><span data-stu-id="1e845-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="1e845-119">**Web アプリケーション**の選択 > **OK**</span><span class="sxs-lookup"><span data-stu-id="1e845-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="1e845-120">プロジェクトをビルドおよび実行します。</span><span class="sxs-lookup"><span data-stu-id="1e845-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="1e845-121">Visual Studio の **[ファイル]** メニューから、 **[新規作成]**  >  **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="1e845-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="1e845-122">プロジェクトに名前を**WebApp1**にする場合の名前空間と一致、[サンプルをダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)コード。</span><span class="sxs-lookup"><span data-stu-id="1e845-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="1e845-123">**ASP.NET Core Web アプリケーション**の選択 > **OK**</span><span class="sxs-lookup"><span data-stu-id="1e845-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="1e845-124">ドロップダウンで**ASP.NET Core 2.2**を選択します。</span><span class="sxs-lookup"><span data-stu-id="1e845-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="1e845-125">**Web アプリケーション**の選択 > **OK**</span><span class="sxs-lookup"><span data-stu-id="1e845-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="1e845-126">プロジェクトをビルドおよび実行します。</span><span class="sxs-lookup"><span data-stu-id="1e845-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="1e845-127">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="1e845-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a><span data-ttu-id="1e845-128">Identity のスキャフォールディングの実行</span><span class="sxs-lookup"><span data-stu-id="1e845-128">Run the Identity scaffolder</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1e845-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e845-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1e845-130">**ソリューション エクスプ ローラー**、プロジェクトを右クリックして >**追加** > **スキャフォールディングされた新しい項目**します。</span><span class="sxs-lookup"><span data-stu-id="1e845-130">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="1e845-131">左側のウィンドウから、**スキャフォールディングの追加**ダイアログ ボックスで、 **Identity** > **追加**します。</span><span class="sxs-lookup"><span data-stu-id="1e845-131">From the left pane of the **Add Scaffold** dialog, select **Identity** > **ADD**.</span></span>
* <span data-ttu-id="1e845-132">**ADD アイデンティティ**ダイアログ ボックスで、次のオプション。</span><span class="sxs-lookup"><span data-stu-id="1e845-132">In the **ADD Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="1e845-133">既存のレイアウト ファイルを選択して *~/Pages/Shared/_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="1e845-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="1e845-134">オーバーライドする次のファイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="1e845-134">Select the following files to override:</span></span>
    * <span data-ttu-id="1e845-135">**アカウントまたは登録**</span><span class="sxs-lookup"><span data-stu-id="1e845-135">**Account/Register**</span></span>
    * <span data-ttu-id="1e845-136">**アカウント/管理/インデックス**</span><span class="sxs-lookup"><span data-stu-id="1e845-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="1e845-137">選択、 **+** 新たに作成するボタン**データ コンテキスト クラス**します。</span><span class="sxs-lookup"><span data-stu-id="1e845-137">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="1e845-138">型を受け入れる (**WebApp1.Models.WebApp1Context**場合は、プロジェクトの名前は**WebApp1**)。</span><span class="sxs-lookup"><span data-stu-id="1e845-138">Accept the type (**WebApp1.Models.WebApp1Context** if the project is named **WebApp1**).</span></span>
  * <span data-ttu-id="1e845-139">選択、 **+** 新たに作成するボタン**ユーザー クラス**します。</span><span class="sxs-lookup"><span data-stu-id="1e845-139">Select the **+** button to create a new **User class**.</span></span> <span data-ttu-id="1e845-140">型を受け入れる (**WebApp1User**場合は、プロジェクトの名前は**WebApp1**) >**追加**します。</span><span class="sxs-lookup"><span data-stu-id="1e845-140">Accept the type (**WebApp1User** if the project is named **WebApp1**) > **Add**.</span></span>
* <span data-ttu-id="1e845-141">選択**追加**します。</span><span class="sxs-lookup"><span data-stu-id="1e845-141">Select **ADD**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="1e845-142">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="1e845-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="1e845-143">ASP.NET Core scaffolder を以前インストールしていない場合は、今すぐインストールします。</span><span class="sxs-lookup"><span data-stu-id="1e845-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="1e845-144">パッケージ参照を追加[Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/)プロジェクト (.csproj) ファイル。</span><span class="sxs-lookup"><span data-stu-id="1e845-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="1e845-145">プロジェクト ディレクトリに、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1e845-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="1e845-146">Identity scaffolder オプションを一覧表示するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1e845-146">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="1e845-147">プロジェクト フォルダーでは、Identity scaffolder を実行します。</span><span class="sxs-lookup"><span data-stu-id="1e845-147">In the project folder, run the Identity scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="1e845-148">指示に従って、[移行、UseAuthentication、およびレイアウト](xref:security/authentication/scaffold-identity#efm)次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="1e845-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="1e845-149">移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="1e845-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="1e845-150">`UseAuthentication` に `Startup.Configure` を追加します。</span><span class="sxs-lookup"><span data-stu-id="1e845-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="1e845-151">追加`<partial name="_LoginPartial" />`レイアウト ファイルにします。</span><span class="sxs-lookup"><span data-stu-id="1e845-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="1e845-152">アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="1e845-152">Test the app:</span></span>
  * <span data-ttu-id="1e845-153">ユーザーを登録する</span><span class="sxs-lookup"><span data-stu-id="1e845-153">Register a user</span></span>
  * <span data-ttu-id="1e845-154">新しいユーザー名を選択します (次に、**ログアウト**リンク)。</span><span class="sxs-lookup"><span data-stu-id="1e845-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="1e845-155">ウィンドウを拡大またはユーザー名とその他のリンクを表示するナビゲーション バーのアイコンを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1e845-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="1e845-156">選択、**個人データ**タブ。</span><span class="sxs-lookup"><span data-stu-id="1e845-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="1e845-157">選択、**ダウンロード**ボタンをクリックし、調査、 *PersonalData.json*ファイル。</span><span class="sxs-lookup"><span data-stu-id="1e845-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="1e845-158">テスト、**削除**ボタンで、ユーザーのログオンを削除します。</span><span class="sxs-lookup"><span data-stu-id="1e845-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-identity-db"></a><span data-ttu-id="1e845-159">Identity DB へのカスタム ユーザーデータの追加</span><span class="sxs-lookup"><span data-stu-id="1e845-159">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="1e845-160">更新プログラム、`IdentityUser`カスタム プロパティを持つクラスを派生します。</span><span class="sxs-lookup"><span data-stu-id="1e845-160">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="1e845-161">ファイルの名前は WebApp1 プロジェクトの名前を付けた場合*Areas/Identity/Data/WebApp1User.cs*します。</span><span class="sxs-lookup"><span data-stu-id="1e845-161">If you named the project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs*.</span></span> <span data-ttu-id="1e845-162">次のコード ファイルを更新します。</span><span class="sxs-lookup"><span data-stu-id="1e845-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="1e845-163">プロパティ[は、次のよう](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute)になります。</span><span class="sxs-lookup"><span data-stu-id="1e845-163">Properties with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="1e845-164">削除されたときに、 *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor ページを呼び出して`UserManager.Delete`します。</span><span class="sxs-lookup"><span data-stu-id="1e845-164">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="1e845-165">によってデータのダウンロードに含まれる、 *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor ページ。</span><span class="sxs-lookup"><span data-stu-id="1e845-165">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="1e845-166">Account/Manage/Index.cshtml ページの更新</span><span class="sxs-lookup"><span data-stu-id="1e845-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="1e845-167">更新プログラム、`InputModel`で*Areas/Identity/Pages/Account/Manage/Index.cshtml.cs*次のようにコードを強調表示されます。</span><span class="sxs-lookup"><span data-stu-id="1e845-167">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="1e845-168">更新プログラム、 *Areas/Identity/Pages/Account/Manage/Index.cshtml*を次の強調表示されているマークアップ。</span><span class="sxs-lookup"><span data-stu-id="1e845-168">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="1e845-169">更新プログラム、 *Areas/Identity/Pages/Account/Manage/Index.cshtml*を次の強調表示されているマークアップ。</span><span class="sxs-lookup"><span data-stu-id="1e845-169">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="1e845-170">Account/Register.cshtml ページの更新</span><span class="sxs-lookup"><span data-stu-id="1e845-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="1e845-171">更新プログラム、`InputModel`で*Areas/Identity/Pages/Account/Register.cshtml.cs*次のようにコードを強調表示されます。</span><span class="sxs-lookup"><span data-stu-id="1e845-171">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="1e845-172">更新プログラム、 *Areas/Identity/Pages/Account/Register.cshtml*を次の強調表示されているマークアップ。</span><span class="sxs-lookup"><span data-stu-id="1e845-172">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="1e845-173">更新プログラム、 *Areas/Identity/Pages/Account/Register.cshtml*を次の強調表示されているマークアップ。</span><span class="sxs-lookup"><span data-stu-id="1e845-173">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="1e845-174">プロジェクトをビルドする。</span><span class="sxs-lookup"><span data-stu-id="1e845-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="1e845-175">カスタム ユーザー データのマイグレーションの追加</span><span class="sxs-lookup"><span data-stu-id="1e845-175">Add a migration for the custom user data</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1e845-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e845-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1e845-177">Visual Studio で**パッケージ マネージャー コンソール**:</span><span class="sxs-lookup"><span data-stu-id="1e845-177">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="1e845-178">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="1e845-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="1e845-179">カスタム ユーザー データの作成、表示、ダウンロード、削除のテスト</span><span class="sxs-lookup"><span data-stu-id="1e845-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="1e845-180">アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="1e845-180">Test the app:</span></span>

* <span data-ttu-id="1e845-181">新しいユーザーを登録します。</span><span class="sxs-lookup"><span data-stu-id="1e845-181">Register a new user.</span></span>
* <span data-ttu-id="1e845-182">カスタムのユーザー データを表示、`/Identity/Account/Manage`ページ。</span><span class="sxs-lookup"><span data-stu-id="1e845-182">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="1e845-183">ダウンロードしてから、ユーザーの個人データを表示、`/Identity/Account/Manage/PersonalData`ページ。</span><span class="sxs-lookup"><span data-stu-id="1e845-183">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>
