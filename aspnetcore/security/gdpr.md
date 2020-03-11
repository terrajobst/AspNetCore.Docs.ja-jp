---
title: ASP.NET Core での一般データ保護規則 (GDPR) のサポート
author: rick-anderson
description: ASP.NET Core web アプリの GDPR 拡張ポイントにアクセスする方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
uid: security/gdpr
ms.openlocfilehash: 2ccba780ba81bd805d08c9b898617387a879bed3
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652238"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a>ASP.NET Core での EU 一般データ保護規則 (GDPR) のサポート

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core は、一部の[EU 一般データ保護規則 (GDPR)](https://www.eugdpr.org/)要件を満たすための api とテンプレートを提供します。

::: moniker range=">= aspnetcore-3.0"

* プロジェクトテンプレートには、拡張ポイントとスタブマークアップが含まれています。これは、プライバシーと cookie の使用ポリシーに置き換えることができます。
* *Pages/privacy. cshtml*ページまたは*Views/Home/privacy*ビューには、サイトのプライバシーポリシーを詳細に説明するページが用意されています。

ASP.NET Core 3.0 テンプレートで生成されたアプリの ASP.NET Core 2.2 テンプレートにあるような既定の cookie 同意機能を有効にするには、次のようにします。

* Using ディレクティブの一覧に `using Microsoft.AspNetCore.Http` を追加します。
* `Startup.Configure`に `Startup.ConfigureServices` および[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy)に[cookiepolicyoptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)を追加します。

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* Cookie 同意部分を *_Layout*ファイルに追加します。

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* *\_CookieConsentPartial*ファイルをプロジェクトに追加します。

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* Cookie の同意機能については、この記事の ASP.NET Core 2.2 バージョンを選択してください。

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* プロジェクトテンプレートには、拡張ポイントとスタブマークアップが含まれています。これは、プライバシーと cookie の使用ポリシーに置き換えることができます。
* Cookie の同意機能を使用すると、個人情報の保存をユーザーに要求する (および追跡する) ことができます。 ユーザーがデータ収集に同意しておらず、アプリの[Checkcon](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded)が `true`に設定されていない場合、重要ではない cookie はブラウザーに送信されません。
* Cookie は、必須としてマークできます。 ユーザーが同意しておらず、追跡が無効になっている場合でも、必須の cookie がブラウザーに送信されます。
* 追跡が無効になっている場合[、Tempdata とセッションクッキー](#tempdata)は機能しません。
* [ [Id 管理](#pd)] ページには、ユーザーデータをダウンロードおよび削除するためのリンクが表示されます。

この[サンプルアプリ](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)を使用すると、ASP.NET Core 2.1 テンプレートに追加された GDPR 拡張ポイントと api の大部分をテストできます。 テスト手順については、 [ReadMe](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)ファイルを参照してください。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a>テンプレートで生成されたコードでの GDPR サポートの ASP.NET Core

プロジェクトテンプレートで作成された Razor Pages および MVC プロジェクトには、次の GDPR サポートが含まれます。

* [Cookiepolicyoptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)と[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy)は、`Startup` クラスで設定されます。
* *\_CookieConsentPartial* [部分ビュー](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)。 このファイルには、 **[Accept]** ボタンが含まれています。 ユーザーが [**同意**する] ボタンをクリックすると、[cookie の保存に同意する] が表示されます。
* *Pages/privacy. cshtml*ページまたは*Views/Home/privacy*ビューには、サイトのプライバシーポリシーを詳細に説明するページが用意されています。 *\_CookieConsentPartial*ファイルは、プライバシーページへのリンクを生成します。
* 個々のユーザーアカウントで作成されたアプリの場合、[管理] ページに、[個人ユーザーデータ](#pd)をダウンロードおよび削除するためのリンクが表示されます。

### <a name="cookiepolicyoptions-and-usecookiepolicy"></a>CookiePolicyOptions および UseCookiePolicy

[Cookiepolicyoptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)は `Startup.ConfigureServices`で初期化されます。

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy)は `Startup.Configure`で呼び出されます。

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_cookieconsentpartialcshtml-partial-view"></a>\_CookieConsentPartial 部分ビュー

*\_CookieConsentPartial*部分ビュー:

[!code-html[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

この部分は次のとおりです。

* ユーザーの追跡の状態を取得します。 アプリが同意を要求するように構成されている場合、ユーザーは、cookie を追跡する前に同意する必要があります。 同意が必要な場合は、 *\_Layout*ファイルによって作成されたナビゲーションバーの上部にある cookie 同意パネルが固定されています。
* プライバシーと cookie の使用ポリシーを要約するための HTML `<p>` 要素を提供します。
* サイトのプライバシーポリシーを詳細に説明する [プライバシー] ページまたはビューへのリンクを提供します。

## <a name="essential-cookies"></a>必須の cookie

Cookie を保存するための同意が指定されていない場合は、必須とマークされた cookie だけがブラウザーに送信されます。 次のコードでは、cookie が必要になります。

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-cookies-arent-essential"></a>TempData プロバイダーとセッション状態の cookie は必須ではありません

[Tempdata プロバイダー](xref:fundamentals/app-state#tempdata)の cookie は必須ではありません。 追跡が無効になっている場合、TempData プロバイダーは機能しません。 追跡が無効になっているときに TempData プロバイダーを有効にするには、`Startup.ConfigureServices`で TempData cookie を必須としてマークします。

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

[セッション状態](xref:fundamentals/app-state)の cookie は必須ではありません。 追跡が無効になっている場合、セッション状態は機能しません。 次のコードでは、セッション cookie が不可欠になります。

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a>個人データ

個々のユーザーアカウントで作成されたアプリには、個人データをダウンロードして削除するコードが含まれています。 ASP.NET Core

ユーザー名を選択し、 **[個人データ]** を選択します。

![[個人データの管理] ページ](gdpr/_static/pd.png)

注:

* `Account/Manage` コードを生成するには、「[スキャフォールディング Identity](xref:security/authentication/scaffold-identity)」を参照してください。
* **[削除]** リンクと **[ダウンロード]** リンクは、既定の id データに対してのみ機能します。 カスタムユーザーデータを作成するアプリは、カスタムユーザーデータを削除/ダウンロードするように拡張する必要があります。 詳細については、「[カスタムユーザーデータを id に追加、ダウンロード、および削除する](xref:security/authentication/add-user-data)」を参照してください。
* Id データベーステーブル `AspNetUserTokens` に格納されているユーザーの保存済みトークンは、[外部キー](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152)による連鎖削除動作によってユーザーが削除されると削除されます。
* Cookie ポリシーを受け入れる前に、Facebook や Google などの[外部プロバイダー認証](xref:security/authentication/social/index)は使用できません。

::: moniker-end

## <a name="encryption-at-rest"></a>保存時の暗号化

一部のデータベースとストレージメカニズムでは、保存時の暗号化が可能です。 保存時の暗号化:

* 保存されたデータを自動的に暗号化します。
* は、データにアクセスするソフトウェアの構成、プログラミング、またはその他の作業を行わずに暗号化します。
* は最も簡単で安全なオプションです。
* データベースでキーと暗号化を管理できるようにします。

例 :

* Microsoft SQL と Azure SQL は、 [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (tde) を提供します。
* [既定では、SQL Azure によってデータベースが暗号化されます。](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* [既定では、Azure の blob、ファイル、テーブル、および Queue Storage が暗号化され](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/)ます。

保存時の組み込みの暗号化を提供しないデータベースの場合は、ディスク暗号化を使用して同じ保護を提供できる場合があります。 例 :

* [Windows Server 用の BitLocker](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* Linux:
  * [eCryptfs](https://launchpad.net/ecryptfs)
  * [Encfs](https://github.com/vgough/encfs)。

## <a name="additional-resources"></a>その他のリソース

* [Microsoft.com/GDPR](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [GDPR-ASP.NET Core に Revoke 同意ボタンを追加する](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)
