---
title: ASP.NET Core でのデータ保護の非 DI 対応シナリオ
author: rick-anderson
description: 依存関係の挿入によって提供されるサービスを使用できない、または使用したくないデータ保護シナリオをサポートする方法について説明します。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/configuration/non-di-scenarios
ms.openlocfilehash: 62280a9f911b003383cbe348b9b62942766a2b99
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654434"
---
# <a name="non-di-aware-scenarios-for-data-protection-in-aspnet-core"></a>ASP.NET Core でのデータ保護の非 DI 対応シナリオ

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core データ保護システムは通常、[サービスコンテナーに追加](xref:security/data-protection/consumer-apis/overview)され、依存関係の注入 (DI) を介して依存コンポーネントによって使用されます。 ただし、特にシステムを既存のアプリケーションにインポートする場合は、これが実現できないか、または望ましくない場合があります。

これらのシナリオをサポートするために、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)パッケージには具象型である[dataprotectionprovider](/dotnet/api/Microsoft.AspNetCore.DataProtection.DataProtectionProvider)が用意されています。これにより、DI に依存せずにデータ保護を簡単に使用できます。 `DataProtectionProvider` 型は[IDataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionprovider)を実装します。 `DataProtectionProvider` を構築するには、次のコードサンプルに示すように、プロバイダーの暗号化キーの格納場所を示す[DirectoryInfo](/dotnet/api/system.io.directoryinfo)インスタンスを提供する必要があります。

[!code-csharp[](non-di-scenarios/_static/nodisample1.cs)]

既定では、具象型 `DataProtectionProvider` は、ファイルシステムに永続化する前に未加工のキーマテリアルを暗号化しません。 これは、開発者がネットワーク共有を指し、データ保護システムが適切な保存キー暗号化メカニズムを自動的に推測できないシナリオをサポートするためです。

また、具象型 `DataProtectionProvider` では、既定では[アプリが分離](xref:security/data-protection/configuration/overview#per-application-isolation)されません。 同じキーディレクトリを使用するすべてのアプリは、[目的のパラメーター](xref:security/data-protection/consumer-apis/purpose-strings)が一致する限り、ペイロードを共有できます。

[Dataprotectionprovider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider)コンストラクターは、システムの動作を調整するために使用できるオプションの構成コールバックを受け入れます。 次のサンプルでは、 [Setapplicationname](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname)への明示的な呼び出しを使用して分離を復元する方法を示しています。 このサンプルでは、Windows DPAPI を使用して永続化されたキーを自動的に暗号化するようにシステムを構成する方法も示します。 ディレクトリが UNC 共有を指している場合は、関連するすべてのコンピューターで共有証明書を配布し、 [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate)の呼び出しで証明書ベースの暗号化を使用するようにシステムを構成することができます。

[!code-csharp[](non-di-scenarios/_static/nodisample2.cs)]

> [!TIP]
> 具象型 `DataProtectionProvider` のインスタンスは、作成するとコストが高くなります。 アプリがこの種類の複数のインスタンスを保持していて、それらがすべて同じキーストレージディレクトリを使用している場合は、アプリのパフォーマンスが低下する可能性があります。 `DataProtectionProvider` の種類を使用する場合は、この型を1回作成し、可能な限り再利用することをお勧めします。 `DataProtectionProvider` 型と、それから作成されたすべての[IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector)インスタンスは、複数の呼び出し元に対してスレッドセーフです。
