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
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a>ASP.NET Core での EU 一般データ保護規則 (GDPR) のサポート

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core には、いくつかの[EU 一般データ保護規則 (GDPR)](https://www.eugdpr.org/)の要件を満たすのに役立つAPIとテンプレートが用意されています。

::: moniker range=">= aspnetcore-3.0"

* プロジェクト テンプレートには、拡張ポイントと、プライバシーと cookie の使用ポリシーに置き換えることができるスタブのマークアップが含まれます。
* *Pages/Privacy.cshtml*ページまたは*Views/Home/Privacy.cshtml*ビューは、サイトのプライバシー ポリシーについて詳しく説明するページを提供します。

そのような既定の cookie の同意の機能を有効にするのには、ASP.NET Core 3.0 のテンプレートが生成されたアプリでの ASP.NET Core 2.2 テンプレートが見つかりません。

* 追加[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)すぎる`Startup.ConfigureServices`と[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy)に`Startup.Configure`:

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* 追加する cookie の同意部分、 *_Layout.cshtml*ファイル。

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* 追加、  *\_CookieConsentPartial.cshtml*ファイルをプロジェクト。

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* Cookie の同意の機能については、この記事の ASP.NET Core 2.2 バージョンを選択します。

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* プロジェクト テンプレートには、拡張ポイントと、プライバシーと cookie の使用ポリシーに置き換えることができるスタブのマークアップが含まれます。
* Cookie の同意の機能は使用すると、同意を求める (および追跡) を個人情報を格納するため、ユーザーから使用できます。 ユーザーがデータの収集に同意していないし、アプリが場合[CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded)に設定`true`不要な cookie がブラウザーに送信されません。
* Cookie は、必須としてマークできます。 重要な cookie は、ユーザーが同意していないし、追跡が無効になっている場合でも、ブラウザーに送信されます。
* [TempData とセッション cookie](#tempdata)追跡を無効にした場合に機能しません。
* [Identity 管理](#pd)ダウンロードして、ユーザー データを削除するリンクを提供します。

[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)では、ASP.NET Core 2.1 のテンプレートに追加されたほとんどの GDPR の拡張点と API をテストできます。 詳しくは、手順のテスト用のファイル[ReadMe](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)を参照してください。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a>テンプレートによって生成されたコードでの ASP.NET Core GDPR サポートします。

Razor ページと MVC プロジェクト テンプレートで作成したプロジェクトには、次の GDPR サポートが含まれます。

* [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)と[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy)で設定されて、`Startup`クラス。
* *\_CookieConsentPartial.cshtml* [部分ビュー](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)します。 **Accept**ボタンがこのファイルに含まれます。 ユーザーがクリックすると、 **Accept**ボタン、cookie を保存することに同意提供されます。
* *Pages/Privacy.cshtml*ページまたは*Views/Home/Privacy.cshtml*ビューは、サイトのプライバシー ポリシーについて詳しく説明するページを提供します。 *\_CookieConsentPartial.cshtml*ファイル プライバシー ページへのリンクが生成されます。
* [管理] ページをアプリの個々 のユーザー アカウントで作成された場合に、ダウンロードして、削除するリンクを提供します[ユーザーの個人データ](#pd)します。

### <a name="cookiepolicyoptions-and-usecookiepolicy"></a>CookiePolicyOptions と UseCookiePolicy

[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)で初期化されます`Startup.ConfigureServices`:

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy)で呼び出される`Startup.Configure`:

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="cookieconsentpartialcshtml-partial-view"></a>\_CookieConsentPartial.cshtml 部分ビュー

*\_CookieConsentPartial.cshtml*部分ビュー。

[!code-html[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

この部分には:

* ユーザーの追跡の状態を取得します。 同意を必要とするアプリを構成する場合、ユーザーは cookie を追跡する前に同意する必要があります。 によって作成されたナビゲーション バーの上部にある cookie の同意のパネルを固定の同意が必要な場合、  *\_Layout.cshtml*ファイル。
* HTML を提供します。`<p>`プライバシーと cookie を要約する要素は、ポリシーを使用します。
* プライバシー ページまたはサイトのプライバシー ポリシーについて説明できるビューへのリンクを提供します。

## <a name="essential-cookies"></a>重要な cookie

場合の同意の cookie を保存するが指定されていない、必須のマークの cookie のみが、ブラウザーに送信されます。 次のコードでは、cookie が不可欠です。

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-cookies-arent-essential"></a>TempData プロバイダーとのセッション状態の cookie は不可欠です。

[TempData プロバイダー](xref:fundamentals/app-state#tempdata) cookie が必須ではありません。 追跡が無効になっている場合は、TempData プロバイダーは機能しません。 追跡を無効にするには、TempData プロバイダーを有効にするするには、重要として TempData cookie をマーク`Startup.ConfigureServices`:

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

[セッション状態](xref:fundamentals/app-state)cookie が必須ではないです。 追跡を無効にすると、セッション状態は機能しません。 次のコードでは、セッション cookie が不可欠です。

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a>個人データ

個々 のユーザー アカウントで作成された ASP.NET Core アプリには、ダウンロードして、個人データを削除するコードが含まれます。

ユーザー名を選択し、選択**個人データ**:

![個人のデータ ページを管理します。](gdpr/_static/pd.png)

メモ:

* 生成する、`Account/Manage`コードは、「[スキャフォールディング Identity](xref:security/authentication/scaffold-identity)します。
* **削除**と**ダウンロード**リンクは、既定の id データに対してのみ機能します。 カスタム ユーザー データを作成するアプリは、カスタム ユーザー データの削除/ダウンロードするように拡張する必要があります。 詳細については、次を参照してください。[追加、ダウンロード、および Id にカスタム ユーザー データの削除](xref:security/authentication/add-user-data)します。
* Id のデータベース テーブルに格納されているユーザーのトークンを保存`AspNetUserTokens`カスケード削除動作のために使用して、ユーザーが削除されたときに削除されます、[外部キー](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152)します。
* [外部プロバイダー認証](xref:security/authentication/social/index)など、Facebook や Google を使用できない、cookie のポリシーが受け入れられる前に、します。

::: moniker-end

## <a name="encryption-at-rest"></a>保存時の暗号化

保存時の暗号化のいくつかのデータベースとストレージのメカニズムを許可します。 保存時の暗号化:

* 格納されたデータを自動的に暗号化します。
* 構成、プログラミングでは、ソフトウェアのデータにアクセスするには、他の作業なしを暗号化します。
* 最も簡単で安全なオプションです。
* により、データベース キーと暗号化を管理できます。

例:

* Microsoft SQL と Azure SQL の提供[Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE)。
* [SQL Azure は、既定では、データベースを暗号化します](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* [Azure の Blob、ファイル、テーブル、および Queue Storage は既定で暗号化](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/)します。

データベースの組み込みの保存時の暗号化を指定しない場合は、ディスク暗号化を使用して、同じレベルの保護を提供することができます。 例:

* [Windows Server 用の BitLocker](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* Linux の場合:
  * [eCryptfs](https://launchpad.net/ecryptfs)
  * [EncFS](https://github.com/vgough/encfs)します。

## <a name="additional-resources"></a>その他の技術情報

* [Microsoft.com/GDPR](https://www.microsoft.com/trustcenter/Privacy/GDPR)
