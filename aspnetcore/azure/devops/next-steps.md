---
title: 次のステップ - ASP.NET Core および Azure を使用した DevOps
author: CamSoper
description: ASP.NET Core および Azure を使用した DevOps のための追加のラーニング パス。
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/next-steps
ms.openlocfilehash: a775dc42551a43bcce72b5f9ca364ed00b1dc4e6
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78647432"
---
# <a name="next-steps"></a>次の手順

このガイドでは ASP.NET Core サンプル アプリ用の DevOps パイプラインを作成しました。 お疲れさまでした。 Azure App Service に ASP.NET Core Web アプリを発行し、変更の継続的インテグレーションを自動化する方法についてご理解いただけたと思います。

Web ホスティングと DevOps 以外にも、Azure には、ASP.NET Core の開発者に役立つ数多くのサービスとしてのプラットフォーム (PaaS) サービスがあります。 このセクションでは、いくつかの最も一般的に使用されるサービスの概要について説明します。

## <a name="storage-and-databases"></a>ストレージとデータベース

[Redis Cache](/azure/redis-cache/) は、高スループットで、待機時間の短い、サービスとして使用できるデータ キャッシュです。 これは、ページ出力のキャッシュ、データベース要求の削減、アプリの複数のインスタンスにわたって ASP.NET Core セッション状態の提供に使用できます。

[Azure Storage](/azure/storage/) は、Azure の非常にスケーラブルなクラウド ストレージです。 開発者は、信頼性の高いメッセージ キューに対して [Queue Storage](/azure/storage/queues/storage-queues-introduction) を利用できます。また、[Table Storage](/azure/storage/tables/table-storage-overview) は、大規模な半構造化データ セットを使用した迅速な開発のために設計された NoSQL キー値ストアです。

[Azure SQL Database](/azure/sql-database/) は、Microsoft SQL Server エンジンを使用して、使い慣れたリレーショナル データベース機能をサービスとして提供します。

[Cosmos DB](/azure/cosmos-db/) は、グローバル分散型のマルチモデル NoSQL データベース サービスです。 SQL API (旧称 DocumentDB)、Cassandra、MongoDB など、複数の API を使用できます。

## <a name="identity"></a>Identity

[Azure Active Directory](/azure/active-directory/) と [Azure Active Directory B2C](/azure/active-directory-b2c/) はどちらも ID サービスです。 Azure Active Directory は、エンタープライズ シナリオ向けに設計されており、Azure AD B2B (企業間) コラボレーションを可能にします。一方、Azure Active Directory B2C は、ソーシャル ネットワークのサインインなど、企業と消費者間のシナリオを対象としています。

## <a name="mobile"></a>携帯

[Notification Hubs](/azure/notification-hubs/) は、さまざまな種類のデバイスで実行されているアプリに何百万ものメッセージを迅速に送信するための、マルチプラットフォームのスケーラブルなプッシュ通知エンジンです。

## <a name="web-infrastructure"></a>Web インフラストラクチャ

[Azure Container Service](/azure/aks/) では、コンテナー オーケストレーションの専門知識がなくても、コンテナー化されたアプリを迅速かつ簡単にデプロイおよび管理できるように、ホストされている Kubernetes 環境を管理します。

[Azure Search](/azure/search/) は、プライベートの異種コンテンツに対してエンタープライズ検索ソリューションを作成するために使用されます。

[Service Fabric](/azure/service-fabric/) は、スケーラブルで信頼性に優れたマイクロサービスとコンテナーのパッケージ化、デプロイ、管理を簡単に行うことができる分散システム プラットフォームです。
