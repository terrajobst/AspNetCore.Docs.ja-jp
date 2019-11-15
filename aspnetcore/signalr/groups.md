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
ms.openlocfilehash: 59e90042ecbaf936602643bbdc3965e036426b26
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963807"
---
# <a name="manage-users-and-groups-in-opno-locsignalr"></a>SignalR でのユーザーとグループの管理

[Brennan Conroy](https://github.com/BrennanConroy)

SignalR を使用すると、特定のユーザーに関連付けられているすべての接続、および名前付きの接続グループにメッセージを送信できます。

[サンプルコードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/)[する (ダウンロードする方法)](xref:index#how-to-download-a-sample)

## <a name="users-in-opno-locsignalr"></a>SignalR のユーザー

SignalR を使用すると、特定のユーザーに関連付けられているすべての接続にメッセージを送信できます。 既定では、SignalR は、接続に関連付けられている `ClaimsPrincipal` の `ClaimTypes.NameIdentifier` をユーザー識別子として使用します。 1人のユーザーが SignalR アプリへの複数の接続を持つことができます。 たとえば、ユーザーは自分のデスクトップや電話に接続することができます。 各デバイスには個別の SignalR 接続がありますが、これらはすべて同じユーザーに関連付けられています。 ユーザーにメッセージが送信されると、そのユーザーに関連付けられているすべての接続がメッセージを受信します。 接続のユーザー識別子には、ハブの `Context.UserIdentifier` プロパティを使用してアクセスできます。

次の例に示すように、ハブメソッドの `User` 関数にユーザー識別子を渡すことによって、特定のユーザーにメッセージを送信します。

> [!NOTE]
> ユーザー識別子では、大文字と小文字が区別されます。

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-opno-locsignalr"></a>SignalR 内のグループ

グループとは、名前に関連付けられている接続のコレクションです。 メッセージは、グループ内のすべての接続に送信できます。 グループはアプリケーションによって管理されるため、接続または複数の接続に送信する方法としては、グループをお勧めします。 接続は、複数のグループのメンバーになることができます。 これにより、グループはチャットアプリケーションのように理想的であり、各部屋をグループとして表すことができます。 接続は、`AddToGroupAsync` および `RemoveFromGroupAsync` 方法を使用して、グループに追加したり、グループから削除したりできます。

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

接続を再接続しても、グループのメンバーシップは保持されません。 再確立された場合、接続はグループに再度参加する必要があります。 アプリケーションが複数のサーバーにスケーリングされている場合、この情報は利用できないため、グループのメンバーをカウントすることはできません。

グループの使用中にリソースへのアクセスを保護するには、ASP.NET Core で[認証と承認](xref:signalr/authn-and-authz)の機能を使用します。 そのグループに対して資格情報が有効である場合にのみ、グループにユーザーを追加すると、そのグループに送信されたメッセージは、承認されたユーザーのみに送られます。 ただし、グループはセキュリティ機能ではありません。 認証要求には、有効期限や失効など、グループにはない機能があります。 グループにアクセスするためのユーザーのアクセス許可が取り消された場合は、手動で検出し、グループから削除する必要があります。

> [!NOTE]
> グループ名は大文字と小文字が区別されます。

## <a name="related-resources"></a>関連資料

* [開始するには](xref:tutorials/signalr)
* [ハブ](xref:signalr/hubs)
* [Azure に発行する](xref:signalr/publish-to-azure-web-app)
