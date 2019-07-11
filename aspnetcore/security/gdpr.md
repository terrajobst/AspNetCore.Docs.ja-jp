---
title: ASP.NET Core での一般データ保護規則 (GDPR) のサポート
author: rick-anderson
description: ASP.NET Core web アプリでは、GDPR の拡張ポイントにアクセスする方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
uid: security/gdpr
ms.openlocfilehash: 01d2f8943c0995c1400122b89c4ca7c459a85279
ms.sourcegitcommit: bee530454ae2b3c25dc7ffebf93536f479a14460
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67724569"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a><span data-ttu-id="f1620-103">ASP.NET Core での EU 一般データ保護規則 (GDPR) のサポート</span><span class="sxs-lookup"><span data-stu-id="f1620-103">EU General Data Protection Regulation (GDPR) support in ASP.NET Core</span></span>

<span data-ttu-id="f1620-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f1620-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f1620-105">ASP.NET Core には、いくつかの[EU 一般データ保護規則 (GDPR)](https://www.eugdpr.org/)の要件を満たすのに役立つAPIとテンプレートが用意されています。</span><span class="sxs-lookup"><span data-stu-id="f1620-105">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://www.eugdpr.org/) requirements:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="f1620-106">プロジェクト テンプレートには、拡張ポイントと、プライバシーと cookie の使用ポリシーに置き換えることができるスタブのマークアップが含まれます。</span><span class="sxs-lookup"><span data-stu-id="f1620-106">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="f1620-107">*Pages/Privacy.cshtml*ページまたは*Views/Home/Privacy.cshtml*ビューは、サイトのプライバシー ポリシーについて詳しく説明するページを提供します。</span><span class="sxs-lookup"><span data-stu-id="f1620-107">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span>

<span data-ttu-id="f1620-108">そのような既定の cookie の同意の機能を有効にするのには、ASP.NET Core 3.0 のテンプレートが生成されたアプリでの ASP.NET Core 2.2 テンプレートが見つかりません。</span><span class="sxs-lookup"><span data-stu-id="f1620-108">To enable the default cookie consent feature like that found in the ASP.NET Core 2.2 templates in an ASP.NET Core 3.0 template generated app:</span></span>

* <span data-ttu-id="f1620-109">追加[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)すぎる`Startup.ConfigureServices`と[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy)に`Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f1620-109">Add [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) too `Startup.ConfigureServices` and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) to `Startup.Configure`:</span></span>

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* <span data-ttu-id="f1620-110">追加する cookie の同意部分、 *_Layout.cshtml*ファイル。</span><span class="sxs-lookup"><span data-stu-id="f1620-110">Add the cookie consent partial to the *_Layout.cshtml* file:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* <span data-ttu-id="f1620-111">追加、  *\_CookieConsentPartial.cshtml*ファイルをプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="f1620-111">Add the *\_CookieConsentPartial.cshtml* file to the project:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* <span data-ttu-id="f1620-112">Cookie の同意の機能については、この記事の ASP.NET Core 2.2 バージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="f1620-112">Select the ASP.NET Core 2.2 version of this article to read about the cookie consent feature.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* <span data-ttu-id="f1620-113">プロジェクト テンプレートには、拡張ポイントと、プライバシーと cookie の使用ポリシーに置き換えることができるスタブのマークアップが含まれます。</span><span class="sxs-lookup"><span data-stu-id="f1620-113">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="f1620-114">Cookie の同意の機能は使用すると、同意を求める (および追跡) を個人情報を格納するため、ユーザーから使用できます。</span><span class="sxs-lookup"><span data-stu-id="f1620-114">A cookie consent feature allows you to ask for (and track) consent from your users for storing personal information.</span></span> <span data-ttu-id="f1620-115">ユーザーがデータの収集に同意していないし、アプリが場合[CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded)に設定`true`不要な cookie がブラウザーに送信されません。</span><span class="sxs-lookup"><span data-stu-id="f1620-115">If a user hasn't consented to data collection and the app has [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) set to `true`, non-essential cookies aren't sent to the browser.</span></span>
* <span data-ttu-id="f1620-116">Cookie は、必須としてマークできます。</span><span class="sxs-lookup"><span data-stu-id="f1620-116">Cookies can be marked as essential.</span></span> <span data-ttu-id="f1620-117">重要な cookie は、ユーザーが同意していないし、追跡が無効になっている場合でも、ブラウザーに送信されます。</span><span class="sxs-lookup"><span data-stu-id="f1620-117">Essential cookies are sent to the browser even when the user hasn't consented and tracking is disabled.</span></span>
* <span data-ttu-id="f1620-118">[TempData とセッション cookie](#tempdata)追跡を無効にした場合に機能しません。</span><span class="sxs-lookup"><span data-stu-id="f1620-118">[TempData and Session cookies](#tempdata) aren't functional when tracking is disabled.</span></span>
* <span data-ttu-id="f1620-119">[Identity 管理](#pd)ダウンロードして、ユーザー データを削除するリンクを提供します。</span><span class="sxs-lookup"><span data-stu-id="f1620-119">The [Identity manage](#pd) page provides a link to download and delete user data.</span></span>

<span data-ttu-id="f1620-120">[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)では、ASP.NET Core 2.1 のテンプレートに追加されたほとんどの GDPR の拡張点と API をテストできます。</span><span class="sxs-lookup"><span data-stu-id="f1620-120">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) allows you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span> <span data-ttu-id="f1620-121">詳しくは、手順のテスト用のファイル[ReadMe](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f1620-121">See the [ReadMe](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) file for testing instructions.</span></span>

<span data-ttu-id="f1620-122">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="f1620-122">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a><span data-ttu-id="f1620-123">テンプレートによって生成されたコードでの ASP.NET Core GDPR サポートします。</span><span class="sxs-lookup"><span data-stu-id="f1620-123">ASP.NET Core GDPR support in template-generated code</span></span>

<span data-ttu-id="f1620-124">Razor ページと MVC プロジェクト テンプレートで作成したプロジェクトには、次の GDPR サポートが含まれます。</span><span class="sxs-lookup"><span data-stu-id="f1620-124">Razor Pages and MVC projects created with the project templates include the following GDPR support:</span></span>

* <span data-ttu-id="f1620-125">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)と[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy)で設定されて、`Startup`クラス。</span><span class="sxs-lookup"><span data-stu-id="f1620-125">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) are set in the `Startup` class.</span></span>
* <span data-ttu-id="f1620-126">*\_CookieConsentPartial.cshtml* [部分ビュー](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)します。</span><span class="sxs-lookup"><span data-stu-id="f1620-126">The *\_CookieConsentPartial.cshtml* [partial view](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="f1620-127">**Accept**ボタンがこのファイルに含まれます。</span><span class="sxs-lookup"><span data-stu-id="f1620-127">An **Accept** button is included in this file.</span></span> <span data-ttu-id="f1620-128">ユーザーがクリックすると、 **Accept**ボタン、cookie を保存することに同意提供されます。</span><span class="sxs-lookup"><span data-stu-id="f1620-128">When the user clicks the **Accept** button, consent to store cookies is provided.</span></span>
* <span data-ttu-id="f1620-129">*Pages/Privacy.cshtml*ページまたは*Views/Home/Privacy.cshtml*ビューは、サイトのプライバシー ポリシーについて詳しく説明するページを提供します。</span><span class="sxs-lookup"><span data-stu-id="f1620-129">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span> <span data-ttu-id="f1620-130">*\_CookieConsentPartial.cshtml*ファイル プライバシー ページへのリンクが生成されます。</span><span class="sxs-lookup"><span data-stu-id="f1620-130">The *\_CookieConsentPartial.cshtml* file generates a link to the Privacy page.</span></span>
* <span data-ttu-id="f1620-131">[管理] ページをアプリの個々 のユーザー アカウントで作成された場合に、ダウンロードして、削除するリンクを提供します[ユーザーの個人データ](#pd)します。</span><span class="sxs-lookup"><span data-stu-id="f1620-131">For apps created with individual user accounts, the Manage page provides links to download and delete [personal user data](#pd).</span></span>

### <a name="cookiepolicyoptions-and-usecookiepolicy"></a><span data-ttu-id="f1620-132">CookiePolicyOptions と UseCookiePolicy</span><span class="sxs-lookup"><span data-stu-id="f1620-132">CookiePolicyOptions and UseCookiePolicy</span></span>

<span data-ttu-id="f1620-133">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)で初期化されます`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f1620-133">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) are initialized in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

<span data-ttu-id="f1620-134">[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy)で呼び出される`Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f1620-134">[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) is called in `Startup.Configure`:</span></span>

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="cookieconsentpartialcshtml-partial-view"></a><span data-ttu-id="f1620-135">\_CookieConsentPartial.cshtml 部分ビュー</span><span class="sxs-lookup"><span data-stu-id="f1620-135">\_CookieConsentPartial.cshtml partial view</span></span>

<span data-ttu-id="f1620-136">*\_CookieConsentPartial.cshtml*部分ビュー。</span><span class="sxs-lookup"><span data-stu-id="f1620-136">The *\_CookieConsentPartial.cshtml* partial view:</span></span>

[!code-html[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

<span data-ttu-id="f1620-137">この部分には:</span><span class="sxs-lookup"><span data-stu-id="f1620-137">This partial:</span></span>

* <span data-ttu-id="f1620-138">ユーザーの追跡の状態を取得します。</span><span class="sxs-lookup"><span data-stu-id="f1620-138">Obtains the state of tracking for the user.</span></span> <span data-ttu-id="f1620-139">同意を必要とするアプリを構成する場合、ユーザーは cookie を追跡する前に同意する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f1620-139">If the app is configured to require consent, the user must consent before cookies can be tracked.</span></span> <span data-ttu-id="f1620-140">によって作成されたナビゲーション バーの上部にある cookie の同意のパネルを固定の同意が必要な場合、  *\_Layout.cshtml*ファイル。</span><span class="sxs-lookup"><span data-stu-id="f1620-140">If consent is required, the cookie consent panel is fixed at top of the navigation bar created by the *\_Layout.cshtml* file.</span></span>
* <span data-ttu-id="f1620-141">HTML を提供します。`<p>`プライバシーと cookie を要約する要素は、ポリシーを使用します。</span><span class="sxs-lookup"><span data-stu-id="f1620-141">Provides an HTML `<p>` element to summarize your privacy and cookie use policy.</span></span>
* <span data-ttu-id="f1620-142">プライバシー ページまたはサイトのプライバシー ポリシーについて説明できるビューへのリンクを提供します。</span><span class="sxs-lookup"><span data-stu-id="f1620-142">Provides a link to Privacy page or view where you can detail your site's privacy policy.</span></span>

## <a name="essential-cookies"></a><span data-ttu-id="f1620-143">重要な cookie</span><span class="sxs-lookup"><span data-stu-id="f1620-143">Essential cookies</span></span>

<span data-ttu-id="f1620-144">場合の同意の cookie を保存するが指定されていない、必須のマークの cookie のみが、ブラウザーに送信されます。</span><span class="sxs-lookup"><span data-stu-id="f1620-144">If consent to store cookies hasn't been provided, only cookies marked essential are sent to the browser.</span></span> <span data-ttu-id="f1620-145">次のコードでは、cookie が不可欠です。</span><span class="sxs-lookup"><span data-stu-id="f1620-145">The following code makes a cookie essential:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-cookies-arent-essential"></a><span data-ttu-id="f1620-146">TempData プロバイダーとのセッション状態の cookie は不可欠です。</span><span class="sxs-lookup"><span data-stu-id="f1620-146">TempData provider and session state cookies aren't essential</span></span>

<span data-ttu-id="f1620-147">[TempData プロバイダー](xref:fundamentals/app-state#tempdata) cookie が必須ではありません。</span><span class="sxs-lookup"><span data-stu-id="f1620-147">The [TempData provider](xref:fundamentals/app-state#tempdata) cookie isn't essential.</span></span> <span data-ttu-id="f1620-148">追跡が無効になっている場合は、TempData プロバイダーは機能しません。</span><span class="sxs-lookup"><span data-stu-id="f1620-148">If tracking is disabled, the TempData provider isn't functional.</span></span> <span data-ttu-id="f1620-149">追跡を無効にするには、TempData プロバイダーを有効にするするには、重要として TempData cookie をマーク`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f1620-149">To enable the TempData provider when tracking is disabled, mark the TempData cookie as essential in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

<span data-ttu-id="f1620-150">[セッション状態](xref:fundamentals/app-state)cookie が必須ではないです。</span><span class="sxs-lookup"><span data-stu-id="f1620-150">[Session state](xref:fundamentals/app-state) cookies are not essential.</span></span> <span data-ttu-id="f1620-151">追跡を無効にすると、セッション状態は機能しません。</span><span class="sxs-lookup"><span data-stu-id="f1620-151">Session state isn't functional when tracking is disabled.</span></span> <span data-ttu-id="f1620-152">次のコードでは、セッション cookie が不可欠です。</span><span class="sxs-lookup"><span data-stu-id="f1620-152">The following code makes session cookies essential:</span></span>

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a><span data-ttu-id="f1620-153">個人データ</span><span class="sxs-lookup"><span data-stu-id="f1620-153">Personal data</span></span>

<span data-ttu-id="f1620-154">個々 のユーザー アカウントで作成された ASP.NET Core アプリには、ダウンロードして、個人データを削除するコードが含まれます。</span><span class="sxs-lookup"><span data-stu-id="f1620-154">ASP.NET Core apps created with individual user accounts include code to download and delete personal data.</span></span>

<span data-ttu-id="f1620-155">ユーザー名を選択し、選択**個人データ**:</span><span class="sxs-lookup"><span data-stu-id="f1620-155">Select the user name and then select **Personal data**:</span></span>

![個人のデータ ページを管理します。](gdpr/_static/pd.png)

<span data-ttu-id="f1620-157">メモ:</span><span class="sxs-lookup"><span data-stu-id="f1620-157">Notes:</span></span>

* <span data-ttu-id="f1620-158">生成する、`Account/Manage`コードは、「[スキャフォールディング Identity](xref:security/authentication/scaffold-identity)します。</span><span class="sxs-lookup"><span data-stu-id="f1620-158">To generate the `Account/Manage` code, see [Scaffold Identity](xref:security/authentication/scaffold-identity).</span></span>
* <span data-ttu-id="f1620-159">**削除**と**ダウンロード**リンクは、既定の id データに対してのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="f1620-159">The **Delete** and **Download** links only act on the default identity data.</span></span> <span data-ttu-id="f1620-160">カスタム ユーザー データを作成するアプリは、カスタム ユーザー データの削除/ダウンロードするように拡張する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f1620-160">Apps that create custom user data must be extended to delete/download the custom user data.</span></span> <span data-ttu-id="f1620-161">詳細については、次を参照してください。[追加、ダウンロード、および Id にカスタム ユーザー データの削除](xref:security/authentication/add-user-data)します。</span><span class="sxs-lookup"><span data-stu-id="f1620-161">For more information, see [Add, download, and delete custom user data to Identity](xref:security/authentication/add-user-data).</span></span>
* <span data-ttu-id="f1620-162">Id のデータベース テーブルに格納されているユーザーのトークンを保存`AspNetUserTokens`カスケード削除動作のために使用して、ユーザーが削除されたときに削除されます、[外部キー](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152)します。</span><span class="sxs-lookup"><span data-stu-id="f1620-162">Saved tokens for the user that are stored in the Identity database table `AspNetUserTokens` are deleted when the user is deleted via the cascading delete behavior due to the [foreign key](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).</span></span>
* <span data-ttu-id="f1620-163">[外部プロバイダー認証](xref:security/authentication/social/index)など、Facebook や Google を使用できない、cookie のポリシーが受け入れられる前に、します。</span><span class="sxs-lookup"><span data-stu-id="f1620-163">[External provider authentication](xref:security/authentication/social/index), such as Facebook and Google, isn't available before the cookie policy is accepted.</span></span>

::: moniker-end

## <a name="encryption-at-rest"></a><span data-ttu-id="f1620-164">保存時の暗号化</span><span class="sxs-lookup"><span data-stu-id="f1620-164">Encryption at rest</span></span>

<span data-ttu-id="f1620-165">保存時の暗号化のいくつかのデータベースとストレージのメカニズムを許可します。</span><span class="sxs-lookup"><span data-stu-id="f1620-165">Some databases and storage mechanisms allow for encryption at rest.</span></span> <span data-ttu-id="f1620-166">保存時の暗号化:</span><span class="sxs-lookup"><span data-stu-id="f1620-166">Encryption at rest:</span></span>

* <span data-ttu-id="f1620-167">格納されたデータを自動的に暗号化します。</span><span class="sxs-lookup"><span data-stu-id="f1620-167">Encrypts stored data automatically.</span></span>
* <span data-ttu-id="f1620-168">構成、プログラミングでは、ソフトウェアのデータにアクセスするには、他の作業なしを暗号化します。</span><span class="sxs-lookup"><span data-stu-id="f1620-168">Encrypts without configuration, programming, or other work for the software that accesses the data.</span></span>
* <span data-ttu-id="f1620-169">最も簡単で安全なオプションです。</span><span class="sxs-lookup"><span data-stu-id="f1620-169">Is the easiest and safest option.</span></span>
* <span data-ttu-id="f1620-170">により、データベース キーと暗号化を管理できます。</span><span class="sxs-lookup"><span data-stu-id="f1620-170">Allows the database to manage keys and encryption.</span></span>

<span data-ttu-id="f1620-171">例:</span><span class="sxs-lookup"><span data-stu-id="f1620-171">For example:</span></span>

* <span data-ttu-id="f1620-172">Microsoft SQL と Azure SQL の提供[Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE)。</span><span class="sxs-lookup"><span data-stu-id="f1620-172">Microsoft SQL and Azure SQL provide [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span></span>
* [<span data-ttu-id="f1620-173">SQL Azure は、既定では、データベースを暗号化します</span><span class="sxs-lookup"><span data-stu-id="f1620-173">SQL Azure encrypts the database by default</span></span>](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* <span data-ttu-id="f1620-174">[Azure の Blob、ファイル、テーブル、および Queue Storage は既定で暗号化](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/)します。</span><span class="sxs-lookup"><span data-stu-id="f1620-174">[Azure Blobs, Files, Table, and Queue Storage are encrypted by default](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span></span>

<span data-ttu-id="f1620-175">データベースの組み込みの保存時の暗号化を指定しない場合は、ディスク暗号化を使用して、同じレベルの保護を提供することができます。</span><span class="sxs-lookup"><span data-stu-id="f1620-175">For databases that don't provide built-in encryption at rest, you may be able to use disk encryption to provide the same protection.</span></span> <span data-ttu-id="f1620-176">例:</span><span class="sxs-lookup"><span data-stu-id="f1620-176">For example:</span></span>

* [<span data-ttu-id="f1620-177">Windows Server 用の BitLocker</span><span class="sxs-lookup"><span data-stu-id="f1620-177">BitLocker for Windows Server</span></span>](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* <span data-ttu-id="f1620-178">Linux の場合:</span><span class="sxs-lookup"><span data-stu-id="f1620-178">Linux:</span></span>
  * [<span data-ttu-id="f1620-179">eCryptfs</span><span class="sxs-lookup"><span data-stu-id="f1620-179">eCryptfs</span></span>](https://launchpad.net/ecryptfs)
  * <span data-ttu-id="f1620-180">[EncFS](https://github.com/vgough/encfs)します。</span><span class="sxs-lookup"><span data-stu-id="f1620-180">[EncFS](https://github.com/vgough/encfs).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f1620-181">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="f1620-181">Additional resources</span></span>

* [<span data-ttu-id="f1620-182">Microsoft.com/GDPR</span><span class="sxs-lookup"><span data-stu-id="f1620-182">Microsoft.com/GDPR</span></span>](https://www.microsoft.com/trustcenter/Privacy/GDPR)
