---
title: ASP.NET Core での TOTP authenticator アプリの QR コード生成を有効にする
author: rick-anderson
description: ASP.NET Core 2 要素認証で動作する TOTP authenticator アプリの QR コード生成を有効にする方法について説明します。
ms.author: riande
ms.date: 08/14/2018
uid: security/authentication/identity-enable-qrcodes
ms.openlocfilehash: a7fdc86b3fe94e714e5147c89a32fce13757d1c1
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654158"
---
# <a name="enable-qr-code-generation-for-totp-authenticator-apps-in-aspnet-core"></a><span data-ttu-id="2a7d2-103">ASP.NET Core での TOTP authenticator アプリの QR コード生成を有効にする</span><span class="sxs-lookup"><span data-stu-id="2a7d2-103">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="2a7d2-104">QR コードには ASP.NET Core 2.0 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-104">QR Codes requires ASP.NET Core 2.0 or later.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="2a7d2-105">ASP.NET Core には、個々の認証用の authenticator アプリケーションのサポートが付属しています。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-105">ASP.NET Core ships with support for authenticator applications for individual authentication.</span></span> <span data-ttu-id="2a7d2-106">時間ベース ワンタイム パスワード アルゴリズム (TOTP) を使用して 2 要素認証 (2 fa) authenticator アプリは、業界の 2 fa のアプローチをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-106">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="2a7d2-107">2 fa を SMS 2 fa を TOTP を使用しています。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-107">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="2a7d2-108">Authenticator アプリには、ユーザー名とパスワードを確認した後に入力する必要がある 6 ~ 8 桁のコードが用意されています。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-108">An authenticator app provides a 6 to 8 digit code which users must enter after confirming their username and password.</span></span> <span data-ttu-id="2a7d2-109">通常、認証アプリはスマートフォンにインストールされます。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-109">Typically an authenticator app is installed on a smart phone.</span></span>

<span data-ttu-id="2a7d2-110">ASP.NET Core web アプリテンプレートでは認証子がサポートされていますが、QRCode の生成はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-110">The ASP.NET Core web app templates support authenticators, but don't provide support for QRCode generation.</span></span> <span data-ttu-id="2a7d2-111">QRCode ジェネレーターにより、2FA のセットアップが簡単になります。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-111">QRCode generators ease the setup of 2FA.</span></span> <span data-ttu-id="2a7d2-112">このドキュメントでは、[2FA] 構成ページに[QR コード](https://wikipedia.org/wiki/QR_code)生成を追加する手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-112">This document will guide you through adding [QR Code](https://wikipedia.org/wiki/QR_code) generation to the 2FA configuration page.</span></span>

<span data-ttu-id="2a7d2-113">[Google](xref:security/authentication/google-logins)や[Facebook](xref:security/authentication/facebook-logins)などの外部認証プロバイダーを使用した場合、2要素認証は行われません。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-113">Two factor authentication does not happen using an external authentication provider, such as [Google](xref:security/authentication/google-logins) or [Facebook](xref:security/authentication/facebook-logins).</span></span> <span data-ttu-id="2a7d2-114">外部ログインは、外部ログインプロバイダーによって提供されるメカニズムによって保護されます。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-114">External logins are protected by whatever mechanism the external login provider provides.</span></span> <span data-ttu-id="2a7d2-115">たとえば、 [Microsoft](xref:security/authentication/microsoft-logins)認証プロバイダーでは、ハードウェアキーまたは別の2fa アプローチが必要です。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-115">Consider, for example, the [Microsoft](xref:security/authentication/microsoft-logins) authentication provider requires a hardware key or another 2FA approach.</span></span> <span data-ttu-id="2a7d2-116">既定のテンプレートで "ローカル" 2FA が適用されている場合は、ユーザーは2つの2FA アプローチを満たす必要があります。これは、一般的には使用されないシナリオです。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-116">If the default templates enforced "local" 2FA then users would be required to satisfy two 2FA approaches, which is not a commonly used scenario.</span></span>

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a><span data-ttu-id="2a7d2-117">2FA 構成ページへの QR コードの追加</span><span class="sxs-lookup"><span data-stu-id="2a7d2-117">Adding QR Codes to the 2FA configuration page</span></span>

<span data-ttu-id="2a7d2-118">この手順では、 https://davidshimjs.github.io/qrcodejs/ リポジトリから*qrcode*を使用します。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-118">These instructions use *qrcode.js* from the https://davidshimjs.github.io/qrcodejs/ repo.</span></span>

* <span data-ttu-id="2a7d2-119">[Qrcode javascript ライブラリ](https://davidshimjs.github.io/qrcodejs/)をプロジェクトの `wwwroot\lib` フォルダーにダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-119">Download the [qrcode.js javascript library](https://davidshimjs.github.io/qrcodejs/) to the `wwwroot\lib` folder in your project.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="2a7d2-120">[スキャフォールディング Identity](xref:security/authentication/scaffold-identity)の指示に従って、 */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*を生成します。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-120">Follow the instructions in [Scaffold Identity](xref:security/authentication/scaffold-identity) to generate */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*.</span></span>
* <span data-ttu-id="2a7d2-121">*/Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*で、ファイルの末尾にある `Scripts` のセクションを探します。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-121">In */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*, locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="2a7d2-122">*Pages/Account/Manage/EnableAuthenticator* (Razor Pages) または*Views/Manage/enableauthenticator* (MVC) で、ファイルの末尾にある `Scripts` セクションを見つけます。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-122">In *Pages/Account/Manage/EnableAuthenticator.cshtml* (Razor Pages) or *Views/Manage/EnableAuthenticator.cshtml* (MVC), locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* <span data-ttu-id="2a7d2-123">`Scripts` セクションを更新して、追加した `qrcodejs` ライブラリへの参照と、QR コードを生成するための呼び出しを追加します。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-123">Update the `Scripts` section to add a reference to the `qrcodejs` library you added and a call to generate the QR Code.</span></span> <span data-ttu-id="2a7d2-124">次のようになります。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-124">It should look as follows:</span></span>

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")

    <script type="text/javascript" src="~/lib/qrcode.js"></script>
    <script type="text/javascript">
        new QRCode(document.getElementById("qrCode"),
            {
                text: "@Html.Raw(Model.AuthenticatorUri)",
                width: 150,
                height: 150
            });
    </script>
}
```

* <span data-ttu-id="2a7d2-125">次の手順にリンクする段落を削除します。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-125">Delete the paragraph which links you to these instructions.</span></span>

<span data-ttu-id="2a7d2-126">アプリを実行して、QR コードをスキャンし、認証子が証明するコードを検証できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-126">Run your app and ensure that you can scan the QR code and validate the code the authenticator proves.</span></span>

## <a name="change-the-site-name-in-the-qr-code"></a><span data-ttu-id="2a7d2-127">QR コードのサイト名を変更する</span><span class="sxs-lookup"><span data-stu-id="2a7d2-127">Change the site name in the QR Code</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="2a7d2-128">QR コードのサイト名は、プロジェクトを最初に作成するときに選択したプロジェクト名から取得されます。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-128">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="2a7d2-129">これを変更するには、 */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml.cs*で `GenerateQrCodeUri(string email, string unformattedKey)` メソッドを検索します。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-129">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="2a7d2-130">QR コードのサイト名は、プロジェクトを最初に作成するときに選択したプロジェクト名から取得されます。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-130">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="2a7d2-131">これを変更するには、 *Pages/Account/Manage/EnableAuthenticator. cshtml* (Razor Pages) ファイルまたは*Controllers/ManageController* (MVC) ファイルで `GenerateQrCodeUri(string email, string unformattedKey)` メソッドを探します。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-131">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the *Pages/Account/Manage/EnableAuthenticator.cshtml.cs* (Razor Pages) file or the *Controllers/ManageController.cs* (MVC) file.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="2a7d2-132">テンプレートの既定のコードは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-132">The default code from the template looks as follows:</span></span>

```csharp
private string GenerateQrCodeUri(string email, string unformattedKey)
{
    return string.Format(
        AuthenticatorUriFormat,
        _urlEncoder.Encode("Razor Pages"),
        _urlEncoder.Encode(email),
        unformattedKey);
}
```

<span data-ttu-id="2a7d2-133">`string.Format` の呼び出しの2番目のパラメーターは、ソリューション名から取得したサイト名です。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-133">The second parameter in the call to `string.Format` is your site name, taken from your solution name.</span></span> <span data-ttu-id="2a7d2-134">任意の値に変更できますが、常に URL エンコードされている必要があります。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-134">It can be changed to any value, but it must always be URL encoded.</span></span>

## <a name="using-a-different-qr-code-library"></a><span data-ttu-id="2a7d2-135">別の QR コードライブラリの使用</span><span class="sxs-lookup"><span data-stu-id="2a7d2-135">Using a different QR Code library</span></span>

<span data-ttu-id="2a7d2-136">QR コードライブラリを任意のライブラリに置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-136">You can replace the QR Code library with your preferred library.</span></span> <span data-ttu-id="2a7d2-137">HTML には、ライブラリが提供するメカニズムによって QR コードを配置するための `qrCode` 要素が含まれています。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-137">The HTML contains a `qrCode` element into which you can place a QR Code by whatever mechanism your library provides.</span></span>

<span data-ttu-id="2a7d2-138">QR コード用に正しく書式設定された URL は、で入手できます。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-138">The correctly formatted URL for the QR Code is available in the:</span></span>

* <span data-ttu-id="2a7d2-139">モデルの `AuthenticatorUri` プロパティです。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-139">`AuthenticatorUri` property of the model.</span></span>
* <span data-ttu-id="2a7d2-140">`qrCodeData` 要素の `data-url` プロパティ。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-140">`data-url` property in the `qrCodeData` element.</span></span>

## <a name="totp-client-and-server-time-skew"></a><span data-ttu-id="2a7d2-141">TOTP クライアントとサーバーの時刻のずれ</span><span class="sxs-lookup"><span data-stu-id="2a7d2-141">TOTP client and server time skew</span></span>

<span data-ttu-id="2a7d2-142">TOTP (時間ベースのワンタイムパスワード) 認証は、サーバーと authenticator デバイスの正確な時刻の両方に依存します。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-142">TOTP (Time-based One-Time Password) authentication depends on both the server and authenticator device having an accurate time.</span></span> <span data-ttu-id="2a7d2-143">トークンは30秒間のみ最後にあります。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-143">Tokens only last for 30 seconds.</span></span> <span data-ttu-id="2a7d2-144">TOTP 2FA ログインが失敗した場合は、サーバーの時刻が正確であること、および適切な NTP サービスに同期されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2a7d2-144">If TOTP 2FA logins are failing, check that the server time is accurate, and preferably synchronized to an accurate NTP service.</span></span>

::: moniker-end
