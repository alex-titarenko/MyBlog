---
id: 85463e53-d881-4390-a449-c2a5e79dbeeb
title: My experience with migration to ASP.NET Core 2.0
published: true
postedOn: 05/29/2018
image: .attachments/8b7bc415646a5c50af160a73a7198e5745dc8c86.png
category: aspnet
tags: dotnet, netcore, aspnet-core, mvc
description: I started developing my website in 2012, and it has gone through so many changes. The last major iteration was switching from classic .NET Framework to .NET Core 2.0 and ASP.NET Core as well. You can find many articles regarding ASP.NET Core migration like this. However, they don't cover all cases, that's why I decided to share my own experience.
---

<style type="text/css">
    img[alt="aspnetcore-migration"] { max-width: 600px }
</style>

I started developing my website in 2012, and it has gone through so many changes. The first version was written in PHP. Then I eventually moved to ASP.NET MVC 3.0. The UI changed a couple of times, and the major version already exceeded number 5.

The last major iteration was switching from classic .NET Framework to .NET Core 2.0 and ASP.NET Core as well.

With those changes, you can get many benefits. Some of them you will find on the official [introduction](https://docs.microsoft.com/en-us/aspnet/core/?view=aspnetcore-2.0#what-is-asp-net-core) page.
You can find many articles regarding ASP.NET Core migration like [this](https://docs.microsoft.com/en-us/aspnet/core/migration/?view=aspnetcore-2.0). However, they don't cover all cases, that's why I decided to share my own experience.

![aspnetcore-migration](.attachments/39f823deb46c561d7083f6f644fe885165e11f00.svg)

<!--more-->

# Project structure

No significant changes here, Controllers, Views, Models, Areas folders stay in its place. The main difference is **Content** folder which renamed to **wwwroot** and represents a home for static content (images, styles, javascript, etc.).

# Dependency injection

Another great feature is built-in dependency injection capabilities in ASP.NET Core. You don't need Ninject or Unity or any other DI frameworks. Open your Startup file and place all of your dependencies inside ```ConfigureServices(IServiceCollection services)``` method.
Use **AddTransient**, **AddSingleton** or other methods of interface IServiceCollection to register your services. 

# Client-side packages

Nuget isn't a client-side package provider for ASP.NET Core applications. If you want to add jQuery to your project, you should consider using different methods.

Visual Studio today supports Bower, NPM and recently [LibMan](https://blogs.msdn.microsoft.com/webdev/2018/04/17/library-manager-client-side-content-manager-for-web-apps/) (you can even use other solutions, which is not natively integrated with IDE).

* **Bower:** popular client-side package provider, but now it's [deprecated](https://bower.io/blog/2017/how-to-migrate-away-from-bower/). I would not recommend using it.
* **NPM:** good choice, but I see two main issues with it:
  * you can't restore your packages to _wwwroot_ folder (which is recommended for static content in ASP.NET Core); to overcome this issue you need to use additional tools like Gulp, Grunt to copy files to the right destination.
  * restored packages will include many other unnecessary files, which will increase the footprint of your application.
* **LibMan:** designed as a replacement for Bower, which allows you to easily acquire and manage static, client-side files for your web project from various sources, including CDNJS. It does not have issues like in NPM and sounds like a right decision for me.


# Web.config file

Good news, ASP.NET Core isn't tied with IIS anymore. This means that web.config is no longer required (if you decide to host your application on IIS, web.config will be auto-generated). Where should you move all data from config file? Well, consider to spread it across different places:

| Web.config section | New location
| ------------- |:-------------|
| appSettings | _appsettings.json_ file |
| system.web\pages\namespaces | _&#95;ViewImports.cshtml_ file |
| system.webServer\httpErrors | ```app.UseStatusCodePagesWithReExecute("/Error/Index", "?statusCode={0}")```, inside _Configure_ method of _Startup_ class |
| system.webServer\staticContent | ```app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = CreateContentTypeProvider() })```, inside _Configure_ method of _Startup_ class |
| Remaining data | most-likely will go to _Startup.cs_ file |

# Global.asax file
This long-living file is also going away with the new framework. Most data will move to _Startup.cs_ file.

# Custom model metadata provider

I used custom model metadata provider to override how MVC handles the generation of metadata for my models. If this applies to you, read the text below:

### Registration
**Before** in your _Global.asax_ _Application&#95;Start_ method you would do ```ModelMetadataProviders.Current = new YourMetadataProvider()```.

**Now**  in your _Startup_ class you will add ```services.AddSingleton<IModelMetadataProvider, YourMetadataProvider>()``` inside _ConfigureServices_ method.

### Implementation
**Before** you would use _DataAnnotationsModelMetadataProvider_ as a base class for your metadata provider, and you would override _CreateMetadata_  method.

**Now** you will use _DefaultModelMetadataProvider_ as a base class for your metadata provider, and you will override _CreateModelMetadata_ method.

# Client-side validation

If you develop _HtmlHelper_ extensions, you might face a situation when you need to get client-side validation attributes.

With classic ASP.NET you can easily do this with **GetUnobtrusiveValidationAttributes** method:

```csharp
tagBuilder.MergeAttributes<string, object>(htmlHelper.GetUnobtrusiveValidationAttributes(expression));
```

A lot of things have changed. Now to accomplish the same goal please use the following code:

```csharp
var modelExplorer = ExpressionMetadataProvider.FromStringExpression(expression, htmlHelper.ViewContext.ViewData, htmlHelper.MetadataProvider);
var validator = htmlHelper.ViewContext.HttpContext.RequestServices.GetService<ValidationHtmlAttributeProvider>();
validator?.AddAndTrackValidationAttributes(htmlHelper.ViewContext, modelExplorer, expression, tagBuilder.Attributes);
```

# Routing

Only registration has changed in routing.

**Before** you would do ```RouteConfig.RegisterRoutes(RouteTable.Routes)``` inside _Global.asax_ _Application&#95;Start_ method.

**Now** you will add ```app.UseMvc(RegisterRoutes)``` inside _Configure_ method of _Startup_ class.

# Areas

To register your application areas: 

**Before** you would do ```AreaRegistration.RegisterAllAreas()``` inside _Global.asax_ _Application&#95;Start_ method.

**Now** you will add ```routes.MapAreaRoute("your-route-name", "your-area-name", "your-area-template")``` to your routing configuration for each area.

# Small rocks

Here I would like to share some small changes which I noticed during migration.

| Before | After |
| ------------- |:-------------|
| _ActionResult_ | renamed to _IActionResult_ |
| _UrlHelper_ | renamed to _IUrlHelper_ |
| _MvcHtmlString_ | renamed to _IHtmlContent_|
| _HttpPostedFile_ | renamed to _IFormFile_|
| _Controller.HttpNotFound()_ | renamed to _Controller.NotFound()_|
| _HttpRequestBase_ | renamed to _HttpRequest_|
| _ActionDescriptor.ActionName_ | renamed to _ActionDescriptor.DisplayName_|
| _HttpPostedFile.InputStream_ | changed to _IFormFile.OpenReadStream()_|
| _Request[]_ | changed to _Request.Form[]_|
| _Request.Files.Get()_ | changed to _Request.Form.Files[]_|
| _Request.HttpMethod_ | renamed to _Request.Method_|
| _Request.Url_ | changed to _Request.GetDisplayUrl()_ (extension method) |
| _Request.UserHostAddress_ | changed to _Request.HttpContext.Connection.RemoteIpAddress_|
| _Request.UserLanguages_ | changed to _Request.Headers\[HeaderNames.AcceptLanguage\]_|
| _Request.UrlReferrer_ | changed to _Request.Headers\[HeaderNames.Referer\]_|
| _Response.Cookies.Set()_ | changed to _Response.Cookies.Append()_|
| _FormsAuthentication.SetAuthCookie()_ | changed to _HttpContext.SignInAsync()_|
| _FormsAuthentication.SignOut()_ | changed to _HttpContext.SignOutAsync()_|
| _AjaxOnlyAttribute_ | removed, consider your own implementation of this attribute|
| _HttpException_ | removed, consider to change your code to something else|
| _OutputCacheAttribute_ | removed, consider to change your code to something else|
| _ChildActionOnlyAttribute_ | removed, consider switching to [View Components](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/view-components?view=aspnetcore-2.0)|

# Summary

I spent roughly three weeks to migrate the whole project to .NET Core and ASP.NET Core. Sometimes not everything was obvious. After spending this time, I can say that the source code became much cleaner, unit-testable, and maintainable. The overall performance of the application has increased as well. Yes, the juice was worth the squeeze.

Hopefully you will find something helpful for your migration story. Goodluck!
