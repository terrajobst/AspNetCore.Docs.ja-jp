---
title: Visual Studio を使用して Azure に ASP.NET Core アプリを発行する
author: rick-anderson
description: Visual Studio を使用して Azure App Service に ASP.NET Core アプリを発行する方法を説明します。
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 7fc3644df3dcb957f2537538aaa9506c6b38a480
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78648494"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a><span data-ttu-id="72659-103">Visual Studio を使用して Azure に ASP.NET Core アプリを発行する</span><span class="sxs-lookup"><span data-stu-id="72659-103">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>

<span data-ttu-id="72659-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="72659-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


<span data-ttu-id="72659-105">macOS で作業している場合は、「[Visual Studio for Mac を使用して Azure App Service に Web アプリを発行する](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="72659-105">See [Publish a Web app to Azure App Service using Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) if you are working on macOS.</span></span>

<span data-ttu-id="72659-106">App Service の配置に関する問題を解決するには、「<xref:test/troubleshoot-azure-iis>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="72659-106">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="set-up"></a><span data-ttu-id="72659-107">設定</span><span class="sxs-lookup"><span data-stu-id="72659-107">Set up</span></span>

* <span data-ttu-id="72659-108">Azure アカウントをお持ちでない場合は、[Azure 無料アカウント](https://azure.microsoft.com/free/dotnet/)を作成します。</span><span class="sxs-lookup"><span data-stu-id="72659-108">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="72659-109">Web アプリの作成</span><span class="sxs-lookup"><span data-stu-id="72659-109">Create a web app</span></span>

<span data-ttu-id="72659-110">Visual Studio のスタート ページで、 **[ファイル]、[新規作成]、[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="72659-110">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![[ファイル] メニュー](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="72659-112">**[新しいプロジェクト]** ダイアログで次のように設定します。</span><span class="sxs-lookup"><span data-stu-id="72659-112">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="72659-113">左側のウィンドウで、 **[.NET Core]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="72659-113">In the left pane, select **.NET Core**.</span></span>
* <span data-ttu-id="72659-114">中央のウィンドウで、 **[ASP.NET Core Web Application]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="72659-114">In the center pane, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="72659-115">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="72659-115">Select **OK**.</span></span>

![[新しいプロジェクト] ダイアログ](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="72659-117">**[新しい ASP.NET Core Web アプリケーション]** ダイアログで次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="72659-117">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="72659-118">**[Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="72659-118">Select **Web Application**.</span></span>
* <span data-ttu-id="72659-119">**[認証の変更]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="72659-119">Select **Change Authentication**.</span></span>

![[新しいプロジェクト] ダイアログ](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="72659-121">**[認証の変更]** ダイアログが表示されます。</span><span class="sxs-lookup"><span data-stu-id="72659-121">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="72659-122">**[個人のユーザー アカウント]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="72659-122">Select **Individual User Accounts**.</span></span>
* <span data-ttu-id="72659-123">**[OK]** を選択して **[新しい ASP.NET Core Web アプリケーション]** に戻り、もう一度 **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="72659-123">Select **OK** to return to the **New ASP.NET Core Web Application**, then select **OK** again.</span></span>

![新しい ASP.NET Core Web 認証ダイアログ](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="72659-125">Visual Studio によってソリューションが作成されます。</span><span class="sxs-lookup"><span data-stu-id="72659-125">Visual Studio creates the solution.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="72659-126">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="72659-126">Run the app</span></span>

* <span data-ttu-id="72659-127">Ctrl キーを押しながら F5 キーを押してプロジェクトを実行します。</span><span class="sxs-lookup"><span data-stu-id="72659-127">Press CTRL+F5 to run the project.</span></span>
* <span data-ttu-id="72659-128">**[About]** リンクと **[Contact]** リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="72659-128">Test the **About** and **Contact** links.</span></span>

![Microsoft Edge で開いているローカルホストの Web アプリケーション](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a><span data-ttu-id="72659-130">ユーザーを登録する</span><span class="sxs-lookup"><span data-stu-id="72659-130">Register a user</span></span>

* <span data-ttu-id="72659-131">**[登録]** を選択して、新しいユーザーを登録します。</span><span class="sxs-lookup"><span data-stu-id="72659-131">Select **Register** and register a new user.</span></span> <span data-ttu-id="72659-132">架空の電子メール アドレスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="72659-132">You can use a fictitious email address.</span></span> <span data-ttu-id="72659-133">送信すると、ページに次のエラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="72659-133">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="72659-134">*"内部サーバー エラー: 要求の処理中にデータベースの操作に失敗しました。SQL 例外: データベースを開けません。Applying existing migrations for Application DB context may resolve this issue. /(アプリケーション DB コンテキストの既存の移行を適用すると問題が解決する場合があります。/)*</span><span class="sxs-lookup"><span data-stu-id="72659-134">*"Internal Server Error: A database operation failed while processing the request. SQL exception: Cannot open the database. Applying existing migrations for Application DB context may resolve this issue."*</span></span>
* <span data-ttu-id="72659-135">**[Apply Migrations]/(移行を適用する/)** を選択し、移行が完了したら、ページを更新します。</span><span class="sxs-lookup"><span data-stu-id="72659-135">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![内部サーバー エラー: 要求の処理中にデータベースの操作に失敗しました。](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="72659-139">アプリには、新しいユーザーの登録に使用した電子メールと **[ログアウト]** リンクが表示されます。</span><span class="sxs-lookup"><span data-stu-id="72659-139">The app displays the email used to register the new user and a **Log out** link.</span></span>

![Microsoft Edge で開いている Web アプリケーション。](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="72659-142">Azure にアプリを配置する</span><span class="sxs-lookup"><span data-stu-id="72659-142">Deploy the app to Azure</span></span>

<span data-ttu-id="72659-143">ソリューション エクスプローラーでプロジェクトを右クリックし、 **[発行]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="72659-143">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![[発行] リンクが選択された状態でコンテキスト メニューが開きます](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="72659-145">**[発行]** ダイアログで、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="72659-145">In the **Publish** dialog:</span></span>

* <span data-ttu-id="72659-146">**[Microsoft Azure App Service]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="72659-146">Select **Microsoft Azure App Service**.</span></span>
* <span data-ttu-id="72659-147">歯車アイコンを選択してから **[プロファイルの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="72659-147">Select the gear icon and then select **Create Profile**.</span></span>
* <span data-ttu-id="72659-148">**[プロファイルの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="72659-148">Select **Create Profile**.</span></span>

![[発行] ダイアログ](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a><span data-ttu-id="72659-150">Azure リソースを作成する</span><span class="sxs-lookup"><span data-stu-id="72659-150">Create Azure resources</span></span>

<span data-ttu-id="72659-151">**[App Service の作成]** ダイアログが表示されます。</span><span class="sxs-lookup"><span data-stu-id="72659-151">The **Create App Service** dialog appears:</span></span>

* <span data-ttu-id="72659-152">ご自分のサブスクリプションを入力します。</span><span class="sxs-lookup"><span data-stu-id="72659-152">Enter your subscription.</span></span>
* <span data-ttu-id="72659-153">**[アプリ名]** 、 **[リソース グループ]** 、 **[App Service プラン]** の各入力フィールドに値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="72659-153">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="72659-154">これらの名前を保持することも、変更することもできます。</span><span class="sxs-lookup"><span data-stu-id="72659-154">You can keep these names or change them.</span></span>

![[App Service] ダイアログ](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* <span data-ttu-id="72659-156">**[サービス]** タブを選択して、新しいデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="72659-156">Select the **Services** tab to create a new database.</span></span>

* <span data-ttu-id="72659-157">緑色の **+** アイコンを選択して新しい SQL Database を作成します。</span><span class="sxs-lookup"><span data-stu-id="72659-157">Select the green **+** icon to create a new SQL Database</span></span>

![新しい SQL Database](publish-to-azure-webapp-using-vs/_static/sql.png)

* <span data-ttu-id="72659-159">**[SQL Database の構成]** ダイアログの **[新規作成]** を選択して新しいデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="72659-159">Select **New...** on the **Configure SQL Database** dialog to create a new database.</span></span>

![新しい SQL Database とサーバー](publish-to-azure-webapp-using-vs/_static/conf.png)

<span data-ttu-id="72659-161">**[SQL Server の構成]** ダイアログが表示されます。</span><span class="sxs-lookup"><span data-stu-id="72659-161">The **Configure SQL Server** dialog appears.</span></span>

* <span data-ttu-id="72659-162">管理者のユーザー名とパスワードを入力し、 **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="72659-162">Enter an administrator user name and password, and then select **OK**.</span></span> <span data-ttu-id="72659-163">既定の **[サーバー名]** をそのまま使用できます。</span><span class="sxs-lookup"><span data-stu-id="72659-163">You can keep the default **Server Name**.</span></span> 

> [!NOTE]
> <span data-ttu-id="72659-164">管理者のユーザー名に "admin" は使用できません。</span><span class="sxs-lookup"><span data-stu-id="72659-164">"admin" isn't allowed as the administrator user name.</span></span>

![[SQL Server の構成] ダイアログ](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* <span data-ttu-id="72659-166">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="72659-166">Select **OK**.</span></span>

<span data-ttu-id="72659-167">Visual Studio が **[App Service の作成]** ダイアログに戻ります。</span><span class="sxs-lookup"><span data-stu-id="72659-167">Visual Studio returns to the **Create App Service** dialog.</span></span>

* <span data-ttu-id="72659-168">**[App Service の作成]** ダイアログで **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="72659-168">Select **Create** on the **Create App Service** dialog.</span></span>

![[SQL Database の構成] ダイアログ](publish-to-azure-webapp-using-vs/_static/conf_final.png)

<span data-ttu-id="72659-170">Visual Studio は、Azure で Web アプリと SQL Server を作成します。</span><span class="sxs-lookup"><span data-stu-id="72659-170">Visual Studio creates the Web app and SQL Server on Azure.</span></span> <span data-ttu-id="72659-171">このステップには数分かかる場合があります。</span><span class="sxs-lookup"><span data-stu-id="72659-171">This step can take a few minutes.</span></span> <span data-ttu-id="72659-172">作成されるリソースについては、「[追加のリソース](#additional-resources)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="72659-172">For information on the resources created, see [Additional resources](#additional-resources).</span></span>

<span data-ttu-id="72659-173">配置が完了したら、 **[設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="72659-173">When deployment completes, select **Settings**:</span></span>

![[SQL Server の構成] ダイアログ](publish-to-azure-webapp-using-vs/_static/set.png)

<span data-ttu-id="72659-175">**[発行]** ダイアログの **[設定]** ページで次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="72659-175">On the **Settings** page of the **Publish** dialog:</span></span>

* <span data-ttu-id="72659-176">**[データベース]** を展開し、 **[この接続文字列を実行時に使用する]** をオンにします。</span><span class="sxs-lookup"><span data-stu-id="72659-176">Expand **Databases** and check **Use this connection string at runtime**.</span></span>
* <span data-ttu-id="72659-177">**[Entity Framework の移行]** を展開し、 **[発行時にこの移行を適用する]** をオンにします。</span><span class="sxs-lookup"><span data-stu-id="72659-177">Expand **Entity Framework Migrations** and check **Apply this migration on publish**.</span></span>

* <span data-ttu-id="72659-178">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="72659-178">Select **Save**.</span></span> <span data-ttu-id="72659-179">Visual Studio が **[発行]** ダイアログに戻ります。</span><span class="sxs-lookup"><span data-stu-id="72659-179">Visual Studio returns to the **Publish** dialog.</span></span> 

![[発行] ダイアログ:[設定] パネル](publish-to-azure-webapp-using-vs/_static/pubs.png)

<span data-ttu-id="72659-181">**[発行]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="72659-181">Click **Publish**.</span></span> <span data-ttu-id="72659-182">Visual Studio が Azure にアプリを発行します。</span><span class="sxs-lookup"><span data-stu-id="72659-182">Visual Studio publishes your app to Azure.</span></span> <span data-ttu-id="72659-183">デプロイが完了すると、ブラウザーでアプリが開きます。</span><span class="sxs-lookup"><span data-stu-id="72659-183">When the deployment completes, the app is opened in a browser.</span></span>

### <a name="test-your-app-in-azure"></a><span data-ttu-id="72659-184">Azure でアプリをテストする</span><span class="sxs-lookup"><span data-stu-id="72659-184">Test your app in Azure</span></span>

* <span data-ttu-id="72659-185">**[About]** リンクと **[Contact]** リンクをテストします</span><span class="sxs-lookup"><span data-stu-id="72659-185">Test the **About** and **Contact** links</span></span>

* <span data-ttu-id="72659-186">新しいユーザーを登録します</span><span class="sxs-lookup"><span data-stu-id="72659-186">Register a new user</span></span>

![Microsoft Edge で開かれている Azure App Service の Web アプリケーション](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a><span data-ttu-id="72659-188">アプリを更新する</span><span class="sxs-lookup"><span data-stu-id="72659-188">Update the app</span></span>

* <span data-ttu-id="72659-189">*Pages/About.cshtml* Razor ページを編集して、その内容を変更します。</span><span class="sxs-lookup"><span data-stu-id="72659-189">Edit the *Pages/About.cshtml* Razor page and change its contents.</span></span> <span data-ttu-id="72659-190">たとえば、"Hello ASP.NET Core!" と表示されるように段落を修正できます。</span><span class="sxs-lookup"><span data-stu-id="72659-190">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* <span data-ttu-id="72659-191">プロジェクトを右クリックし、 **[発行]** をもう一度選択します。</span><span class="sxs-lookup"><span data-stu-id="72659-191">Right-click on the project and select **Publish...** again.</span></span>

![[発行] リンクが選択された状態でコンテキスト メニューが開きます](publish-to-azure-webapp-using-vs/_static/pub.png)

* <span data-ttu-id="72659-193">アプリが発行されたら、Azure に変更内容が反映されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="72659-193">After the app is published, verify the changes you made are available on Azure.</span></span>

![タスクの完了を確認します。](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="72659-195">クリーンアップ</span><span class="sxs-lookup"><span data-stu-id="72659-195">Clean up</span></span>

<span data-ttu-id="72659-196">アプリのテストが完了したら、[Azure Portal](https://portal.azure.com/) に移動し、アプリを削除します。</span><span class="sxs-lookup"><span data-stu-id="72659-196">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="72659-197">**[リソース グループ]** を選択し、作成したリソース グループを選択します。</span><span class="sxs-lookup"><span data-stu-id="72659-197">Select **Resource groups**, then select the resource group you created.</span></span>

![Azure Portal: サイドバー メニューの [リソース グループ]](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="72659-199">**[リソース グループ]** ページで、 **[削除]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="72659-199">In the **Resource groups** page, select **Delete**.</span></span>

![Azure Portal:[リソース グループ] ページ](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="72659-201">リソース グループ名を入力し、 **[削除]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="72659-201">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="72659-202">このチュートリアルで作成されたアプリとその他すべてのリソースが Azure から削除されます。</span><span class="sxs-lookup"><span data-stu-id="72659-202">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="72659-203">次の手順</span><span class="sxs-lookup"><span data-stu-id="72659-203">Next steps</span></span>

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="72659-204">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="72659-204">Additional resources</span></span>

* <span data-ttu-id="72659-205">Visual Studio Code については、「[発行プロファイル](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="72659-205">For Visual Studio Code, see [Publish profiles](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span></span>
* [<span data-ttu-id="72659-206">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="72659-206">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="72659-207">Azure リソース グループ</span><span class="sxs-lookup"><span data-stu-id="72659-207">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [<span data-ttu-id="72659-208">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="72659-208">Azure SQL Database</span></span>](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>
