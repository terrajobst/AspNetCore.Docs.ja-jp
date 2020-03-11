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
# <a name="enable-qr-code-generation-for-totp-authenticator-apps-in-aspnet-core"></a>ASP.NET Core での TOTP authenticator アプリの QR コード生成を有効にする

::: moniker range="<= aspnetcore-2.0"

QR コードには ASP.NET Core 2.0 以降が必要です。

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

ASP.NET Core には、個々の認証用の authenticator アプリケーションのサポートが付属しています。 時間ベース ワンタイム パスワード アルゴリズム (TOTP) を使用して 2 要素認証 (2 fa) authenticator アプリは、業界の 2 fa のアプローチをお勧めします。 2 fa を SMS 2 fa を TOTP を使用しています。 Authenticator アプリには、ユーザー名とパスワードを確認した後に入力する必要がある 6 ~ 8 桁のコードが用意されています。 通常、認証アプリはスマートフォンにインストールされます。

ASP.NET Core web アプリテンプレートでは認証子がサポートされていますが、QRCode の生成はサポートされていません。 QRCode ジェネレーターにより、2FA のセットアップが簡単になります。 このドキュメントでは、[2FA] 構成ページに[QR コード](https://wikipedia.org/wiki/QR_code)生成を追加する手順について説明します。

[Google](xref:security/authentication/google-logins)や[Facebook](xref:security/authentication/facebook-logins)などの外部認証プロバイダーを使用した場合、2要素認証は行われません。 外部ログインは、外部ログインプロバイダーによって提供されるメカニズムによって保護されます。 たとえば、 [Microsoft](xref:security/authentication/microsoft-logins)認証プロバイダーでは、ハードウェアキーまたは別の2fa アプローチが必要です。 既定のテンプレートで "ローカル" 2FA が適用されている場合は、ユーザーは2つの2FA アプローチを満たす必要があります。これは、一般的には使用されないシナリオです。

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a>2FA 構成ページへの QR コードの追加

この手順では、 https://davidshimjs.github.io/qrcodejs/ リポジトリから*qrcode*を使用します。

* [Qrcode javascript ライブラリ](https://davidshimjs.github.io/qrcodejs/)をプロジェクトの `wwwroot\lib` フォルダーにダウンロードします。

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* [スキャフォールディング Identity](xref:security/authentication/scaffold-identity)の指示に従って、 */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*を生成します。
* */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*で、ファイルの末尾にある `Scripts` のセクションを探します。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* *Pages/Account/Manage/EnableAuthenticator* (Razor Pages) または*Views/Manage/enableauthenticator* (MVC) で、ファイルの末尾にある `Scripts` セクションを見つけます。

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* `Scripts` セクションを更新して、追加した `qrcodejs` ライブラリへの参照と、QR コードを生成するための呼び出しを追加します。 次のようになります。

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

* 次の手順にリンクする段落を削除します。

アプリを実行して、QR コードをスキャンし、認証子が証明するコードを検証できることを確認します。

## <a name="change-the-site-name-in-the-qr-code"></a>QR コードのサイト名を変更する

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

QR コードのサイト名は、プロジェクトを最初に作成するときに選択したプロジェクト名から取得されます。 これを変更するには、 */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml.cs*で `GenerateQrCodeUri(string email, string unformattedKey)` メソッドを検索します。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

QR コードのサイト名は、プロジェクトを最初に作成するときに選択したプロジェクト名から取得されます。 これを変更するには、 *Pages/Account/Manage/EnableAuthenticator. cshtml* (Razor Pages) ファイルまたは*Controllers/ManageController* (MVC) ファイルで `GenerateQrCodeUri(string email, string unformattedKey)` メソッドを探します。

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

テンプレートの既定のコードは、次のようになります。

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

`string.Format` の呼び出しの2番目のパラメーターは、ソリューション名から取得したサイト名です。 任意の値に変更できますが、常に URL エンコードされている必要があります。

## <a name="using-a-different-qr-code-library"></a>別の QR コードライブラリの使用

QR コードライブラリを任意のライブラリに置き換えることができます。 HTML には、ライブラリが提供するメカニズムによって QR コードを配置するための `qrCode` 要素が含まれています。

QR コード用に正しく書式設定された URL は、で入手できます。

* モデルの `AuthenticatorUri` プロパティです。
* `qrCodeData` 要素の `data-url` プロパティ。

## <a name="totp-client-and-server-time-skew"></a>TOTP クライアントとサーバーの時刻のずれ

TOTP (時間ベースのワンタイムパスワード) 認証は、サーバーと authenticator デバイスの正確な時刻の両方に依存します。 トークンは30秒間のみ最後にあります。 TOTP 2FA ログインが失敗した場合は、サーバーの時刻が正確であること、および適切な NTP サービスに同期されていることを確認します。

::: moniker-end
