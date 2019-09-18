---
title: 個々のユーザーアカウントで作成された ASP.NET Core プロジェクトに基づくアーティクル
author: rick-anderson
description: 個々のユーザーアカウントで作成された ASP.NET Core プロジェクトに基づいて、記事を発見します。
ms.author: riande
ms.date: 11/30/2017
uid: security/authentication/individual
ms.openlocfilehash: cf548417268a8587787471b9ed91c0ed109fbee9
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080702"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a>個々のユーザーアカウントで作成された ASP.NET Core プロジェクトに基づくアーティクル

ASP.NET Core Id は、Visual Studio の [個別のユーザーアカウント] オプションを使用してプロジェクトテンプレートに含まれています。

認証テンプレートは、.NET Core CLI `-au Individual`で使用できます。

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

Web API 認証については、[この GitHub の問題](https://github.com/aspnet/AspNetCore/issues/5833)を参照してください。

<a name="no"></a>

## <a name="no-authentication"></a>認証なし

.NET Core CLI では、 `-au`オプションを指定して認証を指定します。 Visual Studio では、新しい web アプリケーションで **[認証の変更]** ダイアログを使用できます。 Visual Studio での新しい web アプリの既定値は、認証され**ません**。

認証なしで作成されたプロジェクト:

* サインインおよびサインアウトするための web ページと UI を含めないでください。
* 認証コードを含めないでください。

<a name="win"></a>

## <a name="windows-authentication"></a>[Windows 認証]

Windows 認証は、 `-au Windows`オプションを使用して .NET Core CLI の新しい web アプリに対して指定されます。 Visual Studio では、 **[認証の変更]** ダイアログボックスに**Windows 認証**オプションが表示されます。

[Windows 認証] が選択されている場合、アプリは[Windows 認証 IIS モジュール](xref:host-and-deploy/iis/modules)を使用するように構成されます。 Windows 認証は、イントラネット web サイトを対象としています。

## <a name="additional-resources"></a>その他の技術情報

次の記事では、個々のユーザーアカウントを使用する ASP.NET Core テンプレートで生成されたコードを使用する方法について説明します。

* [SMS での 2 要素認証](xref:security/authentication/2fa)
* [ASP.NET Core でのアカウントの確認とパスワードの回復](xref:security/authentication/accconfirm)
* [承認によって保護されたユーザーデータを含む ASP.NET Core アプリを作成する](xref:security/authorization/secure-data)
