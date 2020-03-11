---
title: ASP.NET Core でのデータ保護キーの管理と有効期間
author: rick-anderson
description: ASP.NET Core におけるデータ保護のキー管理と有効期間について説明します。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/configuration/default-settings
ms.openlocfilehash: 2f022a4c7519485fe629ce47c27d214c8c27d5bc
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655070"
---
# <a name="data-protection-key-management-and-lifetime-in-aspnet-core"></a>ASP.NET Core でのデータ保護キーの管理と有効期間

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="key-management"></a>キー管理

アプリは、運用環境を検出し、キーの構成を独自に処理しようとします。

1. アプリが[Azure アプリ](https://azure.microsoft.com/services/app-service/)でホストされている場合、キーは *%HOME%\ASP.NET\DataProtection-Keys*フォルダーに保存されます。 このフォルダーはネットワーク ストレージにバックアップされ、アプリをホストしているすべてのマシンで同期されています。
   * 保存中のキーは保護されていません。
   * *Dataprotection キー*フォルダーは、単一のデプロイスロット内のアプリのすべてのインスタンスにキーリングを提供します。
   * ステージングや運用などの別のデプロイ スロットでは、キー リングが共有されません。 デプロイスロット間でスワップする場合 (運用環境へのステージングまたは A/B テストを使用する場合など)、データ保護を使用するすべてのアプリは、前のスロット内のキーリングを使用して格納されたデータの暗号化を解除することはできません。 これにより、ユーザーは、データ保護を使用して cookie を保護するため、標準の ASP.NET Core cookie 認証を使用するアプリからログアウトされることになります。 スロットに依存しないキーリングが必要な場合は、Azure Blob Storage、Azure Key Vault、SQL ストア、Redis cache などの外部キーリングプロバイダーを使用します。

1. ユーザープロファイルが使用可能な場合、キーは *%LOCALAPPDATA%\ASP.NET\DataProtection-Keys*フォルダーに保存されます。 オペレーティングシステムが Windows の場合、キーは DPAPI を使用して保存時に暗号化されます。

   アプリ プールの [setProfileEnvironment 属性](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)も有効にする必要があります。 `setProfileEnvironment` の既定値は `true` です。 一部のシナリオ (たとえば、Windows OS) では、`setProfileEnvironment` は `false` に設定されます。 キーが期待どおりにユーザー プロファイル ディレクトリに格納されていない場合:

   1. *%windir%/system32/inetsrv/config* フォルダーに移動します。
   1. *applicationHost.config* ファイルを開きます。
   1. `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 要素を見つけます。
   1. `setProfileEnvironment` 属性 (その規定値は `true` です) が存在しないことを確認するか、属性の値を明示的に `true` に設定します。

1. アプリが IIS でホストされている場合、キーは、ワーカープロセスアカウントに対してのみ機能する特別なレジストリキーの HKLM レジストリに保存されます。 キーは DPAPI を使用して保存時に暗号化されます。

1. これらの条件のいずれも一致しない場合、キーは現在のプロセスの外部では保持されません。 プロセスがシャットダウンされると、生成されたすべてのキーが失われます。

開発者は常にフルコントロールを使用し、キーの格納方法と場所をオーバーライドできます。 上記の最初の3つのオプションでは、ASP.NET **\<machineKey >** 自動生成ルーチンが過去にどのように動作するかと同様に、ほとんどのアプリに適した既定値が提供されます。 最後のフォールバックオプションは、キーの永続化が必要な場合に、開発者が事前に[構成](xref:security/data-protection/configuration/overview)を指定することを必要とする唯一のシナリオですが、このフォールバックはまれな状況でのみ発生します。

Docker コンテナーでホストする場合、キーは、Docker ボリューム (共有ボリュームまたはコンテナーの有効期間を超えて保持されるホストマウントボリューム)、または[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)や[Redis](https://redis.io/)などの外部プロバイダーのフォルダーに保存する必要があります。 アプリが共有ネットワークボリュームにアクセスできない場合は、web ファームのシナリオでも外部プロバイダーが役立ちます (詳細については、「 [Persistkeystofilesystem](xref:security/data-protection/configuration/overview#persistkeystofilesystem) 」を参照してください)。

> [!WARNING]
> 開発者が上記の規則をオーバーライドし、特定のキーリポジトリでデータ保護システムをポイントする場合、保存時のキーの自動暗号化は無効になります。 保存時の保護は、[構成](xref:security/data-protection/configuration/overview)を使用して再度有効にすることができます。

## <a name="key-lifetime"></a>キーの有効期間

既定では、キーの有効期間は90日です。 キーの有効期限が切れると、アプリは自動的に新しいキーを生成し、新しいキーをアクティブなキーとして設定します。 インベントリから削除されたキーがシステムに残っている限り、アプリで保護されているすべてのデータの暗号化を解除できます。 詳細については、「[キー管理](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling)」を参照してください。

## <a name="default-algorithms"></a>既定のアルゴリズム

使用される既定のペイロード保護アルゴリズムは、HMACSHA256 の場合は AES-256-CBC、信頼性を確保する場合はです。 90日ごとに変更された512ビットのマスターキーは、ペイロードごとにこれらのアルゴリズムに使用される2つのサブキーを派生させるために使用されます。 詳細については、「[サブキーの派生](xref:security/data-protection/implementation/subkeyderivation#additional-authenticated-data-and-subkey-derivation)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

* <xref:security/data-protection/extensibility/key-management>
* <xref:host-and-deploy/web-farm>
