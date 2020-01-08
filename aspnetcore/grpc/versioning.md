---
title: GRPC サービスのバージョン管理
author: jamesnk
description: GRPC サービスのバージョンを確認する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/09/2020
uid: grpc/versioning
ms.openlocfilehash: 61dd5dfca4064af3ae58ac288a1ee636450ff56b
ms.sourcegitcommit: 79850db9e79b1705b89f466c6f2c961ff15485de
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2020
ms.locfileid: "75694194"
---
# <a name="versioning-grpc-services"></a><span data-ttu-id="99e6e-103">GRPC サービスのバージョン管理</span><span class="sxs-lookup"><span data-stu-id="99e6e-103">Versioning gRPC services</span></span>

<span data-ttu-id="99e6e-104">[James のニュートン-キング](https://twitter.com/jamesnk)別</span><span class="sxs-lookup"><span data-stu-id="99e6e-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="99e6e-105">アプリに追加された新機能では、クライアントに提供される gRPC サービスを必要とすることがあります。これは、予期しない、または中断する場合があります。</span><span class="sxs-lookup"><span data-stu-id="99e6e-105">New features added to an app can require gRPC services provided to clients to change, sometimes in unexpected and breaking ways.</span></span> <span data-ttu-id="99e6e-106">GRPC サービスが変更した場合:</span><span class="sxs-lookup"><span data-stu-id="99e6e-106">When gRPC services change:</span></span>

* <span data-ttu-id="99e6e-107">変更がクライアントに与える影響について考慮する必要があります。</span><span class="sxs-lookup"><span data-stu-id="99e6e-107">Consideration should be given on how changes impact clients.</span></span>
* <span data-ttu-id="99e6e-108">変更をサポートするためのバージョン管理戦略を実装する必要があります。</span><span class="sxs-lookup"><span data-stu-id="99e6e-108">A versioning strategy to support changes should be implemented.</span></span>

## <a name="backwards-compatibility"></a><span data-ttu-id="99e6e-109">下位互換性</span><span class="sxs-lookup"><span data-stu-id="99e6e-109">Backwards compatibility</span></span>

<span data-ttu-id="99e6e-110">GRPC プロトコルは、時間の経過と共に変化するサービスをサポートするように設計されています。</span><span class="sxs-lookup"><span data-stu-id="99e6e-110">The gRPC protocol is designed to support services that change over time.</span></span> <span data-ttu-id="99e6e-111">通常、gRPC のサービスとメソッドへの追加は非互換性です。</span><span class="sxs-lookup"><span data-stu-id="99e6e-111">Generally additions to gRPC services and methods are non-breaking.</span></span> <span data-ttu-id="99e6e-112">非互換性とは、既存のクライアントが動作し続けることを意味します。</span><span class="sxs-lookup"><span data-stu-id="99e6e-112">Non-breaking means existing clients continue to work.</span></span> <span data-ttu-id="99e6e-113">GRPC サービスの変更または削除は重大な変更です。</span><span class="sxs-lookup"><span data-stu-id="99e6e-113">Changing or deleting gRPC services are breaking changes.</span></span> <span data-ttu-id="99e6e-114">重大な変更とは、既存のクライアントが失敗することを意味します。</span><span class="sxs-lookup"><span data-stu-id="99e6e-114">Breaking changes mean existing clients fail.</span></span>

<span data-ttu-id="99e6e-115">サービスに重大な変更を加えることには、次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="99e6e-115">Making non-breaking changes to a service has a number of benefits:</span></span>

- <span data-ttu-id="99e6e-116">既存のクライアントは引き続き実行されます。</span><span class="sxs-lookup"><span data-stu-id="99e6e-116">Existing clients continue to run.</span></span>
- <span data-ttu-id="99e6e-117">重大な変更をクライアントに通知し、それらを更新する作業を回避します。</span><span class="sxs-lookup"><span data-stu-id="99e6e-117">Avoids work involved with notifying clients of breaking changes, and updating them.</span></span>
- <span data-ttu-id="99e6e-118">ドキュメント化して管理する必要があるのは、1つのバージョンのサービスだけです。</span><span class="sxs-lookup"><span data-stu-id="99e6e-118">Only one version of the service needs to be documented and maintained.</span></span>

<span data-ttu-id="99e6e-119">このコンテンツでは **、gRPC プロトコルと .net バイナリ互換性レベルで**変更が中断されているかどうかを中心に説明します。</span><span class="sxs-lookup"><span data-stu-id="99e6e-119">This content focuses on whether changes are **breaking at a gRPC protocol and .NET binary compatibility level**.</span></span> <span data-ttu-id="99e6e-120">変更を行うときは、古いクライアントが論理的に動作を続行できるかどうかを検討してください。</span><span class="sxs-lookup"><span data-stu-id="99e6e-120">When making changes, consider whether older clients can logically continue working.</span></span> <span data-ttu-id="99e6e-121">たとえば、要求メッセージに新しいフィールドを追加すると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="99e6e-121">For example, adding a new field to a request message:</span></span>

* <span data-ttu-id="99e6e-122">は、プロトコルの互換性に影響する変更ではありません。</span><span class="sxs-lookup"><span data-stu-id="99e6e-122">Is not a protocol breaking change.</span></span>
* <span data-ttu-id="99e6e-123">新しいフィールドが設定されていない場合、サーバー上でエラー状態を返すと、古いクライアントの重大な変更になります。</span><span class="sxs-lookup"><span data-stu-id="99e6e-123">Returning an error status on the server if the new field is not set makes it a breaking change for old clients.</span></span>

### <a name="non-breaking-changes"></a><span data-ttu-id="99e6e-124">非破壊的変更</span><span class="sxs-lookup"><span data-stu-id="99e6e-124">Non-breaking changes</span></span>

<span data-ttu-id="99e6e-125">これらの変更は、gRPC プロトコルレベルと .NET バイナリレベルでは非互換性があります。</span><span class="sxs-lookup"><span data-stu-id="99e6e-125">These changes are non-breaking at a gRPC protocol level, and .NET binary level.</span></span>

- <span data-ttu-id="99e6e-126">**新しいサービスの追加**</span><span class="sxs-lookup"><span data-stu-id="99e6e-126">**Adding a new service**</span></span>
- <span data-ttu-id="99e6e-127">**サービスに新しいメソッドを追加する**</span><span class="sxs-lookup"><span data-stu-id="99e6e-127">**Adding a new method to a service**</span></span>
- <span data-ttu-id="99e6e-128">**要求メッセージへのフィールドの追加**: 要求メッセージに追加されたフィールドは、設定されていない場合、サーバーの[既定値](https://developers.google.com/protocol-buffers/docs/proto3#default)で逆シリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="99e6e-128">**Adding a field to a request message** - Fields added to a request message are deserialized with the [default value](https://developers.google.com/protocol-buffers/docs/proto3#default) on the server when not set.</span></span> <span data-ttu-id="99e6e-129">中断されないようにするには、古いクライアントによって設定されていないサービスを成功させる必要があります。</span><span class="sxs-lookup"><span data-stu-id="99e6e-129">To be non-breaking the service will need to succeed when it is not set by older clients.</span></span>
- <span data-ttu-id="99e6e-130">**応答メッセージにフィールドを追加**すると、応答メッセージに追加されたフィールドは、クライアント上のメッセージの[unknown fields](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)コレクションに逆シリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="99e6e-130">**Adding a field to a response message** - Fields added to a response message are deserialized into the message's [unknown fields](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) collection on the client.</span></span>
- <span data-ttu-id="99e6e-131">**列挙型の列挙型への値の追加**は、数値としてシリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="99e6e-131">**Adding a value to an enum** - Enums are serialized as a numeric value.</span></span> <span data-ttu-id="99e6e-132">新しい列挙値は、列挙型名を指定せずに、クライアント側で列挙値に逆シリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="99e6e-132">New enum values are deserialized on the client to the enum value without an enum name.</span></span> <span data-ttu-id="99e6e-133">古くなっていないクライアントは、値を受け取って除外するときに、正しく動作する必要があります。</span><span class="sxs-lookup"><span data-stu-id="99e6e-133">To be non-breaking older clients will need to run correctly when they receive and unexcepted value.</span></span>

### <a name="binary-breaking-changes"></a><span data-ttu-id="99e6e-134">バイナリの互換性に影響する変更点</span><span class="sxs-lookup"><span data-stu-id="99e6e-134">Binary breaking changes</span></span>

<span data-ttu-id="99e6e-135">次の変更は gRPC プロトコルレベルでは中断されませんが *、最新のプロトコルコントラクト*またはクライアント .net アセンブリにアップグレードする場合は、クライアントを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="99e6e-135">The following changes are non-breaking at a gRPC protocol level, but the client needs to be updated if it upgrades to the latest *.proto* contract or client .NET assembly.</span></span> <span data-ttu-id="99e6e-136">GRPC ライブラリを NuGet に発行する予定の場合、バイナリの互換性が重要です。</span><span class="sxs-lookup"><span data-stu-id="99e6e-136">Binary compatibility is important if you plan to publish a gRPC library to NuGet.</span></span>

- <span data-ttu-id="99e6e-137">削除されたフィールドからフィールド値を**削除**すると、メッセージの[不明なフィールド](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)に逆シリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="99e6e-137">**Removing a field** - Values from a removed field are deserialized to a message's [unknown fields](https://developers.google.com/protocol-buffers/docs/proto3#unknowns).</span></span> <span data-ttu-id="99e6e-138">これは gRPC プロトコルの互換性に影響する変更ではありませんが、最新のコントラクトにアップグレードする場合は、クライアントを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="99e6e-138">This isn't a gRPC protocol breaking change, but the client needs to be updated if it upgrades to the latest contract.</span></span> <span data-ttu-id="99e6e-139">削除したフィールド番号は、今後誤って再利用されないことが重要です。</span><span class="sxs-lookup"><span data-stu-id="99e6e-139">It is important that a removed field number isn't accidentally reused in the future.</span></span> <span data-ttu-id="99e6e-140">この問題が発生しないようにする1つの方法は、Protobuf の[`reserved`](https://developers.google.com/protocol-buffers/docs/proto3#reserved)キーワードを使用して、削除されたフィールドの番号と名前をメッセージで指定することです。</span><span class="sxs-lookup"><span data-stu-id="99e6e-140">One way to make sure this doesn't happen is to specify deleted field numbers and names on the message using Protobuf's [`reserved`](https://developers.google.com/protocol-buffers/docs/proto3#reserved) keyword.</span></span>
- <span data-ttu-id="99e6e-141">**フィールド名の**名前の変更は、生成されたコードでのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="99e6e-141">**Renaming a field** - Field names are only used in generated code.</span></span> <span data-ttu-id="99e6e-142">フィールド番号は、ネットワーク上のフィールドを識別するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="99e6e-142">The field number is used to identify fields on the network.</span></span> <span data-ttu-id="99e6e-143">最新のコントラクトにアップグレードする場合は、クライアントを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="99e6e-143">The client will need to be updated if it upgrades to the latest contract.</span></span>
- <span data-ttu-id="99e6e-144">**メッセージ名の**変更はネットワーク上で送信されないので、これは grpc プロトコルの互換性に影響する変更ではありませんが、最新のコントラクトにアップグレードする場合は、クライアントを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="99e6e-144">**Renaming a message** - Message names are not sent on the network so this isn't a gRPC protocol breaking change, but the client will need to be updated if it upgrades to the latest contract.</span></span>
- <span data-ttu-id="99e6e-145">**Csharp_namespace**変更 `csharp_namespace` を変更すると、生成された .net 型の名前空間が変更されます。</span><span class="sxs-lookup"><span data-stu-id="99e6e-145">**Changing csharp_namespace** - Changing `csharp_namespace` will change the namespace of generated .NET types.</span></span> <span data-ttu-id="99e6e-146">これは gRPC プロトコルの互換性に影響する変更ではありませんが、最新のコントラクトにアップグレードする場合は、クライアントを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="99e6e-146">This isn't a gRPC protocol breaking change, but the client needs to be updated if it upgrades to the latest contract.</span></span>

### <a name="breaking-changes"></a><span data-ttu-id="99e6e-147">互換性に影響する変更</span><span class="sxs-lookup"><span data-stu-id="99e6e-147">Breaking changes</span></span>

<span data-ttu-id="99e6e-148">これらは、プロトコルとバイナリの互換性に影響します。</span><span class="sxs-lookup"><span data-stu-id="99e6e-148">These are protocol and binary breaking changes.</span></span>

- <span data-ttu-id="99e6e-149">**フィールドのデータ型の変更**-フィールドのデータ型を互換性のない[型](https://developers.google.com/protocol-buffers/docs/proto3#updating)に変更すると、メッセージの逆シリアル化時にエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="99e6e-149">**Changing a field data type** - Changing a field's data type to an [incompatible type](https://developers.google.com/protocol-buffers/docs/proto3#updating) will cause errors when deserializing the message.</span></span> <span data-ttu-id="99e6e-150">新しいデータ型に互換性がある場合でも、最新のコントラクトにアップグレードすると、新しい型をサポートするようにクライアントを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="99e6e-150">Even if the new data type is compatible, it is likely the client will need to be updated to support the new type if it upgrades to the latest contract.</span></span>
- <span data-ttu-id="99e6e-151">**フィールド番号の変更**-フィールド番号は、ネットワーク上のフィールドを識別するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="99e6e-151">**Changing a field number** - The field number is used to identify fields on the network.</span></span>
- <span data-ttu-id="99e6e-152">**Package、service、または method** -grpc の名前を変更するには、パッケージ名、サービス名、およびメソッド名を使用して URL を作成します。</span><span class="sxs-lookup"><span data-stu-id="99e6e-152">**Renaming a package, service or method** - gRPC uses the package name, service name and method name to build the URL.</span></span> <span data-ttu-id="99e6e-153">クライアントは、サーバーから未*実装*の状態を取得します。</span><span class="sxs-lookup"><span data-stu-id="99e6e-153">The client gets an *UNIMPLEMENTED* status from the server.</span></span>
- <span data-ttu-id="99e6e-154">**サービスまたはメソッドの削除**-クライアントは、削除されたメソッドを呼び出すときに、サーバーから未*実装*の状態を取得します。</span><span class="sxs-lookup"><span data-stu-id="99e6e-154">**Removing a service or method** - The client gets an *UNIMPLEMENTED* status from the server when calling the removed method.</span></span>

## <a name="version-number-services"></a><span data-ttu-id="99e6e-155">バージョン番号サービス</span><span class="sxs-lookup"><span data-stu-id="99e6e-155">Version number services</span></span>

<span data-ttu-id="99e6e-156">サービスは、古いクライアントとの下位互換性を維持する必要があります。</span><span class="sxs-lookup"><span data-stu-id="99e6e-156">Services should strive to remain backwards compatible with old clients.</span></span> <span data-ttu-id="99e6e-157">最終的にアプリに変更を加える場合は、重大な変更が必要になることがあります。</span><span class="sxs-lookup"><span data-stu-id="99e6e-157">Eventually changes to your app may require breaking changes.</span></span> <span data-ttu-id="99e6e-158">古いクライアントを中断し、サービスと共に強制的に更新することは、優れたユーザーエクスペリエンスではありません。</span><span class="sxs-lookup"><span data-stu-id="99e6e-158">Breaking old clients and forcing them to be updated along with your service isn't a good user experience.</span></span> <span data-ttu-id="99e6e-159">互換性に影響する変更を加える際には、サービスの複数のバージョンを公開する方法があります。</span><span class="sxs-lookup"><span data-stu-id="99e6e-159">A way to maintain backwards compatibility while making breaking changes is to publish multiple versions of a service.</span></span>

<span data-ttu-id="99e6e-160">gRPC では、省略可能な[`package`](https://developers.google.com/protocol-buffers/docs/proto3#packages)指定子がサポートされており、.net 名前空間と同様に機能します。</span><span class="sxs-lookup"><span data-stu-id="99e6e-160">gRPC supports an optional [`package`](https://developers.google.com/protocol-buffers/docs/proto3#packages) specifier, which functions much like a .NET namespace.</span></span> <span data-ttu-id="99e6e-161">実際、`option csharp_namespace` が*proto*ファイルで設定されていない場合、生成された .net 型の .net 名前空間として `package` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="99e6e-161">In fact the `package` will be used as the .NET namespace for generated .NET types if `option csharp_namespace` is not set in the *.proto* file.</span></span> <span data-ttu-id="99e6e-162">パッケージを使用して、サービスとそのメッセージのバージョン番号を指定できます。</span><span class="sxs-lookup"><span data-stu-id="99e6e-162">The package can be used to specify a version number for your service and its messages:</span></span>

[!code-protobuf[](versioning/sample/greet.v1.proto?highlight=3)]

<span data-ttu-id="99e6e-163">パッケージ名をサービス名と組み合わせて、サービスアドレスを識別します。</span><span class="sxs-lookup"><span data-stu-id="99e6e-163">The package name is combined with the service name to identify a service address.</span></span> <span data-ttu-id="99e6e-164">サービスアドレスを使用すると、複数のバージョンのサービスをサイドバイサイドでホストすることができます。</span><span class="sxs-lookup"><span data-stu-id="99e6e-164">A service address allows multiple versions of a service to be hosted side-by-side:</span></span>

* `greet.v1.Greeter`
* `greet.v2.Greeter`

<span data-ttu-id="99e6e-165">バージョン管理されたサービスの実装は*Startup.cs*に登録されます。</span><span class="sxs-lookup"><span data-stu-id="99e6e-165">Implementations of the versioned service are registered in *Startup.cs*:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    // Implements greet.v1.Greeter
    endpoints.MapGrpcService<GreeterServiceV1>();

    // Implements greet.v2.Greeter
    endpoints.MapGrpcService<GreeterServiceV2>();
});
```

<span data-ttu-id="99e6e-166">パッケージ名にバージョン番号を含めると、 *v1*バージョンを呼び出す古いクライアントを引き続きサポートしながら、互換性に影響する変更を含む*v2*バージョンのサービスを発行することができます。</span><span class="sxs-lookup"><span data-stu-id="99e6e-166">Including a version number in the package name gives you the opportunity to publish a *v2* version of your service with breaking changes, while continuing to support older clients who call the *v1* version.</span></span> <span data-ttu-id="99e6e-167">クライアントが*v2*サービスを使用するように更新したら、古いバージョンを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="99e6e-167">Once clients have updated to use the *v2* service you can choose to remove the old version.</span></span> <span data-ttu-id="99e6e-168">サービスの複数のバージョンを発行する予定の場合:</span><span class="sxs-lookup"><span data-stu-id="99e6e-168">When planning to publish multiple versions of a service:</span></span>

- <span data-ttu-id="99e6e-169">適切な場合は、重大な変更を避けてください。</span><span class="sxs-lookup"><span data-stu-id="99e6e-169">Avoid breaking changes if reasonable.</span></span>
- <span data-ttu-id="99e6e-170">互換性に影響する変更を行っていない限り、バージョン番号を更新しないでください。</span><span class="sxs-lookup"><span data-stu-id="99e6e-170">Don't update the version number unless making breaking changes.</span></span>
- <span data-ttu-id="99e6e-171">互換性に影響する変更を加える場合は、バージョン番号を更新してください。</span><span class="sxs-lookup"><span data-stu-id="99e6e-171">Do update the version number when you make breaking changes.</span></span>

<span data-ttu-id="99e6e-172">サービスの複数のバージョンを公開すると、サービスが複製されます。</span><span class="sxs-lookup"><span data-stu-id="99e6e-172">Publishing multiple versions of a service duplicates it.</span></span> <span data-ttu-id="99e6e-173">重複を減らすには、サービス実装から、古い実装と新しい実装で再利用できる集中管理された場所にビジネスロジックを移行することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="99e6e-173">To reduce duplication, consider moving business logic from the service implementations to a centralized location that can be reused by the old and new implementations:</span></span>

[!code-csharp[](versioning/sample/GreeterServiceV1.cs?highlight=10,19)]

<span data-ttu-id="99e6e-174">異なるパッケージ名を使用して生成されたサービスとメッセージは、 **.net の種類**によって異なります。</span><span class="sxs-lookup"><span data-stu-id="99e6e-174">Services and messages generated with different package names are **different .NET types**.</span></span> <span data-ttu-id="99e6e-175">ビジネスロジックを集中管理された場所に移動するには、メッセージを共通の種類にマッピングする必要があります。</span><span class="sxs-lookup"><span data-stu-id="99e6e-175">Moving business logic to a centralized location requires mapping messages to common types.</span></span>
