---
title: ASP.NET Core の Azure Active Directory B2C を使用したクラウド認証
author: camsoper
description: ASP.NET Core で Azure Active Directory B2C 認証をセットアップする方法について説明します。
ms.author: casoper
ms.custom: mvc
ms.date: 01/21/2019
uid: security/authentication/azure-ad-b2c
ms.openlocfilehash: 136fa47788456492a9a7fe6d9d9e5996c13e8c20
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2020
ms.locfileid: "76727276"
---
# <a name="cloud-authentication-with-azure-active-directory-b2c-in-aspnet-core"></a><span data-ttu-id="9f0b7-103">ASP.NET Core の Azure Active Directory B2C を使用したクラウド認証</span><span class="sxs-lookup"><span data-stu-id="9f0b7-103">Cloud authentication with Azure Active Directory B2C in ASP.NET Core</span></span>

<span data-ttu-id="9f0b7-104">作成者: [Cam Soper](https://twitter.com/camsoper)</span><span class="sxs-lookup"><span data-stu-id="9f0b7-104">By [Cam Soper](https://twitter.com/camsoper)</span></span>

<span data-ttu-id="9f0b7-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) は、web アプリとモバイルアプリのクラウド id 管理ソリューションです。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) is a cloud identity management solution for web and mobile apps.</span></span> <span data-ttu-id="9f0b7-106">サービスは、クラウドとオンプレミスでホストされているアプリの認証を提供します。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-106">The service provides authentication for apps hosted in the cloud and on-premises.</span></span> <span data-ttu-id="9f0b7-107">認証の種類は、個々 のアカウントに、ソーシャル ネットワーク アカウントを含めるし、エンタープライズ アカウントをフェデレーションします。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-107">Authentication types include individual accounts, social network accounts, and federated enterprise accounts.</span></span> <span data-ttu-id="9f0b7-108">また、Azure AD B2C では、最小構成で multi-factor authentication を提供できます。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-108">Additionally, Azure AD B2C can provide multi-factor authentication with minimal configuration.</span></span>

> [!TIP]
> <span data-ttu-id="9f0b7-109">Azure Active Directory (Azure AD) と Azure AD B2C は個別の製品を提供します。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-109">Azure Active Directory (Azure AD) and Azure AD B2C are separate product offerings.</span></span> <span data-ttu-id="9f0b7-110">Azure AD テナントは、組織を表し、Azure AD B2C テナントは証明書利用者アプリケーションで使用される id のコレクションを表します。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-110">An Azure AD tenant represents an organization, while an Azure AD B2C tenant represents a collection of identities to be used with relying party applications.</span></span> <span data-ttu-id="9f0b7-111">詳細については、「 [Azure AD B2C: よく寄せられる質問 (faq)](/azure/active-directory-b2c/active-directory-b2c-faqs)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-111">To learn more, see [Azure AD B2C: Frequently asked questions (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).</span></span>

<span data-ttu-id="9f0b7-112">このチュートリアルで学習する内容は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-112">In this tutorial, learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9f0b7-113">Azure Active Directory B2C テナントの作成</span><span class="sxs-lookup"><span data-stu-id="9f0b7-113">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="9f0b7-114">Azure AD B2C にアプリを登録する</span><span class="sxs-lookup"><span data-stu-id="9f0b7-114">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="9f0b7-115">Visual Studio を使用して、認証に Azure AD B2C テナントを使用するように構成された ASP.NET Core web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="9f0b7-115">Use Visual Studio to create an ASP.NET Core web app configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="9f0b7-116">Azure AD B2C テナントの動作を制御するポリシーを構成する</span><span class="sxs-lookup"><span data-stu-id="9f0b7-116">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9f0b7-117">前提条件</span><span class="sxs-lookup"><span data-stu-id="9f0b7-117">Prerequisites</span></span>

<span data-ttu-id="9f0b7-118">次に、このチュートリアルでは必須です。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-118">The following are required for this walkthrough:</span></span>

* [<span data-ttu-id="9f0b7-119">Microsoft Azure サブスクリプション</span><span class="sxs-lookup"><span data-stu-id="9f0b7-119">Microsoft Azure subscription</span></span>](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [<span data-ttu-id="9f0b7-120">Visual Studio 2019</span><span class="sxs-lookup"><span data-stu-id="9f0b7-120">Visual Studio 2019</span></span>](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="create-the-azure-active-directory-b2c-tenant"></a><span data-ttu-id="9f0b7-121">Azure Active Directory B2C テナントを作成します。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-121">Create the Azure Active Directory B2C tenant</span></span>

<span data-ttu-id="9f0b7-122">[ドキュメントで説明されているように](/azure/active-directory-b2c/active-directory-b2c-get-started)Azure Active Directory B2C テナントを作成します。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-122">Create an Azure Active Directory B2C tenant [as described in the documentation](/azure/active-directory-b2c/active-directory-b2c-get-started).</span></span> <span data-ttu-id="9f0b7-123">入力を求められたら、テナントを Azure サブスクリプションに関連付ける省略可能なこのチュートリアルでは。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-123">When prompted, associating the tenant with an Azure subscription is optional for this tutorial.</span></span>

## <a name="register-the-app-in-azure-ad-b2c"></a><span data-ttu-id="9f0b7-124">Azure AD B2C にアプリを登録する</span><span class="sxs-lookup"><span data-stu-id="9f0b7-124">Register the app in Azure AD B2C</span></span>

<span data-ttu-id="9f0b7-125">新しく作成された Azure AD B2C テナントで、「 **web アプリを登録する**」セクションにある[ドキュメントの手順](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application)に従って、アプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-125">In the newly created Azure AD B2C tenant, register your app using [the steps in the documentation](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) under the **Register a web app** section.</span></span> <span data-ttu-id="9f0b7-126">**[Web アプリクライアントシークレットの作成]** セクションで停止します。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-126">Stop at the **Create a web app client secret** section.</span></span> <span data-ttu-id="9f0b7-127">このチュートリアルでは、クライアント シークレットが必要はありません。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-127">A client secret isn't required for this tutorial.</span></span> 

<span data-ttu-id="9f0b7-128">次の値を使用します。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-128">Use the following values:</span></span>

| <span data-ttu-id="9f0b7-129">設定</span><span class="sxs-lookup"><span data-stu-id="9f0b7-129">Setting</span></span>                       | <span data-ttu-id="9f0b7-130">[値]</span><span class="sxs-lookup"><span data-stu-id="9f0b7-130">Value</span></span>                     | <span data-ttu-id="9f0b7-131">メモ</span><span class="sxs-lookup"><span data-stu-id="9f0b7-131">Notes</span></span>                                                                                                                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9f0b7-132">**Name**</span><span class="sxs-lookup"><span data-stu-id="9f0b7-132">**Name**</span></span>                      | <span data-ttu-id="9f0b7-133">*&lt;アプリケーション名&gt;*</span><span class="sxs-lookup"><span data-stu-id="9f0b7-133">*&lt;app name&gt;*</span></span>        | <span data-ttu-id="9f0b7-134">アプリをコンシューマーに説明するアプリの**名前**を入力します。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-134">Enter a **Name** for the app that describes your app to consumers.</span></span>                                                                                                                                 |
| <span data-ttu-id="9f0b7-135">**Web アプリ/Web API を含める**</span><span class="sxs-lookup"><span data-stu-id="9f0b7-135">**Include web app / web API**</span></span> | <span data-ttu-id="9f0b7-136">はい</span><span class="sxs-lookup"><span data-stu-id="9f0b7-136">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="9f0b7-137">**暗黙的フローを許可する**</span><span class="sxs-lookup"><span data-stu-id="9f0b7-137">**Allow implicit flow**</span></span>       | <span data-ttu-id="9f0b7-138">はい</span><span class="sxs-lookup"><span data-stu-id="9f0b7-138">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="9f0b7-139">**応答 URL**</span><span class="sxs-lookup"><span data-stu-id="9f0b7-139">**Reply URL**</span></span>                 | `https://localhost:44300/signin-oidc` | <span data-ttu-id="9f0b7-140">応答 URL は、アプリが要求したトークンを Azure AD B2C が返すエンドポイントです。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-140">Reply URLs are endpoints where Azure AD B2C returns any tokens that your app requests.</span></span> <span data-ttu-id="9f0b7-141">Visual Studio には、使用する応答 URL が用意されています。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-141">Visual Studio provides the Reply URL to use.</span></span> <span data-ttu-id="9f0b7-142">ここでは、`https://localhost:44300/signin-oidc` 入力してフォームを完成させます。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-142">For now, enter `https://localhost:44300/signin-oidc` to complete the form.</span></span> |
| <span data-ttu-id="9f0b7-143">**アプリケーション ID/URI**</span><span class="sxs-lookup"><span data-stu-id="9f0b7-143">**App ID URI**</span></span>                | <span data-ttu-id="9f0b7-144">空白</span><span class="sxs-lookup"><span data-stu-id="9f0b7-144">Leave blank</span></span>               | <span data-ttu-id="9f0b7-145">このチュートリアルでは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-145">Not required for this tutorial.</span></span>                                                                                                                                                                    |
| <span data-ttu-id="9f0b7-146">**ネイティブ クライアントを含める**</span><span class="sxs-lookup"><span data-stu-id="9f0b7-146">**Include native client**</span></span>     | <span data-ttu-id="9f0b7-147">いいえ</span><span class="sxs-lookup"><span data-stu-id="9f0b7-147">No</span></span>                        |                                                                                                                                                                                                    |

> [!WARNING]
> <span data-ttu-id="9f0b7-148">Localhost 以外の応答 URL を設定する場合は、[[応答 url] の一覧で許可されている内容に関する制約](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application)に注意してください。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-148">If setting up a non-localhost Reply URL, be aware of the [constraints on what is allowed in the Reply URL list](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application).</span></span> 

<span data-ttu-id="9f0b7-149">アプリが登録されると、テナント内のアプリの一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-149">After the app is registered, the list of apps in the tenant is displayed.</span></span> <span data-ttu-id="9f0b7-150">登録したばかりのアプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-150">Select the app that was just registered.</span></span> <span data-ttu-id="9f0b7-151">**[アプリケーション ID]** フィールドの右側にある**コピー**アイコンを選択して、クリップボードにコピーします。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-151">Select the **Copy** icon to the right of the **Application ID** field to copy it to the clipboard.</span></span>

<span data-ttu-id="9f0b7-152">現時点では、Azure AD B2C テナントで構成できるものはありませんが、ブラウザーウィンドウは開いたままにしておきます。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-152">Nothing more can be configured in the Azure AD B2C tenant at this time, but leave the browser window open.</span></span> <span data-ttu-id="9f0b7-153">ASP.NET Core アプリが作成された後は、さらに多くの構成があります。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-153">There is more configuration after the ASP.NET Core app is created.</span></span>

## <a name="create-an-aspnet-core-app-in-visual-studio"></a><span data-ttu-id="9f0b7-154">Visual Studio での ASP.NET Core アプリの作成</span><span class="sxs-lookup"><span data-stu-id="9f0b7-154">Create an ASP.NET Core app in Visual Studio</span></span>

<span data-ttu-id="9f0b7-155">認証に Azure AD B2C テナントを使用する Visual Studio Web アプリケーション テンプレートを構成できます。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-155">The Visual Studio Web Application template can be configured to use the Azure AD B2C tenant for authentication.</span></span>

<span data-ttu-id="9f0b7-156">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="9f0b7-156">In Visual Studio:</span></span>

1. <span data-ttu-id="9f0b7-157">新しい ASP.NET Core Web アプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-157">Create a new ASP.NET Core Web Application.</span></span> 
2. <span data-ttu-id="9f0b7-158">テンプレートの一覧から **[Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-158">Select **Web Application** from the list of templates.</span></span>
3. <span data-ttu-id="9f0b7-159">**[認証の変更]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-159">Select the **Change Authentication** button.</span></span>
    
    ![変更の認証 ボタン](./azure-ad-b2c/_static/changeauth.png)

4. <span data-ttu-id="9f0b7-161">**[認証の変更]** ダイアログで、 **[個別のユーザーアカウント]** を選択し、ドロップダウンで **[クラウド内の既存のユーザーストアに接続する]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-161">In the **Change Authentication** dialog, select **Individual User Accounts**, and then select **Connect to an existing user store in the cloud** in the dropdown.</span></span> 
    
    ![認証の変更ダイアログ](./azure-ad-b2c/_static/changeauthdialog.png)

5. <span data-ttu-id="9f0b7-163">次の値をフォームに入力します。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-163">Complete the form with the following values:</span></span>
    
    | <span data-ttu-id="9f0b7-164">設定</span><span class="sxs-lookup"><span data-stu-id="9f0b7-164">Setting</span></span>                       | <span data-ttu-id="9f0b7-165">[値]</span><span class="sxs-lookup"><span data-stu-id="9f0b7-165">Value</span></span>                                                 |
    |-------------------------------|-------------------------------------------------------|
    | <span data-ttu-id="9f0b7-166">**ドメイン名**</span><span class="sxs-lookup"><span data-stu-id="9f0b7-166">**Domain Name**</span></span>               | <span data-ttu-id="9f0b7-167">*B2C テナントのドメイン名を &lt;&gt;*</span><span class="sxs-lookup"><span data-stu-id="9f0b7-167">*&lt;the domain name of your B2C tenant&gt;*</span></span>          |
    | <span data-ttu-id="9f0b7-168">**アプリケーション ID**</span><span class="sxs-lookup"><span data-stu-id="9f0b7-168">**Application ID**</span></span>            | <span data-ttu-id="9f0b7-169">*&lt;クリップボードからアプリケーション ID を貼り付け&gt;*</span><span class="sxs-lookup"><span data-stu-id="9f0b7-169">*&lt;paste the Application ID from the clipboard&gt;*</span></span> |
    | <span data-ttu-id="9f0b7-170">**コールバックパス**</span><span class="sxs-lookup"><span data-stu-id="9f0b7-170">**Callback Path**</span></span>             | <span data-ttu-id="9f0b7-171">*既定値を使用 &lt;&gt;*</span><span class="sxs-lookup"><span data-stu-id="9f0b7-171">*&lt;use the default value&gt;*</span></span>                       |
    | <span data-ttu-id="9f0b7-172">**サインアップまたはサインインポリシー**</span><span class="sxs-lookup"><span data-stu-id="9f0b7-172">**Sign-up or sign-in policy**</span></span> | `B2C_1_SiUpIn`                                        |
    | <span data-ttu-id="9f0b7-173">**パスワードポリシーのリセット**</span><span class="sxs-lookup"><span data-stu-id="9f0b7-173">**Reset password policy**</span></span>     | `B2C_1_SSPR`                                          |
    | <span data-ttu-id="9f0b7-174">**プロファイルポリシーの編集**</span><span class="sxs-lookup"><span data-stu-id="9f0b7-174">**Edit profile policy**</span></span>       | <span data-ttu-id="9f0b7-175">*空白のまま &lt;&gt;*</span><span class="sxs-lookup"><span data-stu-id="9f0b7-175">*&lt;leave blank&gt;*</span></span>                                 |
    
    <span data-ttu-id="9f0b7-176">**[応答 uri]** の横にある **[コピー]** リンクを選択して、応答 uri をクリップボードにコピーします。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-176">Select the **Copy** link next to **Reply URI** to copy the Reply URI to the clipboard.</span></span> <span data-ttu-id="9f0b7-177">[ **OK]** を選択して **[認証の変更]** ダイアログを閉じます。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-177">Select **OK** to close the **Change Authentication** dialog.</span></span> <span data-ttu-id="9f0b7-178">[ **OK]** を選択して web アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-178">Select **OK** to create the web app.</span></span>

## <a name="finish-the-b2c-app-registration"></a><span data-ttu-id="9f0b7-179">B2C アプリの登録を完了する</span><span class="sxs-lookup"><span data-stu-id="9f0b7-179">Finish the B2C app registration</span></span>

<span data-ttu-id="9f0b7-180">B2C アプリのプロパティを開いたままブラウザーウィンドウに戻ります。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-180">Return to the browser window with the B2C app properties still open.</span></span> <span data-ttu-id="9f0b7-181">前に指定した一時的な**応答 URL**を、Visual Studio からコピーした値に変更します。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-181">Change the temporary **Reply URL** specified earlier to the value copied from Visual Studio.</span></span> <span data-ttu-id="9f0b7-182">ウィンドウの上部にある **[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-182">Select **Save** at the top of the window.</span></span>

> [!TIP]
> <span data-ttu-id="9f0b7-183">応答 URL をコピーしていない場合は、web プロジェクトのプロパティの [デバッグ] タブにある HTTPS アドレスを使用して、 *appsettings*からの [の転送**パス**] の値を追加します。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-183">If you didn't copy the Reply URL, use the HTTPS address from the Debug tab in the web project properties, and append the **CallbackPath** value from *appsettings.json*.</span></span>

## <a name="configure-policies"></a><span data-ttu-id="9f0b7-184">ポリシーの構成</span><span class="sxs-lookup"><span data-stu-id="9f0b7-184">Configure policies</span></span>

<span data-ttu-id="9f0b7-185">Azure AD B2C のドキュメントに記載されている手順に従って、[サインアップまたはサインインポリシーを作成](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions)し、[パスワードリセットポリシーを作成](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions)します。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-185">Use the steps in the Azure AD B2C documentation to [create a sign-up or sign-in policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions), and then [create a password reset policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions).</span></span> <span data-ttu-id="9f0b7-186">**Id プロバイダー**、**サインアップ属性**、および**アプリケーション要求**に関するドキュメントに記載されている値の例を使用します。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-186">Use the example values provided in the documentation for **Identity providers**, **Sign-up attributes**, and **Application claims**.</span></span> <span data-ttu-id="9f0b7-187">ドキュメントで説明されているように、 **[今すぐ実行]** ボタンを使用してポリシーをテストすることは、オプションです。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-187">Using the **Run now** button to test the policies as described in the documentation is optional.</span></span>

> [!WARNING]
> <span data-ttu-id="9f0b7-188">ポリシー名は、Visual Studio の **[認証の変更]** ダイアログで使用されていたので、ドキュメントに記載されているとおりに記述してください。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-188">Ensure the policy names are exactly as described in the documentation, as those policies were used in the **Change Authentication** dialog in Visual Studio.</span></span> <span data-ttu-id="9f0b7-189">ポリシー名は、 *appsettings*で検証できます。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-189">The policy names can be verified in *appsettings.json*.</span></span>

## <a name="configure-the-underlying-openidconnectoptionsjwtbearercookie-options"></a><span data-ttu-id="9f0b7-190">基になる OpenIdConnectOptions/JwtBearer/Cookie オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="9f0b7-190">Configure the underlying OpenIdConnectOptions/JwtBearer/Cookie options</span></span>

<span data-ttu-id="9f0b7-191">基になるオプションを直接構成するには、`Startup.ConfigureServices`で適切な scheme 定数を使用します。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-191">To configure the underlying options directly, use the appropriate scheme constant in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(
    AzureAD[B2C]Defaults.OpenIdScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<CookieAuthenticationOptions>(
    AzureAD[B2C]Defaults.CookieScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<JwtBearerOptions>(
    AzureAD[B2C]Defaults.JwtBearerAuthenticationScheme, options => 
    {
        // Omitted for brevity
    });
```

## <a name="run-the-app"></a><span data-ttu-id="9f0b7-192">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="9f0b7-192">Run the app</span></span>

<span data-ttu-id="9f0b7-193">Visual Studio で、 **F5**キーを押してアプリをビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-193">In Visual Studio, press **F5** to build and run the app.</span></span> <span data-ttu-id="9f0b7-194">Web アプリが起動したら、 **[同意]** する を選択して、(メッセージが表示された場合は) cookie の使用を受け入れ、 **[サインイン]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-194">After the web app launches, select **Accept** to accept the use of cookies (if prompted), and then select **Sign in**.</span></span>

![アプリにサインインする](./azure-ad-b2c/_static/signin.png)

<span data-ttu-id="9f0b7-196">ブラウザーが Azure AD B2C テナントにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-196">The browser redirects to the Azure AD B2C tenant.</span></span> <span data-ttu-id="9f0b7-197">(ポリシーのテストを作成した場合は) 既存のアカウントでサインインするか、 **[今すぐサインアップ]** を選択して新しいアカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-197">Sign in with an existing account (if one was created testing the policies) or select **Sign up now** to create a new account.</span></span> <span data-ttu-id="9f0b7-198">忘れたパスワードをリセットするには、[**パスワードを忘れ**た場合] リンクを使用します。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-198">The **Forgot your password?** link is used to reset a forgotten password.</span></span>

![Azure AD B2C のログイン](./azure-ad-b2c/_static/b2csts.png)

<span data-ttu-id="9f0b7-200">サインインに成功すると、ブラウザーが web アプリにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-200">After successfully signing in, the browser redirects to the web app.</span></span>

![成功](./azure-ad-b2c/_static/success.png)

## <a name="next-steps"></a><span data-ttu-id="9f0b7-202">次の手順</span><span class="sxs-lookup"><span data-stu-id="9f0b7-202">Next steps</span></span>

<span data-ttu-id="9f0b7-203">このチュートリアルでは、次の作業を行う方法を学びました。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-203">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9f0b7-204">Azure Active Directory B2C テナントの作成</span><span class="sxs-lookup"><span data-stu-id="9f0b7-204">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="9f0b7-205">Azure AD B2C にアプリを登録する</span><span class="sxs-lookup"><span data-stu-id="9f0b7-205">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="9f0b7-206">Visual Studio を使用して、認証に Azure AD B2C テナントを使用するように構成された ASP.NET Core Web アプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="9f0b7-206">Use Visual Studio to create an ASP.NET Core Web Application configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="9f0b7-207">Azure AD B2C テナントの動作を制御するポリシーを構成する</span><span class="sxs-lookup"><span data-stu-id="9f0b7-207">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

<span data-ttu-id="9f0b7-208">認証に Azure AD B2C を使用するように ASP.NET Core アプリが構成されたので、[承認属性](xref:security/authorization/simple)を使用してアプリをセキュリティで保護することができます。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-208">Now that the ASP.NET Core app is configured to use Azure AD B2C for authentication, the [Authorize attribute](xref:security/authorization/simple) can be used to secure your app.</span></span> <span data-ttu-id="9f0b7-209">次のことを学習してアプリの開発を続けます。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-209">Continue developing your app by learning to:</span></span>

* <span data-ttu-id="9f0b7-210">[Azure AD B2C ユーザーインターフェイスをカスタマイズ](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization)します。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-210">[Customize the Azure AD B2C user interface](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).</span></span>
* <span data-ttu-id="9f0b7-211">[パスワードの複雑さの要件を構成](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity)します。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-211">[Configure password complexity requirements](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).</span></span>
* <span data-ttu-id="9f0b7-212">[多要素認証を有効に](/azure/active-directory-b2c/active-directory-b2c-reference-mfa)します。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-212">[Enable multi-factor authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).</span></span>
* <span data-ttu-id="9f0b7-213">[Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app)、 [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app)、 [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app)、 [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app)、 [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app)など、その他の id プロバイダーを構成します。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-213">Configure additional identity providers, such as [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app), and others.</span></span>
* <span data-ttu-id="9f0b7-214">[Azure AD Graph API を使用](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet)して、Azure AD B2C テナントからグループメンバーシップなどの追加のユーザー情報を取得します。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-214">[Use the Azure AD Graph API](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) to retrieve additional user information, such as group membership, from the Azure AD B2C tenant.</span></span>
* <span data-ttu-id="9f0b7-215">[Azure AD B2C を使用して ASP.NET Core WEB API をセキュリティで保護](https://azure.microsoft.com/resources/samples/active-directory-b2c-dotnetcore-webapi/)します。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-215">[Secure an ASP.NET Core web API using Azure AD B2C](https://azure.microsoft.com/resources/samples/active-directory-b2c-dotnetcore-webapi/).</span></span>
* <span data-ttu-id="9f0b7-216">[Azure AD B2C を使用して .net web アプリから .net WEB API を呼び出し](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet)ます。</span><span class="sxs-lookup"><span data-stu-id="9f0b7-216">[Call a .NET web API from a .NET web app using Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet).</span></span>