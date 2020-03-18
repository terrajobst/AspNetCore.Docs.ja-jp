## <a name="multiple-authentication-providers"></a><span data-ttu-id="1c74e-101">複数の認証プロバイダー</span><span class="sxs-lookup"><span data-stu-id="1c74e-101">Multiple authentication providers</span></span>

<span data-ttu-id="1c74e-102">アプリが複数のプロバイダーを必要とする場合、[AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication) の背後にあるプロバイダーの拡張メソッドをチェインします。</span><span class="sxs-lookup"><span data-stu-id="1c74e-102">When the app requires multiple providers, chain the provider extension methods behind [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication):</span></span>

```csharp
services.AddAuthentication()
    .AddMicrosoftAccount(microsoftOptions => { ... })
    .AddGoogle(googleOptions => { ... })
    .AddTwitter(twitterOptions => { ... })
    .AddFacebook(facebookOptions => { ... });
```
