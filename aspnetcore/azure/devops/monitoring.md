---
title: 監視とデバッグ - ASP.NET Core および Azure を使用した DevOps
author: CamSoper
description: ASP.NET Core と Azure を使用した DevOps ソリューションの一部としてのコードの監視とデバッグ
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 07/10/2019
uid: azure/devops/monitor
ms.openlocfilehash: 1d8ed99f4387dbc99929164c558cc2ce14bd9ea0
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78647456"
---
# <a name="monitor-and-debug"></a><span data-ttu-id="40e1b-103">監視とデバッグ</span><span class="sxs-lookup"><span data-stu-id="40e1b-103">Monitor and debug</span></span>

<span data-ttu-id="40e1b-104">アプリをデプロイし、DevOps パイプラインを作成したら、アプリの監視とトラブルシューティングの方法を理解することが重要です。</span><span class="sxs-lookup"><span data-stu-id="40e1b-104">Having deployed the app and built a DevOps pipeline, it's important to understand how to monitor and troubleshoot the app.</span></span>

<span data-ttu-id="40e1b-105">このセクションでは、次のタスクを実行します。</span><span class="sxs-lookup"><span data-stu-id="40e1b-105">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="40e1b-106">Azure portal で基本的な監視とトラブルシューティングのデータを見つける</span><span class="sxs-lookup"><span data-stu-id="40e1b-106">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="40e1b-107">Azure Monitor が、すべての Azure サービスにわたってメトリックに関するより詳しい調査をどのように提供しているかを学習する</span><span class="sxs-lookup"><span data-stu-id="40e1b-107">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="40e1b-108">アプリ プロファイル用に Web アプリを Application Insights と接続する</span><span class="sxs-lookup"><span data-stu-id="40e1b-108">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="40e1b-109">ログ記録を有効にし、ログをダウンロードする場所を確認する</span><span class="sxs-lookup"><span data-stu-id="40e1b-109">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="40e1b-110">リアルタイムでログをストリームする</span><span class="sxs-lookup"><span data-stu-id="40e1b-110">Stream logs in real time</span></span>
* <span data-ttu-id="40e1b-111">アラートを設定する場所を学習する</span><span class="sxs-lookup"><span data-stu-id="40e1b-111">Learn where to set up alerts</span></span>
* <span data-ttu-id="40e1b-112">Azure App Service Web アプリのリモート デバッグについて学習する</span><span class="sxs-lookup"><span data-stu-id="40e1b-112">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="basic-monitoring-and-troubleshooting"></a><span data-ttu-id="40e1b-113">基本的な監視とトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="40e1b-113">Basic monitoring and troubleshooting</span></span>

<span data-ttu-id="40e1b-114">App Service Web アプリは、リアルタイムで簡単に監視できます。</span><span class="sxs-lookup"><span data-stu-id="40e1b-114">App Service web apps are easily monitored in real time.</span></span> <span data-ttu-id="40e1b-115">Azure portal では、わかりやすいチャートとグラフでメトリックがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="40e1b-115">The Azure portal renders metrics in easy-to-understand charts and graphs.</span></span>

1. <span data-ttu-id="40e1b-116">[Azure portal](https://portal.azure.com) を開き、"*mywebapp\<unique_number\>* " App Service に移動します。</span><span class="sxs-lookup"><span data-stu-id="40e1b-116">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>

1. <span data-ttu-id="40e1b-117">**[概要]** タブには、最近のメトリックを表示するグラフなど、"ひとめでわかる" 情報が表示されます。</span><span class="sxs-lookup"><span data-stu-id="40e1b-117">The **Overview** tab displays useful "at-a-glance" information, including graphs displaying recent metrics.</span></span>

    ![概要パネルを示すスクリーンショット](./media/monitoring/overview.png)

    * <span data-ttu-id="40e1b-119">**Http 5xx**:サーバー側のエラー (通常は ASP.NET Core コードの例外) の数。</span><span class="sxs-lookup"><span data-stu-id="40e1b-119">**Http 5xx**: Count of server-side errors, usually exceptions in ASP.NET Core code.</span></span>
    * <span data-ttu-id="40e1b-120">**受信データ**: Web アプリで受信されるデータ受信。</span><span class="sxs-lookup"><span data-stu-id="40e1b-120">**Data In**: Data ingress coming into your web app.</span></span>
    * <span data-ttu-id="40e1b-121">**送信データ**: Web アプリからクライアントへのデータ送信。</span><span class="sxs-lookup"><span data-stu-id="40e1b-121">**Data Out**: Data egress from your web app to clients.</span></span>
    * <span data-ttu-id="40e1b-122">**要求**: HTTP 要求の数。</span><span class="sxs-lookup"><span data-stu-id="40e1b-122">**Requests**: Count of HTTP requests.</span></span>
    * <span data-ttu-id="40e1b-123">**平均応答時間**: Web アプリが HTTP 要求に応答するまでの平均時間。</span><span class="sxs-lookup"><span data-stu-id="40e1b-123">**Average Response Time**: Average time for the web app to respond to HTTP requests.</span></span>

    <span data-ttu-id="40e1b-124">トラブルシューティングと最適化のためのセルフサービス ツールは、このページにもいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="40e1b-124">Several self-service tools for troubleshooting and optimization are also found on this page.</span></span>

    ![セルフサービス ツールを示すスクリーンショット](./media/monitoring/wizards.png)

    * <span data-ttu-id="40e1b-126">**問題の診断と解決**は、セルフサービスのトラブルシューティング ツールです。</span><span class="sxs-lookup"><span data-stu-id="40e1b-126">**Diagnose and solve problems** is a self-service troubleshooter.</span></span>
    * <span data-ttu-id="40e1b-127">**Application Insights** は、パフォーマンスとアプリの動作のプロファイル用です。このセクションで後述します。</span><span class="sxs-lookup"><span data-stu-id="40e1b-127">**Application Insights** is for profiling performance and app behavior, and is discussed later in this section.</span></span>
    * <span data-ttu-id="40e1b-128">**App Service Advisor** では、アプリのエクスペリエンスを調整するための推奨事項が作成されます。</span><span class="sxs-lookup"><span data-stu-id="40e1b-128">**App Service Advisor** makes recommendations to tune your app experience.</span></span>

## <a name="advanced-monitoring"></a><span data-ttu-id="40e1b-129">高度な監視</span><span class="sxs-lookup"><span data-stu-id="40e1b-129">Advanced monitoring</span></span>

<span data-ttu-id="40e1b-130">[Azure Monitor](/azure/monitoring-and-diagnostics/) は、すべてのメトリックを監視し、Azure サービス全体にアラートを設定するための一元化されたサービスです。</span><span class="sxs-lookup"><span data-stu-id="40e1b-130">[Azure Monitor](/azure/monitoring-and-diagnostics/) is the centralized service for monitoring all metrics and setting alerts across Azure services.</span></span> <span data-ttu-id="40e1b-131">Azure Monitor では、管理者はパフォーマンスを細かく追跡し、傾向を特定できます。</span><span class="sxs-lookup"><span data-stu-id="40e1b-131">Within Azure Monitor, administrators can granularly track performance and identify trends.</span></span> <span data-ttu-id="40e1b-132">各 Azure サービスでは、Azure Monitor に対して独自の[メトリック セット](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions)が提供されています。</span><span class="sxs-lookup"><span data-stu-id="40e1b-132">Each Azure service offers its own [set of metrics](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) to Azure Monitor.</span></span>

## <a name="profile-with-application-insights"></a><span data-ttu-id="40e1b-133">Application Insights を使用したプロファイル</span><span class="sxs-lookup"><span data-stu-id="40e1b-133">Profile with Application Insights</span></span>

<span data-ttu-id="40e1b-134">[Application Insights](/azure/application-insights/app-insights-overview) は、Web アプリのパフォーマンスと安定性、およびユーザーがそれらをどのように使用しているかを分析するための Azure サービスです。</span><span class="sxs-lookup"><span data-stu-id="40e1b-134">[Application Insights](/azure/application-insights/app-insights-overview) is an Azure service for analyzing the performance and stability of web apps and how users use them.</span></span> <span data-ttu-id="40e1b-135">Application Insights のデータは、Azure Monitor よりも広範で深いものです。</span><span class="sxs-lookup"><span data-stu-id="40e1b-135">The data from Application Insights is broader and deeper than that of Azure Monitor.</span></span> <span data-ttu-id="40e1b-136">開発者や管理者は、このデータからアプリを改善するための重要な情報を得ることができます。</span><span class="sxs-lookup"><span data-stu-id="40e1b-136">The data can provide developers and administrators with key information for improving apps.</span></span> <span data-ttu-id="40e1b-137">Application Insights は、コードを変更することなく、Azure App Service リソースに追加できます。</span><span class="sxs-lookup"><span data-stu-id="40e1b-137">Application Insights can be added to an Azure App Service resource without code changes.</span></span>

1. <span data-ttu-id="40e1b-138">[Azure portal](https://portal.azure.com) を開き、"*mywebapp\<unique_number\>* " App Service に移動します。</span><span class="sxs-lookup"><span data-stu-id="40e1b-138">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="40e1b-139">**[概要]** タブで、 **[Application Insights]** タイルをクリックします。</span><span class="sxs-lookup"><span data-stu-id="40e1b-139">From the **Overview** tab, click the **Application Insights** tile.</span></span>

    ![[Application Insights] タイル](./media/monitoring/app-insights.png)

1. <span data-ttu-id="40e1b-141">**[新しいリソースの作成]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="40e1b-141">Select the **Create new resource** radio button.</span></span> <span data-ttu-id="40e1b-142">既定のリソース名を使用して、Application Insights リソースの場所を選択します。</span><span class="sxs-lookup"><span data-stu-id="40e1b-142">Use the default resource name, and select the location for the Application Insights resource.</span></span> <span data-ttu-id="40e1b-143">この場所は、Web アプリの場所と一致している必要はありません。</span><span class="sxs-lookup"><span data-stu-id="40e1b-143">The location doesn't need to match that of your web app.</span></span>

    ![Application Insights のセットアップ](./media/monitoring/new-app-insights.png)

1. <span data-ttu-id="40e1b-145">**[ランタイム/フレームワーク]** には、 **[ASP.NET Core]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="40e1b-145">For **Runtime/Framework**, select **ASP.NET Core**.</span></span> <span data-ttu-id="40e1b-146">既定の設定をそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="40e1b-146">Accept the default settings.</span></span>
1. <span data-ttu-id="40e1b-147">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="40e1b-147">Select **OK**.</span></span> <span data-ttu-id="40e1b-148">確認を求めるメッセージが表示されたら、 **[続行]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="40e1b-148">If prompted to confirm, select **Continue**.</span></span>
1. <span data-ttu-id="40e1b-149">リソースが作成されたら、Application Insights リソースの名前をクリックして、[Application Insights] ページに直接移動します。</span><span class="sxs-lookup"><span data-stu-id="40e1b-149">After the resource has been created, click the name of Application Insights resource to navigate directly to the Application Insights page.</span></span>

    ![新しい Application Insights リソースの準備ができました](./media/monitoring/new-app-insights-done.png)

<span data-ttu-id="40e1b-151">アプリが使用されると、データが蓄積されます。</span><span class="sxs-lookup"><span data-stu-id="40e1b-151">As the app is used, data accumulates.</span></span> <span data-ttu-id="40e1b-152">**[更新]** を選択して、新しいデータでブレードを再度読み込みます。</span><span class="sxs-lookup"><span data-stu-id="40e1b-152">Select **Refresh** to reload the blade with new data.</span></span>

![Application Insights の [概要] タブ](./media/monitoring/app-insights-overview.png)

<span data-ttu-id="40e1b-154">追加の構成をしなくても、Application Insights から有用なサーバー側の情報が提供されます。</span><span class="sxs-lookup"><span data-stu-id="40e1b-154">Application Insights provides useful server-side information with no additional configuration.</span></span> <span data-ttu-id="40e1b-155">Application Insights から最大限の価値を得るには、[Application Insights SDK を使用してアプリをインストルメント化](/azure/application-insights/app-insights-asp-net-core)します。</span><span class="sxs-lookup"><span data-stu-id="40e1b-155">To get the most value from Application Insights, [instrument your app with the Application Insights SDK](/azure/application-insights/app-insights-asp-net-core).</span></span> <span data-ttu-id="40e1b-156">適切に構成されている場合、サービスは、クライアント側のパフォーマンスを含め、Web サーバーとブラウザー全体にエンドツーエンドの監視を提供します。</span><span class="sxs-lookup"><span data-stu-id="40e1b-156">When properly configured, the service provides end-to-end monitoring across the web server and browser, including client-side performance.</span></span> <span data-ttu-id="40e1b-157">詳細については、[Application Insights のドキュメント](/azure/application-insights/app-insights-overview)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="40e1b-157">For more information, see the [Application Insights documentation](/azure/application-insights/app-insights-overview).</span></span>

## <a name="logging"></a><span data-ttu-id="40e1b-158">ログの記録</span><span class="sxs-lookup"><span data-stu-id="40e1b-158">Logging</span></span>

<span data-ttu-id="40e1b-159">Azure App Service では、Web サーバーとアプリのログは既定で無効になっています。</span><span class="sxs-lookup"><span data-stu-id="40e1b-159">Web server and app logs are disabled by default in Azure App Service.</span></span> <span data-ttu-id="40e1b-160">次の手順でログを有効にします。</span><span class="sxs-lookup"><span data-stu-id="40e1b-160">Enable the logs with the following steps:</span></span>

1. <span data-ttu-id="40e1b-161">[Azure portal](https://portal.azure.com) を開き、"*mywebapp\<unique_number\>* " App Service に移動します。</span><span class="sxs-lookup"><span data-stu-id="40e1b-161">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="40e1b-162">左側のメニューで、 **[監視]** セクションまで下にスクロールします。</span><span class="sxs-lookup"><span data-stu-id="40e1b-162">In the menu to the left, scroll down to the **Monitoring** section.</span></span> <span data-ttu-id="40e1b-163">**[診断ログ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="40e1b-163">Select **Diagnostics logs**.</span></span>

    ![診断ログのリンク](./media/monitoring/logging.png)

1. <span data-ttu-id="40e1b-165">**[アプリケーション ログ (ファイルシステム)]** をオンにします。</span><span class="sxs-lookup"><span data-stu-id="40e1b-165">Turn on **Application Logging (Filesystem)**.</span></span> <span data-ttu-id="40e1b-166">メッセージが表示されたら、ボックスをクリックして拡張機能をインストールし、Web アプリでアプリのログ記録を有効にします。</span><span class="sxs-lookup"><span data-stu-id="40e1b-166">If prompted, click the box to install the extensions to enable app logging in the web app.</span></span>
1. <span data-ttu-id="40e1b-167">**[Web サーバーのログ記録]** を **[ファイル システム]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="40e1b-167">Set **Web server logging** to **File System**.</span></span>
1. <span data-ttu-id="40e1b-168">**[保持期間]** を日数で入力します。</span><span class="sxs-lookup"><span data-stu-id="40e1b-168">Enter the **Retention Period** in days.</span></span> <span data-ttu-id="40e1b-169">たとえば、30 を入力します。</span><span class="sxs-lookup"><span data-stu-id="40e1b-169">For example, 30.</span></span>
1. <span data-ttu-id="40e1b-170">**[保存]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="40e1b-170">Click **Save**.</span></span>

<span data-ttu-id="40e1b-171">Web アプリ用に ASP.NET Core と Web サーバー (App Service) のログが生成されます。</span><span class="sxs-lookup"><span data-stu-id="40e1b-171">ASP.NET Core and web server (App Service) logs are generated for the web app.</span></span> <span data-ttu-id="40e1b-172">これらは、表示される FTP/FTPS 情報を使用してダウンロードできます。</span><span class="sxs-lookup"><span data-stu-id="40e1b-172">They can be downloaded using the FTP/FTPS information displayed.</span></span> <span data-ttu-id="40e1b-173">パスワードは、このガイドで前に作成したデプロイ資格情報と同じです。</span><span class="sxs-lookup"><span data-stu-id="40e1b-173">The password is the same as the deployment credentials created earlier in this guide.</span></span> <span data-ttu-id="40e1b-174">ログは、[PowerShell または Azure CLI を使用してローカル コンピューターに直接ストリームする](/azure/app-service/web-sites-enable-diagnostic-log#download)ことができます。</span><span class="sxs-lookup"><span data-stu-id="40e1b-174">The logs can be [streamed directly to your local machine with PowerShell or Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download).</span></span> <span data-ttu-id="40e1b-175">ログは、[Application Insights で表示](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights)することもできます。</span><span class="sxs-lookup"><span data-stu-id="40e1b-175">Logs can also be [viewed in Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span></span>

## <a name="log-streaming"></a><span data-ttu-id="40e1b-176">ログ ストリーミング</span><span class="sxs-lookup"><span data-stu-id="40e1b-176">Log streaming</span></span>

<span data-ttu-id="40e1b-177">アプリと Web サーバーのログは、ポータルを使用してリアルタイムでストリームできます。</span><span class="sxs-lookup"><span data-stu-id="40e1b-177">App and web server logs can be streamed in real time through the portal.</span></span>

1. <span data-ttu-id="40e1b-178">[Azure portal](https://portal.azure.com) を開き、"*mywebapp\<unique_number\>* " App Service に移動します。</span><span class="sxs-lookup"><span data-stu-id="40e1b-178">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="40e1b-179">左側のメニューで、 **[監視]** セクションまで下にスクロールして、 **[ログ ストリーム]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="40e1b-179">In the menu to the left, scroll down to the **Monitoring** section and select **Log stream**.</span></span>

    ![ログ ストリーム リンクを示すスクリーンショット](./media/monitoring/log-stream.png)

<span data-ttu-id="40e1b-181">ログは、[Azure CLI または Azure PowerShell (Cloud Shell を含む) を使用してストリームする](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs)こともできます。</span><span class="sxs-lookup"><span data-stu-id="40e1b-181">Logs can also be [streamed via Azure CLI or Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), including through the Cloud Shell.</span></span>

## <a name="alerts"></a><span data-ttu-id="40e1b-182">アラート</span><span class="sxs-lookup"><span data-stu-id="40e1b-182">Alerts</span></span>

<span data-ttu-id="40e1b-183">Azure Monitor では、メトリック、管理イベント、その他の条件に基づいて、[リアルタイム アラート](/azure/monitoring-and-diagnostics/insights-alerts-portal)も提供します。</span><span class="sxs-lookup"><span data-stu-id="40e1b-183">Azure Monitor also provides [real time alerts](/azure/monitoring-and-diagnostics/insights-alerts-portal) based on metrics, administrative events, and other criteria.</span></span>

> <span data-ttu-id="40e1b-184">*注:現在、Web アプリのメトリックに対するアラートは、アラート (クラシック) サービスでのみ使用できます。*</span><span class="sxs-lookup"><span data-stu-id="40e1b-184">*Note: Currently alerting on web app metrics is only available in the Alerts (classic) service.*</span></span>

<span data-ttu-id="40e1b-185">[アラート (クラシック) サービス](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal)は Azure Monitor 内、または App Service 設定の **[監視]** セクションの下にあります。</span><span class="sxs-lookup"><span data-stu-id="40e1b-185">The [Alerts (classic) service](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) can be found in Azure Monitor or under the **Monitoring** section of the App Service settings.</span></span>

![アラート (クラシック) リンク](./media/monitoring/alerts.png)

## <a name="live-debugging"></a><span data-ttu-id="40e1b-187">ライブ デバッグ</span><span class="sxs-lookup"><span data-stu-id="40e1b-187">Live debugging</span></span>

<span data-ttu-id="40e1b-188">ログが十分な情報を提供していない場合は、Azure App Service を [Visual Studio を使用してリモートでデバッグ](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)できます。</span><span class="sxs-lookup"><span data-stu-id="40e1b-188">Azure App Service can be [debugged remotely with Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) when logs don't provide enough information.</span></span> <span data-ttu-id="40e1b-189">ただし、リモート デバッグでは、デバッグ シンボルを使用してアプリをコンパイルする必要があります。</span><span class="sxs-lookup"><span data-stu-id="40e1b-189">However, remote debugging requires the app to be compiled with debug symbols.</span></span> <span data-ttu-id="40e1b-190">デバッグは、最後の手段として以外は、運用環境では行わないでください。</span><span class="sxs-lookup"><span data-stu-id="40e1b-190">Debugging shouldn't be done in production, except as a last resort.</span></span>

## <a name="conclusion"></a><span data-ttu-id="40e1b-191">まとめ</span><span class="sxs-lookup"><span data-stu-id="40e1b-191">Conclusion</span></span>

<span data-ttu-id="40e1b-192">このセクションでは、次のタスクを完了しました。</span><span class="sxs-lookup"><span data-stu-id="40e1b-192">In this section, you completed the following tasks:</span></span>

* <span data-ttu-id="40e1b-193">Azure portal で基本的な監視とトラブルシューティングのデータを見つける</span><span class="sxs-lookup"><span data-stu-id="40e1b-193">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="40e1b-194">Azure Monitor が、すべての Azure サービスにわたってメトリックに関するより詳しい調査をどのように提供しているかを学習する</span><span class="sxs-lookup"><span data-stu-id="40e1b-194">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="40e1b-195">アプリ プロファイル用に Web アプリを Application Insights と接続する</span><span class="sxs-lookup"><span data-stu-id="40e1b-195">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="40e1b-196">ログ記録を有効にし、ログをダウンロードする場所を確認する</span><span class="sxs-lookup"><span data-stu-id="40e1b-196">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="40e1b-197">リアルタイムでログをストリームする</span><span class="sxs-lookup"><span data-stu-id="40e1b-197">Stream logs in real time</span></span>
* <span data-ttu-id="40e1b-198">アラートを設定する場所を学習する</span><span class="sxs-lookup"><span data-stu-id="40e1b-198">Learn where to set up alerts</span></span>
* <span data-ttu-id="40e1b-199">Azure App Service Web アプリのリモート デバッグについて学習する</span><span class="sxs-lookup"><span data-stu-id="40e1b-199">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="40e1b-200">その他の参考資料</span><span class="sxs-lookup"><span data-stu-id="40e1b-200">Additional reading</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="40e1b-201">Application Insights を使用した Azure Web アプリのパフォーマンスの監視</span><span class="sxs-lookup"><span data-stu-id="40e1b-201">Monitor Azure web app performance with Application Insights</span></span>](/azure/application-insights/app-insights-azure-web-apps)
* [<span data-ttu-id="40e1b-202">Azure App Service の Web アプリの診断ログの有効化</span><span class="sxs-lookup"><span data-stu-id="40e1b-202">Enable diagnostics logging for web apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)
* [<span data-ttu-id="40e1b-203">Visual Studio を使用した Azure App Service での Web アプリのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="40e1b-203">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="40e1b-204">Azure Monitor での Azure サービス クラシック メトリック アラートの作成 - Azure portal</span><span class="sxs-lookup"><span data-stu-id="40e1b-204">Create classic metric alerts in Azure Monitor for Azure services - Azure portal</span></span>](/azure/monitoring-and-diagnostics/insights-alerts-portal)
