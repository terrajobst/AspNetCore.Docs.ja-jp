> [!WARNING]
> <span data-ttu-id="b3ff4-101">セキュリティ上の理由から、ページ モデルのプロパティに対して `GET` 要求データのバインドをオプトインする必要があります。</span><span class="sxs-lookup"><span data-stu-id="b3ff4-101">For security reasons, you must opt in to binding `GET` request data to page model properties.</span></span> <span data-ttu-id="b3ff4-102">プロパティにマップする前に、ユーザー入力を確認してください。</span><span class="sxs-lookup"><span data-stu-id="b3ff4-102">Verify user input before mapping it to properties.</span></span> <span data-ttu-id="b3ff4-103">`GET` バインドをオプトインするのは、クエリ文字列やルート値に依存するシナリオに対処する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="b3ff4-103">Opting into `GET` binding is useful when addressing scenarios that rely on query string or route values.</span></span>
>
> <span data-ttu-id="b3ff4-104">`GET` 要求のプロパティをバインドするには、[`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) 属性の `SupportsGet` プロパティを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="b3ff4-104">To bind a property on `GET` requests, set the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute's `SupportsGet` property to `true`:</span></span>
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> <span data-ttu-id="b3ff4-105">詳細については、[ASP.NET Core コミュニティ スタンドアップ: GET のディスカッションでのバインド (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s) に関する動画を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b3ff4-105">For more information, see [ASP.NET Core Community Standup: Bind on GET discussion (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).</span></span>
