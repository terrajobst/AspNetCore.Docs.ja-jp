---
title: App Service - ASP.NET Core および Azure を使用した DevOps へアプリをデプロイします。
author: CamSoper
description: ASP.NET Core アプリのデプロイを Azure App Service に ASP.NET Core および Azure を使用した DevOps の最初の手順。
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: df41f296e9c4e1eff6e31d45b29ec30ee1e20cf4
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080436"
---
# <a name="deploy-an-app-to-app-service"></a><span data-ttu-id="56ab6-103">App Service にアプリをデプロイします。</span><span class="sxs-lookup"><span data-stu-id="56ab6-103">Deploy an app to App Service</span></span>

<span data-ttu-id="56ab6-104">[Azure App Service](/azure/app-service/)は Azure の web ホスティング プラットフォーム。</span><span class="sxs-lookup"><span data-stu-id="56ab6-104">[Azure App Service](/azure/app-service/) is Azure's web hosting platform.</span></span> <span data-ttu-id="56ab6-105">手動または自動のプロセスによって、web アプリを Azure App Service にデプロイを実行できます。</span><span class="sxs-lookup"><span data-stu-id="56ab6-105">Deploying a web app to Azure App Service can be done manually or by an automated process.</span></span> <span data-ttu-id="56ab6-106">このガイドのこのセクションでは、手動か、コマンドラインを使用してスクリプトをトリガーできるまたは Visual Studio を使用して手動でトリガーされた配置方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-106">This section of the guide discusses deployment methods that can be triggered manually or by script using the command line, or triggered manually using Visual Studio.</span></span>

<span data-ttu-id="56ab6-107">このセクションでは、次のタスクを実行します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-107">In this section, you'll accomplish the following tasks:</span></span>

* <span data-ttu-id="56ab6-108">ダウンロードして、サンプル アプリをビルドします。</span><span class="sxs-lookup"><span data-stu-id="56ab6-108">Download and build the sample app.</span></span>
* <span data-ttu-id="56ab6-109">Azure Cloud Shell を使用して Azure App Service Web アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-109">Create an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="56ab6-110">Git を使用して Azure にサンプル アプリをデプロイします。</span><span class="sxs-lookup"><span data-stu-id="56ab6-110">Deploy the sample app to Azure using Git.</span></span>
* <span data-ttu-id="56ab6-111">Visual Studio を使用してアプリに変更をデプロイします。</span><span class="sxs-lookup"><span data-stu-id="56ab6-111">Deploy a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="56ab6-112">Web アプリには、ステージング スロットを追加します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-112">Add a staging slot to the web app.</span></span>
* <span data-ttu-id="56ab6-113">ステージング スロットに更新プログラムを展開します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-113">Deploy an update to the staging slot.</span></span>
* <span data-ttu-id="56ab6-114">ステージングと運用スロットをスワップします。</span><span class="sxs-lookup"><span data-stu-id="56ab6-114">Swap the staging and production slots.</span></span>

## <a name="download-and-test-the-app"></a><span data-ttu-id="56ab6-115">ダウンロードして、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="56ab6-115">Download and test the app</span></span>

<span data-ttu-id="56ab6-116">このガイドで使用されるアプリは、構築済みの ASP.NET Core アプリ[単純なフィード リーダー](https://github.com/Azure-Samples/simple-feed-reader/)します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-116">The app used in this guide is a pre-built ASP.NET Core app, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/).</span></span> <span data-ttu-id="56ab6-117">使用する Razor ページ アプリは、 `Microsoft.SyndicationFeed.ReaderWriter` API を RSS フィードおよび Atom フィードを取得し、ニュース項目を一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-117">It's a Razor Pages app that uses the `Microsoft.SyndicationFeed.ReaderWriter` API to retrieve an RSS/Atom feed and display the news items in a list.</span></span>

<span data-ttu-id="56ab6-118">自由に、コードのレビューがこのアプリに関する特別なものを理解することが重要です。</span><span class="sxs-lookup"><span data-stu-id="56ab6-118">Feel free to review the code, but it's important to understand that there's nothing special about this app.</span></span> <span data-ttu-id="56ab6-119">説明の目的で、簡単な ASP.NET Core アプリだけです。</span><span class="sxs-lookup"><span data-stu-id="56ab6-119">It's just a simple ASP.NET Core app for illustrative purposes.</span></span>

<span data-ttu-id="56ab6-120">コマンド シェルからコードをダウンロードしてプロジェクトをビルドして、次のように実行します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-120">From a command shell, download the code, build the project, and run it as follows.</span></span>

> <span data-ttu-id="56ab6-121">*注:Linux/macOS ユーザーは、パスに対して適切な変更を行う必要があり`/`ます。たとえば、スラッシュ (`\`) ではなくスラッシュ () を使用します。*</span><span class="sxs-lookup"><span data-stu-id="56ab6-121">*Note: Linux/macOS users should make appropriate changes for paths, e.g., using forward slash (`/`) rather than back slash (`\`).*</span></span>

1. <span data-ttu-id="56ab6-122">ローカル コンピューター上のフォルダーにコードを複製します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-122">Clone the code to a folder on your local machine.</span></span>

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. <span data-ttu-id="56ab6-123">作業フォルダーの変更、*単純なフィード-リーダー*が作成されたフォルダーです。</span><span class="sxs-lookup"><span data-stu-id="56ab6-123">Change your working folder to the *simple-feed-reader* folder that was created.</span></span>

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. <span data-ttu-id="56ab6-124">パッケージを復元し、ソリューションをビルドします。</span><span class="sxs-lookup"><span data-stu-id="56ab6-124">Restore the packages, and build the solution.</span></span>

    ```dotnetcli
    dotnet build
    ```

4. <span data-ttu-id="56ab6-125">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-125">Run the app.</span></span>

    ```dotnetcli
    dotnet run
    ```

    ![Dotnet run コマンドが正常に完了](./media/deploying-to-app-service/dotnet-run.png)

5. <span data-ttu-id="56ab6-127">ブラウザーを開きに移動します`http://localhost:5000`します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-127">Open a browser and navigate to `http://localhost:5000`.</span></span> <span data-ttu-id="56ab6-128">アプリでは、入力するか、配信フィードの URL を貼り付けることができ、ニュース アイテムの一覧を表示します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-128">The app allows you to type or paste a syndication feed URL and view a list of news items.</span></span>

     ![RSS フィードの内容を表示するアプリ](./media/deploying-to-app-service/app-in-browser.png)

6. <span data-ttu-id="56ab6-130">アプリが正常に動作がなければ、キーを押してシャット ダウン**Ctrl**+**C**コマンド シェルでします。</span><span class="sxs-lookup"><span data-stu-id="56ab6-130">Once you're satisfied the app is working correctly, shut it down by pressing **Ctrl**+**C** in the command shell.</span></span>

## <a name="create-the-azure-app-service-web-app"></a><span data-ttu-id="56ab6-131">Azure App Service Web アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-131">Create the Azure App Service Web App</span></span>

<span data-ttu-id="56ab6-132">アプリを展開するには、アプリ サービスを作成する必要があります[Web アプリ](/azure/app-service/app-service-web-overview)します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-132">To deploy the app, you'll need to create an App Service [Web App](/azure/app-service/app-service-web-overview).</span></span> <span data-ttu-id="56ab6-133">Web アプリの作成後は、Git を使用して、ローカル コンピューターからをデプロイします。</span><span class="sxs-lookup"><span data-stu-id="56ab6-133">After creation of the Web App, you'll deploy to it from your local machine using Git.</span></span>

1. <span data-ttu-id="56ab6-134">サインイン、 [Azure Cloud Shell](https://shell.azure.com/bash)します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-134">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash).</span></span> <span data-ttu-id="56ab6-135">メモ:初めてサインインするときに、構成ファイル用のストレージアカウントを作成するように Cloud Shell プロンプトが表示されます。</span><span class="sxs-lookup"><span data-stu-id="56ab6-135">Note: When you sign in for the first time, Cloud Shell prompts to create a storage account for configuration files.</span></span> <span data-ttu-id="56ab6-136">既定値を受け入れるか、一意の名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-136">Accept the defaults or provide a unique name.</span></span>

2. <span data-ttu-id="56ab6-137">次の手順については、Cloud Shell を使用します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-137">Use the Cloud Shell for the following steps.</span></span>

    <span data-ttu-id="56ab6-138">a.</span><span class="sxs-lookup"><span data-stu-id="56ab6-138">a.</span></span> <span data-ttu-id="56ab6-139">Web アプリの名前を格納する変数を宣言します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-139">Declare a variable to store your web app's name.</span></span> <span data-ttu-id="56ab6-140">名前は、既定の URL で使用される一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="56ab6-140">The name must be unique to be used in the default URL.</span></span> <span data-ttu-id="56ab6-141">使用して、`$RANDOM`名前を作成する Bash 関数は、一意性を保証しの形式で結果`webappname99999`します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-141">Using the `$RANDOM` Bash function to construct the name guarantees uniqueness and results in the format `webappname99999`.</span></span>

    ```console
    webappname=mywebapp$RANDOM
    ```

    <span data-ttu-id="56ab6-142">b.</span><span class="sxs-lookup"><span data-stu-id="56ab6-142">b.</span></span> <span data-ttu-id="56ab6-143">リソース グループを作成します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-143">Create a resource group.</span></span> <span data-ttu-id="56ab6-144">リソース グループは、グループとして管理する Azure リソースを集計するための手段を提供します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-144">Resource groups provide a means to aggregate Azure resources to be managed as a group.</span></span>

    ```azure-cli
    az group create --location centralus --name AzureTutorial
    ```

    <span data-ttu-id="56ab6-145">`az`コマンドを呼び出す、 [Azure CLI](/cli/azure/)します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-145">The `az` command invokes the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="56ab6-146">CLI をローカルで実行することができますが、Cloud Shell で使用する時間と構成を保存します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-146">The CLI can be run locally, but using it in the Cloud Shell saves time and configuration.</span></span>

    <span data-ttu-id="56ab6-147">c.</span><span class="sxs-lookup"><span data-stu-id="56ab6-147">c.</span></span> <span data-ttu-id="56ab6-148">S1 レベルでは、App Service プランを作成します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-148">Create an App Service plan in the S1 tier.</span></span> <span data-ttu-id="56ab6-149">App Service プランは、同じ価格レベルを共有する web アプリのグループです。</span><span class="sxs-lookup"><span data-stu-id="56ab6-149">An App Service plan is a grouping of web apps that share the same pricing tier.</span></span> <span data-ttu-id="56ab6-150">S1 レベルが無料ではありませんが、ステージング スロットの機能は必要です。</span><span class="sxs-lookup"><span data-stu-id="56ab6-150">The S1 tier isn't free, but it's required for the staging slots feature.</span></span>

    ```azure-cli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    <span data-ttu-id="56ab6-151">d.</span><span class="sxs-lookup"><span data-stu-id="56ab6-151">d.</span></span> <span data-ttu-id="56ab6-152">同じリソース グループで、App Service プランを使用して web アプリのリソースを作成します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-152">Create the web app resource using the App Service plan in the same resource group.</span></span>

    ```azure-cli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    <span data-ttu-id="56ab6-153">e.</span><span class="sxs-lookup"><span data-stu-id="56ab6-153">e.</span></span> <span data-ttu-id="56ab6-154">デプロイ資格情報を設定します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-154">Set the deployment credentials.</span></span> <span data-ttu-id="56ab6-155">これらのデプロイ資格情報は、サブスクリプション内のすべての web アプリに適用されます。</span><span class="sxs-lookup"><span data-stu-id="56ab6-155">These deployment credentials apply to all the web apps in your subscription.</span></span> <span data-ttu-id="56ab6-156">ユーザー名に特殊文字を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="56ab6-156">Don't use special characters in the user name.</span></span>

    ```azure-cli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    <span data-ttu-id="56ab6-157">f.</span><span class="sxs-lookup"><span data-stu-id="56ab6-157">f.</span></span> <span data-ttu-id="56ab6-158">ローカルの Git および表示から展開を受け入れるように web アプリの構成、 *Git デプロイメント URL*します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-158">Configure the web app to accept deployments from local Git and display the *Git deployment URL*.</span></span> <span data-ttu-id="56ab6-159">**後で参照に対して、この URL に注意してください**します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-159">**Note this URL for reference later**.</span></span>

    ```azure-cli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    <span data-ttu-id="56ab6-160">g.</span><span class="sxs-lookup"><span data-stu-id="56ab6-160">g.</span></span> <span data-ttu-id="56ab6-161">表示、 *web アプリの URL*します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-161">Display the *web app URL*.</span></span> <span data-ttu-id="56ab6-162">空の web アプリを確認するには、この URL に移動します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-162">Browse to this URL to see the blank web app.</span></span> <span data-ttu-id="56ab6-163">**後で参照に対して、この URL に注意してください**します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-163">**Note this URL for reference later**.</span></span>

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. <span data-ttu-id="56ab6-164">コマンド シェルを使用して、ローカル コンピューターで web アプリのプロジェクト フォルダーに移動します (たとえば、 `.\simple-feed-reader\SimpleFeedReader`)。</span><span class="sxs-lookup"><span data-stu-id="56ab6-164">Using a command shell on your local machine, navigate to the web app's project folder (for example, `.\simple-feed-reader\SimpleFeedReader`).</span></span> <span data-ttu-id="56ab6-165">デプロイの URL にプッシュする Git のセットアップには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-165">Execute the following commands to set up Git to push to the deployment URL:</span></span>

    <span data-ttu-id="56ab6-166">a.</span><span class="sxs-lookup"><span data-stu-id="56ab6-166">a.</span></span> <span data-ttu-id="56ab6-167">ローカル リポジトリには、リモートの URL を追加します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-167">Add the remote URL to the local repository.</span></span>

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    <span data-ttu-id="56ab6-168">b.</span><span class="sxs-lookup"><span data-stu-id="56ab6-168">b.</span></span> <span data-ttu-id="56ab6-169">ローカルをプッシュ*マスター*に分岐、 *azure prod*リモートの*マスター*分岐します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-169">Push the local *master* branch to the *azure-prod* remote's *master* branch.</span></span>

    ```console
    git push azure-prod master
    ```

    <span data-ttu-id="56ab6-170">先ほど作成したデプロイ資格情報を求め。</span><span class="sxs-lookup"><span data-stu-id="56ab6-170">You'll be prompted for the deployment credentials you created earlier.</span></span> <span data-ttu-id="56ab6-171">コマンド シェルに出力を確認します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-171">Observe the output in the command shell.</span></span> <span data-ttu-id="56ab6-172">Azure では、ASP.NET Core アプリをリモートでビルドします。</span><span class="sxs-lookup"><span data-stu-id="56ab6-172">Azure builds the ASP.NET Core app remotely.</span></span>

4. <span data-ttu-id="56ab6-173">ブラウザーに移動します。、 *Web アプリの URL*と、アプリがビルドおよびデプロイされたに注意してください。</span><span class="sxs-lookup"><span data-stu-id="56ab6-173">In a browser, navigate to the *Web app URL* and note the app has been built and deployed.</span></span> <span data-ttu-id="56ab6-174">追加の変更がローカル Git リポジトリにコミットできる`git commit`します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-174">Additional changes can be committed to the local Git repository with `git commit`.</span></span> <span data-ttu-id="56ab6-175">これらの変更は、上記の Azure にプッシュされる`git push`コマンド。</span><span class="sxs-lookup"><span data-stu-id="56ab6-175">These changes are pushed to Azure with the preceding `git push` command.</span></span>

## <a name="deployment-with-visual-studio"></a><span data-ttu-id="56ab6-176">Visual Studio でのデプロイ</span><span class="sxs-lookup"><span data-stu-id="56ab6-176">Deployment with Visual Studio</span></span>

> <span data-ttu-id="56ab6-177">*注:このセクションは、Windows のみに適用されます。Linux および macOS ユーザーには、次のステップ 2 で説明されている変更を加える必要があります。ファイルを保存しをローカル リポジトリに変更をコミット`git commit`します。最後に、変更をプッシュ`git push`最初のセクションのようにします。*</span><span class="sxs-lookup"><span data-stu-id="56ab6-177">*Note: This section applies to Windows only. Linux and macOS users should make the change described in step 2 below. Save the file, and commit the change to the local repository with `git commit`. Finally, push the change with `git push`, as in the first section.*</span></span>

<span data-ttu-id="56ab6-178">コマンド シェルから、アプリは既に展開されています。</span><span class="sxs-lookup"><span data-stu-id="56ab6-178">The app has already been deployed from the command shell.</span></span> <span data-ttu-id="56ab6-179">Visual Studio の統合ツールを使用して、アプリに更新プログラムをデプロイしましょう。</span><span class="sxs-lookup"><span data-stu-id="56ab6-179">Let's use Visual Studio's integrated tools to deploy an update to the app.</span></span> <span data-ttu-id="56ab6-180">バック グラウンドでは、Visual Studio には、Visual Studio の使い慣れた UI 内でコマンド ライン ツールと同じことが実現されます。</span><span class="sxs-lookup"><span data-stu-id="56ab6-180">Behind the scenes, Visual Studio accomplishes the same thing as the command line tooling, but within Visual Studio's familiar UI.</span></span>

1. <span data-ttu-id="56ab6-181">開いている*SimpleFeedReader.sln* Visual Studio でします。</span><span class="sxs-lookup"><span data-stu-id="56ab6-181">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
2. <span data-ttu-id="56ab6-182">ソリューション エクスプ ローラーで開く*Pages\Index.cshtml*します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-182">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="56ab6-183">変更`<h2>Simple Feed Reader</h2>`に`<h2>Simple Feed Reader - V2</h2>`します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-183">Change `<h2>Simple Feed Reader</h2>` to `<h2>Simple Feed Reader - V2</h2>`.</span></span>
3. <span data-ttu-id="56ab6-184">キーを押して**Ctrl**+**Shift**+**B**アプリをビルドします。</span><span class="sxs-lookup"><span data-stu-id="56ab6-184">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
4. <span data-ttu-id="56ab6-185">ソリューション エクスプ ローラーでプロジェクトを右クリックし、をクリックして**発行**します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-185">In Solution Explorer, right-click on the project and click **Publish**.</span></span>

    ![右クリックし、発行を示すスクリーン ショット](./media/deploying-to-app-service/publish.png)
5. <span data-ttu-id="56ab6-187">Visual Studio は、新しい App Service リソースを作成できますが、この更新プログラムは、既存のデプロイで公開されます。</span><span class="sxs-lookup"><span data-stu-id="56ab6-187">Visual Studio can create a new App Service resource, but this update will be published over the existing deployment.</span></span> <span data-ttu-id="56ab6-188">**発行先を選択**ダイアログ ボックスで、 **App Service** 、左側のリストから選び**既存の**。</span><span class="sxs-lookup"><span data-stu-id="56ab6-188">In the **Pick a publish target** dialog, select **App Service** from the list on the left, and then select **Select Existing**.</span></span> <span data-ttu-id="56ab6-189">**[発行]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="56ab6-189">Click **Publish**.</span></span>
6. <span data-ttu-id="56ab6-190">**App Service**ダイアログ ボックスで、Microsoft または組織アカウントを Azure サブスクリプションを作成するために使用が右上に表示されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-190">In the **App Service** dialog, confirm that the Microsoft or Organizational account used to create your Azure subscription is displayed in the upper right.</span></span> <span data-ttu-id="56ab6-191">ない場合、ドロップダウン リストをクリックし、追加します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-191">If it's not, click the drop-down and add it.</span></span>
7. <span data-ttu-id="56ab6-192">いることを確認、適切な Azure**サブスクリプション**が選択されています。</span><span class="sxs-lookup"><span data-stu-id="56ab6-192">Confirm that the correct Azure **Subscription** is selected.</span></span> <span data-ttu-id="56ab6-193">**ビュー**、**リソース グループ**します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-193">For **View**, select **Resource Group**.</span></span> <span data-ttu-id="56ab6-194">展開、 **azure チュートリアル**リソース グループと、既存の web アプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-194">Expand the **AzureTutorial** resource group and then select the existing web app.</span></span> <span data-ttu-id="56ab6-195">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="56ab6-195">Click **OK**.</span></span>

    ![App Service の発行 ダイアログのスクリーン ショット](./media/deploying-to-app-service/publish-dialog.png)

<span data-ttu-id="56ab6-197">Visual Studio がビルドされ、アプリを Azure にデプロイします。</span><span class="sxs-lookup"><span data-stu-id="56ab6-197">Visual Studio builds and deploys the app to Azure.</span></span> <span data-ttu-id="56ab6-198">Web アプリの URL に移動します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-198">Browse to the web app URL.</span></span> <span data-ttu-id="56ab6-199">いることを確認、`<h2>`要素の変更がライブです。</span><span class="sxs-lookup"><span data-stu-id="56ab6-199">Validate that the `<h2>` element modification is live.</span></span>

![変更されたタイトルとアプリ](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a><span data-ttu-id="56ab6-201">デプロイ スロット</span><span class="sxs-lookup"><span data-stu-id="56ab6-201">Deployment slots</span></span>

<span data-ttu-id="56ab6-202">デプロイ スロットでは、実稼働環境で実行されているアプリに影響を与えることがなく変更のステージングをサポートします。</span><span class="sxs-lookup"><span data-stu-id="56ab6-202">Deployment slots support the staging of changes without impacting the app running in production.</span></span> <span data-ttu-id="56ab6-203">ステージングされたバージョンのアプリの品質保証チームによって検証されると、運用スロットとステージング スロットをスワップすることができます。</span><span class="sxs-lookup"><span data-stu-id="56ab6-203">Once the staged version of the app is validated by a quality assurance team, the production and staging slots can be swapped.</span></span> <span data-ttu-id="56ab6-204">ステージング環境でのアプリは、この方法で運用環境に昇格されます。</span><span class="sxs-lookup"><span data-stu-id="56ab6-204">The app in staging is promoted to production in this manner.</span></span> <span data-ttu-id="56ab6-205">次の手順は、ステージング スロットを作成、いくつかの変更に配置し、検証後の本番環境とステージング スロットをスワップします。</span><span class="sxs-lookup"><span data-stu-id="56ab6-205">The following steps create a staging slot, deploy some changes to it, and swap the staging slot with production after verification.</span></span>

1. <span data-ttu-id="56ab6-206">サインイン、 [Azure Cloud Shell](https://shell.azure.com/bash)署名されていない場合は、します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-206">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash), if not already signed in.</span></span>
2. <span data-ttu-id="56ab6-207">ステージング スロットを作成します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-207">Create the staging slot.</span></span>

    <span data-ttu-id="56ab6-208">a.</span><span class="sxs-lookup"><span data-stu-id="56ab6-208">a.</span></span> <span data-ttu-id="56ab6-209">デプロイ スロットを作成、名前を持つ*ステージング*します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-209">Create a deployment slot with the name *staging*.</span></span>

    ```azure-cli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    <span data-ttu-id="56ab6-210">b.</span><span class="sxs-lookup"><span data-stu-id="56ab6-210">b.</span></span> <span data-ttu-id="56ab6-211">ローカルの Git および get からのデプロイを使用するステージング スロットの構成、**ステージング**デプロイの URL。</span><span class="sxs-lookup"><span data-stu-id="56ab6-211">Configure the staging slot to use deployment from local Git and get the **staging** deployment URL.</span></span> <span data-ttu-id="56ab6-212">**後で参照に対して、この URL に注意してください**します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-212">**Note this URL for reference later**.</span></span>

    ```azure-cli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    <span data-ttu-id="56ab6-213">c.</span><span class="sxs-lookup"><span data-stu-id="56ab6-213">c.</span></span> <span data-ttu-id="56ab6-214">ステージング スロットの URL を表示します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-214">Display the staging slot's URL.</span></span> <span data-ttu-id="56ab6-215">空のステージング スロットを表示する URL に移動します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-215">Browse to the URL to see the empty staging slot.</span></span> <span data-ttu-id="56ab6-216">**後で参照に対して、この URL に注意してください**します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-216">**Note this URL for reference later**.</span></span>

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. <span data-ttu-id="56ab6-217">テキスト エディターまたは Visual Studio では、次のように変更します。 *Pages/Index.cshtml*もう一度ように、`<h2>`要素を読み取ります`<h2>Simple Feed Reader - V3</h2>`ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-217">In a text editor or Visual Studio, modify *Pages/Index.cshtml* again so that the `<h2>` element reads `<h2>Simple Feed Reader - V3</h2>` and save the file.</span></span>

4. <span data-ttu-id="56ab6-218">いずれかを使用して、ローカルの Git リポジトリにファイルをコミット、**変更**Visual studio のページ*チーム エクスプ ローラー*  タブで、または入力して、次を使用して、ローカル コンピューターのコマンド シェル。</span><span class="sxs-lookup"><span data-stu-id="56ab6-218">Commit the file to the local Git repository, using either the **Changes** page in Visual Studio's *Team Explorer* tab, or by entering the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. <span data-ttu-id="56ab6-219">ローカル コンピューターのコマンド シェルを使用してでは、Git リモートとしてステージング配置の URL を追加し、コミットされた変更をプッシュします。</span><span class="sxs-lookup"><span data-stu-id="56ab6-219">Using the local machine's command shell, add the staging deployment URL as a Git remote and push the committed changes:</span></span>

    <span data-ttu-id="56ab6-220">a.</span><span class="sxs-lookup"><span data-stu-id="56ab6-220">a.</span></span> <span data-ttu-id="56ab6-221">ローカルの Git リポジトリには、ステージング用のリモート URL を追加します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-221">Add the remote URL for staging to the local Git repository.</span></span>

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    <span data-ttu-id="56ab6-222">b.</span><span class="sxs-lookup"><span data-stu-id="56ab6-222">b.</span></span> <span data-ttu-id="56ab6-223">ローカルをプッシュ*マスター*に分岐、 *azure ステージング*リモートの*マスター*分岐します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-223">Push the local *master* branch to the *azure-staging* remote's *master* branch.</span></span>

    ```console
    git push azure-staging master
    ```

    <span data-ttu-id="56ab6-224">Azure の構築し、アプリの配置中には、待機します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-224">Wait while Azure builds and deploys the app.</span></span>

6. <span data-ttu-id="56ab6-225">V3 がステージング スロットにデプロイされていることを確認するには、2 つのブラウザー ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="56ab6-225">To verify that V3 has been deployed to the staging slot, open two browser windows.</span></span> <span data-ttu-id="56ab6-226">1 つのウィンドウでは、元の web アプリの URL に移動します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-226">In one window, navigate to the original web app URL.</span></span> <span data-ttu-id="56ab6-227">その他のウィンドウでは、ステージング web アプリの URL に移動します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-227">In the other window, navigate to the staging web app URL.</span></span> <span data-ttu-id="56ab6-228">運用環境の URL では、アプリの V2 は機能します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-228">The production URL serves V2 of the app.</span></span> <span data-ttu-id="56ab6-229">ステージング URL では、アプリの V3 は機能します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-229">The staging URL serves V3 of the app.</span></span>

    ![ブラウザー ウィンドウを比較するスクリーン ショット](./media/deploying-to-app-service/ready-to-swap.png)

7. <span data-ttu-id="56ab6-231">Cloud Shell では、運用環境に検証/ウォーム アップのステージング スロットをスワップします。</span><span class="sxs-lookup"><span data-stu-id="56ab6-231">In the Cloud Shell, swap the verified/warmed-up staging slot into production.</span></span>

    ```azure-cli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. <span data-ttu-id="56ab6-232">2 つのブラウザー ウィンドウを更新することで、スワップが発生したことを確認します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-232">Verify that the swap occurred by refreshing the two browser windows.</span></span>

    ![スワップ後にブラウザー ウィンドウを比較します。](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a><span data-ttu-id="56ab6-234">まとめ</span><span class="sxs-lookup"><span data-stu-id="56ab6-234">Summary</span></span>

<span data-ttu-id="56ab6-235">このセクションで、次のタスクを完了しました。</span><span class="sxs-lookup"><span data-stu-id="56ab6-235">In this section, the following tasks were completed:</span></span>

* <span data-ttu-id="56ab6-236">ダウンロードして、サンプル アプリをビルドします。</span><span class="sxs-lookup"><span data-stu-id="56ab6-236">Downloaded and built the sample app.</span></span>
* <span data-ttu-id="56ab6-237">Azure Cloud Shell を使用して Azure App Service Web アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-237">Created an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="56ab6-238">サンプル アプリを Git を使用して Azure にデプロイします。</span><span class="sxs-lookup"><span data-stu-id="56ab6-238">Deployed the sample app to Azure using Git.</span></span>
* <span data-ttu-id="56ab6-239">Visual Studio を使用してアプリに変更をデプロイします。</span><span class="sxs-lookup"><span data-stu-id="56ab6-239">Deployed a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="56ab6-240">Web アプリには、ステージング スロットを追加します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-240">Added a staging slot to the web app.</span></span>
* <span data-ttu-id="56ab6-241">ステージング スロットに更新プログラムを展開します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-241">Deployed an update to the staging slot.</span></span>
* <span data-ttu-id="56ab6-242">ステージングと運用スロットをスワップします。</span><span class="sxs-lookup"><span data-stu-id="56ab6-242">Swapped the staging and production slots.</span></span>

<span data-ttu-id="56ab6-243">次のセクションでは、Azure のパイプラインを使用した DevOps パイプラインを構築する方法を学習します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-243">In the next section, you'll learn how to build a DevOps pipeline with Azure Pipelines.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="56ab6-244">その他の参考資料</span><span class="sxs-lookup"><span data-stu-id="56ab6-244">Additional reading</span></span>

* [<span data-ttu-id="56ab6-245">Web Apps の概要</span><span class="sxs-lookup"><span data-stu-id="56ab6-245">Web Apps overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="56ab6-246">Azure App Service で .NET Core および SQL Database web アプリを構築します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-246">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [<span data-ttu-id="56ab6-247">Azure App Service のデプロイ資格情報を構成します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-247">Configure deployment credentials for Azure App Service</span></span>](/azure/app-service/app-service-deployment-credentials)
* [<span data-ttu-id="56ab6-248">Azure App Service でステージング環境を設定します。</span><span class="sxs-lookup"><span data-stu-id="56ab6-248">Set up staging environments in Azure App Service</span></span>](/azure/app-service/web-sites-staged-publishing)
