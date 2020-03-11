---
title: ASP.NET Core のキーの不変性とキー設定
author: rick-anderson
description: ASP.NET Core データ保護のキー不変 Api の実装の詳細について説明します。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 7796cb102c0f6f03809704016fd36b28c7a82438
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653996"
---
# <a name="key-immutability-and-key-settings-in-aspnet-core"></a><span data-ttu-id="29044-103">ASP.NET Core のキーの不変性とキー設定</span><span class="sxs-lookup"><span data-stu-id="29044-103">Key immutability and key settings in ASP.NET Core</span></span>

<span data-ttu-id="29044-104">オブジェクトがバッキングストアに永続化されると、その表現は永久に固定されます。</span><span class="sxs-lookup"><span data-stu-id="29044-104">Once an object is persisted to the backing store, its representation is forever fixed.</span></span> <span data-ttu-id="29044-105">バッキングストアに新しいデータを追加することはできますが、既存のデータを変換することはできません。</span><span class="sxs-lookup"><span data-stu-id="29044-105">New data can be added to the backing store, but existing data can never be mutated.</span></span> <span data-ttu-id="29044-106">この動作の主な目的は、データの破損を防ぐことです。</span><span class="sxs-lookup"><span data-stu-id="29044-106">The primary purpose of this behavior is to prevent data corruption.</span></span>

<span data-ttu-id="29044-107">この動作の1つの結果として、キーがバッキングストアに書き込まれると、変更できなくなります。</span><span class="sxs-lookup"><span data-stu-id="29044-107">One consequence of this behavior is that once a key is written to the backing store, it's immutable.</span></span> <span data-ttu-id="29044-108">作成、アクティブ化、および有効期限の日付は変更できませんが、`IKeyManager`を使用して取り消すことができます。</span><span class="sxs-lookup"><span data-stu-id="29044-108">Its creation, activation, and expiration dates can never be changed, though it can revoked by using `IKeyManager`.</span></span> <span data-ttu-id="29044-109">さらに、基になるアルゴリズム情報、マスターキーマテリアル、および保存時の暗号化プロパティも変更できません。</span><span class="sxs-lookup"><span data-stu-id="29044-109">Additionally, its underlying algorithmic information, master keying material, and encryption at rest properties are also immutable.</span></span>

<span data-ttu-id="29044-110">キーの永続化に影響する設定が開発者によって変更された場合、これらの変更は、次にキーが生成されるまで有効になりません。これは、`IKeyManager.CreateNewKey` を明示的に呼び出すか、データ保護システムの[自動キー生成](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)動作を使用することによって行われます。</span><span class="sxs-lookup"><span data-stu-id="29044-110">If the developer changes any setting that affects key persistence, those changes won't go into effect until the next time a key is generated, either via an explicit call to `IKeyManager.CreateNewKey` or via the data protection system's own [automatic key generation](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) behavior.</span></span> <span data-ttu-id="29044-111">キーの永続化に影響する設定は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="29044-111">The settings that affect key persistence are as follows:</span></span>

* [<span data-ttu-id="29044-112">既定のキーの有効期間</span><span class="sxs-lookup"><span data-stu-id="29044-112">The default key lifetime</span></span>](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)

* [<span data-ttu-id="29044-113">保存時のキー暗号化メカニズム</span><span class="sxs-lookup"><span data-stu-id="29044-113">The key encryption at rest mechanism</span></span>](xref:security/data-protection/implementation/key-encryption-at-rest)

* [<span data-ttu-id="29044-114">キー内に含まれるアルゴリズム情報</span><span class="sxs-lookup"><span data-stu-id="29044-114">The algorithmic information contained within the key</span></span>](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

<span data-ttu-id="29044-115">これらの設定を次の自動キーのローリングタイムより前に開始する必要がある場合は、`IKeyManager.CreateNewKey` を明示的に呼び出すことを検討して、新しいキーを強制的に作成することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="29044-115">If you need these settings to kick in earlier than the next automatic key rolling time, consider making an explicit call to `IKeyManager.CreateNewKey` to force the creation of a new key.</span></span> <span data-ttu-id="29044-116">明示的なアクティベーション日 ({now + 2 日} は、変更が反映されるまでの時間を確保するための適切な目安です) と通話の有効期限を指定してください。</span><span class="sxs-lookup"><span data-stu-id="29044-116">Remember to provide an explicit activation date ({ now + 2 days } is a good rule of thumb to allow time for the change to propagate) and expiration date in the call.</span></span>

>[!TIP]
> <span data-ttu-id="29044-117">リポジトリに接しているすべてのアプリケーションでは、`IDataProtectionBuilder` の拡張メソッドで同じ設定を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="29044-117">All applications touching the repository should specify the same settings with the `IDataProtectionBuilder` extension methods.</span></span> <span data-ttu-id="29044-118">それ以外の場合、永続化されたキーのプロパティは、キー生成ルーチンを呼び出した特定のアプリケーションに依存します。</span><span class="sxs-lookup"><span data-stu-id="29044-118">Otherwise, the properties of the persisted key will be dependent on the particular application that invoked the key generation routines.</span></span>
