---
title: ASP.NET Core で WS-FEDERATION を使用してユーザーを認証する
author: chlowell
description: このチュートリアルでは、ASP.NET Core アプリで WS-FEDERATION を使用する方法について説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
uid: security/authentication/ws-federation
ms.openlocfilehash: d82421a14ede6cb6b01ef59f233bb2eba6b56aec
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651332"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a><span data-ttu-id="52228-103">ASP.NET Core で WS-FEDERATION を使用してユーザーを認証する</span><span class="sxs-lookup"><span data-stu-id="52228-103">Authenticate users with WS-Federation in ASP.NET Core</span></span>

<span data-ttu-id="52228-104">このチュートリアルでは、ユーザーが Active Directory フェデレーションサービス (AD FS) (ADFS) や[Azure Active Directory](/azure/active-directory/) (AAD) などの ws-federation 認証プロバイダーを使用してサインインできるようにする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="52228-104">This tutorial demonstrates how to enable users to sign in with a WS-Federation authentication provider like Active Directory Federation Services (ADFS) or [Azure Active Directory](/azure/active-directory/) (AAD).</span></span> <span data-ttu-id="52228-105">[Facebook、Google、および外部プロバイダー認証](xref:security/authentication/social/index)で説明されている ASP.NET Core 2.0 サンプルアプリを使用します。</span><span class="sxs-lookup"><span data-stu-id="52228-105">It uses the ASP.NET Core 2.0 sample app described in [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="52228-106">ASP.NET Core 2.0 アプリの場合、WS-FEDERATION のサポートは[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation)によって提供されます。</span><span class="sxs-lookup"><span data-stu-id="52228-106">For ASP.NET Core 2.0 apps, WS-Federation support is provided by [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation).</span></span> <span data-ttu-id="52228-107">このコンポーネントは[Owin](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation)から移植され、そのコンポーネントの機構の多くを共有します。</span><span class="sxs-lookup"><span data-stu-id="52228-107">This component is ported from [Microsoft.Owin.Security.WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) and shares many of that component's mechanics.</span></span> <span data-ttu-id="52228-108">ただし、これらのコンポーネントは、いくつかの重要な点で異なります。</span><span class="sxs-lookup"><span data-stu-id="52228-108">However, the components differ in a couple of important ways.</span></span>

<span data-ttu-id="52228-109">既定では、新しいミドルウェアは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="52228-109">By default, the new middleware:</span></span>

* <span data-ttu-id="52228-110">は、要求されていないログインを許可しません。</span><span class="sxs-lookup"><span data-stu-id="52228-110">Doesn't allow unsolicited logins.</span></span> <span data-ttu-id="52228-111">WS-FEDERATION プロトコルのこの機能は、XSRF 攻撃に対して脆弱です。</span><span class="sxs-lookup"><span data-stu-id="52228-111">This feature of the WS-Federation protocol is vulnerable to XSRF attacks.</span></span> <span data-ttu-id="52228-112">ただし、`AllowUnsolicitedLogins` オプションを使用して有効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="52228-112">However, it can be enabled with the `AllowUnsolicitedLogins` option.</span></span>
* <span data-ttu-id="52228-113">では、サインインメッセージのすべてのフォームポストがチェックされません。</span><span class="sxs-lookup"><span data-stu-id="52228-113">Doesn't check every form post for sign-in messages.</span></span> <span data-ttu-id="52228-114">サインインがチェックされるのは、`CallbackPath` に対する要求だけです。 `CallbackPath` の既定値は `/signin-wsfed` ですが、 [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions)クラスの継承された[remoteauthenticationoptions](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)プロパティを使用して変更できます。</span><span class="sxs-lookup"><span data-stu-id="52228-114">Only requests to the `CallbackPath` are checked for sign-ins. `CallbackPath` defaults to `/signin-wsfed` but can be changed via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) class.</span></span> <span data-ttu-id="52228-115">このパスは、 [Skipun認識要求](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests)オプションを有効にすることで、他の認証プロバイダーと共有できます。</span><span class="sxs-lookup"><span data-stu-id="52228-115">This path can be shared with other authentication providers by enabling the [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) option.</span></span>

## <a name="register-the-app-with-active-directory"></a><span data-ttu-id="52228-116">Active Directory にアプリを登録する</span><span class="sxs-lookup"><span data-stu-id="52228-116">Register the app with Active Directory</span></span>

### <a name="active-directory-federation-services"></a><span data-ttu-id="52228-117">Active Directory フェデレーション サービス</span><span class="sxs-lookup"><span data-stu-id="52228-117">Active Directory Federation Services</span></span>

* <span data-ttu-id="52228-118">ADFS 管理コンソールから、サーバーの**証明書利用者信頼の追加ウィザード**を開きます。</span><span class="sxs-lookup"><span data-stu-id="52228-118">Open the server's **Add Relying Party Trust Wizard** from the ADFS Management console:</span></span>

![証明書利用者信頼の追加ウィザード: ようこそ](ws-federation/_static/AdfsAddTrust.png)

* <span data-ttu-id="52228-120">データを手動で入力する場合に選択します。</span><span class="sxs-lookup"><span data-stu-id="52228-120">Choose to enter data manually:</span></span>

![証明書利用者信頼の追加ウィザード: データソースの選択](ws-federation/_static/AdfsSelectDataSource.png)

* <span data-ttu-id="52228-122">証明書利用者の表示名を入力します。</span><span class="sxs-lookup"><span data-stu-id="52228-122">Enter a display name for the relying party.</span></span> <span data-ttu-id="52228-123">名前は ASP.NET Core アプリにとって重要ではありません。</span><span class="sxs-lookup"><span data-stu-id="52228-123">The name isn't important to the ASP.NET Core app.</span></span>

* <span data-ttu-id="52228-124">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation)では、トークンの暗号化がサポートされていないため、トークン暗号化証明書を構成しないでください。</span><span class="sxs-lookup"><span data-stu-id="52228-124">[Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) lacks support for token encryption, so don't configure a token encryption certificate:</span></span>

![証明書利用者信頼の追加ウィザード: 証明書の構成](ws-federation/_static/AdfsConfigureCert.png)

* <span data-ttu-id="52228-126">アプリの URL を使用して、WS-FEDERATION パッシブプロトコルのサポートを有効にします。</span><span class="sxs-lookup"><span data-stu-id="52228-126">Enable support for WS-Federation Passive protocol, using the app's URL.</span></span> <span data-ttu-id="52228-127">アプリケーションのポートが正しいことを確認します。</span><span class="sxs-lookup"><span data-stu-id="52228-127">Verify the port is correct for the app:</span></span>

![証明書利用者信頼の追加ウィザード: URL の構成](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> <span data-ttu-id="52228-129">これは HTTPS URL である必要があります。</span><span class="sxs-lookup"><span data-stu-id="52228-129">This must be an HTTPS URL.</span></span> <span data-ttu-id="52228-130">IIS Express は、開発時にアプリをホストするときに自己署名証明書を提供できます。</span><span class="sxs-lookup"><span data-stu-id="52228-130">IIS Express can provide a self-signed certificate when hosting the app during development.</span></span> <span data-ttu-id="52228-131">Kestrel には、手動による証明書の構成が必要です。</span><span class="sxs-lookup"><span data-stu-id="52228-131">Kestrel requires manual certificate configuration.</span></span> <span data-ttu-id="52228-132">詳細については、 [Kestrel のドキュメント](xref:fundamentals/servers/kestrel)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="52228-132">See the [Kestrel documentation](xref:fundamentals/servers/kestrel) for more details.</span></span>

* <span data-ttu-id="52228-133">ウィザードの残りの部分で **[次へ]** をクリックし、最後**に終了し**ます。</span><span class="sxs-lookup"><span data-stu-id="52228-133">Click **Next** through the rest of the wizard and **Close** at the end.</span></span>

* <span data-ttu-id="52228-134">ASP.NET Core Id には**名前 ID**要求が必要です。</span><span class="sxs-lookup"><span data-stu-id="52228-134">ASP.NET Core Identity requires a **Name ID** claim.</span></span> <span data-ttu-id="52228-135">**[要求規則の編集]** ダイアログボックスで、次のいずれかを追加します。</span><span class="sxs-lookup"><span data-stu-id="52228-135">Add one from the **Edit Claim Rules** dialog:</span></span>

![要求規則の編集](ws-federation/_static/EditClaimRules.png)

* <span data-ttu-id="52228-137">**変換要求規則の追加ウィザード**で、既定の **[LDAP 属性を要求として送信]** テンプレートを選択したままにして、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="52228-137">In the **Add Transform Claim Rule Wizard**, leave the default **Send LDAP Attributes as Claims** template selected, and click **Next**.</span></span> <span data-ttu-id="52228-138">**SAM-Account-name** LDAP 属性を出力方向の要求の**名前 ID**にマッピングする規則を追加します。</span><span class="sxs-lookup"><span data-stu-id="52228-138">Add a rule mapping the **SAM-Account-Name** LDAP attribute to the **Name ID** outgoing claim:</span></span>

![変換要求規則の追加ウィザード: 要求規則の構成](ws-federation/_static/AddTransformClaimRule.png)

* <span data-ttu-id="52228-140">**[要求規則の編集]** ウィンドウで **[完了]**  > [ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="52228-140">Click **Finish** > **OK** in the **Edit Claim Rules** window.</span></span>

### <a name="azure-active-directory"></a><span data-ttu-id="52228-141">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="52228-141">Azure Active Directory</span></span>

* <span data-ttu-id="52228-142">AAD テナントの [アプリの登録] ブレードに移動します。</span><span class="sxs-lookup"><span data-stu-id="52228-142">Navigate to the AAD tenant's app registrations blade.</span></span> <span data-ttu-id="52228-143">**[新しいアプリケーションの登録]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="52228-143">Click **New application registration**:</span></span>

![Azure Active Directory: アプリの登録](ws-federation/_static/AadNewAppRegistration.png)

* <span data-ttu-id="52228-145">アプリ登録の名前を入力します。</span><span class="sxs-lookup"><span data-stu-id="52228-145">Enter a name for the app registration.</span></span> <span data-ttu-id="52228-146">これは、ASP.NET Core アプリにとって重要ではありません。</span><span class="sxs-lookup"><span data-stu-id="52228-146">This isn't important to the ASP.NET Core app.</span></span>
* <span data-ttu-id="52228-147">**サインオン url**としてアプリがリッスンする url を入力してください:</span><span class="sxs-lookup"><span data-stu-id="52228-147">Enter the URL the app listens on as the **Sign-on URL**:</span></span>

![Azure Active Directory: アプリの登録を作成する](ws-federation/_static/AadCreateAppRegistration.png)

* <span data-ttu-id="52228-149">**[エンドポイント]** をクリックして、**フェデレーションメタデータドキュメント**の URL を確認します。</span><span class="sxs-lookup"><span data-stu-id="52228-149">Click **Endpoints** and note the **Federation Metadata Document** URL.</span></span> <span data-ttu-id="52228-150">WS-FEDERATION ミドルウェアの `MetadataAddress`を次に示します。</span><span class="sxs-lookup"><span data-stu-id="52228-150">This is the WS-Federation middleware's `MetadataAddress`:</span></span>

![Azure Active Directory: エンドポイント](ws-federation/_static/AadFederationMetadataDocument.png)

* <span data-ttu-id="52228-152">新しいアプリの登録に移動します。</span><span class="sxs-lookup"><span data-stu-id="52228-152">Navigate to the new app registration.</span></span> <span data-ttu-id="52228-153">[**設定** > **プロパティ**] をクリックし、**アプリ ID URI**をメモします。</span><span class="sxs-lookup"><span data-stu-id="52228-153">Click **Settings** > **Properties** and make note of the **App ID URI**.</span></span> <span data-ttu-id="52228-154">WS-FEDERATION ミドルウェアの `Wtrealm`を次に示します。</span><span class="sxs-lookup"><span data-stu-id="52228-154">This is the WS-Federation middleware's `Wtrealm`:</span></span>

![Azure Active Directory: アプリの登録のプロパティ](ws-federation/_static/AadAppIdUri.png)

## <a name="use-ws-federation-without-aspnet-core-identity"></a><span data-ttu-id="52228-156">ASP.NET Core Id なしで WS-FEDERATION を使用する</span><span class="sxs-lookup"><span data-stu-id="52228-156">Use WS-Federation without ASP.NET Core Identity</span></span>

<span data-ttu-id="52228-157">WS-MANAGEMENT ミドルウェアは、Id なしで使用できます。</span><span class="sxs-lookup"><span data-stu-id="52228-157">The WS-Federation middleware can be used without Identity.</span></span> <span data-ttu-id="52228-158">例 :</span><span class="sxs-lookup"><span data-stu-id="52228-158">For example:</span></span>
::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon21.cs?name=snippet)]
::: moniker-end

## <a name="add-ws-federation-as-an-external-login-provider-for-aspnet-core-identity"></a><span data-ttu-id="52228-159">ASP.NET Core Id の外部ログインプロバイダーとして WS-FEDERATION を追加する</span><span class="sxs-lookup"><span data-stu-id="52228-159">Add WS-Federation as an external login provider for ASP.NET Core Identity</span></span>

* <span data-ttu-id="52228-160">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation)への依存関係をプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="52228-160">Add a dependency on [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) to the project.</span></span>
* <span data-ttu-id="52228-161">WS-FEDERATION を `Startup.ConfigureServices`に追加します。</span><span class="sxs-lookup"><span data-stu-id="52228-161">Add WS-Federation to `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup21.cs?name=snippet)]
::: moniker-end

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a><span data-ttu-id="52228-162">WS-FEDERATION を使用してログインする</span><span class="sxs-lookup"><span data-stu-id="52228-162">Log in with WS-Federation</span></span>

<span data-ttu-id="52228-163">アプリを参照し、nav ヘッダーの **[ログイン]** リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="52228-163">Browse to the app and click the **Log in** link in the nav header.</span></span> <span data-ttu-id="52228-164">WsFederation: ![のログイン ページを使用してログインすることができ](ws-federation/_static/WsFederationButton.png)</span><span class="sxs-lookup"><span data-stu-id="52228-164">There's an option to log in with WsFederation: ![Log in page](ws-federation/_static/WsFederationButton.png)</span></span>

<span data-ttu-id="52228-165">ADFS をプロバイダーとして使用すると、このボタンは adfs サインインページにリダイレクトされます: ![ADFS サインインページ](ws-federation/_static/AdfsLoginPage.png)</span><span class="sxs-lookup"><span data-stu-id="52228-165">With ADFS as the provider, the button redirects to an ADFS sign-in page: ![ADFS sign-in page](ws-federation/_static/AdfsLoginPage.png)</span></span>

<span data-ttu-id="52228-166">プロバイダーとして Azure Active Directory を使用すると、このボタンは AAD サインインページにリダイレクトされます: ![AAD サインインページ](ws-federation/_static/AadSignIn.png)</span><span class="sxs-lookup"><span data-stu-id="52228-166">With Azure Active Directory as the provider, the button redirects to an AAD sign-in page: ![AAD sign-in page](ws-federation/_static/AadSignIn.png)</span></span>

<span data-ttu-id="52228-167">新しいユーザーのサインインが成功すると、アプリのユーザー登録ページにリダイレクトされます。 ![登録 ページ](ws-federation/_static/Register.png)</span><span class="sxs-lookup"><span data-stu-id="52228-167">A successful sign-in for a new user redirects to the app's user registration page: ![Register page](ws-federation/_static/Register.png)</span></span>