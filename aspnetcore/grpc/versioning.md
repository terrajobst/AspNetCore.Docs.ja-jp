---
title: GRPC サービスのバージョン管理
author: jamesnk
description: GRPC サービスのバージョンを確認する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/09/2020
uid: grpc/versioning
ms.openlocfilehash: 9bd76009ba28a1abef25a98686afea6753d4a8f4
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828517"
---
# <a name="versioning-grpc-services"></a><span data-ttu-id="f6d43-103">GRPC サービスのバージョン管理</span><span class="sxs-lookup"><span data-stu-id="f6d43-103">Versioning gRPC services</span></span>

<span data-ttu-id="f6d43-104">[James のニュートン-キング](https://twitter.com/jamesnk)別</span><span class="sxs-lookup"><span data-stu-id="f6d43-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="f6d43-105">アプリに追加された新機能では、クライアントに提供される gRPC サービスを必要とすることがあります。これは、予期しない、または中断する場合があります。</span><span class="sxs-lookup"><span data-stu-id="f6d43-105">New features added to an app can require gRPC services provided to clients to change, sometimes in unexpected and breaking ways.</span></span> <span data-ttu-id="f6d43-106">GRPC サービスが変更した場合:</span><span class="sxs-lookup"><span data-stu-id="f6d43-106">When gRPC services change:</span></span>

* <span data-ttu-id="f6d43-107">変更がクライアントに与える影響について考慮する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6d43-107">Consideration should be given on how changes impact clients.</span></span>
* <span data-ttu-id="f6d43-108">変更をサポートするためのバージョン管理戦略を実装する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6d43-108">A versioning strategy to support changes should be implemented.</span></span>

## <a name="backwards-compatibility"></a><span data-ttu-id="f6d43-109">下位互換性</span><span class="sxs-lookup"><span data-stu-id="f6d43-109">Backwards compatibility</span></span>

<span data-ttu-id="f6d43-110">GRPC プロトコルは、時間の経過と共に変化するサービスをサポートするように設計されています。</span><span class="sxs-lookup"><span data-stu-id="f6d43-110">The gRPC protocol is designed to support services that change over time.</span></span> <span data-ttu-id="f6d43-111">一般に、gRPC サービスとメソッドへの追加は非互換性です。</span><span class="sxs-lookup"><span data-stu-id="f6d43-111">Generally, additions to gRPC services and methods are non-breaking.</span></span> <span data-ttu-id="f6d43-112">互換性に影響しない変更により、既存のクライアントは変更なしで作業を続行できます。</span><span class="sxs-lookup"><span data-stu-id="f6d43-112">Non-breaking changes allow existing clients to continue working without changes.</span></span> <span data-ttu-id="f6d43-113">GRPC サービスの変更または削除は重大な変更です。</span><span class="sxs-lookup"><span data-stu-id="f6d43-113">Changing or deleting gRPC services are breaking changes.</span></span> <span data-ttu-id="f6d43-114">GRPC サービスに重大な変更がある場合は、そのサービスを使用しているクライアントを更新して再デプロイする必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6d43-114">When gRPC services have breaking changes, clients using that service have to be updated and redeployed.</span></span>

<span data-ttu-id="f6d43-115">サービスに重大な変更を加えることには、次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="f6d43-115">Making non-breaking changes to a service has a number of benefits:</span></span>

* <span data-ttu-id="f6d43-116">既存のクライアントは引き続き実行されます。</span><span class="sxs-lookup"><span data-stu-id="f6d43-116">Existing clients continue to run.</span></span>
* <span data-ttu-id="f6d43-117">重大な変更をクライアントに通知し、それらを更新する作業を回避します。</span><span class="sxs-lookup"><span data-stu-id="f6d43-117">Avoids work involved with notifying clients of breaking changes, and updating them.</span></span>
* <span data-ttu-id="f6d43-118">ドキュメント化して管理する必要があるのは、1つのバージョンのサービスだけです。</span><span class="sxs-lookup"><span data-stu-id="f6d43-118">Only one version of the service needs to be documented and maintained.</span></span>

### <a name="non-breaking-changes"></a><span data-ttu-id="f6d43-119">非破壊的変更</span><span class="sxs-lookup"><span data-stu-id="f6d43-119">Non-breaking changes</span></span>

<span data-ttu-id="f6d43-120">これらの変更は、gRPC プロトコルレベルと .NET バイナリレベルでは非互換性があります。</span><span class="sxs-lookup"><span data-stu-id="f6d43-120">These changes are non-breaking at a gRPC protocol level and .NET binary level.</span></span>

* <span data-ttu-id="f6d43-121">**新しいサービスの追加**</span><span class="sxs-lookup"><span data-stu-id="f6d43-121">**Adding a new service**</span></span>
* <span data-ttu-id="f6d43-122">**サービスに新しいメソッドを追加する**</span><span class="sxs-lookup"><span data-stu-id="f6d43-122">**Adding a new method to a service**</span></span>
* <span data-ttu-id="f6d43-123">**要求メッセージへのフィールドの追加**: 要求メッセージに追加されたフィールドは、設定されていない場合、サーバーの[既定値](https://developers.google.com/protocol-buffers/docs/proto3#default)で逆シリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="f6d43-123">**Adding a field to a request message** - Fields added to a request message are deserialized with the [default value](https://developers.google.com/protocol-buffers/docs/proto3#default) on the server when not set.</span></span> <span data-ttu-id="f6d43-124">互換性に影響しない変更にするには、新しいフィールドが古いクライアントによって設定されていないと、サービスは成功する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6d43-124">To be a non-breaking change, the service must succeed when the new field isn't set by older clients.</span></span>
* <span data-ttu-id="f6d43-125">**応答メッセージにフィールドを追加**すると、応答メッセージに追加されたフィールドは、クライアント上のメッセージの[unknown fields](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)コレクションに逆シリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="f6d43-125">**Adding a field to a response message** - Fields added to a response message are deserialized into the message's [unknown fields](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) collection on the client.</span></span>
* <span data-ttu-id="f6d43-126">**列挙型の列挙型への値の追加**は、数値としてシリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="f6d43-126">**Adding a value to an enum** - Enums are serialized as a numeric value.</span></span> <span data-ttu-id="f6d43-127">新しい列挙値は、列挙型名を指定せずに、クライアント側で列挙値に逆シリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="f6d43-127">New enum values are deserialized on the client to the enum value without an enum name.</span></span> <span data-ttu-id="f6d43-128">互換性に影響しない変更にするには、新しい列挙値を受け取るときに古いクライアントが正しく動作する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6d43-128">To be a non-breaking change, older clients must run correctly when receiving the new enum value.</span></span>

### <a name="binary-breaking-changes"></a><span data-ttu-id="f6d43-129">バイナリの互換性に影響する変更点</span><span class="sxs-lookup"><span data-stu-id="f6d43-129">Binary breaking changes</span></span>

<span data-ttu-id="f6d43-130">次の変更は gRPC プロトコルレベルでは中断されませんが *、最新のプロトコルコントラクト*またはクライアント .net アセンブリにアップグレードする場合は、クライアントを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6d43-130">The following changes are non-breaking at a gRPC protocol level, but the client needs to be updated if it upgrades to the latest *.proto* contract or client .NET assembly.</span></span> <span data-ttu-id="f6d43-131">GRPC ライブラリを NuGet に発行する予定の場合、バイナリの互換性が重要です。</span><span class="sxs-lookup"><span data-stu-id="f6d43-131">Binary compatibility is important if you plan to publish a gRPC library to NuGet.</span></span>

* <span data-ttu-id="f6d43-132">削除されたフィールドからフィールド値を**削除**すると、メッセージの[不明なフィールド](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)に逆シリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="f6d43-132">**Removing a field** - Values from a removed field are deserialized to a message's [unknown fields](https://developers.google.com/protocol-buffers/docs/proto3#unknowns).</span></span> <span data-ttu-id="f6d43-133">これは gRPC プロトコルの互換性に影響する変更ではありませんが、最新のコントラクトにアップグレードする場合は、クライアントを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6d43-133">This isn't a gRPC protocol breaking change, but the client needs to be updated if it upgrades to the latest contract.</span></span> <span data-ttu-id="f6d43-134">削除したフィールド番号は、今後誤って再利用されないことが重要です。</span><span class="sxs-lookup"><span data-stu-id="f6d43-134">It's important that a removed field number isn't accidentally reused in the future.</span></span> <span data-ttu-id="f6d43-135">これが行われないようにするには、Protobuf の[予約済み](https://developers.google.com/protocol-buffers/docs/proto3#reserved)キーワードを使用して、削除されたフィールドの番号と名前をメッセージに指定します。</span><span class="sxs-lookup"><span data-stu-id="f6d43-135">To ensure this doesn't happen, specify deleted field numbers and names on the message using Protobuf's [reserved](https://developers.google.com/protocol-buffers/docs/proto3#reserved) keyword.</span></span>
* <span data-ttu-id="f6d43-136">**メッセージ**メッセージ名の名前を変更しても、通常はネットワーク上で送信されないので、これは grpc プロトコルの互換性に影響する変更ではありません。</span><span class="sxs-lookup"><span data-stu-id="f6d43-136">**Renaming a message** - Message names aren't typically sent on the network, so this isn't a gRPC protocol breaking change.</span></span> <span data-ttu-id="f6d43-137">最新のコントラクトにアップグレードする場合は、クライアントを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6d43-137">The client will need to be updated if it upgrades to the latest contract.</span></span> <span data-ttu-id="f6d43-138">メッセージ名がメッセージの種類を識別するために使用されている場合、メッセージ名がネットワーク上**で送信さ**れる1つの状況は、[任意](https://developers.google.com/protocol-buffers/docs/proto3#any)のフィールドです。</span><span class="sxs-lookup"><span data-stu-id="f6d43-138">One situation where message names **are** sent on the network is with [Any](https://developers.google.com/protocol-buffers/docs/proto3#any) fields, when the message name is used to identify the message type.</span></span>
* <span data-ttu-id="f6d43-139">**Csharp_namespace**変更 `csharp_namespace` を変更すると、生成された .net 型の名前空間が変更されます。</span><span class="sxs-lookup"><span data-stu-id="f6d43-139">**Changing csharp_namespace** - Changing `csharp_namespace` will change the namespace of generated .NET types.</span></span> <span data-ttu-id="f6d43-140">これは gRPC プロトコルの互換性に影響する変更ではありませんが、最新のコントラクトにアップグレードする場合は、クライアントを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6d43-140">This isn't a gRPC protocol breaking change, but the client needs to be updated if it upgrades to the latest contract.</span></span>

### <a name="protocol-breaking-changes"></a><span data-ttu-id="f6d43-141">プロトコルの互換性に影響する変更点</span><span class="sxs-lookup"><span data-stu-id="f6d43-141">Protocol breaking changes</span></span>

<span data-ttu-id="f6d43-142">次の項目は、プロトコルとバイナリの互換性に影響します。</span><span class="sxs-lookup"><span data-stu-id="f6d43-142">The following items are protocol and binary breaking changes:</span></span>

* <span data-ttu-id="f6d43-143">**フィールド**名の変更-Protobuf コンテンツでは、フィールド名は生成されたコードでのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="f6d43-143">**Renaming a field** - With Protobuf content, the field names are only used in generated code.</span></span> <span data-ttu-id="f6d43-144">フィールド番号は、ネットワーク上のフィールドを識別するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="f6d43-144">The field number is used to identify fields on the network.</span></span> <span data-ttu-id="f6d43-145">フィールド名の変更は、Protobuf のプロトコルの互換性に影響する変更点ではありません。</span><span class="sxs-lookup"><span data-stu-id="f6d43-145">Renaming a field isn't a protocol breaking change for Protobuf.</span></span> <span data-ttu-id="f6d43-146">ただし、サーバーで JSON コンテンツを使用している場合は、フィールドの名前を変更しても重大な変更になります。</span><span class="sxs-lookup"><span data-stu-id="f6d43-146">However, if a server is using JSON content then renaming a field is a breaking change.</span></span>
* <span data-ttu-id="f6d43-147">**フィールドのデータ型の変更**-フィールドのデータ型を互換性のない[型](https://developers.google.com/protocol-buffers/docs/proto3#updating)に変更すると、メッセージの逆シリアル化時にエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="f6d43-147">**Changing a field data type** - Changing a field's data type to an [incompatible type](https://developers.google.com/protocol-buffers/docs/proto3#updating) will cause errors when deserializing the message.</span></span> <span data-ttu-id="f6d43-148">新しいデータ型に互換性がある場合でも、最新のコントラクトにアップグレードすると、新しい型をサポートするようにクライアントを更新する必要が生じる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f6d43-148">Even if the new data type is compatible, it's likely the client needs to be updated to support the new type if it upgrades to the latest contract.</span></span>
* <span data-ttu-id="f6d43-149">**フィールド番号の変更**-Protobuf ペイロードでは、フィールド番号がネットワーク上のフィールドを識別するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="f6d43-149">**Changing a field number** - With Protobuf payloads, the field number is used to identify fields on the network.</span></span>
* <span data-ttu-id="f6d43-150">**Package、service、または method** -grpc の名前を変更するには、パッケージ名、サービス名、およびメソッド名を使用して URL を作成します。</span><span class="sxs-lookup"><span data-stu-id="f6d43-150">**Renaming a package, service or method** - gRPC uses the package name, service name, and method name to build the URL.</span></span> <span data-ttu-id="f6d43-151">クライアントは、サーバーから未*実装*の状態を取得します。</span><span class="sxs-lookup"><span data-stu-id="f6d43-151">The client gets an *UNIMPLEMENTED* status from the server.</span></span>
* <span data-ttu-id="f6d43-152">**サービスまたはメソッドの削除**-クライアントは、削除されたメソッドを呼び出すときに、サーバーから未*実装*の状態を取得します。</span><span class="sxs-lookup"><span data-stu-id="f6d43-152">**Removing a service or method** - The client gets an *UNIMPLEMENTED* status from the server when calling the removed method.</span></span>

### <a name="behavior-breaking-changes"></a><span data-ttu-id="f6d43-153">動作の互換性に影響する変更点</span><span class="sxs-lookup"><span data-stu-id="f6d43-153">Behavior breaking changes</span></span>

<span data-ttu-id="f6d43-154">互換性に影響しない変更を行う場合は、古いクライアントが新しいサービス動作を続行できるかどうかも検討する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6d43-154">When making non-breaking changes, you must also consider whether older clients can continue working with the new service behavior.</span></span> <span data-ttu-id="f6d43-155">たとえば、要求メッセージに新しいフィールドを追加すると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="f6d43-155">For example, adding a new field to a request message:</span></span>

* <span data-ttu-id="f6d43-156">は、プロトコルの互換性に影響する変更ではありません。</span><span class="sxs-lookup"><span data-stu-id="f6d43-156">Isn't a protocol breaking change.</span></span>
* <span data-ttu-id="f6d43-157">新しいフィールドが設定されていない場合にサーバー上でエラー状態を返すと、古いクライアントの重大な変更になります。</span><span class="sxs-lookup"><span data-stu-id="f6d43-157">Returning an error status on the server if the new field isn't set makes it a breaking change for old clients.</span></span>

<span data-ttu-id="f6d43-158">動作の互換性は、アプリ固有のコードによって決まります。</span><span class="sxs-lookup"><span data-stu-id="f6d43-158">Behavior compatibility is determined by your app-specific code.</span></span>

## <a name="version-number-services"></a><span data-ttu-id="f6d43-159">バージョン番号サービス</span><span class="sxs-lookup"><span data-stu-id="f6d43-159">Version number services</span></span>

<span data-ttu-id="f6d43-160">サービスは、古いクライアントとの下位互換性を維持する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6d43-160">Services should strive to remain backwards compatible with old clients.</span></span> <span data-ttu-id="f6d43-161">最終的にアプリに変更を加える場合は、重大な変更が必要になることがあります。</span><span class="sxs-lookup"><span data-stu-id="f6d43-161">Eventually changes to your app may require breaking changes.</span></span> <span data-ttu-id="f6d43-162">古いクライアントを中断し、サービスと共に強制的に更新することは、優れたユーザーエクスペリエンスではありません。</span><span class="sxs-lookup"><span data-stu-id="f6d43-162">Breaking old clients and forcing them to be updated along with your service isn't a good user experience.</span></span> <span data-ttu-id="f6d43-163">互換性に影響する変更を加える際には、サービスの複数のバージョンを公開する方法があります。</span><span class="sxs-lookup"><span data-stu-id="f6d43-163">A way to maintain backwards compatibility while making breaking changes is to publish multiple versions of a service.</span></span>

<span data-ttu-id="f6d43-164">gRPC では、オプションの[パッケージ](https://developers.google.com/protocol-buffers/docs/proto3#packages)指定子がサポートされています。これは、.net 名前空間と同様に機能します。</span><span class="sxs-lookup"><span data-stu-id="f6d43-164">gRPC supports an optional [package](https://developers.google.com/protocol-buffers/docs/proto3#packages) specifier, which functions much like a .NET namespace.</span></span> <span data-ttu-id="f6d43-165">実際、`option csharp_namespace` が*proto*ファイルで設定されていない場合、生成された .net 型の .net 名前空間として `package` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="f6d43-165">In fact, the `package` will be used as the .NET namespace for generated .NET types if `option csharp_namespace` is not set in the *.proto* file.</span></span> <span data-ttu-id="f6d43-166">パッケージを使用して、サービスとそのメッセージのバージョン番号を指定できます。</span><span class="sxs-lookup"><span data-stu-id="f6d43-166">The package can be used to specify a version number for your service and its messages:</span></span>

[!code-protobuf[](versioning/sample/greet.v1.proto?highlight=3)]

<span data-ttu-id="f6d43-167">パッケージ名をサービス名と組み合わせて、サービスアドレスを識別します。</span><span class="sxs-lookup"><span data-stu-id="f6d43-167">The package name is combined with the service name to identify a service address.</span></span> <span data-ttu-id="f6d43-168">サービスアドレスを使用すると、複数のバージョンのサービスをサイドバイサイドでホストすることができます。</span><span class="sxs-lookup"><span data-stu-id="f6d43-168">A service address allows multiple versions of a service to be hosted side-by-side:</span></span>

* `greet.v1.Greeter`
* `greet.v2.Greeter`

<span data-ttu-id="f6d43-169">バージョン管理されたサービスの実装は*Startup.cs*に登録されます。</span><span class="sxs-lookup"><span data-stu-id="f6d43-169">Implementations of the versioned service are registered in *Startup.cs*:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    // Implements greet.v1.Greeter
    endpoints.MapGrpcService<GreeterServiceV1>();

    // Implements greet.v2.Greeter
    endpoints.MapGrpcService<GreeterServiceV2>();
});
```

<span data-ttu-id="f6d43-170">パッケージ名にバージョン番号を含めると、 *v1*バージョンを呼び出す古いクライアントを引き続きサポートしながら、互換性に影響する変更を含む*v2*バージョンのサービスを発行することができます。</span><span class="sxs-lookup"><span data-stu-id="f6d43-170">Including a version number in the package name gives you the opportunity to publish a *v2* version of your service with breaking changes, while continuing to support older clients who call the *v1* version.</span></span> <span data-ttu-id="f6d43-171">クライアントが*v2*サービスを使用するように更新されたら、古いバージョンを削除することを選択できます。</span><span class="sxs-lookup"><span data-stu-id="f6d43-171">Once clients have updated to use the *v2* service, you can choose to remove the old version.</span></span> <span data-ttu-id="f6d43-172">サービスの複数のバージョンを発行する予定の場合:</span><span class="sxs-lookup"><span data-stu-id="f6d43-172">When planning to publish multiple versions of a service:</span></span>

* <span data-ttu-id="f6d43-173">適切な場合は、重大な変更を避けてください。</span><span class="sxs-lookup"><span data-stu-id="f6d43-173">Avoid breaking changes if reasonable.</span></span>
* <span data-ttu-id="f6d43-174">互換性に影響する変更を行っていない限り、バージョン番号を更新しないでください。</span><span class="sxs-lookup"><span data-stu-id="f6d43-174">Don't update the version number unless making breaking changes.</span></span>
* <span data-ttu-id="f6d43-175">互換性に影響する変更を加える場合は、バージョン番号を更新してください。</span><span class="sxs-lookup"><span data-stu-id="f6d43-175">Do update the version number when you make breaking changes.</span></span>

<span data-ttu-id="f6d43-176">サービスの複数のバージョンを公開すると、サービスが複製されます。</span><span class="sxs-lookup"><span data-stu-id="f6d43-176">Publishing multiple versions of a service duplicates it.</span></span> <span data-ttu-id="f6d43-177">重複を減らすには、サービス実装から、古い実装と新しい実装で再利用できる集中管理された場所にビジネスロジックを移行することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="f6d43-177">To reduce duplication, consider moving business logic from the service implementations to a centralized location that can be reused by the old and new implementations:</span></span>

[!code-csharp[](versioning/sample/GreeterServiceV1.cs?highlight=10,19)]

<span data-ttu-id="f6d43-178">異なるパッケージ名を使用して生成されたサービスとメッセージは、 **.net の種類**によって異なります。</span><span class="sxs-lookup"><span data-stu-id="f6d43-178">Services and messages generated with different package names are **different .NET types**.</span></span> <span data-ttu-id="f6d43-179">ビジネスロジックを集中管理された場所に移動するには、メッセージを共通の種類にマッピングする必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6d43-179">Moving business logic to a centralized location requires mapping messages to common types.</span></span>
