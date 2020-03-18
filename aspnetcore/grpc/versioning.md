---
title: gRPC サービスのバージョン管理
author: jamesnk
description: gRPC サービスのバージョンを管理する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/09/2020
uid: grpc/versioning
ms.openlocfilehash: 9bd76009ba28a1abef25a98686afea6753d4a8f4
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78649358"
---
# <a name="versioning-grpc-services"></a><span data-ttu-id="240ef-103">gRPC サービスのバージョン管理</span><span class="sxs-lookup"><span data-stu-id="240ef-103">Versioning gRPC services</span></span>

<span data-ttu-id="240ef-104">作成者: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="240ef-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="240ef-105">アプリに追加された新機能では、クライアントに提供されている gRPC サービスを、たまに予期しない方法または破壊的な方法で変更することが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="240ef-105">New features added to an app can require gRPC services provided to clients to change, sometimes in unexpected and breaking ways.</span></span> <span data-ttu-id="240ef-106">gRPC サービスが変更される場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="240ef-106">When gRPC services change:</span></span>

* <span data-ttu-id="240ef-107">変更がクライアントに与える影響について考慮する必要があります。</span><span class="sxs-lookup"><span data-stu-id="240ef-107">Consideration should be given on how changes impact clients.</span></span>
* <span data-ttu-id="240ef-108">変更をサポートするためのバージョン管理戦略を実装する必要があります。</span><span class="sxs-lookup"><span data-stu-id="240ef-108">A versioning strategy to support changes should be implemented.</span></span>

## <a name="backwards-compatibility"></a><span data-ttu-id="240ef-109">下位互換性</span><span class="sxs-lookup"><span data-stu-id="240ef-109">Backwards compatibility</span></span>

<span data-ttu-id="240ef-110">gRPC プロトコルは、時間の経過と共に変更されるサービスに対応するように設計されています。</span><span class="sxs-lookup"><span data-stu-id="240ef-110">The gRPC protocol is designed to support services that change over time.</span></span> <span data-ttu-id="240ef-111">通常、gRPC サービスとメソッドへの追加は非破壊的です。</span><span class="sxs-lookup"><span data-stu-id="240ef-111">Generally, additions to gRPC services and methods are non-breaking.</span></span> <span data-ttu-id="240ef-112">非破壊的変更では、既存のクライアントは変更なしで動作し続けることが可能です。</span><span class="sxs-lookup"><span data-stu-id="240ef-112">Non-breaking changes allow existing clients to continue working without changes.</span></span> <span data-ttu-id="240ef-113">gRPC サービスの変更または削除は破壊的変更です。</span><span class="sxs-lookup"><span data-stu-id="240ef-113">Changing or deleting gRPC services are breaking changes.</span></span> <span data-ttu-id="240ef-114">gRPC サービスに破壊的変更が含まれている場合は、そのサービスを使用しているクライアントを更新して再デプロイする必要があります。</span><span class="sxs-lookup"><span data-stu-id="240ef-114">When gRPC services have breaking changes, clients using that service have to be updated and redeployed.</span></span>

<span data-ttu-id="240ef-115">非破壊的変更をサービスに加えることには、いくつかの利点があります。</span><span class="sxs-lookup"><span data-stu-id="240ef-115">Making non-breaking changes to a service has a number of benefits:</span></span>

* <span data-ttu-id="240ef-116">既存のクライアントは引き続き実行されます。</span><span class="sxs-lookup"><span data-stu-id="240ef-116">Existing clients continue to run.</span></span>
* <span data-ttu-id="240ef-117">クライアントへの破壊的変更の通知とクライアントの更新にかかわる作業が回避されます。</span><span class="sxs-lookup"><span data-stu-id="240ef-117">Avoids work involved with notifying clients of breaking changes, and updating them.</span></span>
* <span data-ttu-id="240ef-118">文書化して管理する必要があるサービスのバージョンは 1 つだけです。</span><span class="sxs-lookup"><span data-stu-id="240ef-118">Only one version of the service needs to be documented and maintained.</span></span>

### <a name="non-breaking-changes"></a><span data-ttu-id="240ef-119">非破壊的変更</span><span class="sxs-lookup"><span data-stu-id="240ef-119">Non-breaking changes</span></span>

<span data-ttu-id="240ef-120">次の変更は、gRPC プロトコル レベルと .NET バイナリ レベルで非破壊的です。</span><span class="sxs-lookup"><span data-stu-id="240ef-120">These changes are non-breaking at a gRPC protocol level and .NET binary level.</span></span>

* <span data-ttu-id="240ef-121">**新しいサービスの追加**</span><span class="sxs-lookup"><span data-stu-id="240ef-121">**Adding a new service**</span></span>
* <span data-ttu-id="240ef-122">**サービスへの新しいメソッドの追加**</span><span class="sxs-lookup"><span data-stu-id="240ef-122">**Adding a new method to a service**</span></span>
* <span data-ttu-id="240ef-123">**要求メッセージへのフィールドの追加** - 要求メッセージに追加されたフィールドは、設定されていない場合、サーバーの[既定値](https://developers.google.com/protocol-buffers/docs/proto3#default)を使用して逆シリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="240ef-123">**Adding a field to a request message** - Fields added to a request message are deserialized with the [default value](https://developers.google.com/protocol-buffers/docs/proto3#default) on the server when not set.</span></span> <span data-ttu-id="240ef-124">非破壊的変更にするには、新しいフィールドが古いクライアントによって設定されていないときにサービスが成功する必要があります。</span><span class="sxs-lookup"><span data-stu-id="240ef-124">To be a non-breaking change, the service must succeed when the new field isn't set by older clients.</span></span>
* <span data-ttu-id="240ef-125">**応答メッセージへのフィールドの追加** - 応答メッセージに追加されたフィールドは、クライアントでメッセージの[不明なフィールド](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)のコレクションに逆シリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="240ef-125">**Adding a field to a response message** - Fields added to a response message are deserialized into the message's [unknown fields](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) collection on the client.</span></span>
* <span data-ttu-id="240ef-126">**列挙型への値の追加** - 列挙型は数値としてシリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="240ef-126">**Adding a value to an enum** - Enums are serialized as a numeric value.</span></span> <span data-ttu-id="240ef-127">新しい列挙値は、クライアントで、列挙型名を指定せずに列挙値に逆シリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="240ef-127">New enum values are deserialized on the client to the enum value without an enum name.</span></span> <span data-ttu-id="240ef-128">非破壊的変更にするには、新しい列挙値を受け取るときに古いクライアントが正しく動作する必要があります。</span><span class="sxs-lookup"><span data-stu-id="240ef-128">To be a non-breaking change, older clients must run correctly when receiving the new enum value.</span></span>

### <a name="binary-breaking-changes"></a><span data-ttu-id="240ef-129">バイナリの破壊的変更</span><span class="sxs-lookup"><span data-stu-id="240ef-129">Binary breaking changes</span></span>

<span data-ttu-id="240ef-130">次の変更は、gRPC プロトコル レベルでは非破壊的ですが、最新の *.proto* コントラクトまたはクライアントの .NET アセンブリにアップグレードする場合は、クライアントを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="240ef-130">The following changes are non-breaking at a gRPC protocol level, but the client needs to be updated if it upgrades to the latest *.proto* contract or client .NET assembly.</span></span> <span data-ttu-id="240ef-131">gRPC ライブラリを NuGet に公開する予定の場合、バイナリの互換性は重要です。</span><span class="sxs-lookup"><span data-stu-id="240ef-131">Binary compatibility is important if you plan to publish a gRPC library to NuGet.</span></span>

* <span data-ttu-id="240ef-132">**フィールドの削除** - 削除されたフィールド の値は、メッセージの[不明なフィールド](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)に逆シリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="240ef-132">**Removing a field** - Values from a removed field are deserialized to a message's [unknown fields](https://developers.google.com/protocol-buffers/docs/proto3#unknowns).</span></span> <span data-ttu-id="240ef-133">これは gRPC プロトコルの破壊的変更ではありませんが、最新のコントラクトにアップグレードする場合は、クライアントを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="240ef-133">This isn't a gRPC protocol breaking change, but the client needs to be updated if it upgrades to the latest contract.</span></span> <span data-ttu-id="240ef-134">削除されたフィールド番号が今後誤って再利用されないことが重要です。</span><span class="sxs-lookup"><span data-stu-id="240ef-134">It's important that a removed field number isn't accidentally reused in the future.</span></span> <span data-ttu-id="240ef-135">これが行われないようにするには、Protobuf の[予約](https://developers.google.com/protocol-buffers/docs/proto3#reserved)キーワードを使用して、削除されたフィールドの番号と名前をメッセージ上に指定します。</span><span class="sxs-lookup"><span data-stu-id="240ef-135">To ensure this doesn't happen, specify deleted field numbers and names on the message using Protobuf's [reserved](https://developers.google.com/protocol-buffers/docs/proto3#reserved) keyword.</span></span>
* <span data-ttu-id="240ef-136">**メッセージ名の変更** - 通常、ネットワーク上ではメッセージ名は送信されないので、これは gRPC プロトコルの破壊的変更ではありません。</span><span class="sxs-lookup"><span data-stu-id="240ef-136">**Renaming a message** - Message names aren't typically sent on the network, so this isn't a gRPC protocol breaking change.</span></span> <span data-ttu-id="240ef-137">最新のコントラクトにアップグレードする場合は、クライアントを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="240ef-137">The client will need to be updated if it upgrades to the latest contract.</span></span> <span data-ttu-id="240ef-138">ネットワーク上でメッセージ名が送信**される**状況の 1 つは、[Any](https://developers.google.com/protocol-buffers/docs/proto3#any) フィールドを使用する (メッセージ型の識別にメッセージ名を使用する) 場合です。</span><span class="sxs-lookup"><span data-stu-id="240ef-138">One situation where message names **are** sent on the network is with [Any](https://developers.google.com/protocol-buffers/docs/proto3#any) fields, when the message name is used to identify the message type.</span></span>
* <span data-ttu-id="240ef-139">**csharp_namespace の変更** - `csharp_namespace` を変更すると、生成された .NET 型の名前空間が変更されます。</span><span class="sxs-lookup"><span data-stu-id="240ef-139">**Changing csharp_namespace** - Changing `csharp_namespace` will change the namespace of generated .NET types.</span></span> <span data-ttu-id="240ef-140">これは gRPC プロトコルの破壊的変更ではありませんが、最新のコントラクトにアップグレードする場合は、クライアントを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="240ef-140">This isn't a gRPC protocol breaking change, but the client needs to be updated if it upgrades to the latest contract.</span></span>

### <a name="protocol-breaking-changes"></a><span data-ttu-id="240ef-141">プロトコルの破壊的変更</span><span class="sxs-lookup"><span data-stu-id="240ef-141">Protocol breaking changes</span></span>

<span data-ttu-id="240ef-142">次の項目は、プロトコルとバイナリの破壊的変更です。</span><span class="sxs-lookup"><span data-stu-id="240ef-142">The following items are protocol and binary breaking changes:</span></span>

* <span data-ttu-id="240ef-143">**フィールド名の変更** - Protobuf コンテンツでは、フィールド名は生成されたコードでのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="240ef-143">**Renaming a field** - With Protobuf content, the field names are only used in generated code.</span></span> <span data-ttu-id="240ef-144">ネットワーク上でのフィールドの識別には、フィールド番号が使用されます。</span><span class="sxs-lookup"><span data-stu-id="240ef-144">The field number is used to identify fields on the network.</span></span> <span data-ttu-id="240ef-145">フィールド名の変更は、Protobuf ではプロトコルの破壊的変更ではありません。</span><span class="sxs-lookup"><span data-stu-id="240ef-145">Renaming a field isn't a protocol breaking change for Protobuf.</span></span> <span data-ttu-id="240ef-146">ただし、サーバーで JSON コンテンツを使用している場合は、フィールド名の変更は破壊的変更です。</span><span class="sxs-lookup"><span data-stu-id="240ef-146">However, if a server is using JSON content then renaming a field is a breaking change.</span></span>
* <span data-ttu-id="240ef-147">**フィールドのデータ型の変更** - フィールドのデータ型を[互換性のない型](https://developers.google.com/protocol-buffers/docs/proto3#updating)に変更すると、メッセージを逆シリアル化しているときにエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="240ef-147">**Changing a field data type** - Changing a field's data type to an [incompatible type](https://developers.google.com/protocol-buffers/docs/proto3#updating) will cause errors when deserializing the message.</span></span> <span data-ttu-id="240ef-148">新しいデータ型に互換性がある場合でも、最新のコントラクトにアップグレードすると、新しい型をサポートするためにクライアントを更新する必要が生じる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="240ef-148">Even if the new data type is compatible, it's likely the client needs to be updated to support the new type if it upgrades to the latest contract.</span></span>
* <span data-ttu-id="240ef-149">**フィールド番号の変更** - Protobuf ペイロードでは、フィールド番号がネットワーク上でのフィールドの識別に使用されます。</span><span class="sxs-lookup"><span data-stu-id="240ef-149">**Changing a field number** - With Protobuf payloads, the field number is used to identify fields on the network.</span></span>
* <span data-ttu-id="240ef-150">**パッケージ名、サービス名、またはメソッド名の変更** - gRPC では、パッケージ名、サービス名、およびメソッド名を使用して URL を構築します。</span><span class="sxs-lookup"><span data-stu-id="240ef-150">**Renaming a package, service or method** - gRPC uses the package name, service name, and method name to build the URL.</span></span> <span data-ttu-id="240ef-151">クライアントでは、サーバーから *UNIMPLEMENTED* 状態を取得します。</span><span class="sxs-lookup"><span data-stu-id="240ef-151">The client gets an *UNIMPLEMENTED* status from the server.</span></span>
* <span data-ttu-id="240ef-152">**サービスまたはメソッドの削除** - クライアントでは、削除されたメソッドを呼び出すと、サーバーから *UNIMPLEMENTED* 状態を取得します。</span><span class="sxs-lookup"><span data-stu-id="240ef-152">**Removing a service or method** - The client gets an *UNIMPLEMENTED* status from the server when calling the removed method.</span></span>

### <a name="behavior-breaking-changes"></a><span data-ttu-id="240ef-153">動作の破壊的変更</span><span class="sxs-lookup"><span data-stu-id="240ef-153">Behavior breaking changes</span></span>

<span data-ttu-id="240ef-154">非破壊的変更を行う場合は、古いクライアントが新しいサービスの動作で続行できるかどうかも検討する必要があります。</span><span class="sxs-lookup"><span data-stu-id="240ef-154">When making non-breaking changes, you must also consider whether older clients can continue working with the new service behavior.</span></span> <span data-ttu-id="240ef-155">たとえば、要求メッセージに新しいフィールドを追加すると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="240ef-155">For example, adding a new field to a request message:</span></span>

* <span data-ttu-id="240ef-156">プロトコルの破壊的変更ではありません。</span><span class="sxs-lookup"><span data-stu-id="240ef-156">Isn't a protocol breaking change.</span></span>
* <span data-ttu-id="240ef-157">新しいフィールドが設定されていない場合にサーバーでエラー状態を返すと、古いクライアントでは破壊的変更になります。</span><span class="sxs-lookup"><span data-stu-id="240ef-157">Returning an error status on the server if the new field isn't set makes it a breaking change for old clients.</span></span>

<span data-ttu-id="240ef-158">動作の互換性は、アプリ固有のコードによって決まります。</span><span class="sxs-lookup"><span data-stu-id="240ef-158">Behavior compatibility is determined by your app-specific code.</span></span>

## <a name="version-number-services"></a><span data-ttu-id="240ef-159">バージョン番号サービス</span><span class="sxs-lookup"><span data-stu-id="240ef-159">Version number services</span></span>

<span data-ttu-id="240ef-160">サービスは、古いクライアントとの下位互換性を維持する必要があります。</span><span class="sxs-lookup"><span data-stu-id="240ef-160">Services should strive to remain backwards compatible with old clients.</span></span> <span data-ttu-id="240ef-161">最終的に、アプリに変更を加える場合は、破壊的変更が必要になることがあります。</span><span class="sxs-lookup"><span data-stu-id="240ef-161">Eventually changes to your app may require breaking changes.</span></span> <span data-ttu-id="240ef-162">古いクライアントを中断し、サービスと共に強制的に更新することは、優れたユーザー エクスペリエンスではありません。</span><span class="sxs-lookup"><span data-stu-id="240ef-162">Breaking old clients and forcing them to be updated along with your service isn't a good user experience.</span></span> <span data-ttu-id="240ef-163">破壊的変更を加えながら下位互換性を確保する場合、サービスの複数のバージョンを発行する方法があります。</span><span class="sxs-lookup"><span data-stu-id="240ef-163">A way to maintain backwards compatibility while making breaking changes is to publish multiple versions of a service.</span></span>

<span data-ttu-id="240ef-164">gRPC では、.NET 名前空間と同様に機能する、省略可能な [package](https://developers.google.com/protocol-buffers/docs/proto3#packages) 指定子がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="240ef-164">gRPC supports an optional [package](https://developers.google.com/protocol-buffers/docs/proto3#packages) specifier, which functions much like a .NET namespace.</span></span> <span data-ttu-id="240ef-165">実際には、 *.proto* ファイルで `option csharp_namespace` が設定されていない場合に、生成された .NET 型の .NET 名前空間として `package` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="240ef-165">In fact, the `package` will be used as the .NET namespace for generated .NET types if `option csharp_namespace` is not set in the *.proto* file.</span></span> <span data-ttu-id="240ef-166">package を使用すると、サービスとそのメッセージのバージョン番号を指定できます。</span><span class="sxs-lookup"><span data-stu-id="240ef-166">The package can be used to specify a version number for your service and its messages:</span></span>

[!code-protobuf[](versioning/sample/greet.v1.proto?highlight=3)]

<span data-ttu-id="240ef-167">パッケージ名をサービス名と組み合わせることで、サービス アドレスを識別します。</span><span class="sxs-lookup"><span data-stu-id="240ef-167">The package name is combined with the service name to identify a service address.</span></span> <span data-ttu-id="240ef-168">サービス アドレスによって、サービスの複数のバージョンを並行してホストすることが可能になります。</span><span class="sxs-lookup"><span data-stu-id="240ef-168">A service address allows multiple versions of a service to be hosted side-by-side:</span></span>

* `greet.v1.Greeter`
* `greet.v2.Greeter`

<span data-ttu-id="240ef-169">バージョン管理されたサービスの実装は、*Startup.cs* に登録されます。</span><span class="sxs-lookup"><span data-stu-id="240ef-169">Implementations of the versioned service are registered in *Startup.cs*:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    // Implements greet.v1.Greeter
    endpoints.MapGrpcService<GreeterServiceV1>();

    // Implements greet.v2.Greeter
    endpoints.MapGrpcService<GreeterServiceV2>();
});
```

<span data-ttu-id="240ef-170">パッケージ名にバージョン番号を含めると、*v1* バージョンを呼び出す古いクライアントを引き続きサポートしながら、破壊的変更を含む *v2* バージョンのサービスを発行できるようになります。</span><span class="sxs-lookup"><span data-stu-id="240ef-170">Including a version number in the package name gives you the opportunity to publish a *v2* version of your service with breaking changes, while continuing to support older clients who call the *v1* version.</span></span> <span data-ttu-id="240ef-171">*v2* サービスを使用するようにクライアントが更新されたら、古いバージョンを削除することを選択できます。</span><span class="sxs-lookup"><span data-stu-id="240ef-171">Once clients have updated to use the *v2* service, you can choose to remove the old version.</span></span> <span data-ttu-id="240ef-172">サービスの複数のバージョンを発行する予定の場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="240ef-172">When planning to publish multiple versions of a service:</span></span>

* <span data-ttu-id="240ef-173">不適切でなければ、破壊的変更は行わないでください。</span><span class="sxs-lookup"><span data-stu-id="240ef-173">Avoid breaking changes if reasonable.</span></span>
* <span data-ttu-id="240ef-174">破壊的変更を行わない限り、バージョン番号を更新しないでください。</span><span class="sxs-lookup"><span data-stu-id="240ef-174">Don't update the version number unless making breaking changes.</span></span>
* <span data-ttu-id="240ef-175">破壊的変更を行う場合は、バージョン番号を更新してください。</span><span class="sxs-lookup"><span data-stu-id="240ef-175">Do update the version number when you make breaking changes.</span></span>

<span data-ttu-id="240ef-176">サービスの複数のバージョンを発行すると、サービスが複製されます。</span><span class="sxs-lookup"><span data-stu-id="240ef-176">Publishing multiple versions of a service duplicates it.</span></span> <span data-ttu-id="240ef-177">重複を減らすために、サービスの実装のビジネス ロジックを、古い実装と新しい実装で再利用できる集中管理された場所に移行することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="240ef-177">To reduce duplication, consider moving business logic from the service implementations to a centralized location that can be reused by the old and new implementations:</span></span>

[!code-csharp[](versioning/sample/GreeterServiceV1.cs?highlight=10,19)]

<span data-ttu-id="240ef-178">異なるパッケージ名で生成されたサービスとメッセージの **.NET 型はさまざま**です。</span><span class="sxs-lookup"><span data-stu-id="240ef-178">Services and messages generated with different package names are **different .NET types**.</span></span> <span data-ttu-id="240ef-179">ビジネス ロジックを集中管理された場所に移行するには、メッセージを共通の型にマッピングする必要があります。</span><span class="sxs-lookup"><span data-stu-id="240ef-179">Moving business logic to a centralized location requires mapping messages to common types.</span></span>
