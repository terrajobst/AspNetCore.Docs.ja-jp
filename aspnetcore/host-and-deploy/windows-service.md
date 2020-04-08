---
title: Windows サービスでの ASP.NET Core のホスト
author: rick-anderson
description: Windows サービスで ASP.NET Core アプリケーションをホストする方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/windows-service
ms.openlocfilehash: 5cb61d330df7e15fbd54396207792596ae018fd3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417586"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="9d2b4-103">Windows サービスでの ASP.NET Core のホスト</span><span class="sxs-lookup"><span data-stu-id="9d2b4-103">Host ASP.NET Core in a Windows Service</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9d2b4-104">ASP.NET Core アプリは、IIS を 使用せずに、[Windows サービス](/dotnet/framework/windows-services/introduction-to-windows-service-applications)として Windows にホストできます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-104">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="9d2b4-105">Windows サービスとしてホストされている場合、サーバーの再起動後にアプリが自動的に起動します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-105">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="9d2b4-106">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9d2b4-107">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="9d2b4-107">Prerequisites</span></span>

* [<span data-ttu-id="9d2b4-108">ASP.NET Core SDK 2.1 以降</span><span class="sxs-lookup"><span data-stu-id="9d2b4-108">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="9d2b4-109">PowerShell 6.2 以降</span><span class="sxs-lookup"><span data-stu-id="9d2b4-109">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="9d2b4-110">ワーカー サービス テンプレート</span><span class="sxs-lookup"><span data-stu-id="9d2b4-110">Worker Service template</span></span>

<span data-ttu-id="9d2b4-111">ASP.NET Core ワーカー サービス テンプレートは、実行時間が長いサービス アプリを作成する場合の出発点として利用できます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-111">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="9d2b4-112">テンプレートを Windows サービス アプリの基礎として使用するには:</span><span class="sxs-lookup"><span data-stu-id="9d2b4-112">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="9d2b4-113">.NET Core テンプレートからワーカー サービス アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-113">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="9d2b4-114">「[アプリの構成](#app-configuration)」セクションのガイダンスに従って、Windows サービスとして実行できるようにワーカー サービス アプリを更新します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-114">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="9d2b4-115">アプリの構成</span><span class="sxs-lookup"><span data-stu-id="9d2b4-115">App configuration</span></span>

<span data-ttu-id="9d2b4-116">アプリには、[Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices)のパッケージ参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-116">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="9d2b4-117">ホストのビルド時に `IHostBuilder.UseWindowsService` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-117">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="9d2b4-118">アプリが Windows サービスとして実行している場合、メソッドは</span><span class="sxs-lookup"><span data-stu-id="9d2b4-118">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="9d2b4-119">ホストの有効期間を `WindowsServiceLifetime` に設定します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-119">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="9d2b4-120">[コンテンツ ルート](xref:fundamentals/index#content-root)を [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory) に設定します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-120">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="9d2b4-121">詳しくは、「[現在のディレクトリとコンテンツのルート](#current-directory-and-content-root)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-121">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="9d2b4-122">イベント ログへのログ記録を有効にします。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-122">Enables logging to the event log:</span></span>
  * <span data-ttu-id="9d2b4-123">アプリケーション名が既定のソース名として使用されます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-123">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="9d2b4-124">既定のログ レベルは、ホストを構築するために `CreateDefaultBuilder` が呼び出される ASP.NET Core テンプレートに基づくアプリに対して "*警告*" 以上です。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-124">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="9d2b4-125">既定のログ レベルを、*appsettings.json*/*appsettings.{環境}.json* の `Logging:EventLog:LogLevel:Default` キーまたは他の構成プロバイダーでオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-125">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="9d2b4-126">管理者のみが新しいイベント ソースを作成できます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-126">Only administrators can create new event sources.</span></span> <span data-ttu-id="9d2b4-127">アプリケーション名を使用して、イベント ソースを作成できない場合、警告が*アプリケーション* ソースに記録され、イベント ログが無効になります。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-127">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="9d2b4-128">*Program.cs* の `CreateHostBuilder` の場合:</span><span class="sxs-lookup"><span data-stu-id="9d2b4-128">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="9d2b4-129">このトピックには、次のサンプル アプリが付属しています。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-129">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="9d2b4-130">バックグラウンド ワーカー サービスのサンプル &ndash; バックグラウンド タスク用に[ホステッド サービス](xref:fundamentals/host/hosted-services)を使用する[ワーカー サービステンプレート](#worker-service-template)に基づく、非 Web アプリのサンプルです。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-130">Background Worker Service Sample &ndash; A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="9d2b4-131">Web アプリ サービスのサンプル &ndash; バックグラウンド タスク用の[ホステッド サービス](xref:fundamentals/host/hosted-services)を使用する Windows サービスとして実行される、Razor Pages Web アプリのサンプルです。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-131">Web App Service Sample &ndash; A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="9d2b4-132">MVC のガイダンスについては、「<xref:mvc/overview>」と「<xref:migration/22-to-30>」にある記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-132">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="9d2b4-133">配置の種類</span><span class="sxs-lookup"><span data-stu-id="9d2b4-133">Deployment type</span></span>

<span data-ttu-id="9d2b4-134">展開のシナリオに関する情報および注意事項については、「[.NET Core アプリケーションの展開](/dotnet/core/deploying/)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-134">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="9d2b4-135">SDK</span><span class="sxs-lookup"><span data-stu-id="9d2b4-135">SDK</span></span>

<span data-ttu-id="9d2b4-136">Razor Pages または MVC フレームワークを使用する Web アプリベースのサービスでは、プロジェクト ファイルに Web SDK を指定します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-136">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="9d2b4-137">サービスでバックグラウンド タスク ([ホステッド サービス](xref:fundamentals/host/hosted-services)など) のみを実行する場合は、プロジェクト ファイルにワーカー SDK を指定します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-137">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="9d2b4-138">フレームワーク依存型展開 (FDD)</span><span class="sxs-lookup"><span data-stu-id="9d2b4-138">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="9d2b4-139">フレームワーク依存型展開 (FDD) は、ターゲット システムに .NET Core のシステム全体の共有バージョンが存在することに依存します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-139">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="9d2b4-140">この記事のガイダンスに従って、FDD シナリオを採用すると、*フレームワーク依存型実行可能ファイル* と呼ばれる実行可能ファイル ( *.exe*) が SDK によって生成されます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-140">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="9d2b4-141">[Web SDK](#sdk) を使用している場合、*web.config* ファイル (通常 ASP.NET Core アプリを発行する際に生成されます) は、Windows サービス アプリに対しては必要ありません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-141">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="9d2b4-142">*web.config* ファイルの作成を無効にするには、`true` に設定した `<IsTransformWebConfigDisabled>` プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-142">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="9d2b4-143">自己完結型の展開 (SCD)</span><span class="sxs-lookup"><span data-stu-id="9d2b4-143">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="9d2b4-144">自己完結型の展開 (SCD) は、ホスト システムに共有フレームワークが存在することに依存しません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-144">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="9d2b4-145">ランタイムとアプリの依存関係が、アプリと共に展開されます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-145">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="9d2b4-146">Windows [ランタイム識別子 (RID)](/dotnet/core/rid-catalog) は、ターゲット フレームワークを格納する `<PropertyGroup>` に含まれます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-146">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="9d2b4-147">複数の RID を発行するには、次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-147">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="9d2b4-148">セミコロンで区切られたリストの形式で RID を指定します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-148">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="9d2b4-149">プロパティ名 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (複数) を使用します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-149">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="9d2b4-150">詳細については、「[.NET Core の RID カタログ](/dotnet/core/rid-catalog)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-150">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="9d2b4-151">サービス ユーザー アカウント</span><span class="sxs-lookup"><span data-stu-id="9d2b4-151">Service user account</span></span>

<span data-ttu-id="9d2b4-152">サービス用のユーザー アカウントを作成するには、PowerShell 6 の管理コマンド シェルから [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) コマンドレットを使用します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-152">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="9d2b4-153">Windows 10 October 2018 Update (バージョン 1809/ビルド 10.0.17763) 以降:</span><span class="sxs-lookup"><span data-stu-id="9d2b4-153">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="9d2b4-154">Windows 10 October 2018 Update (バージョン 1809/ビルド 10.0.17763) 以前の Windows OS:</span><span class="sxs-lookup"><span data-stu-id="9d2b4-154">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="9d2b4-155">入力を求められたら、[強力なパスワード](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)を指定します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-155">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="9d2b4-156">[New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) コマンドレットに <xref:System.DateTime> という有効期限で `-AccountExpires` パラメーターを指定しない場合、アカウントは期限切れになりません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-156">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="9d2b4-157">詳しくは、「[Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/)」および「[Service User Accounts (サービス ユーザー アカウント)](/windows/desktop/services/service-user-accounts)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-157">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="9d2b4-158">Active Directory を使う場合、ユーザーを管理するための別の方法は、マネージド サービス アカウントを使うことです。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-158">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="9d2b4-159">詳細については、「[Group Managed Service Accounts Overview (グループ マネージド サービス アカウントの概要)](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-159">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="9d2b4-160">サービスとしてログオン権利</span><span class="sxs-lookup"><span data-stu-id="9d2b4-160">Log on as a service rights</span></span>

<span data-ttu-id="9d2b4-161">サービス ユーザー アカウントに*サービスとしてログオン*権利を確立するには、次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-161">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="9d2b4-162">*secpol.msc* を実行して、ローカル セキュリティ ポリシー エディターを開きます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-162">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="9d2b4-163">**[ローカル ポリシー]** ノードを展開し、 **[ユーザー権利の割り当て]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-163">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="9d2b4-164">**[サービスとしてログオン]** ポリシーを開きます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-164">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="9d2b4-165">**[ユーザーまたはグループの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-165">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="9d2b4-166">次のいずれかの方法を使用して、オブジェクト名 (ユーザー アカウント) を指定します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-166">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="9d2b4-167">オブジェクト名フィールドにユーザー アカウント (`{DOMAIN OR COMPUTER NAME\USER}`) を入力し、 **[OK]** を選択して、ポリシーにユーザーを追加します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-167">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="9d2b4-168">**[詳細]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-168">Select **Advanced**.</span></span> <span data-ttu-id="9d2b4-169">**[検索開始]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-169">Select **Find Now**.</span></span> <span data-ttu-id="9d2b4-170">一覧からユーザー アカウントを選択します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-170">Select the user account from the list.</span></span> <span data-ttu-id="9d2b4-171">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-171">Select **OK**.</span></span> <span data-ttu-id="9d2b4-172">再度 **[OK]** を選択して、ポリシーにユーザーを追加します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-172">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="9d2b4-173">**[OK]** または **[適用]** を選択して、変更を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-173">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="9d2b4-174">Windows サービスを作成して管理する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-174">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="9d2b4-175">サービスを作成する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-175">Create a service</span></span>

<span data-ttu-id="9d2b4-176">PowerShell コマンドを使用して、サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-176">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="9d2b4-177">PowerShell 6 の管理コマンド シェルから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-177">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="9d2b4-178">`{EXE PATH}` &ndash; ホスト上のアプリのフォルダーへのパス (`d:\myservice` など)。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-178">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="9d2b4-179">このパスに、アプリの実行可能ファイルは含めないでください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-179">Don't include the app's executable in the path.</span></span> <span data-ttu-id="9d2b4-180">末尾のスラッシュは、必要ありません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-180">A trailing slash isn't required.</span></span>
* <span data-ttu-id="9d2b4-181">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; サービスのユーザー アカウント (`Contoso\ServiceUser` など)。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-181">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="9d2b4-182">`{SERVICE NAME}` &ndash; サービス名 (`MyService` など)。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-182">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="9d2b4-183">`{EXE FILE PATH}` &ndash; アプリの実行可能ファイルのパス (`d:\myservice\myservice.exe` など)。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-183">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="9d2b4-184">拡張子付きの実行可能ファイルのファイル名を含めます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-184">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="9d2b4-185">`{DESCRIPTION}` &ndash; サービスの説明 (`My sample service` など)。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-185">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="9d2b4-186">`{DISPLAY NAME}` &ndash; サービスの表示名 (`My Service` など)。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-186">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="9d2b4-187">サービスを開始する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-187">Start a service</span></span>

<span data-ttu-id="9d2b4-188">次の PowerShell 6 コマンドでサービスを開始します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-188">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="9d2b4-189">このコマンドでサービスを開始するには数秒かかります。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-189">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="9d2b4-190">サービスの状態を確認する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-190">Determine a service's status</span></span>

<span data-ttu-id="9d2b4-191">サービスの状態を確認するには、次の PowerShell 6 コマンドを使います。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-191">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="9d2b4-192">この状態は、次のいずれかの値として報告されます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-192">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="9d2b4-193">サービスを停止する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-193">Stop a service</span></span>

<span data-ttu-id="9d2b4-194">次の PowerShell 6 コマンドでサービスを停止します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-194">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="9d2b4-195">サービスを削除する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-195">Remove a service</span></span>

<span data-ttu-id="9d2b4-196">サービスを停止した後、少ししてから、次の Powershell 6 コマンドを使ってサービスを削除します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-196">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="9d2b4-197">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="9d2b4-197">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="9d2b4-198">インターネットまたは企業ネットワークからの要求とやり取りするサービスやプロキシまたはロード バランサーの背後にあるサービスでは、追加の構成が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-198">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="9d2b4-199">詳細については、「<xref:host-and-deploy/proxy-load-balancer>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-199">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="9d2b4-200">エンドポイントを構成する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-200">Configure endpoints</span></span>

<span data-ttu-id="9d2b4-201">既定では、ASP.NET Core は `http://localhost:5000` にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-201">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="9d2b4-202">`ASPNETCORE_URLS` 環境変数を設定して、URL とポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-202">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="9d2b4-203">追加の URL とポートの構成方法については、関連するサーバーの記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-203">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="9d2b4-204">上記のガイダンスでは、HTTPS エンドポイントのサポートについて説明しています。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-204">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="9d2b4-205">たとえば、Windows サービスで認証が使用されている場合は、アプリを HTTPS 用に構成します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-205">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="9d2b4-206">サービス エンドポイントをセキュリティで保護するために ASP.NET Core の HTTPS 開発証明書を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-206">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="9d2b4-207">現在のディレクトリとコンテンツのルート</span><span class="sxs-lookup"><span data-stu-id="9d2b4-207">Current directory and content root</span></span>

<span data-ttu-id="9d2b4-208">Windows サービスに対して <xref:System.IO.Directory.GetCurrentDirectory*> を呼び出して返される現在の作業ディレクトリは *C:\\WINDOWS\\system32* フォルダーです。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-208">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="9d2b4-209">*system32* フォルダーは、サービスのファイル (設定ファイルなど) を保存するために適した場所ではありません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-209">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="9d2b4-210">次のいずれかの方法を使用して、サービスのアセットと設定ファイルを管理し、アクセスします。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-210">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="9d2b4-211">ContentRootPath または ContentRootFileProvider を使用する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-211">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="9d2b4-212">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) または <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> を使用して、アプリのリソースを見つけます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-212">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="9d2b4-213">アプリがサービスとして実行されると、<xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> によって <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> が [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory) に設定されます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-213">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="9d2b4-214">アプリの既定設定ファイルである *appsettings.json* と *appsettings.{Environment}.json* は、[ホストの構築中に CreateDefaultBuilder](xref:fundamentals/host/generic-host#set-up-a-host) を呼び出すことで、アプリのコンテンツ ルートから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-214">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="9d2b4-215"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> の開発者コードによって読み込まれた他の設定ファイルについては、<xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> を呼び出す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-215">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="9d2b4-216">次の例では、*custom_settings.json* ファイルはアプリのコンテンツ ルートに存在しており、明示的にベース パスを設定しなくても読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-216">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="9d2b4-217">Windows サービス アプリからは *C:\\WINDOWS\\system32* フォルダーが現在のディレクトリとして返されるため、<xref:System.IO.Directory.GetCurrentDirectory*> を使用してリソース パスを取得しないでください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-217">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory*> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="9d2b4-218">ディスク上の適切な場所にサービスのファイルを格納する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-218">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="9d2b4-219">ファイルを含むフォルダーに対して <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> を使用するときは、<xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> を使用して絶対パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-219">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="9d2b4-220">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="9d2b4-220">Troubleshoot</span></span>

<span data-ttu-id="9d2b4-221">Windows サービス アプリのトラブルシューティングについては、「<xref:test/troubleshoot>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-221">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="9d2b4-222">一般的なエラー</span><span class="sxs-lookup"><span data-stu-id="9d2b4-222">Common errors</span></span>

* <span data-ttu-id="9d2b4-223">PowerShell の以前のバージョンまたはプレリリース バージョンが使用されています。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-223">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="9d2b4-224">登録されたサービスに、[dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドからのアプリの**発行済み**出力が使用されません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-224">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="9d2b4-225">[dotnet build](/dotnet/core/tools/dotnet-build) コマンドの出力が、アプリの展開でサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-225">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="9d2b4-226">発行された資産は、展開の種類に応じて次のいずれかのフォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-226">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="9d2b4-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="9d2b4-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="9d2b4-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="9d2b4-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="9d2b4-229">サービスが実行中の状態ではありません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-229">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="9d2b4-230">アプリに使用されるリソース (証明書など) のパスが正しくありません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-230">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="9d2b4-231">Windows サービスのベース パスは *c:\\Windows\\System32* です。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-231">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="9d2b4-232">"*サービスとしてログオンする*" アクセス権がユーザーにありません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-232">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="9d2b4-233">`New-Service` PowerShell コマンドの実行時に、ユーザーのパスワードの有効期限が切れていたか、正しく渡されていません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-233">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="9d2b4-234">アプリに ASP.NET Core 認証が必要ですが、セキュリティで保護された接続 (HTTPS) 用に構成されていません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-234">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="9d2b4-235">要求 URL ポートが正しくないか、アプリで正しく構成されていません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-235">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="9d2b4-236">システム イベント ログとアプリケーション イベント ログ</span><span class="sxs-lookup"><span data-stu-id="9d2b4-236">System and Application Event Logs</span></span>

<span data-ttu-id="9d2b4-237">システム イベント ログとアプリケーション イベント ログにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-237">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="9d2b4-238">[スタート] メニューを開き、*イベント ビューアー*を検索し、**イベント ビューアー** アプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-238">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="9d2b4-239">**イベント ビューアー**で **[Windows ログ]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-239">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="9d2b4-240">**[システム]** を選択してシステム イベント ログを開きます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-240">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="9d2b4-241">**[Application]** を選択して、アプリケーション イベント ログを開きます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-241">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="9d2b4-242">失敗したアプリに関連するエラーを検索します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-242">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="9d2b4-243">コマンド プロンプトでアプリを実行する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-243">Run the app at a command prompt</span></span>

<span data-ttu-id="9d2b4-244">多くのスタートアップ エラーでは、イベント ログに有用な情報が生成されません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-244">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="9d2b4-245">ホスティング システムのコマンド プロンプトでアプリを実行すると、エラーの原因がわかることがあります。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-245">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="9d2b4-246">アプリからさらに詳細情報をログに記録するには、[ログ レベル](xref:fundamentals/logging/index#log-level)を低くするか、[開発環境](xref:fundamentals/environments)でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-246">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="9d2b4-247">パッケージ キャッシュをクリアする</span><span class="sxs-lookup"><span data-stu-id="9d2b4-247">Clear package caches</span></span>

<span data-ttu-id="9d2b4-248">開発マシンで .NET Core SDK をアップグレードしたり、アプリ内のパッケージ バージョンを変更したりした直後に、機能しているアプリが失敗することがあります。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-248">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="9d2b4-249">場合によっては、パッケージに統一性がないと、メジャー アップグレード実行時にアプリが破壊されることがあります。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-249">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="9d2b4-250">これらの問題のほとんどは、次の手順で解決できます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-250">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="9d2b4-251">*bin* フォルダーと *obj* フォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-251">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="9d2b4-252">コマンド シェルから [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) を実行して、パッケージ キャッシュをクリアします。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-252">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="9d2b4-253">パッケージ キャッシュをクリアするには、[nuget.exe](https://www.nuget.org/downloads) ツールを使用して `nuget locals all -clear` コマンドを実行する方法もあります。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-253">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="9d2b4-254">*nuget.exe* は、Windows デスクトップ オペレーティング システムにバンドルされているインストールではなく、[NuGet Web サイト](https://www.nuget.org/downloads)から別に入手する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-254">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="9d2b4-255">プロジェクトを復元してリビルドします。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-255">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="9d2b4-256">アプリを再展開する前に、サーバー上の展開フォルダー内のすべてのファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-256">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="9d2b4-257">アプリの速度低下またはハング</span><span class="sxs-lookup"><span data-stu-id="9d2b4-257">Slow or hanging app</span></span>

<span data-ttu-id="9d2b4-258">"*クラッシュ ダンプ*" とはシステムのメモリのスナップショットであり、アプリのクラッシュ、起動の失敗、遅いアプリの原因を突き止めるのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-258">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="9d2b4-259">アプリのクラッシュまたは例外の発生</span><span class="sxs-lookup"><span data-stu-id="9d2b4-259">App crashes or encounters an exception</span></span>

<span data-ttu-id="9d2b4-260">[Windows エラー報告 (WER)](/windows/desktop/wer/windows-error-reporting) からダンプを取得して分析します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-260">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="9d2b4-261">`c:\dumps` にクラッシュ ダンプ ファイルを保持するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-261">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="9d2b4-262">次のアプリケーションの実行可能ファイル名で [EnableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1)を実行します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-262">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="9d2b4-263">クラッシュが発生する条件の下でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-263">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="9d2b4-264">クラッシュが発生した後、[DisableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1)を実行します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-264">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span></span>

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="9d2b4-265">アプリがクラッシュし、ダンプの収集が完了したら、アプリを普通に終了してかまいません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-265">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="9d2b4-266">PowerShell スクリプトにより、アプリごとに最大 5 つのダンプを収集する WER が構成されます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-266">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="9d2b4-267">クラッシュ ダンプでは、大量のディスク領域 (1 つにつき最大、数ギガバイト) が使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-267">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="9d2b4-268">アプリが起動時または正常な実行中にハングまたは失敗する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-268">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="9d2b4-269">アプリが起動時または正常な実行中に "*ハング*" (応答を停止するがクラッシュしない) または失敗するときは、「[User-Mode Dump Files:Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)」(ユーザー モード ダンプ ファイル: 最適なツールの選択) を参照し、適切なツールを選択してダンプを生成します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-269">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="9d2b4-270">ダンプを分析する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-270">Analyze the dump</span></span>

<span data-ttu-id="9d2b4-271">ダンプはいくつかの方法で分析できます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-271">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="9d2b4-272">詳細については、「[Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)」(ユーザー モード ダンプ ファイルの分析) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-272">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9d2b4-273">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="9d2b4-273">Additional resources</span></span>

* <span data-ttu-id="9d2b4-274">[Kestrel エンドポイント構成](xref:fundamentals/servers/kestrel#endpoint-configuration) (HTTPS 構成と SNI サポートを含む)</span><span class="sxs-lookup"><span data-stu-id="9d2b4-274">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="9d2b4-275">ASP.NET Core アプリは、IIS を 使用せずに、[Windows サービス](/dotnet/framework/windows-services/introduction-to-windows-service-applications)として Windows にホストできます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-275">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="9d2b4-276">Windows サービスとしてホストされている場合、サーバーの再起動後にアプリが自動的に起動します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-276">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="9d2b4-277">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-277">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9d2b4-278">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="9d2b4-278">Prerequisites</span></span>

* [<span data-ttu-id="9d2b4-279">ASP.NET Core SDK 2.1 以降</span><span class="sxs-lookup"><span data-stu-id="9d2b4-279">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="9d2b4-280">PowerShell 6.2 以降</span><span class="sxs-lookup"><span data-stu-id="9d2b4-280">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="9d2b4-281">アプリの構成</span><span class="sxs-lookup"><span data-stu-id="9d2b4-281">App configuration</span></span>

<span data-ttu-id="9d2b4-282">アプリでは、[Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) と [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) へのパッケージ参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-282">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="9d2b4-283">サービスの外部で実行しているときにテストとデバッグを行うには、アプリがサービスとして実行しているかコンソール アプリとして実行しているかを判別するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-283">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="9d2b4-284">デバッガーがアタッチされているか、`--console` スイッチが存在するかを検査します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-284">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="9d2b4-285">いずれかの条件が満たされる場合 (アプリがサービスとして実行していない場合)、<xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-285">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="9d2b4-286">条件が満たされない場合 (アプリがサービスとして実行している場合):</span><span class="sxs-lookup"><span data-stu-id="9d2b4-286">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="9d2b4-287"><xref:System.IO.Directory.SetCurrentDirectory*> を呼び出し、アプリの発行場所のパスを使用します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-287">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="9d2b4-288">パスを取得するために <xref:System.IO.Directory.GetCurrentDirectory*> を呼び出さないでください。<xref:System.IO.Directory.GetCurrentDirectory*> が呼び出されると、Windows サービス アプリは *C:\\WINDOWS\\system32* フォルダーを戻すためです。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-288">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="9d2b4-289">詳しくは、「[現在のディレクトリとコンテンツのルート](#current-directory-and-content-root)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-289">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="9d2b4-290">`CreateWebHostBuilder` でアプリを構成する前に、この手順に従います。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-290">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="9d2b4-291"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> を呼び出して、アプリをサービスとして実行します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-291">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="9d2b4-292">[コマンドライン構成プロバイダー](xref:fundamentals/configuration/index#command-line-configuration-provider)では、コマンドライン引数に名前と値の組が必要であるため、<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> が引数を受け取る前に `--console` スイッチは引数から削除されます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-292">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="9d2b4-293">Windows イベント ログに書き込むには、EventLog プロバイダーを <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> に追加します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-293">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="9d2b4-294">*appsettings.Production.json* ファイルで `Logging:LogLevel:Default` キーを使用してログ レベルを設定します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-294">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="9d2b4-295">サンプル アプリの次の例では、アプリ内で有効期間イベントを処理するために、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> の代わりに `RunAsCustomService` を呼び出しています。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-295">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="9d2b4-296">詳しくは、「[イベントの開始と停止を扱う](#handle-starting-and-stopping-events)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-296">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="9d2b4-297">配置の種類</span><span class="sxs-lookup"><span data-stu-id="9d2b4-297">Deployment type</span></span>

<span data-ttu-id="9d2b4-298">展開のシナリオに関する情報および注意事項については、「[.NET Core アプリケーションの展開](/dotnet/core/deploying/)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-298">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="9d2b4-299">SDK</span><span class="sxs-lookup"><span data-stu-id="9d2b4-299">SDK</span></span>

<span data-ttu-id="9d2b4-300">Razor Pages または MVC フレームワークを使用する Web アプリベースのサービスでは、プロジェクト ファイルに Web SDK を指定します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-300">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="9d2b4-301">サービスでバックグラウンド タスク ([ホステッド サービス](xref:fundamentals/host/hosted-services)など) のみを実行する場合は、プロジェクト ファイルにワーカー SDK を指定します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-301">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="9d2b4-302">フレームワーク依存型展開 (FDD)</span><span class="sxs-lookup"><span data-stu-id="9d2b4-302">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="9d2b4-303">フレームワーク依存型展開 (FDD) は、ターゲット システムに .NET Core のシステム全体の共有バージョンが存在することに依存します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-303">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="9d2b4-304">この記事のガイダンスに従って、FDD シナリオを採用すると、*フレームワーク依存型実行可能ファイル* と呼ばれる実行可能ファイル ( *.exe*) が SDK によって生成されます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-304">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="9d2b4-305">Windows [ランタイム識別子 (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) にはターゲット フレームワークが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-305">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="9d2b4-306">次の例では、RID が `win7-x64` に設定されています。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-306">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="9d2b4-307">`<SelfContained>` プロパティが `false` に設定されている。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-307">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="9d2b4-308">これらのプロパティによって、Windows 用の実行可能ファイル ( *.exe*) と共有 .NET Core フレームワークに依存するアプリを生成するよう SDK に指示します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-308">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="9d2b4-309">*web.config* ファイル (通常 ASP.NET Core アプリを発行する際に生成されます) は、Windows サービス アプリに対しては必要ありません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-309">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="9d2b4-310">*web.config* ファイルの作成を無効にするには、`true` に設定した `<IsTransformWebConfigDisabled>` プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-310">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="9d2b4-311">自己完結型の展開 (SCD)</span><span class="sxs-lookup"><span data-stu-id="9d2b4-311">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="9d2b4-312">自己完結型の展開 (SCD) は、ホスト システムに共有フレームワークが存在することに依存しません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-312">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="9d2b4-313">ランタイムとアプリの依存関係が、アプリと共に展開されます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-313">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="9d2b4-314">Windows [ランタイム識別子 (RID)](/dotnet/core/rid-catalog) は、ターゲット フレームワークを格納する `<PropertyGroup>` に含まれます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-314">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="9d2b4-315">複数の RID を発行するには、次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-315">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="9d2b4-316">セミコロンで区切られたリストの形式で RID を指定します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-316">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="9d2b4-317">プロパティ名 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (複数) を使用します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-317">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="9d2b4-318">詳細については、「[.NET Core の RID カタログ](/dotnet/core/rid-catalog)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-318">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="9d2b4-319">`<SelfContained>` プロパティが `true` に設定されています。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-319">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="9d2b4-320">サービス ユーザー アカウント</span><span class="sxs-lookup"><span data-stu-id="9d2b4-320">Service user account</span></span>

<span data-ttu-id="9d2b4-321">サービス用のユーザー アカウントを作成するには、PowerShell 6 の管理コマンド シェルから [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) コマンドレットを使用します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-321">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="9d2b4-322">Windows 10 October 2018 Update (バージョン 1809/ビルド 10.0.17763) 以降:</span><span class="sxs-lookup"><span data-stu-id="9d2b4-322">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="9d2b4-323">Windows 10 October 2018 Update (バージョン 1809/ビルド 10.0.17763) 以前の Windows OS:</span><span class="sxs-lookup"><span data-stu-id="9d2b4-323">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="9d2b4-324">入力を求められたら、[強力なパスワード](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)を指定します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-324">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="9d2b4-325">[New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) コマンドレットに <xref:System.DateTime> という有効期限で `-AccountExpires` パラメーターを指定しない場合、アカウントは期限切れになりません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-325">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="9d2b4-326">詳しくは、「[Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/)」および「[Service User Accounts (サービス ユーザー アカウント)](/windows/desktop/services/service-user-accounts)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-326">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="9d2b4-327">Active Directory を使う場合、ユーザーを管理するための別の方法は、マネージド サービス アカウントを使うことです。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-327">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="9d2b4-328">詳細については、「[Group Managed Service Accounts Overview (グループ マネージド サービス アカウントの概要)](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-328">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="9d2b4-329">サービスとしてログオン権利</span><span class="sxs-lookup"><span data-stu-id="9d2b4-329">Log on as a service rights</span></span>

<span data-ttu-id="9d2b4-330">サービス ユーザー アカウントに*サービスとしてログオン*権利を確立するには、次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-330">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="9d2b4-331">*secpol.msc* を実行して、ローカル セキュリティ ポリシー エディターを開きます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-331">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="9d2b4-332">**[ローカル ポリシー]** ノードを展開し、 **[ユーザー権利の割り当て]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-332">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="9d2b4-333">**[サービスとしてログオン]** ポリシーを開きます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-333">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="9d2b4-334">**[ユーザーまたはグループの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-334">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="9d2b4-335">次のいずれかの方法を使用して、オブジェクト名 (ユーザー アカウント) を指定します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-335">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="9d2b4-336">オブジェクト名フィールドにユーザー アカウント (`{DOMAIN OR COMPUTER NAME\USER}`) を入力し、 **[OK]** を選択して、ポリシーにユーザーを追加します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-336">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="9d2b4-337">**[詳細]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-337">Select **Advanced**.</span></span> <span data-ttu-id="9d2b4-338">**[検索開始]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-338">Select **Find Now**.</span></span> <span data-ttu-id="9d2b4-339">一覧からユーザー アカウントを選択します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-339">Select the user account from the list.</span></span> <span data-ttu-id="9d2b4-340">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-340">Select **OK**.</span></span> <span data-ttu-id="9d2b4-341">再度 **[OK]** を選択して、ポリシーにユーザーを追加します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-341">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="9d2b4-342">**[OK]** または **[適用]** を選択して、変更を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-342">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="9d2b4-343">Windows サービスを作成して管理する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-343">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="9d2b4-344">サービスを作成する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-344">Create a service</span></span>

<span data-ttu-id="9d2b4-345">PowerShell コマンドを使用して、サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-345">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="9d2b4-346">PowerShell 6 の管理コマンド シェルから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-346">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="9d2b4-347">`{EXE PATH}` &ndash; ホスト上のアプリのフォルダーへのパス (`d:\myservice` など)。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-347">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="9d2b4-348">このパスに、アプリの実行可能ファイルは含めないでください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-348">Don't include the app's executable in the path.</span></span> <span data-ttu-id="9d2b4-349">末尾のスラッシュは、必要ありません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-349">A trailing slash isn't required.</span></span>
* <span data-ttu-id="9d2b4-350">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; サービスのユーザー アカウント (`Contoso\ServiceUser` など)。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-350">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="9d2b4-351">`{SERVICE NAME}` &ndash; サービス名 (`MyService` など)。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-351">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="9d2b4-352">`{EXE FILE PATH}` &ndash; アプリの実行可能ファイルのパス (`d:\myservice\myservice.exe` など)。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-352">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="9d2b4-353">拡張子付きの実行可能ファイルのファイル名を含めます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-353">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="9d2b4-354">`{DESCRIPTION}` &ndash; サービスの説明 (`My sample service` など)。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-354">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="9d2b4-355">`{DISPLAY NAME}` &ndash; サービスの表示名 (`My Service` など)。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-355">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="9d2b4-356">サービスを開始する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-356">Start a service</span></span>

<span data-ttu-id="9d2b4-357">次の PowerShell 6 コマンドでサービスを開始します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-357">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="9d2b4-358">このコマンドでサービスを開始するには数秒かかります。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-358">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="9d2b4-359">サービスの状態を確認する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-359">Determine a service's status</span></span>

<span data-ttu-id="9d2b4-360">サービスの状態を確認するには、次の PowerShell 6 コマンドを使います。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-360">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="9d2b4-361">この状態は、次のいずれかの値として報告されます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-361">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="9d2b4-362">サービスを停止する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-362">Stop a service</span></span>

<span data-ttu-id="9d2b4-363">次の PowerShell 6 コマンドでサービスを停止します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-363">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="9d2b4-364">サービスを削除する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-364">Remove a service</span></span>

<span data-ttu-id="9d2b4-365">サービスを停止した後、少ししてから、次の Powershell 6 コマンドを使ってサービスを削除します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-365">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="9d2b4-366">イベントの開始と停止を扱う</span><span class="sxs-lookup"><span data-stu-id="9d2b4-366">Handle starting and stopping events</span></span>

<span data-ttu-id="9d2b4-367"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>、および <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> イベントを処理するには、次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-367">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="9d2b4-368">`OnStarting`、`OnStarted`、および `OnStopping` メソッドを使用して、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> から派生するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-368">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="9d2b4-369">`CustomWebHostService` を <xref:System.ServiceProcess.ServiceBase.Run*> に渡す <xref:Microsoft.AspNetCore.Hosting.IWebHost> の拡張メソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-369">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="9d2b4-370">`Program.Main` で、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> ではなく、拡張メソッド `RunAsCustomService` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-370">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="9d2b4-371">`Program.Main` 内の <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> の場所を確認するには、「[展開の種類](#deployment-type)」セクションに示されているコード サンプルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-371">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="9d2b4-372">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="9d2b4-372">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="9d2b4-373">インターネットまたは企業ネットワークからの要求とやり取りするサービスやプロキシまたはロード バランサーの背後にあるサービスでは、追加の構成が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-373">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="9d2b4-374">詳細については、「<xref:host-and-deploy/proxy-load-balancer>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-374">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="9d2b4-375">エンドポイントを構成する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-375">Configure endpoints</span></span>

<span data-ttu-id="9d2b4-376">既定では、ASP.NET Core は `http://localhost:5000` にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-376">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="9d2b4-377">`ASPNETCORE_URLS` 環境変数を設定して、URL とポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-377">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="9d2b4-378">追加の URL とポートの構成方法については、関連するサーバーの記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-378">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="9d2b4-379">上記のガイダンスでは、HTTPS エンドポイントのサポートについて説明しています。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-379">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="9d2b4-380">たとえば、Windows サービスで認証が使用されている場合は、アプリを HTTPS 用に構成します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-380">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="9d2b4-381">サービス エンドポイントをセキュリティで保護するために ASP.NET Core の HTTPS 開発証明書を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-381">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="9d2b4-382">現在のディレクトリとコンテンツのルート</span><span class="sxs-lookup"><span data-stu-id="9d2b4-382">Current directory and content root</span></span>

<span data-ttu-id="9d2b4-383">Windows サービスに対して <xref:System.IO.Directory.GetCurrentDirectory*> を呼び出して返される現在の作業ディレクトリは *C:\\WINDOWS\\system32* フォルダーです。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-383">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="9d2b4-384">*system32* フォルダーは、サービスのファイル (設定ファイルなど) を保存するために適した場所ではありません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-384">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="9d2b4-385">次のいずれかの方法を使用して、サービスのアセットと設定ファイルを管理し、アクセスします。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-385">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="9d2b4-386">アプリのフォルダーにコンテンツ ルート パスを設定する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-386">Set the content root path to the app's folder</span></span>

<span data-ttu-id="9d2b4-387"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> は、サービスの作成時に `binPath` 引数に指定されたものと同じパスです。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-387">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="9d2b4-388">`GetCurrentDirectory` を呼び出して設定ファイルへのパスを作成する代わりに、アプリの[コンテンツ ルート](xref:fundamentals/index#content-root)へのパスを指定して <xref:System.IO.Directory.SetCurrentDirectory*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-388">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="9d2b4-389">`Program.Main` で、サービスの実行可能ファイルがあるフォルダーへのパスを判別し、そのパスを使用してアプリのコンテンツ ルートを確立します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-389">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="9d2b4-390">ディスク上の適切な場所にサービスのファイルを格納する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-390">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="9d2b4-391">ファイルを含むフォルダーに対して <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> を使用するときは、<xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> を使用して絶対パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-391">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="9d2b4-392">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="9d2b4-392">Troubleshoot</span></span>

<span data-ttu-id="9d2b4-393">Windows サービス アプリのトラブルシューティングについては、「<xref:test/troubleshoot>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-393">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="9d2b4-394">一般的なエラー</span><span class="sxs-lookup"><span data-stu-id="9d2b4-394">Common errors</span></span>

* <span data-ttu-id="9d2b4-395">PowerShell の以前のバージョンまたはプレリリース バージョンが使用されています。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-395">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="9d2b4-396">登録されたサービスに、[dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドからのアプリの**発行済み**出力が使用されません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-396">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="9d2b4-397">[dotnet build](/dotnet/core/tools/dotnet-build) コマンドの出力が、アプリの展開でサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-397">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="9d2b4-398">発行された資産は、展開の種類に応じて次のいずれかのフォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-398">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="9d2b4-399">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="9d2b4-399">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="9d2b4-400">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="9d2b4-400">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="9d2b4-401">サービスが実行中の状態ではありません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-401">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="9d2b4-402">アプリに使用されるリソース (証明書など) のパスが正しくありません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-402">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="9d2b4-403">Windows サービスのベース パスは *c:\\Windows\\System32* です。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-403">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="9d2b4-404">"*サービスとしてログオンする*" アクセス権がユーザーにありません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-404">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="9d2b4-405">`New-Service` PowerShell コマンドの実行時に、ユーザーのパスワードの有効期限が切れていたか、正しく渡されていません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-405">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="9d2b4-406">アプリに ASP.NET Core 認証が必要ですが、セキュリティで保護された接続 (HTTPS) 用に構成されていません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-406">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="9d2b4-407">要求 URL ポートが正しくないか、アプリで正しく構成されていません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-407">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="9d2b4-408">システム イベント ログとアプリケーション イベント ログ</span><span class="sxs-lookup"><span data-stu-id="9d2b4-408">System and Application Event Logs</span></span>

<span data-ttu-id="9d2b4-409">システム イベント ログとアプリケーション イベント ログにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-409">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="9d2b4-410">[スタート] メニューを開き、*イベント ビューアー*を検索し、**イベント ビューアー** アプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-410">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="9d2b4-411">**イベント ビューアー**で **[Windows ログ]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-411">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="9d2b4-412">**[システム]** を選択してシステム イベント ログを開きます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-412">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="9d2b4-413">**[Application]** を選択して、アプリケーション イベント ログを開きます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-413">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="9d2b4-414">失敗したアプリに関連するエラーを検索します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-414">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="9d2b4-415">コマンド プロンプトでアプリを実行する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-415">Run the app at a command prompt</span></span>

<span data-ttu-id="9d2b4-416">多くのスタートアップ エラーでは、イベント ログに有用な情報が生成されません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-416">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="9d2b4-417">ホスティング システムのコマンド プロンプトでアプリを実行すると、エラーの原因がわかることがあります。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-417">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="9d2b4-418">アプリからさらに詳細情報をログに記録するには、[ログ レベル](xref:fundamentals/logging/index#log-level)を低くするか、[開発環境](xref:fundamentals/environments)でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-418">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="9d2b4-419">パッケージ キャッシュをクリアする</span><span class="sxs-lookup"><span data-stu-id="9d2b4-419">Clear package caches</span></span>

<span data-ttu-id="9d2b4-420">開発マシンで .NET Core SDK をアップグレードしたり、アプリ内のパッケージ バージョンを変更したりした直後に、機能しているアプリが失敗することがあります。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-420">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="9d2b4-421">場合によっては、パッケージに統一性がないと、メジャー アップグレード実行時にアプリが破壊されることがあります。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-421">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="9d2b4-422">これらの問題のほとんどは、次の手順で解決できます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-422">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="9d2b4-423">*bin* フォルダーと *obj* フォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-423">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="9d2b4-424">コマンド シェルから [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) を実行して、パッケージ キャッシュをクリアします。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-424">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="9d2b4-425">パッケージ キャッシュをクリアするには、[nuget.exe](https://www.nuget.org/downloads) ツールを使用して `nuget locals all -clear` コマンドを実行する方法もあります。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-425">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="9d2b4-426">*nuget.exe* は、Windows デスクトップ オペレーティング システムにバンドルされているインストールではなく、[NuGet Web サイト](https://www.nuget.org/downloads)から別に入手する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-426">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="9d2b4-427">プロジェクトを復元してリビルドします。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-427">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="9d2b4-428">アプリを再展開する前に、サーバー上の展開フォルダー内のすべてのファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-428">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="9d2b4-429">アプリの速度低下またはハング</span><span class="sxs-lookup"><span data-stu-id="9d2b4-429">Slow or hanging app</span></span>

<span data-ttu-id="9d2b4-430">"*クラッシュ ダンプ*" とはシステムのメモリのスナップショットであり、アプリのクラッシュ、起動の失敗、遅いアプリの原因を突き止めるのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-430">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="9d2b4-431">アプリのクラッシュまたは例外の発生</span><span class="sxs-lookup"><span data-stu-id="9d2b4-431">App crashes or encounters an exception</span></span>

<span data-ttu-id="9d2b4-432">[Windows エラー報告 (WER)](/windows/desktop/wer/windows-error-reporting) からダンプを取得して分析します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-432">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="9d2b4-433">`c:\dumps` にクラッシュ ダンプ ファイルを保持するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-433">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="9d2b4-434">次のアプリケーションの実行可能ファイル名で [EnableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1)を実行します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-434">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="9d2b4-435">クラッシュが発生する条件の下でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-435">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="9d2b4-436">クラッシュが発生した後、[DisableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1)を実行します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-436">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="9d2b4-437">アプリがクラッシュし、ダンプの収集が完了したら、アプリを普通に終了してかまいません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-437">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="9d2b4-438">PowerShell スクリプトにより、アプリごとに最大 5 つのダンプを収集する WER が構成されます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-438">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="9d2b4-439">クラッシュ ダンプでは、大量のディスク領域 (1 つにつき最大、数ギガバイト) が使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-439">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="9d2b4-440">アプリが起動時または正常な実行中にハングまたは失敗する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-440">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="9d2b4-441">アプリが起動時または正常な実行中に "*ハング*" (応答を停止するがクラッシュしない) または失敗するときは、「[User-Mode Dump Files:Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)」(ユーザー モード ダンプ ファイル: 最適なツールの選択) を参照し、適切なツールを選択してダンプを生成します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-441">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="9d2b4-442">ダンプを分析する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-442">Analyze the dump</span></span>

<span data-ttu-id="9d2b4-443">ダンプはいくつかの方法で分析できます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-443">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="9d2b4-444">詳細については、「[Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)」(ユーザー モード ダンプ ファイルの分析) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-444">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9d2b4-445">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="9d2b4-445">Additional resources</span></span>

* <span data-ttu-id="9d2b4-446">[Kestrel エンドポイント構成](xref:fundamentals/servers/kestrel#endpoint-configuration) (HTTPS 構成と SNI サポートを含む)</span><span class="sxs-lookup"><span data-stu-id="9d2b4-446">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="9d2b4-447">ASP.NET Core アプリは、IIS を 使用せずに、[Windows サービス](/dotnet/framework/windows-services/introduction-to-windows-service-applications)として Windows にホストできます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-447">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="9d2b4-448">Windows サービスとしてホストされている場合、サーバーの再起動後にアプリが自動的に起動します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-448">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="9d2b4-449">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-449">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9d2b4-450">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="9d2b4-450">Prerequisites</span></span>

* [<span data-ttu-id="9d2b4-451">ASP.NET Core SDK 2.1 以降</span><span class="sxs-lookup"><span data-stu-id="9d2b4-451">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="9d2b4-452">PowerShell 6.2 以降</span><span class="sxs-lookup"><span data-stu-id="9d2b4-452">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="9d2b4-453">アプリの構成</span><span class="sxs-lookup"><span data-stu-id="9d2b4-453">App configuration</span></span>

<span data-ttu-id="9d2b4-454">アプリでは、[Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) と [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) へのパッケージ参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-454">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="9d2b4-455">サービスの外部で実行しているときにテストとデバッグを行うには、アプリがサービスとして実行しているかコンソール アプリとして実行しているかを判別するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-455">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="9d2b4-456">デバッガーがアタッチされているか、`--console` スイッチが存在するかを検査します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-456">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="9d2b4-457">いずれかの条件が満たされる場合 (アプリがサービスとして実行していない場合)、<xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-457">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="9d2b4-458">条件が満たされない場合 (アプリがサービスとして実行している場合):</span><span class="sxs-lookup"><span data-stu-id="9d2b4-458">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="9d2b4-459"><xref:System.IO.Directory.SetCurrentDirectory*> を呼び出し、アプリの発行場所のパスを使用します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-459">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="9d2b4-460">パスを取得するために <xref:System.IO.Directory.GetCurrentDirectory*> を呼び出さないでください。<xref:System.IO.Directory.GetCurrentDirectory*> が呼び出されると、Windows サービス アプリは *C:\\WINDOWS\\system32* フォルダーを戻すためです。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-460">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="9d2b4-461">詳しくは、「[現在のディレクトリとコンテンツのルート](#current-directory-and-content-root)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-461">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="9d2b4-462">`CreateWebHostBuilder` でアプリを構成する前に、この手順に従います。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-462">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="9d2b4-463"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> を呼び出して、アプリをサービスとして実行します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-463">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="9d2b4-464">[コマンドライン構成プロバイダー](xref:fundamentals/configuration/index#command-line-configuration-provider)では、コマンドライン引数に名前と値の組が必要であるため、<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> が引数を受け取る前に `--console` スイッチは引数から削除されます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-464">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="9d2b4-465">Windows イベント ログに書き込むには、EventLog プロバイダーを <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> に追加します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-465">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="9d2b4-466">*appsettings.Production.json* ファイルで `Logging:LogLevel:Default` キーを使用してログ レベルを設定します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-466">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="9d2b4-467">サンプル アプリの次の例では、アプリ内で有効期間イベントを処理するために、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> の代わりに `RunAsCustomService` を呼び出しています。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-467">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="9d2b4-468">詳しくは、「[イベントの開始と停止を扱う](#handle-starting-and-stopping-events)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-468">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="9d2b4-469">配置の種類</span><span class="sxs-lookup"><span data-stu-id="9d2b4-469">Deployment type</span></span>

<span data-ttu-id="9d2b4-470">展開のシナリオに関する情報および注意事項については、「[.NET Core アプリケーションの展開](/dotnet/core/deploying/)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-470">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="9d2b4-471">SDK</span><span class="sxs-lookup"><span data-stu-id="9d2b4-471">SDK</span></span>

<span data-ttu-id="9d2b4-472">Razor Pages または MVC フレームワークを使用する Web アプリベースのサービスでは、プロジェクト ファイルに Web SDK を指定します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-472">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="9d2b4-473">サービスでバックグラウンド タスク ([ホステッド サービス](xref:fundamentals/host/hosted-services)など) のみを実行する場合は、プロジェクト ファイルにワーカー SDK を指定します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-473">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="9d2b4-474">フレームワーク依存型展開 (FDD)</span><span class="sxs-lookup"><span data-stu-id="9d2b4-474">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="9d2b4-475">フレームワーク依存型展開 (FDD) は、ターゲット システムに .NET Core のシステム全体の共有バージョンが存在することに依存します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-475">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="9d2b4-476">この記事のガイダンスに従って、FDD シナリオを採用すると、*フレームワーク依存型実行可能ファイル* と呼ばれる実行可能ファイル ( *.exe*) が SDK によって生成されます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-476">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="9d2b4-477">Windows [ランタイム識別子 (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) にはターゲット フレームワークが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-477">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="9d2b4-478">次の例では、RID が `win7-x64` に設定されています。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-478">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="9d2b4-479">`<SelfContained>` プロパティが `false` に設定されている。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-479">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="9d2b4-480">これらのプロパティによって、Windows 用の実行可能ファイル ( *.exe*) と共有 .NET Core フレームワークに依存するアプリを生成するよう SDK に指示します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-480">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="9d2b4-481">`<UseAppHost>` プロパティが `true` に設定されている。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-481">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="9d2b4-482">このプロパティによって、サービスに FDD のアクティブ化パス (実行可能ファイル、 *.exe*) を指定します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-482">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="9d2b4-483">*web.config* ファイル (通常 ASP.NET Core アプリを発行する際に生成されます) は、Windows サービス アプリに対しては必要ありません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-483">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="9d2b4-484">*web.config* ファイルの作成を無効にするには、`true` に設定した `<IsTransformWebConfigDisabled>` プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-484">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="9d2b4-485">自己完結型の展開 (SCD)</span><span class="sxs-lookup"><span data-stu-id="9d2b4-485">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="9d2b4-486">自己完結型の展開 (SCD) は、ホスト システムに共有フレームワークが存在することに依存しません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-486">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="9d2b4-487">ランタイムとアプリの依存関係が、アプリと共に展開されます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-487">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="9d2b4-488">Windows [ランタイム識別子 (RID)](/dotnet/core/rid-catalog) は、ターゲット フレームワークを格納する `<PropertyGroup>` に含まれます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-488">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="9d2b4-489">複数の RID を発行するには、次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-489">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="9d2b4-490">セミコロンで区切られたリストの形式で RID を指定します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-490">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="9d2b4-491">プロパティ名 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (複数) を使用します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-491">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="9d2b4-492">詳細については、「[.NET Core の RID カタログ](/dotnet/core/rid-catalog)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-492">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="9d2b4-493">`<SelfContained>` プロパティが `true` に設定されています。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-493">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="9d2b4-494">サービス ユーザー アカウント</span><span class="sxs-lookup"><span data-stu-id="9d2b4-494">Service user account</span></span>

<span data-ttu-id="9d2b4-495">サービス用のユーザー アカウントを作成するには、PowerShell 6 の管理コマンド シェルから [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) コマンドレットを使用します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-495">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="9d2b4-496">Windows 10 October 2018 Update (バージョン 1809/ビルド 10.0.17763) 以降:</span><span class="sxs-lookup"><span data-stu-id="9d2b4-496">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="9d2b4-497">Windows 10 October 2018 Update (バージョン 1809/ビルド 10.0.17763) 以前の Windows OS:</span><span class="sxs-lookup"><span data-stu-id="9d2b4-497">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="9d2b4-498">入力を求められたら、[強力なパスワード](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)を指定します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-498">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="9d2b4-499">[New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) コマンドレットに <xref:System.DateTime> という有効期限で `-AccountExpires` パラメーターを指定しない場合、アカウントは期限切れになりません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-499">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="9d2b4-500">詳しくは、「[Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/)」および「[Service User Accounts (サービス ユーザー アカウント)](/windows/desktop/services/service-user-accounts)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-500">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="9d2b4-501">Active Directory を使う場合、ユーザーを管理するための別の方法は、マネージド サービス アカウントを使うことです。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-501">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="9d2b4-502">詳細については、「[Group Managed Service Accounts Overview (グループ マネージド サービス アカウントの概要)](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-502">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="9d2b4-503">サービスとしてログオン権利</span><span class="sxs-lookup"><span data-stu-id="9d2b4-503">Log on as a service rights</span></span>

<span data-ttu-id="9d2b4-504">サービス ユーザー アカウントに*サービスとしてログオン*権利を確立するには、次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-504">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="9d2b4-505">*secpol.msc* を実行して、ローカル セキュリティ ポリシー エディターを開きます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-505">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="9d2b4-506">**[ローカル ポリシー]** ノードを展開し、 **[ユーザー権利の割り当て]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-506">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="9d2b4-507">**[サービスとしてログオン]** ポリシーを開きます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-507">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="9d2b4-508">**[ユーザーまたはグループの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-508">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="9d2b4-509">次のいずれかの方法を使用して、オブジェクト名 (ユーザー アカウント) を指定します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-509">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="9d2b4-510">オブジェクト名フィールドにユーザー アカウント (`{DOMAIN OR COMPUTER NAME\USER}`) を入力し、 **[OK]** を選択して、ポリシーにユーザーを追加します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-510">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="9d2b4-511">**[詳細]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-511">Select **Advanced**.</span></span> <span data-ttu-id="9d2b4-512">**[検索開始]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-512">Select **Find Now**.</span></span> <span data-ttu-id="9d2b4-513">一覧からユーザー アカウントを選択します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-513">Select the user account from the list.</span></span> <span data-ttu-id="9d2b4-514">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-514">Select **OK**.</span></span> <span data-ttu-id="9d2b4-515">再度 **[OK]** を選択して、ポリシーにユーザーを追加します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-515">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="9d2b4-516">**[OK]** または **[適用]** を選択して、変更を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-516">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="9d2b4-517">Windows サービスを作成して管理する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-517">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="9d2b4-518">サービスを作成する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-518">Create a service</span></span>

<span data-ttu-id="9d2b4-519">PowerShell コマンドを使用して、サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-519">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="9d2b4-520">PowerShell 6 の管理コマンド シェルから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-520">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="9d2b4-521">`{EXE PATH}` &ndash; ホスト上のアプリのフォルダーへのパス (`d:\myservice` など)。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-521">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="9d2b4-522">このパスに、アプリの実行可能ファイルは含めないでください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-522">Don't include the app's executable in the path.</span></span> <span data-ttu-id="9d2b4-523">末尾のスラッシュは、必要ありません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-523">A trailing slash isn't required.</span></span>
* <span data-ttu-id="9d2b4-524">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; サービスのユーザー アカウント (`Contoso\ServiceUser` など)。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-524">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="9d2b4-525">`{SERVICE NAME}` &ndash; サービス名 (`MyService` など)。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-525">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="9d2b4-526">`{EXE FILE PATH}` &ndash; アプリの実行可能ファイルのパス (`d:\myservice\myservice.exe` など)。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-526">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="9d2b4-527">拡張子付きの実行可能ファイルのファイル名を含めます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-527">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="9d2b4-528">`{DESCRIPTION}` &ndash; サービスの説明 (`My sample service` など)。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-528">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="9d2b4-529">`{DISPLAY NAME}` &ndash; サービスの表示名 (`My Service` など)。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-529">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="9d2b4-530">サービスを開始する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-530">Start a service</span></span>

<span data-ttu-id="9d2b4-531">次の PowerShell 6 コマンドでサービスを開始します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-531">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="9d2b4-532">このコマンドでサービスを開始するには数秒かかります。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-532">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="9d2b4-533">サービスの状態を確認する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-533">Determine a service's status</span></span>

<span data-ttu-id="9d2b4-534">サービスの状態を確認するには、次の PowerShell 6 コマンドを使います。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-534">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="9d2b4-535">この状態は、次のいずれかの値として報告されます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-535">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="9d2b4-536">サービスを停止する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-536">Stop a service</span></span>

<span data-ttu-id="9d2b4-537">次の PowerShell 6 コマンドでサービスを停止します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-537">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="9d2b4-538">サービスを削除する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-538">Remove a service</span></span>

<span data-ttu-id="9d2b4-539">サービスを停止した後、少ししてから、次の Powershell 6 コマンドを使ってサービスを削除します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-539">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="9d2b4-540">イベントの開始と停止を扱う</span><span class="sxs-lookup"><span data-stu-id="9d2b4-540">Handle starting and stopping events</span></span>

<span data-ttu-id="9d2b4-541"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>、および <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> イベントを処理するには、次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-541">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="9d2b4-542">`OnStarting`、`OnStarted`、および `OnStopping` メソッドを使用して、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> から派生するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-542">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="9d2b4-543">`CustomWebHostService` を <xref:System.ServiceProcess.ServiceBase.Run*> に渡す <xref:Microsoft.AspNetCore.Hosting.IWebHost> の拡張メソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-543">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="9d2b4-544">`Program.Main` で、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> ではなく、拡張メソッド `RunAsCustomService` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-544">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="9d2b4-545">`Program.Main` 内の <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> の場所を確認するには、「[展開の種類](#deployment-type)」セクションに示されているコード サンプルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-545">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="9d2b4-546">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="9d2b4-546">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="9d2b4-547">インターネットまたは企業ネットワークからの要求とやり取りするサービスやプロキシまたはロード バランサーの背後にあるサービスでは、追加の構成が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-547">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="9d2b4-548">詳細については、「<xref:host-and-deploy/proxy-load-balancer>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-548">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="9d2b4-549">エンドポイントを構成する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-549">Configure endpoints</span></span>

<span data-ttu-id="9d2b4-550">既定では、ASP.NET Core は `http://localhost:5000` にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-550">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="9d2b4-551">`ASPNETCORE_URLS` 環境変数を設定して、URL とポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-551">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="9d2b4-552">追加の URL とポートの構成方法については、関連するサーバーの記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-552">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="9d2b4-553">上記のガイダンスでは、HTTPS エンドポイントのサポートについて説明しています。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-553">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="9d2b4-554">たとえば、Windows サービスで認証が使用されている場合は、アプリを HTTPS 用に構成します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-554">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="9d2b4-555">サービス エンドポイントをセキュリティで保護するために ASP.NET Core の HTTPS 開発証明書を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-555">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="9d2b4-556">現在のディレクトリとコンテンツのルート</span><span class="sxs-lookup"><span data-stu-id="9d2b4-556">Current directory and content root</span></span>

<span data-ttu-id="9d2b4-557">Windows サービスに対して <xref:System.IO.Directory.GetCurrentDirectory*> を呼び出して返される現在の作業ディレクトリは *C:\\WINDOWS\\system32* フォルダーです。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-557">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="9d2b4-558">*system32* フォルダーは、サービスのファイル (設定ファイルなど) を保存するために適した場所ではありません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-558">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="9d2b4-559">次のいずれかの方法を使用して、サービスのアセットと設定ファイルを管理し、アクセスします。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-559">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="9d2b4-560">アプリのフォルダーにコンテンツ ルート パスを設定する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-560">Set the content root path to the app's folder</span></span>

<span data-ttu-id="9d2b4-561"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> は、サービスの作成時に `binPath` 引数に指定されたものと同じパスです。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-561">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="9d2b4-562">`GetCurrentDirectory` を呼び出して設定ファイルへのパスを作成する代わりに、アプリの[コンテンツ ルート](xref:fundamentals/index#content-root)へのパスを指定して <xref:System.IO.Directory.SetCurrentDirectory*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-562">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="9d2b4-563">`Program.Main` で、サービスの実行可能ファイルがあるフォルダーへのパスを判別し、そのパスを使用してアプリのコンテンツ ルートを確立します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-563">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="9d2b4-564">ディスク上の適切な場所にサービスのファイルを格納する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-564">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="9d2b4-565">ファイルを含むフォルダーに対して <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> を使用するときは、<xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> を使用して絶対パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-565">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="9d2b4-566">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="9d2b4-566">Troubleshoot</span></span>

<span data-ttu-id="9d2b4-567">Windows サービス アプリのトラブルシューティングについては、「<xref:test/troubleshoot>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-567">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="9d2b4-568">一般的なエラー</span><span class="sxs-lookup"><span data-stu-id="9d2b4-568">Common errors</span></span>

* <span data-ttu-id="9d2b4-569">PowerShell の以前のバージョンまたはプレリリース バージョンが使用されています。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-569">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="9d2b4-570">登録されたサービスに、[dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドからのアプリの**発行済み**出力が使用されません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-570">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="9d2b4-571">[dotnet build](/dotnet/core/tools/dotnet-build) コマンドの出力が、アプリの展開でサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-571">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="9d2b4-572">発行された資産は、展開の種類に応じて次のいずれかのフォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-572">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="9d2b4-573">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="9d2b4-573">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="9d2b4-574">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="9d2b4-574">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="9d2b4-575">サービスが実行中の状態ではありません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-575">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="9d2b4-576">アプリに使用されるリソース (証明書など) のパスが正しくありません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-576">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="9d2b4-577">Windows サービスのベース パスは *c:\\Windows\\System32* です。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-577">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="9d2b4-578">"*サービスとしてログオンする*" アクセス権がユーザーにありません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-578">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="9d2b4-579">`New-Service` PowerShell コマンドの実行時に、ユーザーのパスワードの有効期限が切れていたか、正しく渡されていません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-579">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="9d2b4-580">アプリに ASP.NET Core 認証が必要ですが、セキュリティで保護された接続 (HTTPS) 用に構成されていません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-580">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="9d2b4-581">要求 URL ポートが正しくないか、アプリで正しく構成されていません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-581">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="9d2b4-582">システム イベント ログとアプリケーション イベント ログ</span><span class="sxs-lookup"><span data-stu-id="9d2b4-582">System and Application Event Logs</span></span>

<span data-ttu-id="9d2b4-583">システム イベント ログとアプリケーション イベント ログにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-583">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="9d2b4-584">[スタート] メニューを開き、*イベント ビューアー*を検索し、**イベント ビューアー** アプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-584">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="9d2b4-585">**イベント ビューアー**で **[Windows ログ]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-585">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="9d2b4-586">**[システム]** を選択してシステム イベント ログを開きます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-586">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="9d2b4-587">**[Application]** を選択して、アプリケーション イベント ログを開きます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-587">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="9d2b4-588">失敗したアプリに関連するエラーを検索します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-588">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="9d2b4-589">コマンド プロンプトでアプリを実行する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-589">Run the app at a command prompt</span></span>

<span data-ttu-id="9d2b4-590">多くのスタートアップ エラーでは、イベント ログに有用な情報が生成されません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-590">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="9d2b4-591">ホスティング システムのコマンド プロンプトでアプリを実行すると、エラーの原因がわかることがあります。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-591">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="9d2b4-592">アプリからさらに詳細情報をログに記録するには、[ログ レベル](xref:fundamentals/logging/index#log-level)を低くするか、[開発環境](xref:fundamentals/environments)でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-592">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="9d2b4-593">パッケージ キャッシュをクリアする</span><span class="sxs-lookup"><span data-stu-id="9d2b4-593">Clear package caches</span></span>

<span data-ttu-id="9d2b4-594">開発マシンで .NET Core SDK をアップグレードしたり、アプリ内のパッケージ バージョンを変更したりした直後に、機能しているアプリが失敗することがあります。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-594">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="9d2b4-595">場合によっては、パッケージに統一性がないと、メジャー アップグレード実行時にアプリが破壊されることがあります。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-595">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="9d2b4-596">これらの問題のほとんどは、次の手順で解決できます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-596">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="9d2b4-597">*bin* フォルダーと *obj* フォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-597">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="9d2b4-598">コマンド シェルから [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) を実行して、パッケージ キャッシュをクリアします。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-598">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="9d2b4-599">パッケージ キャッシュをクリアするには、[nuget.exe](https://www.nuget.org/downloads) ツールを使用して `nuget locals all -clear` コマンドを実行する方法もあります。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-599">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="9d2b4-600">*nuget.exe* は、Windows デスクトップ オペレーティング システムにバンドルされているインストールではなく、[NuGet Web サイト](https://www.nuget.org/downloads)から別に入手する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-600">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="9d2b4-601">プロジェクトを復元してリビルドします。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-601">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="9d2b4-602">アプリを再展開する前に、サーバー上の展開フォルダー内のすべてのファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-602">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="9d2b4-603">アプリの速度低下またはハング</span><span class="sxs-lookup"><span data-stu-id="9d2b4-603">Slow or hanging app</span></span>

<span data-ttu-id="9d2b4-604">"*クラッシュ ダンプ*" とはシステムのメモリのスナップショットであり、アプリのクラッシュ、起動の失敗、遅いアプリの原因を突き止めるのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-604">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="9d2b4-605">アプリのクラッシュまたは例外の発生</span><span class="sxs-lookup"><span data-stu-id="9d2b4-605">App crashes or encounters an exception</span></span>

<span data-ttu-id="9d2b4-606">[Windows エラー報告 (WER)](/windows/desktop/wer/windows-error-reporting) からダンプを取得して分析します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-606">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="9d2b4-607">`c:\dumps` にクラッシュ ダンプ ファイルを保持するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-607">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="9d2b4-608">次のアプリケーションの実行可能ファイル名で [EnableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1)を実行します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-608">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="9d2b4-609">クラッシュが発生する条件の下でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-609">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="9d2b4-610">クラッシュが発生した後、[DisableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1)を実行します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-610">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="9d2b4-611">アプリがクラッシュし、ダンプの収集が完了したら、アプリを普通に終了してかまいません。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-611">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="9d2b4-612">PowerShell スクリプトにより、アプリごとに最大 5 つのダンプを収集する WER が構成されます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-612">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="9d2b4-613">クラッシュ ダンプでは、大量のディスク領域 (1 つにつき最大、数ギガバイト) が使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-613">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="9d2b4-614">アプリが起動時または正常な実行中にハングまたは失敗する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-614">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="9d2b4-615">アプリが起動時または正常な実行中に "*ハング*" (応答を停止するがクラッシュしない) または失敗するときは、「[User-Mode Dump Files:Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)」(ユーザー モード ダンプ ファイル: 最適なツールの選択) を参照し、適切なツールを選択してダンプを生成します。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-615">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="9d2b4-616">ダンプを分析する</span><span class="sxs-lookup"><span data-stu-id="9d2b4-616">Analyze the dump</span></span>

<span data-ttu-id="9d2b4-617">ダンプはいくつかの方法で分析できます。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-617">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="9d2b4-618">詳細については、「[Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)」(ユーザー モード ダンプ ファイルの分析) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d2b4-618">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9d2b4-619">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="9d2b4-619">Additional resources</span></span>

* <span data-ttu-id="9d2b4-620">[Kestrel エンドポイント構成](xref:fundamentals/servers/kestrel#endpoint-configuration) (HTTPS 構成と SNI サポートを含む)</span><span class="sxs-lookup"><span data-stu-id="9d2b4-620">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
