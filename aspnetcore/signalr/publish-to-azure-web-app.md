---
title: 発行、ASP.NET Core SignalR にアプリを Azure App Service
author: bradygaster
description: Azure App Service に ASP.NET Core SignalR のアプリを発行する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/26/2019
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: 87a9c93add373b24e3c473912cdbfcc00bbebf7e
ms.sourcegitcommit: 9bb29f9ba6f0645ee8b9cabda07e3a5aa52cd659
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2019
ms.locfileid: "67406114"
---
# <a name="publish-an-aspnet-core-signalr-app-to-azure-app-service"></a><span data-ttu-id="8fe48-103">発行、ASP.NET Core SignalR にアプリを Azure App Service</span><span class="sxs-lookup"><span data-stu-id="8fe48-103">Publish an ASP.NET Core SignalR app to Azure App Service</span></span>

<span data-ttu-id="8fe48-104">によって[Brady Gaster](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="8fe48-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="8fe48-105">[Azure App Service](/azure/app-service/app-service-web-overview)は、 [Microsoft クラウド コンピューティング](https://azure.microsoft.com/)ASP.NET Core を含む、web アプリをホストするためのプラットフォーム サービスです。</span><span class="sxs-lookup"><span data-stu-id="8fe48-105">[Azure App Service](/azure/app-service/app-service-web-overview) is a [Microsoft cloud computing](https://azure.microsoft.com/) platform service for hosting web apps, including ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="8fe48-106">この記事では、Visual Studio から ASP.NET Core SignalR アプリの発行を指します。</span><span class="sxs-lookup"><span data-stu-id="8fe48-106">This article refers to publishing an ASP.NET Core SignalR app from Visual Studio.</span></span> <span data-ttu-id="8fe48-107">詳細については、次を参照してください。 [for Azure SignalR サービス](https://azure.microsoft.com/services/signalr-service)します。</span><span class="sxs-lookup"><span data-stu-id="8fe48-107">For more information, see [SignalR service for Azure](https://azure.microsoft.com/services/signalr-service).</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="8fe48-108">アプリの発行</span><span class="sxs-lookup"><span data-stu-id="8fe48-108">Publish the app</span></span>

<span data-ttu-id="8fe48-109">この記事では、Visual Studio でツールを使用して公開について説明します。</span><span class="sxs-lookup"><span data-stu-id="8fe48-109">This article covers publishing using the tools in Visual Studio.</span></span> <span data-ttu-id="8fe48-110">Visual Studio Code のユーザーが使用できる[Azure CLI](/cli/azure)アプリを Azure に発行するコマンド。</span><span class="sxs-lookup"><span data-stu-id="8fe48-110">Visual Studio Code users can use [Azure CLI](/cli/azure) commands to publish apps to Azure.</span></span> <span data-ttu-id="8fe48-111">詳細については、次を参照してください。[コマンド ライン ツールを使用して Azure に ASP.NET Core アプリを発行](/azure/app-service/app-service-web-get-started-dotnet)します。</span><span class="sxs-lookup"><span data-stu-id="8fe48-111">For more information, see [Publish an ASP.NET Core app to Azure with command line tools](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

1. <span data-ttu-id="8fe48-112">**[ソリューション エクスプローラー]** でプロジェクトを右クリックし、 **[発行]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="8fe48-112">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>

1. <span data-ttu-id="8fe48-113">確認します**App Service**と**新規作成**で選択されている場合は、**発行先を選択**ダイアログ。</span><span class="sxs-lookup"><span data-stu-id="8fe48-113">Confirm that **App Service** and **Create new** are selected in the **Pick a publish target** dialog.</span></span>

1. <span data-ttu-id="8fe48-114">選択**プロファイルの作成**から、**発行**ダウン ドロップダウン ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="8fe48-114">Select **Create Profile** from the **Publish** button drop down.</span></span>

   <span data-ttu-id="8fe48-115">次の表で説明されている情報を入力して、 **App Service の作成**ダイアログと選択**作成**。</span><span class="sxs-lookup"><span data-stu-id="8fe48-115">Enter the information described in the following table in the **Create App Service** dialog and select **Create**.</span></span>

   | <span data-ttu-id="8fe48-116">アイテム</span><span class="sxs-lookup"><span data-stu-id="8fe48-116">Item</span></span>               | <span data-ttu-id="8fe48-117">説明</span><span class="sxs-lookup"><span data-stu-id="8fe48-117">Description</span></span> |
   | ------------------ | ----------- |
   | <span data-ttu-id="8fe48-118">**Name**</span><span class="sxs-lookup"><span data-stu-id="8fe48-118">**Name**</span></span>           | <span data-ttu-id="8fe48-119">アプリの一意の名前。</span><span class="sxs-lookup"><span data-stu-id="8fe48-119">Unique name of the app.</span></span> |
   | <span data-ttu-id="8fe48-120">**サブスクリプション**</span><span class="sxs-lookup"><span data-stu-id="8fe48-120">**Subscription**</span></span>   | <span data-ttu-id="8fe48-121">アプリを使用する azure サブスクリプション。</span><span class="sxs-lookup"><span data-stu-id="8fe48-121">Azure subscription that the app uses.</span></span> |
   | <span data-ttu-id="8fe48-122">**リソース グループ**</span><span class="sxs-lookup"><span data-stu-id="8fe48-122">**Resource Group**</span></span> | <span data-ttu-id="8fe48-123">アプリが所属する関連リソースのグループです。</span><span class="sxs-lookup"><span data-stu-id="8fe48-123">Group of related resources to which the app belongs.</span></span> |
   | <span data-ttu-id="8fe48-124">**ホスティング プラン**</span><span class="sxs-lookup"><span data-stu-id="8fe48-124">**Hosting Plan**</span></span>   | <span data-ttu-id="8fe48-125">Web アプリ用のプランの価格を設定します。</span><span class="sxs-lookup"><span data-stu-id="8fe48-125">Pricing plan for the web app.</span></span> |

1. <span data-ttu-id="8fe48-126">選択、 **Azure SignalR サービス**で、**依存関係** > **追加**ドロップダウン リスト。</span><span class="sxs-lookup"><span data-stu-id="8fe48-126">Select the **Azure SignalR Service** in the **Dependencies** > **Add** drop-down list:</span></span>

   ![Azure SignalR サービスの選択範囲を示す追加のドロップダウン リストでの依存関係の領域](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. <span data-ttu-id="8fe48-128">**Azure SignalR サービス**ダイアログ ボックスで、 **Azure SignalR サービスの新しいインスタンスを作成**です。</span><span class="sxs-lookup"><span data-stu-id="8fe48-128">In the **Azure SignalR Service** dialog, select **Create a new Azure SignalR Service instance**.</span></span>

1. <span data-ttu-id="8fe48-129">提供、**名前**、**リソース グループ**、および**場所**します。</span><span class="sxs-lookup"><span data-stu-id="8fe48-129">Provide a **Name**, **Resource Group**, and **Location**.</span></span> <span data-ttu-id="8fe48-130">戻り、 **Azure SignalR サービス**ダイアログと選択**追加**します。</span><span class="sxs-lookup"><span data-stu-id="8fe48-130">Return to the **Azure SignalR Service** dialog and select **Add**.</span></span>

<span data-ttu-id="8fe48-131">Visual Studio では、次のタスクを実行します。</span><span class="sxs-lookup"><span data-stu-id="8fe48-131">Visual Studio completes the following tasks:</span></span>

* <span data-ttu-id="8fe48-132">発行プロファイルを作成します。 発行の設定を格納しています。</span><span class="sxs-lookup"><span data-stu-id="8fe48-132">Creates a Publish Profile containing publish settings.</span></span>
* <span data-ttu-id="8fe48-133">作成、 *Azure Web App*で提供された詳細。</span><span class="sxs-lookup"><span data-stu-id="8fe48-133">Creates an *Azure Web App* with the provided details.</span></span>
* <span data-ttu-id="8fe48-134">アプリを発行します。</span><span class="sxs-lookup"><span data-stu-id="8fe48-134">Publishes the app.</span></span>
* <span data-ttu-id="8fe48-135">Web アプリが読み込まれますブラウザーを起動します。</span><span class="sxs-lookup"><span data-stu-id="8fe48-135">Launches a browser, which loads the web app.</span></span>

<span data-ttu-id="8fe48-136">アプリの URL の形式が`{APP SERVICE NAME}.azurewebsites.net`します。</span><span class="sxs-lookup"><span data-stu-id="8fe48-136">The format of the app's URL is `{APP SERVICE NAME}.azurewebsites.net`.</span></span> <span data-ttu-id="8fe48-137">たとえば、という名前のアプリ`SignalRChatApp`の URL が`https://signalrchatapp.azurewebsites.net`します。</span><span class="sxs-lookup"><span data-stu-id="8fe48-137">For example, an app named `SignalRChatApp` has a URL of `https://signalrchatapp.azurewebsites.net`.</span></span>

<span data-ttu-id="8fe48-138">場合、HTTP *502.2 - 無効なゲートウェイ*プレビュー リリースの .NET Core を対象とするアプリをデプロイするときにエラーが発生しを参照してください[Azure App Service に ASP.NET Core の展開のプレビュー リリース](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)問題を解決します。</span><span class="sxs-lookup"><span data-stu-id="8fe48-138">If an HTTP *502.2 - Bad Gateway* error occurs when deploying an app that targets a preview .NET Core release, see [Deploy ASP.NET Core preview release to Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) to resolve it.</span></span>

## <a name="configure-the-app-in-azure-app-service"></a><span data-ttu-id="8fe48-139">Azure App Service でアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="8fe48-139">Configure the app in Azure App Service</span></span>

> [!NOTE]
> <span data-ttu-id="8fe48-140">*このセクションでは、Azure SignalR サービスを使用していないアプリにのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="8fe48-140">*This section only applies to apps not using the Azure SignalR Service.*</span></span>
>
> <span data-ttu-id="8fe48-141">アプリでは、Azure SignalR サービスを使用する場合、App Service に Application Request Routing (ARR) アフィニティおよびこのセクションで説明されている Web ソケットの構成は不要です。</span><span class="sxs-lookup"><span data-stu-id="8fe48-141">If the app uses the Azure SignalR Service, the App Service doesn't require the configuration of Application Request Routing (ARR) Affinity and Web Sockets described in this section.</span></span> <span data-ttu-id="8fe48-142">Azure SignalR サービスに、アプリに直接ではなく、クライアントはその Web ソケットを接続します。</span><span class="sxs-lookup"><span data-stu-id="8fe48-142">Clients connect their Web Sockets to the Azure SignalR Service, not directly to the app.</span></span>

<span data-ttu-id="8fe48-143">Azure SignalR サービス ホストされているアプリを有効にします。</span><span class="sxs-lookup"><span data-stu-id="8fe48-143">For apps hosted without the Azure SignalR Service, enable:</span></span>

* <span data-ttu-id="8fe48-144">[ARR アフィニティ](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html)同じ App Service インスタンスにユーザーからの要求をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="8fe48-144">[ARR Affinity](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) to route requests from a user back to the same App Service instance.</span></span> <span data-ttu-id="8fe48-145">既定の設定は**で**します。</span><span class="sxs-lookup"><span data-stu-id="8fe48-145">The default setting is **On**.</span></span>
* <span data-ttu-id="8fe48-146">[Web ソケット](xref:fundamentals/websockets)関数への Web ソケット トランスポートを許可します。</span><span class="sxs-lookup"><span data-stu-id="8fe48-146">[Web Sockets](xref:fundamentals/websockets) to allow the Web Sockets transport to function.</span></span> <span data-ttu-id="8fe48-147">既定の設定は**オフ**します。</span><span class="sxs-lookup"><span data-stu-id="8fe48-147">The default setting is **Off**.</span></span>

1. <span data-ttu-id="8fe48-148">Azure portal での web アプリに移動します**App Services**します。</span><span class="sxs-lookup"><span data-stu-id="8fe48-148">In the Azure portal, navigate to the web app in **App Services**.</span></span>
1. <span data-ttu-id="8fe48-149">開いている**構成** > **全般設定**します。</span><span class="sxs-lookup"><span data-stu-id="8fe48-149">Open **Configuration** > **General settings**.</span></span>
1. <span data-ttu-id="8fe48-150">設定**Web ソケット**に**で**します。</span><span class="sxs-lookup"><span data-stu-id="8fe48-150">Set **Web sockets** to **On**.</span></span>
1. <span data-ttu-id="8fe48-151">いることを確認**ARR アフィニティ**に設定されている**で**します。</span><span class="sxs-lookup"><span data-stu-id="8fe48-151">Verify that **ARR affinity** is set to **On**.</span></span>

## <a name="app-service-plan-limits"></a><span data-ttu-id="8fe48-152">App Service プランの制限します。</span><span class="sxs-lookup"><span data-stu-id="8fe48-152">App Service Plan limits</span></span>

<span data-ttu-id="8fe48-153">Web ソケットと他のトランスポートに制限は選択されている App Service プランに基づきます。</span><span class="sxs-lookup"><span data-stu-id="8fe48-153">Web Sockets and other transports are limited based on the App Service Plan selected.</span></span> <span data-ttu-id="8fe48-154">詳細については、次を参照してください、 *Azure Cloud Services の制限*と*App Service の制限*のセクションでは、 [Azure サブスクリプションとサービスの制限、クォータ、制約](/azure/azure-subscription-service-limits#app-service-limits)。アーティクルです。</span><span class="sxs-lookup"><span data-stu-id="8fe48-154">For more information, see the *Azure Cloud Services limits* and *App Service limits* sections of the [Azure subscription and service limits, quotas, and constraints](/azure/azure-subscription-service-limits#app-service-limits) article.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8fe48-155">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="8fe48-155">Additional resources</span></span>

* [<span data-ttu-id="8fe48-156">Azure SignalR サービスとは何ですか。</span><span class="sxs-lookup"><span data-stu-id="8fe48-156">What is Azure SignalR Service?</span></span>](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="8fe48-157">コマンド ライン ツールを使用して Azure に ASP.NET Core アプリを発行します。</span><span class="sxs-lookup"><span data-stu-id="8fe48-157">Publish an ASP.NET Core app to Azure with command line tools</span></span>](/azure/app-service/app-service-web-get-started-dotnet)
* [<span data-ttu-id="8fe48-158">ホストし、Azure で ASP.NET Core プレビュー アプリを展開します。</span><span class="sxs-lookup"><span data-stu-id="8fe48-158">Host and deploy ASP.NET Core Preview apps on Azure</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
