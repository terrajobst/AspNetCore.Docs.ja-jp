---
title: Visual Studio および Git と ASP.NET Core を組み合わせた Azure への継続的配置
author: rick-anderson
description: Visual Studio で ASP.NET Core Web アプリを作成し、それを Azure App Service に配置する方法について説明します。Git を利用し、継続的に配置します。
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2018
uid: host-and-deploy/azure-apps/azure-continuous-deployment
ms.openlocfilehash: 3b344505739bb4292ed1683c73ff314b6e4e01e9
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78648110"
---
# <a name="continuous-deployment-to-azure-with-visual-studio-and-git-with-aspnet-core"></a><span data-ttu-id="d5e41-103">Visual Studio および Git と ASP.NET Core を組み合わせた Azure への継続的配置</span><span class="sxs-lookup"><span data-stu-id="d5e41-103">Continuous deployment to Azure with Visual Studio and Git with ASP.NET Core</span></span>

<span data-ttu-id="d5e41-104">作成者: [Erik Reitan](https://github.com/Erikre)</span><span class="sxs-lookup"><span data-stu-id="d5e41-104">By [Erik Reitan](https://github.com/Erikre)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="d5e41-105">このチュートリアルでは、Visual Studio で ASP.NET Core Web アプリを作成し、それを Visual Studio から Azure App Service に継続的に配置する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-105">This tutorial shows how to create an ASP.NET Core web app using Visual Studio and deploy it from Visual Studio to Azure App Service using continuous deployment.</span></span>

<span data-ttu-id="d5e41-106">[Azure Pipelines を使用した初めてのパイプラインの作成](/azure/devops/pipelines/get-started-yaml)に関する記事を参照してください。記事では、Azure DevOps Services を使用した [Azure App Service](/azure/app-service/app-service-web-overview) 向けの継続的デリバリー (CD) ワークフローの構成方法が示されています。</span><span class="sxs-lookup"><span data-stu-id="d5e41-106">See also [Create your first pipeline with Azure Pipelines](/azure/devops/pipelines/get-started-yaml), which shows how to configure a continuous delivery (CD) workflow for [Azure App Service](/azure/app-service/app-service-web-overview) using Azure DevOps Services.</span></span> <span data-ttu-id="d5e41-107">Azure Pipelines (Azure DevOps Services サービス) を利用すると、Azure App Service でホストされているアプリの更新プログラムを公開するための堅牢な配置パイプラインの設定が簡単になります。</span><span class="sxs-lookup"><span data-stu-id="d5e41-107">Azure Pipelines (an Azure DevOps Services service) simplifies setting up a robust deployment pipeline to publish updates for apps hosted in Azure App Service.</span></span> <span data-ttu-id="d5e41-108">Azure Portal で、このパイプラインのビルド、テスト実行、ステージング スロットへの配置、運用への配置を構成できます。</span><span class="sxs-lookup"><span data-stu-id="d5e41-108">The pipeline can be configured from the Azure portal to build, run tests, deploy to a staging slot, and then deploy to production.</span></span>

> [!NOTE]
> <span data-ttu-id="d5e41-109">このチュートリアルを完了するには、Microsoft Azure アカウントが必要です。</span><span class="sxs-lookup"><span data-stu-id="d5e41-109">To complete this tutorial, a Microsoft Azure account is required.</span></span> <span data-ttu-id="d5e41-110">アカウントを持っていない場合は、[MSDN サブスクライバー特典を有効にする](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F)か、[無料試用版にサインアップ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。</span><span class="sxs-lookup"><span data-stu-id="d5e41-110">To obtain an account, [activate MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) or [sign up for a free trial](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d5e41-111">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="d5e41-111">Prerequisites</span></span>

<span data-ttu-id="d5e41-112">このチュートリアルでは、次のソフトウェアがインストールされていることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="d5e41-112">This tutorial assumes the following software is installed:</span></span>

* [<span data-ttu-id="d5e41-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5e41-113">Visual Studio</span></span>](https://visualstudio.microsoft.com)
* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]
* <span data-ttu-id="d5e41-114">[Git](https://git-scm.com/downloads) for Windows</span><span class="sxs-lookup"><span data-stu-id="d5e41-114">[Git](https://git-scm.com/downloads) for Windows</span></span>

## <a name="create-an-aspnet-core-web-app"></a><span data-ttu-id="d5e41-115">ASP.NET Core Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="d5e41-115">Create an ASP.NET Core web app</span></span>

1. <span data-ttu-id="d5e41-116">Visual Studio を起動します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-116">Start Visual Studio.</span></span>

1. <span data-ttu-id="d5e41-117">**[ファイル]** メニューで **[新規作成]**  >  **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-117">From the **File** menu, select **New** > **Project**.</span></span>

1. <span data-ttu-id="d5e41-118">**[ASP.NET Core Web アプリケーション]** プロジェクト テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-118">Select the **ASP.NET Core Web Application** project template.</span></span> <span data-ttu-id="d5e41-119">このテンプレートは、 **[インストール済み]**  >  **[テンプレート]**  >  **[Visual C#]**  >  **[.NET Core]** の下にあります。</span><span class="sxs-lookup"><span data-stu-id="d5e41-119">It appears under **Installed** > **Templates** > **Visual C#** > **.NET Core**.</span></span> <span data-ttu-id="d5e41-120">プロジェクトに `SampleWebAppDemo` という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="d5e41-120">Name the project `SampleWebAppDemo`.</span></span> <span data-ttu-id="d5e41-121">**[新しい Git リポジトリの作成]** オプションを選択し、 **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="d5e41-121">Select the **Create new Git repository** option and click **OK**.</span></span>

   ![[新しいプロジェクト] ダイアログ](azure-continuous-deployment/_static/01-new-project.png)

1. <span data-ttu-id="d5e41-123">**[New ASP.NET Core Project]\(新しい ASP.NET Core プロジェクト\)** ダイアログで、ASP.NET Core の **[空]** テンプレートを選択し、 **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="d5e41-123">In the **New ASP.NET Core Project** dialog, select the ASP.NET Core **Empty** template, then click **OK**.</span></span>

   ![[新しい ASP.NET Core プロジェクト] ダイアログ](azure-continuous-deployment/_static/02-web-site-template.png)

> [!NOTE]
> <span data-ttu-id="d5e41-125">.NET Core の最新のリリースは 2.0 です。</span><span class="sxs-lookup"><span data-stu-id="d5e41-125">The most recent release of .NET Core is 2.0.</span></span>

### <a name="running-the-web-app-locally"></a><span data-ttu-id="d5e41-126">Web アプリのローカル実行</span><span class="sxs-lookup"><span data-stu-id="d5e41-126">Running the web app locally</span></span>

1. <span data-ttu-id="d5e41-127">Visual Studio でアプリの作成が完了したら、 **[デバッグ]** 、 **[デバッグの開始]** の順に選択し、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-127">Once Visual Studio finishes creating the app, run the app by selecting **Debug** > **Start Debugging**.</span></span> <span data-ttu-id="d5e41-128">または、**F5** キーを押します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-128">As an alternative, press **F5**.</span></span>

   <span data-ttu-id="d5e41-129">Visual Studio と新しいアプリの初期化に時間がかかる場合があります。</span><span class="sxs-lookup"><span data-stu-id="d5e41-129">It may take time to initialize Visual Studio and the new app.</span></span> <span data-ttu-id="d5e41-130">完了すると、ブラウザーに実行中のアプリが表示されます。</span><span class="sxs-lookup"><span data-stu-id="d5e41-130">Once it's complete, the browser shows the running app.</span></span>

   ![ブラウザー ウィンドウの実行中のアプリに 'Hello World!' と表示されます。](azure-continuous-deployment/_static/04-browser-runapp.png)

1. <span data-ttu-id="d5e41-132">実行中の Web アプリを確認したら、ブラウザーを閉じ、Visual Studio のツール バーにある "デバッグの停止" アイコンを選択してアプリを停止します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-132">After reviewing the running Web app, close the browser and select the "Stop Debugging" icon in the toolbar of Visual Studio to stop the app.</span></span>

## <a name="create-a-web-app-in-the-azure-portal"></a><span data-ttu-id="d5e41-133">Azure Portal で Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="d5e41-133">Create a web app in the Azure Portal</span></span>

<span data-ttu-id="d5e41-134">Azure Portal で次の手順を実行して Web アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-134">The following steps create a web app in the Azure Portal:</span></span>

1. <span data-ttu-id="d5e41-135">[Azure Portal](https://portal.azure.com) にログインします。</span><span class="sxs-lookup"><span data-stu-id="d5e41-135">Log in to the [Azure Portal](https://portal.azure.com).</span></span>

1. <span data-ttu-id="d5e41-136">Portal インターフェイスの左上にある **[新規作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-136">Select **NEW** at the top left of the portal interface.</span></span>

1. <span data-ttu-id="d5e41-137">**[Web + モバイル]**  >  **[Web アプリ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-137">Select **Web + Mobile** > **Web App**.</span></span>

   ![Microsoft Azure Portal:新しいボタン:Marketplace での Web + モバイル:おすすめアプリでの Web アプリのボタン](azure-continuous-deployment/_static/05-azure-newwebapp.png)

1. <span data-ttu-id="d5e41-139">**[Web アプリ]** ブレードに **[App Service の名前]** の一意の名前を入力します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-139">In the **Web App** blade, enter a unique value for the **App Service Name**.</span></span>

   ![[Web アプリ] ブレード](azure-continuous-deployment/_static/06-azure-newappblade.png)

   > [!NOTE]
   > <span data-ttu-id="d5e41-141">**[App Service の名前]** の名前は一意にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d5e41-141">The **App Service Name** name must be unique.</span></span> <span data-ttu-id="d5e41-142">名前を入力する場合、この規則が適用されます。</span><span class="sxs-lookup"><span data-stu-id="d5e41-142">The portal enforces this rule when the name is provided.</span></span> <span data-ttu-id="d5e41-143">別の値を入力する場合は、このチュートリアルで **SampleWebAppDemo** が出てくるたびに、その値を置き換えてください。</span><span class="sxs-lookup"><span data-stu-id="d5e41-143">If providing a different value, substitute that value for each occurrence of **SampleWebAppDemo** in this tutorial.</span></span>

   <span data-ttu-id="d5e41-144">**[Web アプリ]** ブレードではまた、既存の **[App Service プラン/場所]** を選択するか、新しい App Service プラン/場所を作成できます。</span><span class="sxs-lookup"><span data-stu-id="d5e41-144">Also in the **Web App** blade, select an existing **App Service Plan/Location** or create a new one.</span></span> <span data-ttu-id="d5e41-145">新しいプランを作成した場合、価格レベル、場所、その他のオプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-145">If creating a new plan, select the pricing tier, location, and other options.</span></span> <span data-ttu-id="d5e41-146">App Service プランに関する詳細については、「[Azure App Service プランの概要](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d5e41-146">For more information on App Service plans, see [Azure App Service plans in-depth overview](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).</span></span>

1. <span data-ttu-id="d5e41-147">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-147">Select **Create**.</span></span> <span data-ttu-id="d5e41-148">Web アプリがプロビジョニングされ、起動します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-148">Azure will provision and start the web app.</span></span>

   ![Azure Portal: サンプル Web アプリ デモ 01 の [要点] ブレード](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a><span data-ttu-id="d5e41-150">新しい Web アプリに対して Git 公開を有効にする</span><span class="sxs-lookup"><span data-stu-id="d5e41-150">Enable Git publishing for the new web app</span></span>

<span data-ttu-id="d5e41-151">Git は分散型バージョン管理システムであり、これを利用して Azure App Service Web アプリを展開できます。</span><span class="sxs-lookup"><span data-stu-id="d5e41-151">Git is a distributed version control system that can be used to deploy an Azure App Service web app.</span></span> <span data-ttu-id="d5e41-152">Web アプリ コードはローカルの Git リポジトリに格納されます。また、コードはリモート リポジトリにプッシュすることによって Azure に展開されます。</span><span class="sxs-lookup"><span data-stu-id="d5e41-152">Web app code is stored in a local Git repository, and the code is deployed to Azure by pushing to a remote repository.</span></span>

1. <span data-ttu-id="d5e41-153">[Azure Portal](https://portal.azure.com) にログインします。</span><span class="sxs-lookup"><span data-stu-id="d5e41-153">Log into the [Azure Portal](https://portal.azure.com).</span></span>

1. <span data-ttu-id="d5e41-154">**[App Services]** を選択し、Azure サブスクリプションに関連付けられているアプリ サービスの一覧を表示します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-154">Select **App Services** to view a list of the app services associated with the Azure subscription.</span></span>

1. <span data-ttu-id="d5e41-155">このチュートリアルの前のセクションで作成した Web アプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-155">Select the web app created in the previous section of this tutorial.</span></span>

1. <span data-ttu-id="d5e41-156">**[デプロイ]** ブレードで、 **[デプロイ オプション]**  >  **[ソースの選択]**  >  **[ローカル Git リポジトリ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-156">In the **Deployment** blade, select **Deployment options** > **Choose Source** > **Local Git Repository**.</span></span>

   ![[設定] ブレード:[展開ソース] ブレード:[ソースの選択] ブレード](azure-continuous-deployment/_static/deployment-options.png)

1. <span data-ttu-id="d5e41-158">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-158">Select **OK**.</span></span>

1. <span data-ttu-id="d5e41-159">Web アプリや他の App Service アプリを公開するための展開の資格情報を設定していない場合は、ここで設定してください。</span><span class="sxs-lookup"><span data-stu-id="d5e41-159">If deployment credentials for publishing a web app or other App Service app haven't previously been set up, set them up now:</span></span>

   * <span data-ttu-id="d5e41-160">**[設定]**  >  **[デプロイ資格情報]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-160">Select **Settings** > **Deployment credentials**.</span></span> <span data-ttu-id="d5e41-161">**[デプロイ資格情報の設定]** ブレードが表示されます。</span><span class="sxs-lookup"><span data-stu-id="d5e41-161">The **Set deployment credentials** blade is displayed.</span></span>
   * <span data-ttu-id="d5e41-162">ユーザー名とパスワードを作成します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-162">Create a user name and password.</span></span> <span data-ttu-id="d5e41-163">後で Git を設定するときに使用するパスワードを保存します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-163">Save the password for later use when setting up Git.</span></span>
   * <span data-ttu-id="d5e41-164">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-164">Select **Save**.</span></span>

1. <span data-ttu-id="d5e41-165">**[Web アプリ]** ブレードで、 **[設定]**  >  **[プロパティ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-165">In the **Web App** blade, select **Settings** > **Properties**.</span></span> <span data-ttu-id="d5e41-166">展開するリモート Git リポジトリの URL が **[GIT URL]** の下に表示されます。</span><span class="sxs-lookup"><span data-stu-id="d5e41-166">The URL of the remote Git repository to deploy to is shown under **GIT URL**.</span></span>

1. <span data-ttu-id="d5e41-167">**GIT URL** の値をコピーします。後で使用します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-167">Copy the **GIT URL** value for later use in the tutorial.</span></span>

   ![Azure Portal: アプリケーションの [プロパティ] ブレード](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-the-web-app-to-azure-app-service"></a><span data-ttu-id="d5e41-169">Azure App Service に Web アプリを発行する</span><span class="sxs-lookup"><span data-stu-id="d5e41-169">Publish the web app to Azure App Service</span></span>

<span data-ttu-id="d5e41-170">このセクションでは、Visual Studio でローカル Git リポジトリを作成し、そのリポジトリから Azure にプッシュし、Web アプリを展開します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-170">In this section, create a local Git repository using Visual Studio and push from that repository to Azure to deploy the web app.</span></span> <span data-ttu-id="d5e41-171">実行する必要がある手順は、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="d5e41-171">The steps involved include the following:</span></span>

* <span data-ttu-id="d5e41-172">ローカル リポジトリを Azure に展開できるように、GIT URL 値を使用してリモート リポジトリ設定を追加します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-172">Add the remote repository setting using the GIT URL value, so the local repository can be deployed to Azure.</span></span>
* <span data-ttu-id="d5e41-173">プロジェクトの変更をコミットします。</span><span class="sxs-lookup"><span data-stu-id="d5e41-173">Commit project changes.</span></span>
* <span data-ttu-id="d5e41-174">ローカル リポジトリから Azure のリモート リポジトリにプロジェクトの変更をプッシュします。</span><span class="sxs-lookup"><span data-stu-id="d5e41-174">Push project changes from the local repository to the remote repository on Azure.</span></span>

1. <span data-ttu-id="d5e41-175">**ソリューション エクスプローラー**で **[ソリューション 'SampleWebAppDemo']** を右クリックし、 **[コミット]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-175">In **Solution Explorer** right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="d5e41-176">**チーム エクスプローラー**が表示されます。</span><span class="sxs-lookup"><span data-stu-id="d5e41-176">The **Team Explorer** is displayed.</span></span>

   ![[チーム エクスプローラー接続] タブ](azure-continuous-deployment/_static/10-team-explorer.png)

1. <span data-ttu-id="d5e41-178">**チーム エクスプローラー**で、 **[ホーム]** (ホーム アイコン)、 **[設定]** 、 **[リポジトリ設定]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-178">In **Team Explorer**, select the **Home** (home icon) > **Settings** > **Repository Settings**.</span></span>

1. <span data-ttu-id="d5e41-179">**[リポジトリ設定]** の **[リモート]** セクションで、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-179">In the **Remotes** section of the **Repository Settings**, select **Add**.</span></span> <span data-ttu-id="d5e41-180">**[リモートの追加]** ダイアログ ボックスが表示されます。</span><span class="sxs-lookup"><span data-stu-id="d5e41-180">The **Add Remote** dialog box is displayed.</span></span>

1. <span data-ttu-id="d5e41-181">リモートの **[名前]** を **[Azure-SampleApp]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-181">Set the **Name** of the remote to **Azure-SampleApp**.</span></span>

1. <span data-ttu-id="d5e41-182">**[フェッチ]** の値を先にこのチュートリアルで Azure からコピーした **Git URL** に設定します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-182">Set the value for **Fetch** to the **Git URL** that copied from Azure earlier in this tutorial.</span></span> <span data-ttu-id="d5e41-183">これは **.git** で終わる URL であることにご注意ください。</span><span class="sxs-lookup"><span data-stu-id="d5e41-183">Note that this is the URL that ends with **.git**.</span></span>

   ![[リモートの編集] ダイアログ](azure-continuous-deployment/_static/11-add-remote.png)

   > [!NOTE]
   > <span data-ttu-id="d5e41-185">あるいは、**コマンド ウィンドウ**からリモート リポジトリを指定できます。**コマンド ウィンドウ**を開き、プロジェクト ディレクトリに移動し、コマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-185">As an alternative, specify the remote repository from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering the command.</span></span> <span data-ttu-id="d5e41-186">例:</span><span class="sxs-lookup"><span data-stu-id="d5e41-186">Example:</span></span>
   >
   > `git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

1. <span data-ttu-id="d5e41-187">**[ホーム]** (ホーム アイコン)、 **[設定]** 、 **[グローバル設定]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-187">Select the **Home** (home icon) > **Settings** > **Global Settings**.</span></span> <span data-ttu-id="d5e41-188">名前と電子メール アドレスが設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-188">Confirm that the name and email address are set.</span></span> <span data-ttu-id="d5e41-189">必要に応じて **[更新]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-189">Select **Update** if required.</span></span>

1. <span data-ttu-id="d5e41-190">**[ホーム]** 、 **[変更]** の順に選択し、 **[変更]** ビューに戻ります。</span><span class="sxs-lookup"><span data-stu-id="d5e41-190">Select **Home** > **Changes** to return to the **Changes** view.</span></span>

1. <span data-ttu-id="d5e41-191">「**Initial Push #1**」のようなコミット メッセージを入力し、 **[コミット]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-191">Enter a commit message, such as **Initial Push #1** and select **Commit**.</span></span> <span data-ttu-id="d5e41-192">このアクションによりローカルで*コミット*されます。</span><span class="sxs-lookup"><span data-stu-id="d5e41-192">This action creates a *commit* locally.</span></span>

   ![[チーム エクスプローラー接続] タブ](azure-continuous-deployment/_static/12-initial-commit.png)

   > [!NOTE]
   > <span data-ttu-id="d5e41-194">あるいは、**コマンド ウィンドウ**から変更をコミットできます。**コマンド ウィンドウ**を開き、プロジェクト ディレクトリに移動し、git コマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-194">As an alternative, commit changes from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering the git commands.</span></span> <span data-ttu-id="d5e41-195">例:</span><span class="sxs-lookup"><span data-stu-id="d5e41-195">Example:</span></span>
   >
   > `git add .`
   >
   > `git commit -am "Initial Push #1"`

1. <span data-ttu-id="d5e41-196">**[ホーム]** 、 **[同期]** 、 **[アクション]** 、 **[コマンド プロンプトを開く]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-196">Select **Home** > **Sync** > **Actions** > **Open Command Prompt**.</span></span> <span data-ttu-id="d5e41-197">コマンド プロンプトでプロジェクト ディレクトリが開きます。</span><span class="sxs-lookup"><span data-stu-id="d5e41-197">The command prompt opens to the project directory.</span></span>

1. <span data-ttu-id="d5e41-198">コマンド ウィンドウで次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-198">Enter the following command in the command window:</span></span>

   `git push -u Azure-SampleApp master`

1. <span data-ttu-id="d5e41-199">Azure で先に作成した Azure **デプロイ資格情報**パスワードを入力します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-199">Enter the Azure **deployment credentials** password created earlier in Azure.</span></span>

   <span data-ttu-id="d5e41-200">このコマンドによりローカル プロジェクト ファイルを Azure にプッシュするプロセスが開始されます。</span><span class="sxs-lookup"><span data-stu-id="d5e41-200">This command starts the process of pushing the local project files to Azure.</span></span> <span data-ttu-id="d5e41-201">上記のコマンドからの出力は、展開が成功したというメッセージで終わります。</span><span class="sxs-lookup"><span data-stu-id="d5e41-201">The output from the above command ends with a message that the deployment was successful.</span></span>

   ```
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
   * [new branch]      master -> master
   Branch master set up to track remote branch master from Azure-SampleApp.
   ```

   > [!NOTE]
   > <span data-ttu-id="d5e41-202">プロジェクトのコラボレーションが必要な場合は、Azure にプッシュする前に [GitHub](https://github.com) にプッシュすることを検討してください。</span><span class="sxs-lookup"><span data-stu-id="d5e41-202">If collaboration on the project is required, consider pushing to [GitHub](https://github.com) before pushing to Azure.</span></span>
 
### <a name="verify-the-active-deployment"></a><span data-ttu-id="d5e41-203">アクティブ デプロイを検証する</span><span class="sxs-lookup"><span data-stu-id="d5e41-203">Verify the Active Deployment</span></span>

<span data-ttu-id="d5e41-204">ローカル環境から Azure への Web アプリの転送が成功していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-204">Verify that the web app transfer from the local environment to Azure is successful.</span></span>

<span data-ttu-id="d5e41-205">[Azure Portal](https://portal.azure.com) で、Web アプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-205">In the [Azure Portal](https://portal.azure.com), select the web app.</span></span> <span data-ttu-id="d5e41-206">**[デプロイ]**  >  **[デプロイ オプション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-206">Select **Deployment** > **Deployment options**.</span></span>

![Azure Portal: [設定] ブレード:[デプロイ] ブレードにデプロイ成功が表示されています](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a><span data-ttu-id="d5e41-208">Azure でアプリを実行する</span><span class="sxs-lookup"><span data-stu-id="d5e41-208">Run the app in Azure</span></span>

<span data-ttu-id="d5e41-209">Web アプリが Azure に展開されたら、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-209">Now that the web app is deployed to Azure, run the app.</span></span>

<span data-ttu-id="d5e41-210">その方法は 2 つあります。</span><span class="sxs-lookup"><span data-stu-id="d5e41-210">This can be accomplished in two ways:</span></span>

* <span data-ttu-id="d5e41-211">Azure Portal で、Web アプリ用の Web アプリ ブレードを探します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-211">In the Azure Portal, locate the web app blade for the web app.</span></span> <span data-ttu-id="d5e41-212">**[参照]** を選択して、既定のブラウザーでアプリを表示します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-212">Select **Browse** to view the app in the default browser.</span></span>
* <span data-ttu-id="d5e41-213">ブラウザーを開き、Web アプリの URL を入力します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-213">Open a browser and enter the URL for the web app.</span></span> <span data-ttu-id="d5e41-214">例 : `http://SampleWebAppDemo.azurewebsites.net`</span><span class="sxs-lookup"><span data-stu-id="d5e41-214">Example: `http://SampleWebAppDemo.azurewebsites.net`</span></span>

## <a name="update-the-web-app-and-republish"></a><span data-ttu-id="d5e41-215">Web アプリを更新し、再発行する</span><span class="sxs-lookup"><span data-stu-id="d5e41-215">Update the web app and republish</span></span>

<span data-ttu-id="d5e41-216">ローカル コードを変更した後は、再発行します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-216">After making changes to the local code, republish:</span></span>

1. <span data-ttu-id="d5e41-217">Visual Studio の**ソリューション エクスプローラー**で、*Startup.cs* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="d5e41-217">In **Solution Explorer** of Visual Studio, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="d5e41-218">`Configure` メソッドで、次のように表示されるように `Response.WriteAsync` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-218">In the `Configure` method, modify the `Response.WriteAsync` method so that it appears as follows:</span></span>

   ```csharp
   await context.Response.WriteAsync("Hello World! Deploy to Azure.");
   ```

1. <span data-ttu-id="d5e41-219">変更を *Startup.cs* に保存します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-219">Save the changes to *Startup.cs*.</span></span>

1. <span data-ttu-id="d5e41-220">**ソリューション エクスプローラー**で **[ソリューション 'SampleWebAppDemo']** を右クリックし、 **[コミット]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-220">In **Solution Explorer**, right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="d5e41-221">**チーム エクスプローラー**が表示されます。</span><span class="sxs-lookup"><span data-stu-id="d5e41-221">The **Team Explorer** is displayed.</span></span>

1. <span data-ttu-id="d5e41-222">`Update #2` のようなコミット メッセージを入力します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-222">Enter a commit message, such as `Update #2`.</span></span>

1. <span data-ttu-id="d5e41-223">**[コミット]** ボタンを押し、プロジェクトの変更をコミットします。</span><span class="sxs-lookup"><span data-stu-id="d5e41-223">Press the **Commit** button to commit the project changes.</span></span>

1. <span data-ttu-id="d5e41-224">**[ホーム]** 、 **[同期]** 、 **[アクション]** 、 **[プッシュ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-224">Select **Home** > **Sync** > **Actions** > **Push**.</span></span>

> [!NOTE]
> <span data-ttu-id="d5e41-225">あるいは、**コマンド ウィンドウ**から変更をプッシュできます。**コマンド ウィンドウ**を開き、プロジェクト ディレクトリに移動し、git コマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-225">As an alternative, push the changes from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering a git command.</span></span> <span data-ttu-id="d5e41-226">例:</span><span class="sxs-lookup"><span data-stu-id="d5e41-226">Example:</span></span>
> 
> `git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a><span data-ttu-id="d5e41-227">Azure で更新後の Web アプリを表示する</span><span class="sxs-lookup"><span data-stu-id="d5e41-227">View the updated web app in Azure</span></span>

<span data-ttu-id="d5e41-228">更新後の Web アプリを表示します。Azure Portal の Web アプリ ブレードから **[参照]** を選択するか、ブラウザーを開き、Web アプリの URL を入力します。</span><span class="sxs-lookup"><span data-stu-id="d5e41-228">View the updated web app by selecting **Browse** from the web app blade in the Azure Portal or by opening a browser and entering the URL for the web app.</span></span> <span data-ttu-id="d5e41-229">例 : `http://SampleWebAppDemo.azurewebsites.net`</span><span class="sxs-lookup"><span data-stu-id="d5e41-229">Example: `http://SampleWebAppDemo.azurewebsites.net`</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d5e41-230">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="d5e41-230">Additional resources</span></span>

* [<span data-ttu-id="d5e41-231">Azure Pipelines による最初のパイプラインの作成</span><span class="sxs-lookup"><span data-stu-id="d5e41-231">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="d5e41-232">プロジェクト Kudu</span><span class="sxs-lookup"><span data-stu-id="d5e41-232">Project Kudu</span></span>](https://github.com/projectkudu/kudu/wiki)
* <xref:host-and-deploy/visual-studio-publish-profiles>
