---
title: SignalR でのユーザーとグループの管理
author: bradygaster
description: ASP.NET Core SignalR ユーザーとグループの管理の概要について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/groups
ms.openlocfilehash: 7e8c85dcbc46daa68988374f499f19a9d2cead47
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654248"
---
# <a name="manage-users-and-groups-in-opno-locsignalr"></a>SignalR でのユーザーとグループの管理

[Brennan Conroy](https://github.com/BrennanConroy)

SignalR を使用すると、特定のユーザーに関連付けられているすべての接続、および名前付きの接続グループにメッセージを送信できます。

[サンプルコードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/)[する (ダウンロードする方法)](xref:index#how-to-download-a-sample)

## <a name="users-in-opno-locsignalr"></a>SignalR のユーザー

SignalR を使用すると、特定のユーザーに関連付けられているすべての接続にメッセージを送信できます。 既定では、SignalR は、接続に関連付けられている `ClaimsPrincipal` の `ClaimTypes.NameIdentifier` をユーザー識別子として使用します。 1人のユーザーが SignalR アプリへの複数の接続を持つことができます。 たとえば、ユーザーは自分のデスクトップと同様に携帯電話でも接続することができます。 各デバイスには個別の SignalR 接続がありますが、これらはすべて同じユーザーに関連付けられています。 メッセージがユーザーに送信されると、ユーザーに関連付けられているすべての接続でメッセージを受信します。 接続のユーザー識別子には、ハブの `Context.UserIdentifier` プロパティを使用してアクセスできます。

次の例に示すように、ハブメソッドの `User` 関数にユーザー識別子を渡すことによって、特定のユーザーにメッセージを送信します。

> [!NOTE]
> ユーザー識別子では、大文字と小文字が区別されます。

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-opno-locsignalr"></a>SignalR 内のグループ

グループとは、名前に関連付けられている接続のコレクションです。 メッセージは、グループ内のすべての接続に送信できます。 グループはアプリケーションによって管理されるため、単一または複数の接続に送信するお勧めの方法です。 接続は、複数のグループのメンバーになることができます。 これにより、グループは各部屋をグループとして表現できるチャットアプリケーションのようなものに最適です。 接続は、`AddToGroupAsync` および `RemoveFromGroupAsync` 方法を使用して、グループに追加したり、グループから削除したりできます。

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

接続が再接続されると、グループメンバーシップは保持されません。 接続が再確立されたときに、グループに再度参加する必要があります。 アプリケーションが複数のサーバーにスケーリングされる場合にこの情報は使用ではないため、グループのメンバーをカウントすることはできません。

グループの使用中にリソースへのアクセスを保護するには、ASP.NET Core で[認証と承認](xref:signalr/authn-and-authz)の機能を使用します。 グループに対して資格情報が有効な場合にのみユーザーをグループに追加すると、そのグループに送信されたメッセージは承認されたユーザーにのみ届きます。 ただし、グループはセキュリティ機能ではありません。 認証要求には、有効期限や失効など、グループにはない機能があります。 グループにアクセスするユーザーのアクセス許可が取り消された場合は、手動で検出しグループから削除する必要があります。

> [!NOTE]
> グループ名は大文字小文字を区別します。

## <a name="related-resources"></a>関連リソース

* [開始するには](xref:tutorials/signalr)
* [ハブ](xref:signalr/hubs)
* [Azure に発行する](xref:signalr/publish-to-azure-web-app)
