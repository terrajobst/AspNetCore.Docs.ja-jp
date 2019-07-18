---
title: 監視とデバッグ - ASP.NET Core および Azure を使用した DevOps
author: CamSoper
description: 監視と ASP.NET Core と Azure で DevOps ソリューションの一部として、コードのデバッグ
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 07/10/2019
uid: azure/devops/monitor
ms.openlocfilehash: 1d8ed99f4387dbc99929164c558cc2ce14bd9ea0
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68307959"
---
# <a name="monitor-and-debug"></a><span data-ttu-id="4d9bb-103">監視とデバッグ</span><span class="sxs-lookup"><span data-stu-id="4d9bb-103">Monitor and debug</span></span>

<span data-ttu-id="4d9bb-104">アプリ展開と DevOps パイプラインを構築するには、重要性を監視し、アプリのトラブルシューティングの方法を理解します。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-104">Having deployed the app and built a DevOps pipeline, it's important to understand how to monitor and troubleshoot the app.</span></span>

<span data-ttu-id="4d9bb-105">このセクションで、次のタスクを完了します。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-105">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="4d9bb-106">基本的な監視とトラブルシューティング、Azure portal でのデータを検索します。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-106">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="4d9bb-107">Azure Monitor ですべての Azure サービス間でのメトリックについて詳しく説明を提供する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-107">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="4d9bb-108">アプリのプロファイリング用 Application Insights による web アプリを接続します。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-108">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="4d9bb-109">ログ記録をオンにし、ログをダウンロードする方法を学習します</span><span class="sxs-lookup"><span data-stu-id="4d9bb-109">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="4d9bb-110">Stream のログをリアルタイムで</span><span class="sxs-lookup"><span data-stu-id="4d9bb-110">Stream logs in real time</span></span>
* <span data-ttu-id="4d9bb-111">アラートを設定する方法を学習します</span><span class="sxs-lookup"><span data-stu-id="4d9bb-111">Learn where to set up alerts</span></span>
* <span data-ttu-id="4d9bb-112">リモート Azure App Service web アプリのデバッグについて説明します。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-112">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="basic-monitoring-and-troubleshooting"></a><span data-ttu-id="4d9bb-113">基本的な監視とトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="4d9bb-113">Basic monitoring and troubleshooting</span></span>

<span data-ttu-id="4d9bb-114">App Service web apps が簡単にリアルタイムで監視されます。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-114">App Service web apps are easily monitored in real time.</span></span> <span data-ttu-id="4d9bb-115">Azure portal では、わかりやすいチャートやグラフのメトリックを表示します。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-115">The Azure portal renders metrics in easy-to-understand charts and graphs.</span></span>

1. <span data-ttu-id="4d9bb-116">開く、 [Azure portal](https://portal.azure.com)、順に移動します、 *mywebapp\<unique_number\>*  App Service。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-116">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>

1. <span data-ttu-id="4d9bb-117">**概要**タブには、最新のメトリックを表示するグラフを含む、「概要」の有用な情報が表示されます。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-117">The **Overview** tab displays useful "at-a-glance" information, including graphs displaying recent metrics.</span></span>

    ![スクリーン ショットが表示された [概要] パネル](./media/monitoring/overview.png)

    * <span data-ttu-id="4d9bb-119">**Http 5xx**:サーバー側のエラーの数。通常は ASP.NET Core コードで例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-119">**Http 5xx**: Count of server-side errors, usually exceptions in ASP.NET Core code.</span></span>
    * <span data-ttu-id="4d9bb-120">**データ**:Web アプリに送信されるデータの受信。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-120">**Data In**: Data ingress coming into your web app.</span></span>
    * <span data-ttu-id="4d9bb-121">**送信データ**:Web アプリからクライアントへのデータ送信。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-121">**Data Out**: Data egress from your web app to clients.</span></span>
    * <span data-ttu-id="4d9bb-122">**要求**:HTTP 要求の数。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-122">**Requests**: Count of HTTP requests.</span></span>
    * <span data-ttu-id="4d9bb-123">**平均応答時間**:Web アプリが HTTP 要求に応答するまでの平均時間。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-123">**Average Response Time**: Average time for the web app to respond to HTTP requests.</span></span>

    <span data-ttu-id="4d9bb-124">このページ上のトラブルシューティングと最適化のためのいくつかのセルフ サービス ツールにもあります。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-124">Several self-service tools for troubleshooting and optimization are also found on this page.</span></span>

    ![スクリーン ショットが表示されたセルフ サービス ツール](./media/monitoring/wizards.png)

    * <span data-ttu-id="4d9bb-126">**診断し、問題の解決**はセルフ サービスのトラブルシューティング ツール。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-126">**Diagnose and solve problems** is a self-service troubleshooter.</span></span>
    * <span data-ttu-id="4d9bb-127">**Application Insights**パフォーマンスとアプリの動作のプロファイルには、このセクションでは、後述します。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-127">**Application Insights** is for profiling performance and app behavior, and is discussed later in this section.</span></span>
    * <span data-ttu-id="4d9bb-128">**App Service Advisor**はアプリのエクスペリエンスを調整する推奨を作成します。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-128">**App Service Advisor** makes recommendations to tune your app experience.</span></span>

## <a name="advanced-monitoring"></a><span data-ttu-id="4d9bb-129">高度な監視</span><span class="sxs-lookup"><span data-stu-id="4d9bb-129">Advanced monitoring</span></span>

<span data-ttu-id="4d9bb-130">[Azure Monitor](/azure/monitoring-and-diagnostics/)はすべてのメトリックを監視および Azure サービス間でアラートを設定するための一元的なサービスです。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-130">[Azure Monitor](/azure/monitoring-and-diagnostics/) is the centralized service for monitoring all metrics and setting alerts across Azure services.</span></span> <span data-ttu-id="4d9bb-131">Azure Monitor 内で、管理者が細かくのパフォーマンスを追跡および傾向を特定できます。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-131">Within Azure Monitor, administrators can granularly track performance and identify trends.</span></span> <span data-ttu-id="4d9bb-132">各 Azure サービスは、独自[一連のメトリック](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions)Azure Monitor にします。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-132">Each Azure service offers its own [set of metrics](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) to Azure Monitor.</span></span>

## <a name="profile-with-application-insights"></a><span data-ttu-id="4d9bb-133">Application Insights でのプロファイル</span><span class="sxs-lookup"><span data-stu-id="4d9bb-133">Profile with Application Insights</span></span>

<span data-ttu-id="4d9bb-134">[Application Insights](/azure/application-insights/app-insights-overview)はパフォーマンスと安定性の web アプリとユーザーがそれらを使用する方法を分析するための Azure のサービスです。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-134">[Application Insights](/azure/application-insights/app-insights-overview) is an Azure service for analyzing the performance and stability of web apps and how users use them.</span></span> <span data-ttu-id="4d9bb-135">Application Insights からデータは、Azure Monitor のより深いです。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-135">The data from Application Insights is broader and deeper than that of Azure Monitor.</span></span> <span data-ttu-id="4d9bb-136">データには、開発者および管理者にアプリを向上させるためのキーの情報を提供できます。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-136">The data can provide developers and administrators with key information for improving apps.</span></span> <span data-ttu-id="4d9bb-137">Application Insights は、コードを変更せずに Azure App Service リソースに追加できます。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-137">Application Insights can be added to an Azure App Service resource without code changes.</span></span>

1. <span data-ttu-id="4d9bb-138">開く、 [Azure portal](https://portal.azure.com)、順に移動します、 *mywebapp\<unique_number\>*  App Service。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-138">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="4d9bb-139">**概要**タブをクリックし、 **Application Insights**を並べて表示します。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-139">From the **Overview** tab, click the **Application Insights** tile.</span></span>

    ![Application Insights タイルします。](./media/monitoring/app-insights.png)

1. <span data-ttu-id="4d9bb-141">選択、**新しいリソースを作成**ラジオ ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-141">Select the **Create new resource** radio button.</span></span> <span data-ttu-id="4d9bb-142">既定のリソース名を使用し、Application Insights リソースの場所を選択します。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-142">Use the default resource name, and select the location for the Application Insights resource.</span></span> <span data-ttu-id="4d9bb-143">場所の web アプリと一致する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-143">The location doesn't need to match that of your web app.</span></span>

    ![Application Insights のセットアップ](./media/monitoring/new-app-insights.png)

1. <span data-ttu-id="4d9bb-145">**ランタイム/フレームワーク**、 **ASP.NET Core**します。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-145">For **Runtime/Framework**, select **ASP.NET Core**.</span></span> <span data-ttu-id="4d9bb-146">既定の設定をそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-146">Accept the default settings.</span></span>
1. <span data-ttu-id="4d9bb-147">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-147">Select **OK**.</span></span> <span data-ttu-id="4d9bb-148">選択の確認を求められたら、**続行**します。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-148">If prompted to confirm, select **Continue**.</span></span>
1. <span data-ttu-id="4d9bb-149">リソースが作成された後は、Application Insights のページに直接移動する Application Insights リソースの名前をクリックします。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-149">After the resource has been created, click the name of Application Insights resource to navigate directly to the Application Insights page.</span></span>

    ![新しい Application Insights リソースが準備完了](./media/monitoring/new-app-insights-done.png)

<span data-ttu-id="4d9bb-151">アプリが使用されるため、データが蓄積されます。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-151">As the app is used, data accumulates.</span></span> <span data-ttu-id="4d9bb-152">選択**更新**ブレードに新しいデータを再読み込みします。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-152">Select **Refresh** to reload the blade with new data.</span></span>

![Application Insights の概要 タブ](./media/monitoring/app-insights-overview.png)

<span data-ttu-id="4d9bb-154">Application Insights は、追加構成なしで有用なサーバー側の情報を提供します。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-154">Application Insights provides useful server-side information with no additional configuration.</span></span> <span data-ttu-id="4d9bb-155">Application Insights から最大限の価値を取得する[Application Insights SDK を使用してアプリケーションをインストルメント化](/azure/application-insights/app-insights-asp-net-core)します。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-155">To get the most value from Application Insights, [instrument your app with the Application Insights SDK](/azure/application-insights/app-insights-asp-net-core).</span></span> <span data-ttu-id="4d9bb-156">適切に構成されたサービスは、web サーバーおよびクライアント側のパフォーマンスを含む、ブラウザー間でのエンド ツー エンド監視を提供します。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-156">When properly configured, the service provides end-to-end monitoring across the web server and browser, including client-side performance.</span></span> <span data-ttu-id="4d9bb-157">詳細については、、 [Application Insights のドキュメント](/azure/application-insights/app-insights-overview)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-157">For more information, see the [Application Insights documentation](/azure/application-insights/app-insights-overview).</span></span>

## <a name="logging"></a><span data-ttu-id="4d9bb-158">ログの記録</span><span class="sxs-lookup"><span data-stu-id="4d9bb-158">Logging</span></span>

<span data-ttu-id="4d9bb-159">Azure App Service で既定では、web サーバーとアプリのログが無効になります。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-159">Web server and app logs are disabled by default in Azure App Service.</span></span> <span data-ttu-id="4d9bb-160">次の手順でログを有効にします。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-160">Enable the logs with the following steps:</span></span>

1. <span data-ttu-id="4d9bb-161">開く、 [Azure portal](https://portal.azure.com)に移動し、 *mywebapp\<unique_number\>*  App Service。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-161">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="4d9bb-162">左側のメニューでスクロールして、**監視**セクション。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-162">In the menu to the left, scroll down to the **Monitoring** section.</span></span> <span data-ttu-id="4d9bb-163">選択**診断ログ**します。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-163">Select **Diagnostics logs**.</span></span>

    ![診断ログのリンク](./media/monitoring/logging.png)

1. <span data-ttu-id="4d9bb-165">オンにする**アプリケーションのログ記録 (ファイル システム)** します。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-165">Turn on **Application Logging (Filesystem)**.</span></span> <span data-ttu-id="4d9bb-166">メッセージが表示されたら、アプリ、web アプリでのログ記録を有効にする拡張機能をインストールするボックスをクリックします。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-166">If prompted, click the box to install the extensions to enable app logging in the web app.</span></span>
1. <span data-ttu-id="4d9bb-167">設定**Web サーバーのログ記録**に**ファイル システム**します。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-167">Set **Web server logging** to **File System**.</span></span>
1. <span data-ttu-id="4d9bb-168">入力、**保有期間**日以内にします。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-168">Enter the **Retention Period** in days.</span></span> <span data-ttu-id="4d9bb-169">たとえば、30 です。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-169">For example, 30.</span></span>
1. <span data-ttu-id="4d9bb-170">**[保存]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-170">Click **Save**.</span></span>

<span data-ttu-id="4d9bb-171">Web アプリでは、ASP.NET Core と web サーバー (App Service) のログが生成されます。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-171">ASP.NET Core and web server (App Service) logs are generated for the web app.</span></span> <span data-ttu-id="4d9bb-172">表示される FTP または FTPS の情報を使用してダウンロードできます。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-172">They can be downloaded using the FTP/FTPS information displayed.</span></span> <span data-ttu-id="4d9bb-173">パスワードは、このガイドの前半で作成したデプロイ資格情報と同じです。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-173">The password is the same as the deployment credentials created earlier in this guide.</span></span> <span data-ttu-id="4d9bb-174">ログを指定できます[PowerShell または Azure CLI をローカル コンピューターに直接ストリーム転送](/azure/app-service/web-sites-enable-diagnostic-log#download)します。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-174">The logs can be [streamed directly to your local machine with PowerShell or Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download).</span></span> <span data-ttu-id="4d9bb-175">記録をできますも[Application Insights で表示](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights)します。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-175">Logs can also be [viewed in Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span></span>

## <a name="log-streaming"></a><span data-ttu-id="4d9bb-176">ログ ストリーミング</span><span class="sxs-lookup"><span data-stu-id="4d9bb-176">Log streaming</span></span>

<span data-ttu-id="4d9bb-177">アプリと web サーバーのログは、ポータルからリアルタイムでストリーミングできます。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-177">App and web server logs can be streamed in real time through the portal.</span></span>

1. <span data-ttu-id="4d9bb-178">開く、 [Azure portal](https://portal.azure.com)に移動し、 *mywebapp\<unique_number\>*  App Service。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-178">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="4d9bb-179">左側のメニューでスクロールして、**監視**セクションし、選択**ログ ストリーム**します。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-179">In the menu to the left, scroll down to the **Monitoring** section and select **Log stream**.</span></span>

    ![ログ ストリーム リンクを示すスクリーン ショット](./media/monitoring/log-stream.png)

<span data-ttu-id="4d9bb-181">ログこともできます[Azure CLI または Azure PowerShell を使用してストリーミング](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs)など、Cloud Shell を使用します。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-181">Logs can also be [streamed via Azure CLI or Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), including through the Cloud Shell.</span></span>

## <a name="alerts"></a><span data-ttu-id="4d9bb-182">アラート</span><span class="sxs-lookup"><span data-stu-id="4d9bb-182">Alerts</span></span>

<span data-ttu-id="4d9bb-183">Azure Monitor も用意されています。[リアルタイム アラート](/azure/monitoring-and-diagnostics/insights-alerts-portal)メトリック、イベントの管理、およびその他の条件に基づいて。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-183">Azure Monitor also provides [real time alerts](/azure/monitoring-and-diagnostics/insights-alerts-portal) based on metrics, administrative events, and other criteria.</span></span>

> <span data-ttu-id="4d9bb-184">*注 :現在、web アプリのメトリックに対するアラートは、Alerts (クラシック) サービスでのみ使用できます。*</span><span class="sxs-lookup"><span data-stu-id="4d9bb-184">*Note: Currently alerting on web app metrics is only available in the Alerts (classic) service.*</span></span>

<span data-ttu-id="4d9bb-185">[アラート (クラシック) サービス](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal)Azure Monitor で、または 、**監視**App Service の設定のセクション。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-185">The [Alerts (classic) service](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) can be found in Azure Monitor or under the **Monitoring** section of the App Service settings.</span></span>

![アラート (クラシック) のリンク](./media/monitoring/alerts.png)

## <a name="live-debugging"></a><span data-ttu-id="4d9bb-187">ライブ デバッグ</span><span class="sxs-lookup"><span data-stu-id="4d9bb-187">Live debugging</span></span>

<span data-ttu-id="4d9bb-188">Azure App Service、 [Visual Studio でリモートでデバッグ](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)ログが十分な情報を指定しない場合。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-188">Azure App Service can be [debugged remotely with Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) when logs don't provide enough information.</span></span> <span data-ttu-id="4d9bb-189">ただし、リモート デバッグには、アプリは、デバッグ シンボルを指定してコンパイルする必要があります。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-189">However, remote debugging requires the app to be compiled with debug symbols.</span></span> <span data-ttu-id="4d9bb-190">デバッグしないでくださいで行う、運用環境を除く、最後の手段として。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-190">Debugging shouldn't be done in production, except as a last resort.</span></span>

## <a name="conclusion"></a><span data-ttu-id="4d9bb-191">まとめ</span><span class="sxs-lookup"><span data-stu-id="4d9bb-191">Conclusion</span></span>

<span data-ttu-id="4d9bb-192">このセクションでは、次のタスクが完了しました。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-192">In this section, you completed the following tasks:</span></span>

* <span data-ttu-id="4d9bb-193">基本的な監視とトラブルシューティング、Azure portal でのデータを検索します。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-193">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="4d9bb-194">Azure Monitor ですべての Azure サービス間でのメトリックについて詳しく説明を提供する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-194">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="4d9bb-195">アプリのプロファイリング用 Application Insights による web アプリを接続します。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-195">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="4d9bb-196">ログ記録をオンにし、ログをダウンロードする方法を学習します</span><span class="sxs-lookup"><span data-stu-id="4d9bb-196">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="4d9bb-197">Stream のログをリアルタイムで</span><span class="sxs-lookup"><span data-stu-id="4d9bb-197">Stream logs in real time</span></span>
* <span data-ttu-id="4d9bb-198">アラートを設定する方法を学習します</span><span class="sxs-lookup"><span data-stu-id="4d9bb-198">Learn where to set up alerts</span></span>
* <span data-ttu-id="4d9bb-199">リモート Azure App Service web アプリのデバッグについて説明します。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-199">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="4d9bb-200">その他の参考資料</span><span class="sxs-lookup"><span data-stu-id="4d9bb-200">Additional reading</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="4d9bb-201">Application Insights で Azure web アプリのパフォーマンスを監視します。</span><span class="sxs-lookup"><span data-stu-id="4d9bb-201">Monitor Azure web app performance with Application Insights</span></span>](/azure/application-insights/app-insights-azure-web-apps)
* [<span data-ttu-id="4d9bb-202">Azure App Service の Web アプリの診断ログの有効化</span><span class="sxs-lookup"><span data-stu-id="4d9bb-202">Enable diagnostics logging for web apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)
* [<span data-ttu-id="4d9bb-203">Visual Studio を使用した Azure App Service での Web アプリのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="4d9bb-203">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="4d9bb-204">Azure Monitor での Azure サービス クラシック メトリック アラートを作成する Azure portal</span><span class="sxs-lookup"><span data-stu-id="4d9bb-204">Create classic metric alerts in Azure Monitor for Azure services - Azure portal</span></span>](/azure/monitoring-and-diagnostics/insights-alerts-portal)
