---
title: 個々のユーザーアカウントで作成された ASP.NET Core プロジェクトに基づくアーティクル
author: rick-anderson
description: 個々のユーザーアカウントで作成された ASP.NET Core プロジェクトに基づいて、記事を発見します。
ms.author: riande
ms.date: 12/11/2019
uid: security/authentication/individual
ms.openlocfilehash: 7ef0d5eabded61d04fb9fe7be384a663ad7ea5f4
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828712"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a>個々のユーザーアカウントで作成された ASP.NET Core プロジェクトに基づくアーティクル

ASP.NET Core Id は、Visual Studio の [個別のユーザーアカウント] オプションを使用してプロジェクトテンプレートに含まれています。

認証テンプレートは、`-au Individual`と共に .NET Core CLI で使用できます。

::: moniker range=">= aspnetcore-2.1"

```dotnetcli
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```dotnetcli
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

Web API 認証については、[この GitHub の問題](https://github.com/dotnet/AspNetCore/issues/5833)を参照してください。

<a name="no"></a>

## <a name="no-authentication"></a>認証なし

認証は、.NET Core CLI で `-au` オプションと共に指定されます。 Visual Studio では、新しい web アプリケーションで **[認証の変更]** ダイアログを使用できます。 Visual Studio での新しい web アプリの既定値は、認証され**ません**。

認証なしで作成されたプロジェクト:

* サインインおよびサインアウトするための web ページと UI を含めないでください。
* 認証コードを含めないでください。

<a name="win"></a>

## <a name="windows-authentication"></a>Windows 認証

`-au Windows` オプションを使用して .NET Core CLI の新しい web アプリに対して Windows 認証が指定されています。 Visual Studio では、 **[認証の変更]** ダイアログボックスに**Windows 認証**オプションが表示されます。

[Windows 認証] が選択されている場合、アプリは[Windows 認証 IIS モジュール](xref:host-and-deploy/iis/modules)を使用するように構成されます。 Windows 認証は、イントラネット web サイトを対象としています。

## <a name="dotnet-new-webapp-authentication-options"></a>dotnet 新しい webapp 認証オプション

次の表は、新しい web アプリで使用できる認証オプションを示しています。

| オプション | 認証の種類 | 詳細情報のリンク |
 | ----------------- | ------------ | ---------- |
| [なし]            |  認証しない | | 
| 個人      |  個々の認証 | <xref:security/authentication/identity>
| IndividualB2C   |  Azure AD B2C を使用したクラウドホストの個々の認証 | [Azure AD B2C](/azure/active-directory-b2c/) |
| SingleOrg       |  単一テナントの組織認証 | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| MultiOrg        |  複数のテナントの組織認証 | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Windows         |  Windows 認証 | [Windows 認証](xref:security/authentication/windowsauth)

## <a name="visual-studio-new-webapp-authentication-options"></a>Visual Studio の新しい webapp 認証オプション

次の表は、Visual Studio で新しい web アプリを作成するときに使用できる認証オプションを示しています。

| オプション | 認証の種類 | 詳細情報のリンク |
 | ----------------- | ------------ | ---------- |
| [なし]            |  認証しない | | 
| 個々のユーザーアカウント/アプリ内のユーザーアカウントを格納する |  個々の認証 | <xref:security/authentication/identity> |
| 個々のユーザーアカウント/クラウド内の既存のユーザーストアに接続する |  Azure AD B2C を使用したクラウドホストの個々の認証 | [Azure AD B2C](/azure/active-directory-b2c/) |
| 職場または学校のクラウド/単一組織  |  単一テナントの組織認証 | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| 職場または学校のクラウド/複数の組織 |  複数のテナントの組織認証 | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Windows         |  Windows 認証 | [Windows 認証](xref:security/authentication/windowsauth)

## <a name="additional-resources"></a>その他の技術情報

次の記事では、個々のユーザーアカウントを使用する ASP.NET Core テンプレートで生成されたコードを使用する方法について説明します。

* [ASP.NET Core でのアカウントの確認とパスワードの回復](xref:security/authentication/accconfirm)
* [承認によって保護されたユーザーデータを含む ASP.NET Core アプリを作成する](xref:security/authorization/secure-data)
