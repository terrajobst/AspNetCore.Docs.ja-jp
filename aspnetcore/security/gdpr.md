---
title: ASP.NET Core での一般データ保護規則 (GDPR) のサポート
author: rick-anderson
description: ASP.NET Core web アプリの GDPR 拡張ポイントにアクセスする方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
uid: security/gdpr
ms.openlocfilehash: 1086c22c2f3c27373d8cb779f4b1d8eb6792ec2e
ms.sourcegitcommit: 2fa0ffe82a47c7317efc9ea908365881cbcb8ed7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2019
ms.locfileid: "69572874"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a>ASP.NET Core での EU 一般データ保護規則 (GDPR) のサポート

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core には、いくつかの[EU 一般データ保護規則 (GDPR)](https://www.eugdpr.org/)の要件を満たすのに役立つAPIとテンプレートが用意されています。

::: moniker range=">= aspnetcore-3.0"

* プロジェクトテンプレートには、拡張ポイントとスタブマークアップが含まれています。これは、プライバシーと cookie の使用ポリシーに置き換えることができます。
* *Pages/privacy. cshtml*ページまたは*Views/Home/privacy*ビューには、サイトのプライバシーポリシーを詳細に説明するページが用意されています。

ASP.NET Core 3.0 テンプレートで生成されたアプリの ASP.NET Core 2.2 テンプレートにあるような既定の cookie 同意機能を有効にするには、次のようにします。

* Using `using Microsoft.AspNetCore.Http`ディレクティブのリストにを追加します。
* [Cookiepolicyoptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)を`Startup.ConfigureServices`と[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) `Startup.Configure`に追加します。

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* 次のように、cookie の同意を部分的なファイルに追加します *。*

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* CookieConsentPartial ファイルをプロジェクトに追加します。  *\_*

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* Cookie の同意機能については、この記事の ASP.NET Core 2.2 バージョンを選択してください。

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* プロジェクトテンプレートには、拡張ポイントとスタブマークアップが含まれています。これは、プライバシーと cookie の使用ポリシーに置き換えることができます。
* Cookie の同意機能を使用すると、個人情報の保存をユーザーに要求する (および追跡する) ことができます。 ユーザーがデータ収集に同意しておらず、アプリの[checkcon](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded)がに`true`設定されている場合、必須ではない cookie はブラウザーに送信されません。
* Cookie は、必須としてマークできます。 ユーザーが同意しておらず、追跡が無効になっている場合でも、必須の cookie がブラウザーに送信されます。
* 追跡が無効になっている場合[、Tempdata とセッションクッキー](#tempdata)は機能しません。
* [ [Id 管理](#pd)] ページには、ユーザーデータをダウンロードおよび削除するためのリンクが表示されます。

[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)では、ASP.NET Core 2.1 のテンプレートに追加されたほとんどの GDPR の拡張点と API をテストできます。 詳しくは、手順のテスト用のファイル[ReadMe](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)を参照してください。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a>テンプレートで生成されたコードでの GDPR サポートの ASP.NET Core

プロジェクトテンプレートで作成された Razor Pages および MVC プロジェクトには、次の GDPR サポートが含まれます。

* [Cookiepolicyoptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)と[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy)は`Startup`クラスで設定されます。
* CookieConsentPartial 部分[ビュー](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)。  *\_* このファイルには、 **[Accept]** ボタンが含まれています。 ユーザーが [**同意**する] ボタンをクリックすると、[cookie の保存に同意する] が表示されます。
* *Pages/privacy. cshtml*ページまたは*Views/Home/privacy*ビューには、サイトのプライバシーポリシーを詳細に説明するページが用意されています。 *\_CookieConsentPartial*ファイルは、プライバシーページへのリンクを生成します。
* 個々のユーザーアカウントで作成されたアプリの場合、[管理] ページに、[個人ユーザーデータ](#pd)をダウンロードおよび削除するためのリンクが表示されます。

### <a name="cookiepolicyoptions-and-usecookiepolicy"></a>CookiePolicyOptions および UseCookiePolicy

[Cookiepolicyoptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)は次の`Startup.ConfigureServices`もので初期化されます。

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy)は次の`Startup.Configure`もので呼び出されます。

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_cookieconsentpartialcshtml-partial-view"></a>\_CookieConsentPartial 部分ビュー

*\_CookieConsentPartial*部分ビューは次のとおりです。

[!code-html[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

この部分は次のとおりです。

* ユーザーの追跡の状態を取得します。 アプリが同意を要求するように構成されている場合、ユーザーは、cookie を追跡する前に同意する必要があります。 同意が必要な場合、cookie 同意パネルは、  *\_Layout*ファイルによって作成されたナビゲーションバーの上部に固定されます。
* プライバシーと cookie `<p>`の使用ポリシーを要約する HTML 要素を提供します。
* サイトのプライバシーポリシーを詳細に説明する [プライバシー] ページまたはビューへのリンクを提供します。

## <a name="essential-cookies"></a>必須の cookie

Cookie を保存するための同意が指定されていない場合は、必須とマークされた cookie だけがブラウザーに送信されます。 次のコードでは、cookie が必要になります。

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-cookies-arent-essential"></a>TempData プロバイダーとセッション状態の cookie は必須ではありません

[Tempdata プロバイダー](xref:fundamentals/app-state#tempdata)の cookie は必須ではありません。 追跡が無効になっている場合、TempData プロバイダーは機能しません。 追跡が無効になっているときに TempData プロバイダーを有効にするには、 `Startup.ConfigureServices`tempdata cookie をに必須としてマークします。

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

[セッション状態](xref:fundamentals/app-state)の cookie は必須ではありません。 追跡が無効になっている場合、セッション状態は機能しません。 次のコードでは、セッション cookie が不可欠になります。

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a>個人データ

個々のユーザーアカウントで作成されたアプリには、個人データをダウンロードして削除するコードが含まれています。 ASP.NET Core

ユーザー名を選択し、 **[個人データ]** を選択します。

![[個人データの管理] ページ](gdpr/_static/pd.png)

メモ:

* `Account/Manage`コードを生成するには、「[スキャフォールディング Identity](xref:security/authentication/scaffold-identity)」を参照してください。
* **[削除]** リンクと **[ダウンロード]** リンクは、既定の id データに対してのみ機能します。 カスタムユーザーデータを作成するアプリは、カスタムユーザーデータを削除/ダウンロードするように拡張する必要があります。 詳細については、「[カスタムユーザーデータを id に追加、ダウンロード、および削除する](xref:security/authentication/add-user-data)」を参照してください。
* Id データベーステーブル`AspNetUserTokens`に格納されているユーザーの保存済みトークンは、[外部キー](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152)による連鎖削除動作によってユーザーが削除されると削除されます。
* Cookie ポリシーを受け入れる前に、Facebook や Google などの[外部プロバイダー認証](xref:security/authentication/social/index)は使用できません。

::: moniker-end

## <a name="encryption-at-rest"></a>保存時の暗号化

一部のデータベースとストレージメカニズムでは、保存時の暗号化が可能です。 保存時の暗号化:

* 保存されたデータを自動的に暗号化します。
* は、データにアクセスするソフトウェアの構成、プログラミング、またはその他の作業を行わずに暗号化します。
* は最も簡単で安全なオプションです。
* データベースでキーと暗号化を管理できるようにします。

例:

* Microsoft SQL と Azure SQL は、 [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (tde) を提供します。
* [既定では、SQL Azure によってデータベースが暗号化されます。](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* [既定では、Azure の blob、ファイル、テーブル、および Queue Storage が暗号化され](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/)ます。

保存時の組み込みの暗号化を提供しないデータベースの場合は、ディスク暗号化を使用して同じ保護を提供できる場合があります。 例:

* [Windows Server 用の BitLocker](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* Linux の場合:
  * [eCryptfs](https://launchpad.net/ecryptfs)
  * [Encfs](https://github.com/vgough/encfs)。

## <a name="additional-resources"></a>その他の技術情報

* [Microsoft.com/GDPR](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [GDPR-ASP.NET Core に Revoke 同意ボタンを追加する](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)
