---
title: ASP.NET Core での Windows 認証を構成します。
author: scottaddie
description: ASP.NET Core での IIS と HTTP.sys は Windows 認証を構成する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 07/01/2019
uid: security/authentication/windowsauth
ms.openlocfilehash: 30f1f554a29412ed6b84115d457d2da1aba91c17
ms.sourcegitcommit: eb3e51d58dd713eefc242148f45bd9486be3a78a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67500507"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="3cf0c-103">ASP.NET Core での Windows 認証を構成します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-103">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="3cf0c-104">によって[Scott Addie](https://twitter.com/Scott_Addie)と[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3cf0c-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3cf0c-105">Windows 認証 (Negotiate、Kerberos、または NTLM 認証とも呼ばれます) でホストされている ASP.NET Core アプリ用に構成できます[IIS](xref:host-and-deploy/iis/index)、 [Kestrel](xref:fundamentals/servers/kestrel)、または[HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="3cf0c-105">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel), or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3cf0c-106">Windows 認証 (Negotiate、Kerberos、または NTLM 認証とも呼ばれます) でホストされている ASP.NET Core アプリ用に構成できます[IIS](xref:host-and-deploy/iis/index)または[HTTP.sys](xref:fundamentals/servers/httpsys)します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-106">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

<span data-ttu-id="3cf0c-107">Windows 認証は、ASP.NET Core アプリのユーザーを認証するオペレーティング システムに依存します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-107">Windows Authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="3cf0c-108">ユーザーを識別するために Active Directory ドメインの id または Windows アカウントを使用して、企業ネットワークで、サーバーの実行時に、Windows 認証を使用できます。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-108">You can use Windows Authentication when your server runs on a corporate network using Active Directory domain identities or Windows accounts to identify users.</span></span> <span data-ttu-id="3cf0c-109">Windows 認証は、同じ Windows ドメインに属しているユーザー、クライアント アプリ、および web サーバーのイントラネット環境に最適です。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-109">Windows Authentication is best suited to intranet environments where users, client apps, and web servers belong to the same Windows domain.</span></span>

> [!NOTE]
> <span data-ttu-id="3cf0c-110">Http/2 では、Windows 認証はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-110">Windows Authentication isn't supported with HTTP/2.</span></span> <span data-ttu-id="3cf0c-111">Http/2 の応答の認証チャレンジを送信できますが、クライアントは、http/1.1 に、認証する前にダウン グレードする必要があります。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-111">Authentication challenges can be sent on HTTP/2 responses, but the client must downgrade to HTTP/1.1 before authenticating.</span></span>

## <a name="iisiis-express"></a><span data-ttu-id="3cf0c-112">IIS または IIS Express</span><span class="sxs-lookup"><span data-stu-id="3cf0c-112">IIS/IIS Express</span></span>

<span data-ttu-id="3cf0c-113">認証サービスを呼び出すことによって追加<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>(<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName>名前空間) で`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3cf0c-113">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a><span data-ttu-id="3cf0c-114">起動設定 (デバッガー)</span><span class="sxs-lookup"><span data-stu-id="3cf0c-114">Launch settings (debugger)</span></span>

<span data-ttu-id="3cf0c-115">起動設定の構成にのみ影響、 *Properties/launchSettings.json* IIS Express 用のファイルし、IIS の Windows 認証を構成しません。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-115">Configuration for launch settings only affects the *Properties/launchSettings.json* file for IIS Express and doesn't configure IIS for Windows Authentication.</span></span> <span data-ttu-id="3cf0c-116">サーバーの構成については、 [IIS](#iis)セクション。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-116">Server configuration is explained in the [IIS](#iis) section.</span></span>

<span data-ttu-id="3cf0c-117">**Web アプリケーション**を更新する Windows 認証をサポートする Visual Studio または .NET Core CLI を使用して利用可能なテンプレートを構成することができます、 *Properties/launchSettings.json*ファイル自動的に。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-117">The **Web Application** template available via Visual Studio or the .NET Core CLI can be configured to support Windows Authentication, which updates the *Properties/launchSettings.json* file automatically.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3cf0c-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cf0c-118">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3cf0c-119">**新しいプロジェクト**</span><span class="sxs-lookup"><span data-stu-id="3cf0c-119">**New project**</span></span>

1. <span data-ttu-id="3cf0c-120">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-120">Create a new project.</span></span>
1. <span data-ttu-id="3cf0c-121">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-121">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="3cf0c-122">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-122">Select **Next**.</span></span>
1. <span data-ttu-id="3cf0c-123">名前を入力、**プロジェクト名**フィールド。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-123">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="3cf0c-124">確認、**場所**エントリが正しいか、プロジェクトの場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-124">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="3cf0c-125">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-125">Select **Create**.</span></span>
1. <span data-ttu-id="3cf0c-126">選択**変更** **認証**します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-126">Select **Change** under **Authentication**.</span></span>
1. <span data-ttu-id="3cf0c-127">**認証の変更**ウィンドウで、 **Windows 認証**します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-127">In the **Change Authentication** window, select **Windows Authentication**.</span></span> <span data-ttu-id="3cf0c-128">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-128">Select **OK**.</span></span>
1. <span data-ttu-id="3cf0c-129">**[Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-129">Select **Web Application**.</span></span>
1. <span data-ttu-id="3cf0c-130">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-130">Select **Create**.</span></span>

<span data-ttu-id="3cf0c-131">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-131">Run the app.</span></span> <span data-ttu-id="3cf0c-132">ユーザー名は、レンダリングされたアプリのユーザー インターフェイスに表示されます。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-132">The username appears in the rendered app's user interface.</span></span>

<span data-ttu-id="3cf0c-133">**既存のプロジェクト**</span><span class="sxs-lookup"><span data-stu-id="3cf0c-133">**Existing project**</span></span>

<span data-ttu-id="3cf0c-134">プロジェクトのプロパティは、Windows 認証を有効にして、匿名認証を無効にします。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-134">The project's properties enable Windows Authentication and disable Anonymous Authentication:</span></span>

1. <span data-ttu-id="3cf0c-135">**ソリューション エクスプローラー**でプロジェクトを右クリックして、 **[プロパティ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-135">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
1. <span data-ttu-id="3cf0c-136">**[デバッグ]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-136">Select the **Debug** tab.</span></span>
1. <span data-ttu-id="3cf0c-137">チェック ボックスをオフ**匿名認証を有効にする**します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-137">Clear the check box for **Enable Anonymous Authentication**.</span></span>
1. <span data-ttu-id="3cf0c-138">チェック ボックスをオン**Windows 認証を有効にする**します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-138">Select the check box for **Enable Windows Authentication**.</span></span>
1. <span data-ttu-id="3cf0c-139">保存して、プロパティ ページを閉じます。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-139">Save and close the property page.</span></span>

<span data-ttu-id="3cf0c-140">プロパティを構成する代わりに、`iisSettings`のノード、 *launchSettings.json*ファイル。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-140">Alternatively, the properties can be configured in the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[<span data-ttu-id="3cf0c-141">Visual Studio Code / .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3cf0c-141">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="3cf0c-142">**新しいプロジェクト**</span><span class="sxs-lookup"><span data-stu-id="3cf0c-142">**New project**</span></span>

<span data-ttu-id="3cf0c-143">実行、[新しい dotnet](/dotnet/core/tools/dotnet-new)コマンドと、`webapp`引数 (ASP.NET Core Web アプリ) と`--auth Windows`スイッチします。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-143">Execute the [dotnet new](/dotnet/core/tools/dotnet-new) command with the `webapp` argument (ASP.NET Core Web App) and `--auth Windows` switch:</span></span>

```console
dotnet new webapp --auth Windows
```

<span data-ttu-id="3cf0c-144">**既存のプロジェクト**</span><span class="sxs-lookup"><span data-stu-id="3cf0c-144">**Existing project**</span></span>

<span data-ttu-id="3cf0c-145">更新プログラム、`iisSettings`のノード、 *launchSettings.json*ファイル。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-145">Update the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

<span data-ttu-id="3cf0c-146">既存のプロジェクトを変更する場合は、プロジェクト ファイルにはへのパッケージ参照が含まれていることを確認、 [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)**または**、 [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-146">When modifying an existing project, confirm that the project file includes a package reference for the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) **or** the [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet package.</span></span>

### <a name="iis"></a><span data-ttu-id="3cf0c-147">IIS</span><span class="sxs-lookup"><span data-stu-id="3cf0c-147">IIS</span></span>

<span data-ttu-id="3cf0c-148">IIS を使用して、 [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)ASP.NET Core アプリをホストします。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-148">IIS uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="3cf0c-149">使用した IIS の Windows 認証が構成されている、 *web.config*ファイル。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-149">Windows Authentication is configured for IIS via the *web.config* file.</span></span> <span data-ttu-id="3cf0c-150">以下のセクションで表示する方法。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-150">The following sections show how to:</span></span>

* <span data-ttu-id="3cf0c-151">ローカルの提供*web.config*ファイルをアプリが展開されると、サーバーで Windows 認証をアクティブにします。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-151">Provide a local *web.config* file that activates Windows Authentication on the server when the app is deployed.</span></span>
* <span data-ttu-id="3cf0c-152">IIS マネージャーを使用して、構成、 *web.config*サーバーに既に展開されている ASP.NET Core アプリのファイル。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-152">Use the IIS Manager to configure the *web.config* file of an ASP.NET Core app that has already been deployed to the server.</span></span>

<span data-ttu-id="3cf0c-153">これをいない場合は、ASP.NET Core アプリをホストする IIS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-153">If you haven't already done so, enable IIS to host ASP.NET Core apps.</span></span> <span data-ttu-id="3cf0c-154">詳細については、「 <xref:host-and-deploy/iis/index> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-154">For more information, see <xref:host-and-deploy/iis/index>.</span></span>

<span data-ttu-id="3cf0c-155">Windows 認証の IIS の役割サービスを有効にします。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-155">Enable the IIS Role Service for Windows Authentication.</span></span> <span data-ttu-id="3cf0c-156">詳細については、[(手順 2 参照)、IIS の役割サービスで Windows 認証を有効にする](xref:host-and-deploy/iis/index#iis-configuration)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-156">For more information, see [Enable Windows Authentication in IIS Role Services (see Step 2)](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

<span data-ttu-id="3cf0c-157">[IIS 統合ミドルウェア](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)既定で自動的に要求の認証に構成されます。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-157">[IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) is configured to automatically authenticate requests by default.</span></span> <span data-ttu-id="3cf0c-158">詳細については、次を参照してください。 [ASP.NET Core の IIS と Windows ホスト。IIS のオプション (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options)します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-158">For more information, see [Host ASP.NET Core on Windows with IIS: IIS options (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span></span>

<span data-ttu-id="3cf0c-159">ASP.NET Core モジュールは、既定では、アプリに Windows 認証トークンを転送するように構成されます。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-159">The ASP.NET Core Module is configured to forward the Windows Authentication token to the app by default.</span></span> <span data-ttu-id="3cf0c-160">詳細については、次を参照してください。 [ASP.NET Core モジュール構成リファレンス。AspNetCore 要素の属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-160">For more information, see [ASP.NET Core Module configuration reference: Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

<span data-ttu-id="3cf0c-161">使用**か**の次の方法。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-161">Use **either** of the following approaches:</span></span>

* <span data-ttu-id="3cf0c-162">**発行して、プロジェクトを配置する前に**次の追加*web.config*ファイルをプロジェクトのルート。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-162">**Before publishing and deploying the project,** add the following *web.config* file to the project root:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  <span data-ttu-id="3cf0c-163">プロジェクトが .NET Core SDK によって公開されると (なし、`<IsTransformWebConfigDisabled>`プロパティに設定`true`プロジェクト ファイル内)、公開された*web.config*ファイルが含まれています、`<location><system.webServer><security><authentication>`セクション。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-163">When the project is published by the .NET Core SDK (without the `<IsTransformWebConfigDisabled>` property set to `true` in the project file), the published *web.config* file includes the `<location><system.webServer><security><authentication>` section.</span></span> <span data-ttu-id="3cf0c-164">詳細については、`<IsTransformWebConfigDisabled>`プロパティを参照してください<xref:host-and-deploy/iis/index#webconfig-file>します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-164">For more information on the `<IsTransformWebConfigDisabled>` property, see <xref:host-and-deploy/iis/index#webconfig-file>.</span></span>

* <span data-ttu-id="3cf0c-165">**発行し、プロジェクトを配置した後**サーバー側の構成、IIS マネージャーでを実行します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-165">**After publishing and deploying the project,** perform server-side configuration with the IIS Manager:</span></span>

  1. <span data-ttu-id="3cf0c-166">IIS マネージャーで、下にある IIS サイトを選択して、**サイト**のノード、**接続**サイドバーです。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-166">In IIS Manager, select the IIS site under the **Sites** node of the **Connections** sidebar.</span></span>
  1. <span data-ttu-id="3cf0c-167">ダブルクリック**認証**で、 **IIS**領域。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-167">Double-click **Authentication** in the **IIS** area.</span></span>
  1. <span data-ttu-id="3cf0c-168">選択**匿名認証**します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-168">Select **Anonymous Authentication**.</span></span> <span data-ttu-id="3cf0c-169">選択**を無効にする**で、**アクション**サイドバーです。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-169">Select **Disable** in the **Actions** sidebar.</span></span>
  1. <span data-ttu-id="3cf0c-170">選択**Windows 認証**します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-170">Select **Windows Authentication**.</span></span> <span data-ttu-id="3cf0c-171">選択**を有効にする**で、**アクション**サイドバーです。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-171">Select **Enable** in the **Actions** sidebar.</span></span>

  <span data-ttu-id="3cf0c-172">これらのアクションが実行したときに、IIS マネージャーは、アプリを変更します*web.config*ファイル。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-172">When these actions are taken, IIS Manager modifies the app's *web.config* file.</span></span> <span data-ttu-id="3cf0c-173">A`<system.webServer><security><authentication>`ノードが更新された設定を使用した追加`anonymousAuthentication`と`windowsAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="3cf0c-173">A `<system.webServer><security><authentication>` node is added with updated settings for `anonymousAuthentication` and `windowsAuthentication`:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  <span data-ttu-id="3cf0c-174">`<system.webServer>`セクションに追加、 *web.config*ファイルを IIS マネージャーでは、アプリの外部で`<location>`は .NET Core SDK により、アプリが公開されるときに追加されたセクションです。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-174">The `<system.webServer>` section added to the *web.config* file by IIS Manager is outside of the app's `<location>` section added by the .NET Core SDK when the app is published.</span></span> <span data-ttu-id="3cf0c-175">外部のセクションが追加されるため、`<location>`ノード、いずれかで、設定が継承されます[サブ アプリ](xref:host-and-deploy/iis/index#sub-applications)現在のアプリにします。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-175">Because the section is added outside of the `<location>` node, the settings are inherited by any [sub-apps](xref:host-and-deploy/iis/index#sub-applications) to the current app.</span></span> <span data-ttu-id="3cf0c-176">継承を防ぐためには、移動、追加した`<security>`内のセクション、 `<location><system.webServer>` .NET Core SDK が提供されているセクション。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-176">To prevent inheritance, move the added `<security>` section inside of the `<location><system.webServer>` section that the .NET Core SDK provided.</span></span>

  <span data-ttu-id="3cf0c-177">IIS マネージャーを使用するには、IIS の構成を追加する、影響を受けるのみアプリの*web.config*サーバー上のファイル。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-177">When IIS Manager is used to add the IIS configuration, it only affects the app's *web.config* file on the server.</span></span> <span data-ttu-id="3cf0c-178">場合、アプリの後続の配置は、サーバーの設定を上書き可能性があります、サーバーのコピーの*web.config*はプロジェクトの置き換え*web.config*ファイル。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-178">A subsequent deployment of the app may overwrite the settings on the server if the server's copy of *web.config* is replaced by the project's *web.config* file.</span></span> <span data-ttu-id="3cf0c-179">使用**か**の設定を管理する次の方法。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-179">Use **either** of the following approaches to manage the settings:</span></span>

  * <span data-ttu-id="3cf0c-180">設定をリセットする IIS マネージャーを使用して、 *web.config*ファイルについては、展開で、ファイルが上書きされます。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-180">Use IIS Manager to reset the settings in the *web.config* file after the file is overwritten on deployment.</span></span>
  * <span data-ttu-id="3cf0c-181">追加、 *web.config ファイル*アプリの設定でローカルにします。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-181">Add a *web.config file* to the app locally with the settings.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="kestrel"></a><span data-ttu-id="3cf0c-182">Kestrel</span><span class="sxs-lookup"><span data-stu-id="3cf0c-182">Kestrel</span></span>

 <span data-ttu-id="3cf0c-183">[Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate)と NuGet パッケージを使用する[Kestrel](xref:fundamentals/servers/kestrel) Negotiate、Kerberos、および NTLM を使用して、Windows、Linux、macOS で Windows 認証をサポートします。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-183">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) NuGet package can be used with [Kestrel](xref:fundamentals/servers/kestrel) to support Windows Authentication using Negotiate, Kerberos, and NTLM on Windows, Linux, and macOS.</span></span>

> [!WARNING]
> <span data-ttu-id="3cf0c-184">接続で要求間で資格情報を保持できます。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-184">Credentials can be persisted across requests on a connection.</span></span> <span data-ttu-id="3cf0c-185">*ネゴシエート、プロキシは、Kestrel での 1 対 1 の接続のアフィニティ (固定接続) を保持しない限り、認証をプロキシで使用しないでください。*</span><span class="sxs-lookup"><span data-stu-id="3cf0c-185">*Negotiate authentication must not be used with proxies unless the proxy maintains a 1:1 connection affinity (a persistent connection) with Kestrel.*</span></span>

> [!NOTE]
> <span data-ttu-id="3cf0c-186">ネゴシエート ハンドラーは、基になるサーバーが Windows 認証をネイティブにサポートし、有効になっている場合を検出します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-186">The Negotiate handler detects if the underlying server supports Windows Authentication natively and if it's enabled.</span></span> <span data-ttu-id="3cf0c-187">サーバーは、Windows 認証をサポートしています。 無効になっている場合は、サーバーの実装を有効にするように求めるエラーがスローされます。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-187">If the server supports Windows Authentication but it's disabled, an error is thrown asking you to enable the server implementation.</span></span> <span data-ttu-id="3cf0c-188">サーバーで Windows 認証が有効な場合に、ネゴシエート ハンドラーが透過的に転送します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-188">When Windows Authentication is enabled in the server, the Negotiate handler transparently forwards to it.</span></span>

 <span data-ttu-id="3cf0c-189">認証サービスを呼び出すことによって追加<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>(`Microsoft.AspNetCore.Authentication.Negotiate`名前空間) と`AddNegotitate`(`Microsoft.AspNetCore.Authentication.Negotiate`名前空間) で`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3cf0c-189">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (`Microsoft.AspNetCore.Authentication.Negotiate` namespace) and `AddNegotitate` (`Microsoft.AspNetCore.Authentication.Negotiate` namespace) in `Startup.ConfigureServices`:</span></span>

 ```csharp
services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
    .AddNegotiate();
```

<span data-ttu-id="3cf0c-190">認証ミドルウェアを呼び出すことによって追加<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>で`Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="3cf0c-190">Add Authentication Middleware by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> in `Startup.Configure`:</span></span>

 ```csharp
app.UseAuthentication();

app.UseMvc();
```

<span data-ttu-id="3cf0c-191">ミドルウェアの詳細については、次を参照してください。<xref:fundamentals/middleware/index>します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-191">For more information on middleware, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="3cf0c-192">匿名の要求が許可されます。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-192">Anonymous requests are allowed.</span></span> <span data-ttu-id="3cf0c-193">使用[ASP.NET Core の承認](xref:security/authorization/introduction)課題の匿名認証を要求します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-193">Use [ASP.NET Core Authorization](xref:security/authorization/introduction) to challenge anonymous requests for authentication.</span></span>

### <a name="windows-environment-configuration"></a><span data-ttu-id="3cf0c-194">Windows 環境の構成</span><span class="sxs-lookup"><span data-stu-id="3cf0c-194">Windows environment configuration</span></span>

<span data-ttu-id="3cf0c-195">[Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate)コンポーネントは、ユーザー モード認証を実行します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-195">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) component performs User Mode authentication.</span></span> <span data-ttu-id="3cf0c-196">サービス プリンシパル名 (Spn) は、マシン アカウントではなく、サービスを実行するユーザー アカウントに追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-196">Service Principal Names (SPNs) must be added to the user account running the service, not the machine account.</span></span> <span data-ttu-id="3cf0c-197">実行`setspn -S HTTP/mysrevername.mydomain.com myuser`管理コマンド シェルでします。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-197">Execute `setspn -S HTTP/mysrevername.mydomain.com myuser` in an administrative command shell.</span></span>

### <a name="linux-and-macos-environment-configuration"></a><span data-ttu-id="3cf0c-198">Linux と macOS の環境の構成</span><span class="sxs-lookup"><span data-stu-id="3cf0c-198">Linux and macOS environment configuration</span></span>

<span data-ttu-id="3cf0c-199">Linux または macOS マシンを Windows ドメインに参加するための手順については、 [Windows 認証に Kerberos を使用して、SQL server の Azure Data Studio の接続](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller)記事。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-199">Instructions for joining a Linux or macOS machine to a Windows domain are available in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article.</span></span> <span data-ttu-id="3cf0c-200">手順については、ドメイン上の Linux マシンのコンピューター アカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-200">The instructions create a machine account for the Linux machine on the domain.</span></span> <span data-ttu-id="3cf0c-201">そのコンピューター アカウントに Spn を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-201">SPNs must be added to that machine account.</span></span>

> [!NOTE]
> <span data-ttu-id="3cf0c-202">ガイダンスに従うと、 [Windows 認証に Kerberos を使用して、SQL server の Azure Data Studio の接続](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller)に関する記事で、置き換える`python-software-properties`で`python3-software-properties`必要な場合。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-202">When following the guidance in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article, replace `python-software-properties` with `python3-software-properties` if needed.</span></span>

<span data-ttu-id="3cf0c-203">Linux または macOS マシンをドメインに参加すると、追加の手順が提供する必要が、 [keytab ファイル](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/)Spn で。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-203">Once the Linux or macOS machine is joined to the domain, additional steps are required to provide a [keytab file](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) with the SPNs:</span></span>

* <span data-ttu-id="3cf0c-204">ドメイン コント ローラーで、コンピューター アカウントに新しい web サービスの Spn を追加します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-204">On the domain controller, add new web service SPNs to the machine account:</span></span>
  * `setspn -S HTTP/mywebservice.mydomain.com mymachine`
  * `setspn -S HTTP/mywebservice@MYDOMAIN.COM mymachine`
* <span data-ttu-id="3cf0c-205">使用[ktpass](/windows-server/administration/windows-commands/ktpass) keytab ファイルを生成します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-205">Use [ktpass](/windows-server/administration/windows-commands/ktpass) to generate a keytab file:</span></span>
  * `ktpass -princ HTTP/mywebservice.mydomain.com@MYDOMAIN.COM -pass myKeyTabFilePassword -mapuser MYDOMAIN\mymachine$ -pType KRB5_NT_PRINCIPAL -out c:\temp\mymachine.HTTP.keytab -crypto AES256-SHA1`
  * <span data-ttu-id="3cf0c-206">一部のフィールドで指定されなければなりません大文字のとおりです。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-206">Some fields must be specified in uppercase as indicated.</span></span>
* <span data-ttu-id="3cf0c-207">Linux または macOS マシンに keytab ファイルをコピーします。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-207">Copy the keytab file to the Linux or macOS machine.</span></span>
* <span data-ttu-id="3cf0c-208">環境変数を介して keytab ファイルを選択します。 `export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span><span class="sxs-lookup"><span data-stu-id="3cf0c-208">Select the keytab file via an environment variable: `export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span></span>
* <span data-ttu-id="3cf0c-209">呼び出す`klist`を現在使用可能な Spn を表示します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-209">Invoke `klist` to show the SPNs currently available for use.</span></span>

> [!NOTE]
> <span data-ttu-id="3cf0c-210">Keytab ファイルは、ドメイン アクセスの資格情報を含み、適切に保護する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-210">A keytab file contains domain access credentials and must be protected accordingly.</span></span>

::: moniker-end

## <a name="httpsys"></a><span data-ttu-id="3cf0c-211">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="3cf0c-211">HTTP.sys</span></span>

<span data-ttu-id="3cf0c-212">[HTTP.sys](xref:fundamentals/servers/httpsys) Negotiate、NTLM、または基本認証を使用してカーネル モードの Windows 認証をサポートします。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-212">[HTTP.sys](xref:fundamentals/servers/httpsys) supports Kernel Mode Windows Authentication using Negotiate, NTLM, or Basic authentication.</span></span>

<span data-ttu-id="3cf0c-213">認証サービスを呼び出すことによって追加<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>(<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName>名前空間) で`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3cf0c-213">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

<span data-ttu-id="3cf0c-214">Windows 認証を使用した HTTP.sys を使用するアプリの web ホストを構成する (*Program.cs*)。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-214">Configure the app's web host to use HTTP.sys with Windows Authentication (*Program.cs*).</span></span> <span data-ttu-id="3cf0c-215"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName>名前空間。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-215"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> is in the <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="3cf0c-216">HTTP.sys では、Kerberos 認証プロトコルを使用したカーネル モード認証に処理が委任されます。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-216">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="3cf0c-217">Kerberos および HTTP.sys ではユーザー モード認証がサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-217">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="3cf0c-218">Active Directory から取得され、クライアントによって、ユーザーを認証するサーバーに転送される Kerberos トークン/チケットを暗号化解除するには、コンピューター アカウントを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-218">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="3cf0c-219">アプリのユーザーではなく、ホストのサービス プリンシパル名 (SPN) を登録します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-219">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

> [!NOTE]
> <span data-ttu-id="3cf0c-220">HTTP.sys は、Nano Server バージョン 1709 以降でサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-220">HTTP.sys isn't supported on Nano Server version 1709 or later.</span></span> <span data-ttu-id="3cf0c-221">Nano Server の Windows 認証と HTTP.sys を使用する、 [(microsoft/windowsservercore) の Server Core コンテナー](https://hub.docker.com/r/microsoft/windowsservercore/)します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-221">To use Windows Authentication and HTTP.sys with Nano Server, use a [Server Core (microsoft/windowsservercore) container](https://hub.docker.com/r/microsoft/windowsservercore/).</span></span> <span data-ttu-id="3cf0c-222">Server Core の詳細については、[Windows Server の Server Core インストール オプションとは何ですか?](/windows-server/administration/server-core/what-is-server-core)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-222">For more information on Server Core, see [What is the Server Core installation option in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span></span>

## <a name="authorize-users"></a><span data-ttu-id="3cf0c-223">ユーザーを認証します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-223">Authorize users</span></span>

<span data-ttu-id="3cf0c-224">匿名アクセスの構成の状態にする方法が決定します、`[Authorize]`と`[AllowAnonymous]`属性は、アプリで使用します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-224">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="3cf0c-225">次の 2 つのセクションでは、匿名アクセスの許可されていないと、許可されている構成の状態を処理する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-225">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="3cf0c-226">匿名アクセスを禁止します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-226">Disallow anonymous access</span></span>

<span data-ttu-id="3cf0c-227">Windows 認証が有効になっており、匿名アクセスが無効になっているときに、`[Authorize]`と`[AllowAnonymous]`属性は影響ありません。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-227">When Windows Authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="3cf0c-228">匿名アクセスを禁止する IIS サイトを構成する場合、要求がアプリに到達しません。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-228">If an IIS site is configured to disallow anonymous access, the request never reaches the app.</span></span> <span data-ttu-id="3cf0c-229">このため、`[AllowAnonymous]`属性には適用されません。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-229">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="3cf0c-230">匿名アクセスを許可します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-230">Allow anonymous access</span></span>

<span data-ttu-id="3cf0c-231">Windows 認証と匿名アクセスの両方が有効になっているときに使用して、`[Authorize]`と`[AllowAnonymous]`属性。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-231">When both Windows Authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="3cf0c-232">`[Authorize]`属性では、認証を必要とするアプリのエンドポイントをセキュリティで保護できます。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-232">The `[Authorize]` attribute allows you to secure endpoints of the app which require authentication.</span></span> <span data-ttu-id="3cf0c-233">`[AllowAnonymous]`属性のオーバーライド、`[Authorize]`への匿名アクセスを許可するアプリ内の属性。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-233">The `[AllowAnonymous]` attribute overrides the `[Authorize]` attribute in apps that allow anonymous access.</span></span> <span data-ttu-id="3cf0c-234">属性の使用方法の詳細を参照してください。<xref:security/authorization/simple>します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-234">For attribute usage details, see <xref:security/authorization/simple>.</span></span>

> [!NOTE]
> <span data-ttu-id="3cf0c-235">既定では、ページにアクセスするための承認を持たないユーザーには、空の HTTP 403 応答が表示されます。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-235">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="3cf0c-236">[StatusCodePages ミドルウェア](xref:fundamentals/error-handling#usestatuscodepages)「アクセスが拒否されました」のより優れたエクスペリエンスをユーザーに提供するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-236">The [StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) can be configured to provide users with a better "Access Denied" experience.</span></span>

## <a name="impersonation"></a><span data-ttu-id="3cf0c-237">偽装</span><span class="sxs-lookup"><span data-stu-id="3cf0c-237">Impersonation</span></span>

<span data-ttu-id="3cf0c-238">ASP.NET Core では、権限借用を実装しません。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-238">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="3cf0c-239">アプリは、アプリ プールまたはプロセス id を使用して、すべての要求をアプリの id で実行します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-239">Apps run with the app's identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="3cf0c-240">使用の場合は、アプリは、ユーザーの代理としてアクションを実行する必要があります、 [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*)で、[ターミナル インライン ミドルウェア](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)で`Startup.Configure`します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-240">If the app should perform an action on behalf of a user, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in a [terminal inline middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span></span> <span data-ttu-id="3cf0c-241">このコンテキストで 1 つのアクションを実行し、コンテキストを閉じます。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-241">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

<span data-ttu-id="3cf0c-242">`RunImpersonated` 非同期操作をサポートしていないし、複雑なシナリオでは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-242">`RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="3cf0c-243">全体要求またはミドルウェアのチェーンをラッピングされていないサポートなど、お勧めします。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-243">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3cf0c-244">中に、 [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate)パッケージが Windows での認証ができるように、Linux、および macOS での偽装は、Windows でのみサポートされます。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-244">While the [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package enables authentication on Windows, Linux, and macOS, impersonation is only supported on Windows.</span></span>

::: moniker-end

## <a name="claims-transformations"></a><span data-ttu-id="3cf0c-245">要求の変換</span><span class="sxs-lookup"><span data-stu-id="3cf0c-245">Claims transformations</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3cf0c-246">IIS でホストする場合<xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*>されていないユーザーを初期化するために内部的に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-246">When hosting with IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="3cf0c-247">そのため、認証のたびに要求を変換するための <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装は既定で有効になっていません。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-247">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="3cf0c-248">要求の変換を有効にするためのコード例と詳細については、次を参照してください。<xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-248">For more information and a code example that activates claims transformations, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3cf0c-249">IIS のインプロセス モードでホストする場合<xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*>されていないユーザーを初期化するために内部的に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-249">When hosting with IIS in-process mode, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="3cf0c-250">そのため、認証のたびに要求を変換するための <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装は既定で有効になっていません。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-250">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="3cf0c-251">詳細については、インプロセスをホストする場合は、クレームの変換をアクティブにするためのコード例を参照してください。<xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>します。</span><span class="sxs-lookup"><span data-stu-id="3cf0c-251">For more information and a code example that activates claims transformations when hosting in-process, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="3cf0c-252">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="3cf0c-252">Additional resources</span></span>

* [<span data-ttu-id="3cf0c-253">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="3cf0c-253">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
