# Version Specific Upgrades

_This document covers specific upgrade steps if a version requires them. Most versions do not require specific upgrade steps. In most cases, you will be able to upgrade directly from your current version to the latest version._

Follow the steps in the [general upgrade guide](../), then these additional instructions for the specific versions.

## Breaking changes

{% content-ref url="umbraco10-breaking-changes.md" %}
[umbraco10-breaking-changes.md](umbraco10-breaking-changes.md)
{% endcontent-ref %}

{% content-ref url="umbraco11-breaking-changes.md" %}
[umbraco11-breaking-changes.md](umbraco11-breaking-changes.md)
{% endcontent-ref %}

## Find you upgrade path

Are you looking to upgrade an Umbraco Cloud project from 9 to 10? Follow the guide made for [Upgrading your project from Umbraco 9 to 10](../../../../../umbraco-cloud/upgrades/major-upgrades.md) instead, as it requires a few steps specific to Umbraco Cloud.

<details>

<summary>10.latest to version 11</summary>

It might be necessary to delete all of the `bin` and `obj` directories in each of the projects of your solution. It has been observed that Visual Studio's "Clean Solution" option is sometimes not enough.

</details>

<details>

<summary>9.latest to 10</summary>

**Important**: .NET version 6.0.5 is the minimum required version for Umbraco 10 to be able to run. You can check with `dotnet --list-sdks` what your latest installed Software Development Kit (SDK) version is. SDK version 6.0.300 is the one that includes .NET 6.0.5. At the time of writing, .NET 6.0.6 is out with an SDK version of 6.0.301.

Watch the ['Upgrading from Umbraco 9 to Umbraco 10 video tutorial'](https://www.youtube.com/watch?v=075H_ekJBKI&ab_channel=UmbracoLearningBase) for a complete walk-through of all the steps.

The upgrade path between Umbraco 9 and Umbraco 10 can be done directly by upgrading your project using NuGet. You will need to ensure the packages you are using are available in Umbraco 10.

### SQL CE is no longer a supported database engine

There is no official migration path from SQL CE to another database engine.

The following options may suit your needs:

* Follow a community guide to migrate from a SQL CE database to SQL Server, like the [article by Jan Reilink](https://www.saotn.org/convert-sqlce-database-to-sql-server/)
* Setup a new database for v10 and use [uSync](https://jumoo.co.uk/usync/) to transfer document types and content across.
* Setup a new database for v10 and use a premium tool such as [redgate SQL Data Compare](https://www.red-gate.com/products/sql-development/sql-data-compare/) to copy database contents across.
* Setup a new database for v10 and use a premium tool such as [Umbraco Deploy](https://umbraco.com/products/umbraco-deploy) to transfer document types and content across.

### Steps to upgrade using Visual Studio

It's recommended that you upgrade the site offline, and test the upgrade fully before deploying it to the production environment.

1. Stop your site in IIS to prevent any changes being made to the database or filesystem while you are upgrading.
2. Open your Umbraco 9 project in Visual Studio.
3. Right-click on the project name in the Solution Explorer and select **Properties**.
4. Select **.NET 6.0** from the **Target Framework** drop-down.
5. Go to **Tools** > **NuGet Package Manager** > **Manage NuGet Packages for Solution...**
6. Go to the **Installed** tab in the NuGet Package manager.
7. Choose **Umbraco.Cms**.
8. Select **10.0.0** from the **Version** drop-down and click **Install** to upgrade your project to version 10.
9. Update `Program.cs` to the following:

```csharp
public class Program
{
    public static void Main(string[] args)
        => CreateHostBuilder(args)
            .Build()
            .Run();

    // The calls to `ConfigureUmbracoDefaults` and `webBuilder.UseStaticWebAssets()` are new.
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureUmbracoDefaults()
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

10. Remove the following files and folders:
    * `/wwwroot/umbraco`
    * `/umbraco/PartialViewMacros`
    * `/umbraco/UmbracoBackOffice`
    * `/umbraco/UmbracoInstall`
    * `/umbraco/UmbracoWebsite`
    * `/umbraco/config/lang`
    * `/umbraco/config/appsettings-schema.json`
11. If using Umbraco Forms, update your files and folders according to the [Upgrading - version specific](../../../../../umbraco-forms/installation/version-specific.md) for version 10 article.
12. Restart your site in IIS, build and run your project to finish the installation of Umbraco 10.

To re-enable the appsettings IntelliSense, you must update your schema reference in the `appsettings.json` file and any other `appsettings.{Environment}.json` files from:

```json
"$schema": "./umbraco/config/appsettings-schema.json",
```

To:

```json
"$schema": "./appsettings-schema.json",
```

To upgrade to Umbraco 10, your database needs to be at least on Umbraco 8.18.

### Upgrade of any publicly hosted environment

When the upgrade is completed and tested, and prior to deploying to any publicly accessible environment, you should consider the following:

1. Ensure you have backups for both the database and the file system.
2. Stop the site so it is not accessible during the upgrade process.
3. Delete the relevant folders from the filesystem prior to deploying:
   * `/wwwroot/umbraco`
   * `/umbraco/PartialViewMacros`
   * `/umbraco/UmbracoBackOffice`
   * `/umbraco/UmbracoInstall`
   * `/umbraco/UmbracoWebsite`
   * `/umbraco/config/lang`
   * `/umbraco/config/appsettings-schema.json`
4. Deploy the site how you normally would to your public facing environment.
5. Start the site. At this point it will launch and upgrade the database, after which the site should become accessible and your upgrade is complete.
6. Check the logs for any errors which may have occurred during the upgrade process.

</details>

<details>

<summary>8.latest to 9</summary>

There is no direct upgrade path from Umbraco 8 to Umbraco 9. It is however possible to migrate from Umbraco 8 sites to Umbraco 9 sites.

You can reuse your content restoring your Umbraco 8 database into a new database used for a Umbraco 9 site.

You will need to ensure packages you are using is available in Umbraco 9, and you will need to reimplement your custom code and templates.

The direct upgrade path is not possible due to the fact that the codebase has been fundamentally updated in Umbraco 9. The underlying web framework has been updated from ASP.NET to ASP.NET Core.

It is not possible to take this step while maintaining full compatibility with Umbraco 8.

</details>

<details>

<summary>8.0.0 to 8.1.0</summary>

There are a few breaking changes from 8.0.x to 8.1.0. Make sure to check the [full list](https://github.com/umbraco/Umbraco-CMS/issues?q=is%3Aissue+label%3Arelease%2F8.1.0+is%3Aclosed+label%3Acategory%2Fbreaking).

### IPublishedContent breaking changes in 8.1.0

Due to the [changes in `IPublishedContent`](https://github.com/umbraco/Umbraco-CMS/issues/5170) there are few steps you will need to take to make sure that your site works.

The `IPublishedContent` interface is central to Umbraco, as it represents published content and media items at rendering layer level. This could be in controllers or views. In other words, it is the interface that is used everywhere when building sites.

The introduction of multilingual support in version 8 required changes to the interface. For instance, a property value could be obtained with `GetPropertyValue(alias)`in version 7. Version 8 requires a new parameter for culture, and the call thus became `Value(alias, culture)`.

In the excitement of the version 8 release, we assumed that `IPublishedContent` was "done". By our tests, everything was looking good. However, feedback from early testers showed that the interface was in some places odd or inconsistent, or had issues.

Fixing the bugs is a requirement. Some of the required bug fixes could not be achieved without introducing some breaking changes.

At that point, we had decided to give `IPublishedContent` some love. We fixed the bugs and made it clean, friendly, discoverable and predictable for the entire life of version 8.

Breaking changes to such a central interface is not something we take lightly. Even though they do not impact the "concepts" nor require heavy refactoring, they may demand an amount of small fixes here and there.

The general idea underlying these changes is that:

* The proper way to retrieve "something" from an `IPublishedContent` instance is always through a method, for example: `Children()`. And, when that method can be multilingual, the method accepts a `culture` parameter, which can be left `null` to get the "current" culture value.
* To reduce the amount of breaking changes, and to simplify things for non-multilingual sites, existing properties such as `document.Name` and `document.Children` (and others) still exist, and return the value for the current culture. In other words, these properties are now implemented as `document.Name => document.Name()` or `document.Children => document.Children()`.

The rest of this document presents each change in details.

### More interfaces

It was possible to mock and test the `IPublishedContent` interface in version 7. It has been improved in version 8, but it still relies on concrete `PublishedContentType` and `PublishedPropertyType` classes to represent the content types, which complicates things.

In version 8.1, these two classes are abstracted as `IPublishedContentType` and `IPublishedPropertyType`, thus making `IPublishedContent` easier to mock and test.

>**CHANGE**: This impacts every method accepting or returning a content type. For instance, the signature of most `IPropertyValueConverter` methods changes. References to `PublishedContentType` must be replaced with references to `IPublishedContentType`.

The following `IPublishedContent` members change:

### Name

The `document.Name` property is complemented by the `document.Name(string culture = null)` extension method. The property returns the name for the current culture. The `document.GetCulture(...).Name` syntax is removed.

>**CHANGE**: Calls to `document.GetCulture(culture).Name` must be replaced with `document.Name(culture)`.

### UrlSegment

The `document.UrlSegment` property is complemented by the `document.UrlSegment(string culture = null)` extension method. The property returns the Url segment for the current culture. The `document.GetCulture(...).UrlSegment` syntax is removed.

>**CHANGE**: Calls to `document.GetCulture(culture).UrlSegment` must be replaced with `document.UrlSegment(culture)`.

### Culture

The `document.GetCulture()` method is removed. The proper way to get a culture date is `document.CultureDate(string culture = null)`. The `document.Cultures` property now returns the invariant culture, for invariant documents.

>**CHANGE**: Calls to `document.GetCulture(culture).Date` must be replaced with `document.CultureDate(culture)`. Calls to `document.Cultures` must take into account the invariant culture.

### Children

The `document.Children` property is complemented by the `document.Children(string culture = null)` extension method which, when a culture is specified always return children available for the specified culture. The property returns the children available for the current culture.

A new `document.ChildrenForAllCultures` property is introduced, which returns _all_ children, regardless of whether they are available for a culture or not.

>**CHANGE**: Calls to `document.Children` may have to be replaced by `document.ChildrenForAllCultures` depending on if the 8.0.x usage of this was relying on it returning unfiltered/all children regardless of the current routed culture.

### Url

The `document.Url` property is complemented by the `document.Url(string culture = null, UrlMode mode = UrlMode.Auto)` extension method. The `document.GetUrl(...)` and `document.UrlAbsolute()` methods are removed. The `UrlProviderMode` enumeration is renamed `UrlMode`.

>**CHANGE**: Calls to `document.GetUrl(...)` must be replaced with `document.Url(...)`. Calls to `document.UrlAbsolute()` must be replaced with `document.Url(mode: UrlMode.Absolute)`.

### UmbracoContext

Due to the `UrlProviderMode` enumeration being renamed `UrlMode`, the signature of some overloads of the `Url(...)` method has changed. Methods that do not have a mode parameter remain unchanged.

>**CHANGE**: Code such as `context.Url(1234, UrlProviderMode.Absolute)` must become `context.Url(1234, UrlMode.Absolute)`.

The `UmbracoContext` class gives access to the rendering layer, which is more than a "cache". To reflect this, its `ContentCache` and `MediaCache` properties are renamed `Content` and `Media`. However, the old properties remain as obsolete properties.

>**CHANGE**: None required in 8.1, but code such as `context.ContentCache.GetById(1234)` should eventually be converted to `context.Content.GetById(1234)` as the obsolete properties may be removed in a further release.

### GetCulture

Version 7 had a `document.GetCulture()` method that was deriving a culture from domains configured in the tree. Somehow, that method was lost during version 8 development (issue [#5269](https://github.com/umbraco/Umbraco-CMS/issues/5269)).

Because that method is useful, especially when building traditional, non-multilingual sites, it has been re-introduced in version 8.1 as `document.GetCultureFromDomains()`.

>**CHANGE**: None.

### DomainHelper

`DomainHelper` has been replaced with a static `DomainUtilities` class.

>**CHANGE**: It is rare that `DomainHelper` is used in code since it only contains one public method but if developers are using this, it can no longer be injected since it's now a static class called `DomainUtilities`.

### Models Builder

If you're using ModelsBuilder in `dll` mode you need to delete the dlls before upgrading. Otherwise they're going to be wrong and cause your whole site to throw errors.

If you're using ModelsBuilder in `AppData` mode and you have your generated models in your solution you need to update them after upgrading. `PublishedContentType` will need to be replaced with `IPublishedContentType`. If you have an implementation of the `PropertyValueConverter` class, you need to replace all references to `PublishedPropertyType` with `IPublishedPropertyType` within that class. Only after you do that will your solution build again.

### AutoMapper

Umbraco 8.1 replaces AutoMapper with [UmbracoMapper](../../../../reference/mapping.md). This in itself will not break anything on your site. If you have used AutoMapper in your own code you will have to either include the package yourself or switch your implementation to use UmbracoMapper.

</details>

<details>

<summary>7.latest to 8.0.0</summary>

There is no direct upgrade path from Umbraco 7 to Umbraco 8. It is however possible to migrate content from Umbraco 7 sites to Umbraco 8 sites. We have added content migrations in Umbraco 8.1.0 enabling you to migrate your content from Umbraco 7 to Umbrao 8.

It is not possible to upgrade an Umbraco 7 site to Umbraco 8 is because the codebase has been fundamentally updated in Umbraco 8. A lot of outdated code and technology has been removed and instead new, faster and more secure technology has been implemented.

In Umbraco 8 we have added improvements and updated dependencies. We have also done a thorough clean-up to make it simpler for you to work with and extend your Umbraco project.

## [Migrate your content to Umbraco 8](migrating-content-to-umbraco-8.md)

</details>

<details>

<summary>7.6.3 to 7.7.0</summary>

Version 7.7.0 introduces User Groups and a better user management and security facilities. This means that anything to do with "User Types" no longer exist including APIs that work with User Types. If your code or any package's code makes reference to "User Type" APIs, you need to make changes to your code. In many cases, we've added backward compatibility for these scenarios and obsoleted APIs that should no longer be used.

We are now by default using the e-mail address and not the username for the credentials. When trying to login to the backoffice you need to use the e-mail address as opposed to the username. If you do an upgrade from an older version and would like to keep using the username, change the `<usernameIsEmail>true</usernameIsEmail>` setting to **false**.

For a full list of breaking changes see: [the list on the issue tracker](https://issues.umbraco.org/issues/?q=&project=U4&tagValue=&release=7.7.0&issueType=&search=search)

Version 7.7.2 no longer ships with the `CookComputing.XmlRpcV2` assembly. If you reference this assembly or have a package that requires this assembly, you need to copy it back into your website.

This version also ships with far less client files that were only relevant for older versions of Umbraco (i.e. < 7.0.0). There might be some packages that were referencing these old client files. If you seen missing image references you may need to contact the vendor of the package in question to update their references.

</details>

<details>

<summary>7.6.0 to 7.6.3</summary>

In short:

In Umbraco version 7.6.2 we made a mistake in the Property Value Converts (PVCs). This was corrected 2 days later in version 7.6.3. If you were having problems with querying the following Data Types on the frontend, make sure to upgrade to 7.6.3:

* Multi Node Tree Picker
* Related Links
* Member Picker

Depending on whether you tried to fix problem with those Data Types you will need to fix them after you upgrade to 7.6.3.

### Property Value Converters (PVC)

Umbraco stores data for Data Types in different ways. For a lot of pickers it will store `1072` or `1083,1283`. These numbers refer to the identifier of the item in Umbraco. In the past, when building your templates, you would manually have to take that value and find the content item it belongs to. Then you would be able to get the data you wanted from there. An example of that is shown below:

```csharp
@{
    IPublishedContent contactPage;
    var contactPageId = Model.Content.GetPropertyValue<int>("contactPagePicker");
    if (contactPageId > 0)
    {
        contactPage = Umbraco.TypedContent(contactPageId);
    }
}

<p>
  <a href="@contactPage.Url">@contactPage.Name</a>
</p>
```

In Umbraco 7.6.0, this is what you would do instead:

```html
<p>
    <a href="@Model.Content.ContactPagePicker.Url">@Model.ContactPagePicker.Name</a>
</p>
```

This is possible using Models Builder and through the inclusion of [core property value converters](https://our.umbraco.com/projects/developer-tools/umbraco-core-property-value-converters/), a package by community member Jeavon Leopold.

In order to not break everybody's sites (the results of queries are different when PVC's are enabled) we disabled these PVC's by default.

Umbraco 7.6.0 also came with new pickers that store their data as a [UDI (Umbraco Identifier)](https://our.umbraco.com/Documentation/Reference/Querying/Udi). We wanted to simplify the use of these new pickers and by default we wanted PVC's to always be enabled for those pickers.

We noticed that some new pickers also got their PVC's disabled when the configuration setting was set to false (`<EnablePropertyValueConverters>false</EnablePropertyValueConverters>`).

To make everything consistent, we made sure that the UDI pickers would always use PVC's in 7.6.2, this however reversed the behavior. So when PVC's were enabled, the property would not be converted and when PVC's were disabled, the property would be converted after all. This is the exact opposite behavior of 7.6.2.

So we have fixed this now in 7.6.3.

This issue only affects:

* Multi Node Tree Picker
* Related Links
* Member Picker

Have you already upgraded to 7.6.2 and fixed queries for those three Data Types you have to fix them again in version 7.6.3.

</details>

<details>

<summary>7.4.0 to 7.6.0</summary>

Find a list of all the breaking changes below and [a list of the items is also available on the tracker](http://issues.umbraco.org/issues/U4?q=Due+in+version%3A+7.6.0+Backwards+compatible%3F%3A+No+)

The three most important things to note are:

1. In web.config do not change `useLegacyEncoding` to `false` if it is currently set to `true` - changing the password encoding will cause you not being able to log in any more
2. In umbracoSettings.config leave `EnablePropertyValueConverters` set to `false` - this will help your existing content queries to still work
3. In tinyMceConfig.config make sure to remove `<plugin loadOnFrontend="true">umbracolink</plugin>` so that the rich text editor works as it should

### Breaking Changes

### Dependencies

### UrlRewriting.Net ([U4-9004](https://issues.umbraco.org/issue/U4-9004))

`UrlRewriting` was old, leaking memory, and slowing down website startup when dealing with more than a few rules. It's entirely replaced by the [IIS Url Rewrite](https://www.iis.net/downloads/microsoft/url-rewrite) extension.

### Json.Net ([U4-9499](https://issues.umbraco.org/issue/U4-9499))

Json.Net has been updated to version 10.0.0 to benefit from improvements in features, fixes and performances (see [release notes](https://github.com/JamesNK/Newtonsoft.Json/releases)). This might be a breaking change for people relying on one of the changed functionality.

### Log4net ([U4-1324](https://issues.umbraco.org/issue/U4-1324))

Umbraco has used a custom build of an old (1.2.11) version of log4net that supported Medium Trust. However, Umbraco itself does not support Medium Trust anymore, and therefore log4net has been upgraded to the standard, latest build of log4net 2.0.8.

### ImageProcessor ([U4-8963](https://issues.umbraco.org/issue/U4-8963))

An optional parameter has been added to the `GetCropUrl` method in order to support the background color parameter. This breaks the method signature and therefore might require a recompile of user's code.

### HtmlAgilityPack ([U4-9655](https://issues.umbraco.org/issue/U4-9655))

The HtmlAgilityPack has been upgraded to version 1.4.9.5. The Umbraco upgrade process should take care of setting up the binding redirects appropriately.

### Core

### Membership Provider Encoding ([U4-6566](https://issues.umbraco.org/issue/U4-6566))

The Membership Provider `useLegacyEncoding` setting is now `false` by default, as the legacy password encoding has weaknesses.

This change only impacts new installs (no change for upgrades).

### Property Value Converters ([U4-7318](https://issues.umbraco.org/issue/U4-7318))

A large amount of property value converters contributed by the community have been merged in and are now the default value converters. These converters change the object types returned by `GetPropertyValue` for more convenient types.

For example, the `SliderValueConverter` returns a `decimal` or a `Range<decimal>` value that can directly be used in views, instead of the CSV string value that was previously returned.

This change only impacts new installs (no change for upgrades).

The new property value converters are controlled by an `umbracoSettings.config` setting. In section `settings/content`, setting `EnablePropertyValueConverters` needs to be present and `true` to activate them.

### Database ([U4-9201](https://issues.umbraco.org/issue/U4-9201))

Umbraco has been using a PetaPoco-managed `UmbracoDatabase` instance since version 7 came out. We realized that some of our legacy code still bypassed that mechanism and used parallel, out-of-band database connections, causing issues with transactions.

The legacy code has been refactored to rely on the `UmbracoDatabase` instance. However, because that database is disposed during `EndRequest`, code that ran after it has been disposed may not work anymore. This should then be updated to used either an `HttpModule` event that occurs before `EndRequest`, or the new `UmbracoModule.EndRequest` event.

More details are available on [issue 146](https://github.com/kipusoep/UrlTracker/issues/146) on the 301 Redirect Tracker GitHub issue tracker.

### Scopes ([U4-9406](https://issues.umbraco.org/issue/U4-9406))

Version 7.6 introduces the notion of _scopes_, which allow for wrapping multiple service-level operations in one single transaction. The scopes API is partially public. Scopes are not meant for public use at this stage and we need a few more releases to ensure that the APIs are stable.

Scopes _should not_ change how Umbraco functions.

Introducing scopes means that some public APIs signatures are changing. Most of these changes target internal and/or non-breaking APIs (as per our [guidelines](https://our.umbraco.com/Documentation/Development-Guidelines/breaking-changes)). This should therefore have no impact on sites but may break unit tests.

### Property Editors storing UDI instead of ID ([U4-9310](https://issues.umbraco.org/issue/U4-9310))

The property editors for pickers for content, media, members and related links have been updated to store UDI instead of node ID. Pickers in sites being upgraded have been marked as obsolete, but will continue to work as they always did.

New sites will have the obsolete pickers filtered out from the list of available property editors, but they can be enabled by a configuration flag.

### Rich Text Editor (RTE) Images attributes ([U4-6228](https://issues.umbraco.org/issue/U4-6228), [U4-6595](http://issues.umbraco.org/issue/U4-6595))

For a long time we had a `rel` attribute on an `<img>` tag when inserting into the RTE. This is invalid HTML markup. We worked around this by stripping this attribute using a Property Editor Value converter. Some developers relied on this attribute so we didn't change it to a "data-id" attribute which would have been valid. In 7.6 we are not storing integer IDs in these attributes. Instead storing UDI values so with this change we no longer use `rel` or `data-id` and instead there will be a "data-udi" attribute. This change should affect only a small amount of people that were previously relying on the values from the "rel" attribute.

### Others

We are shipping with SignalR in the core at version 2.2.1. If you already have SignalR installed into your app and are using an older version there may be conflicts.

The creation and editing of WebForms templates will no longer be supported as for version 7.6.0.

### Upgrading via NuGet

This is an important one and there was not a perfect solution to this. We have removed the UrlRewriting dependency and no longer ship with it. However, if you are using it we didn't want to have NuGet delete all of your rewrites. The good news is that if you are using it, the NuGet upgrade will not delete your rewrite file and everything should continue to work.

However, if you are not using it, **you will get an error after upgrading. Here's how to fix it:**

Since you aren't using UrlRewriting you will have probably never edited the UrlRewriting file. In this case, NuGet will detect that and remove it. However you will need to manually remove these UrlRewriting references from your `web.config`:

```xml
<section name="urlrewritingnet" restartOnExternalChanges="true" requirePermission="false" type="UrlRewritingNet.Configuration.UrlRewriteSection, UrlRewritingNet.UrlRewriter" />
```

and

```xml
<urlrewritingnet configSource="config\UrlRewriting.config" />
```

Remove the following `httpModules` from your `web.config`:

```xml
<system.web>
<httpModules>
    <add name="UrlRewriteModule" type="UrlRewritingNet.Web.UrlRewriteModule, UrlRewritingNet.UrlRewriter"/>
    ...
</httpModules>
<system.web>
```

and

```xml
<system.webServer>
    <modules>
    <remove name="UrlRewriteModule"/>
    <add name="UrlRewriteModule" type="UrlRewritingNet.Web.UrlRewriteModule, UrlRewritingNet.UrlRewriter"/>
    ...
    </modules>
</system.webServer>
```

### Forms

Umbraco Forms 6.0.0 has been released to be compatible with Umbraco 7.6. It is a new major version release of Forms primarily due to the strict dependency on 7.6+. If you are using Forms, you will need to update it to version 6.0.0

There is **[important Forms upgrade documentation that you will need to read the here](../../../../../umbraco-forms/installation/version-specific.md#version-4-to-version-6)**.

### Courier

Umbraco Courier 3.1.0 has been released to be compatible with Umbraco 7.6. If you are using Courier, you will need to update it to version 3.1.0.

</details>

<details>

<summary>7.3.0 to 7.4.0</summary>

For manual upgrades:

* Copy the new folder `~/App_Plugins/ModelsBuilder` into the site
* Do not forget to merge `~/Config/trees.config` and `~/Config/Dashboard.config` - they contain new and updated entries that are required to be there
  * If you forget `trees.config` you will either not be able to browse the Developer section or you will be logged out immediately when trying to go to the developer section
* You may experience an error saying `Invalid object name 'umbracoUser'` - this can be fixed by [clearing your cookies on localhost](http://issues.umbraco.org/issue/U4-8031)

</details>

<details>

<summary>7.2.0 to 7.3.0</summary>

Make sure to manually clear your cookies after updating all the files, otherwise you might an error relating to `Umbraco.Core.Security.UmbracoBackOfficeIdentity.AddUserDataClaims()`. The error looks like: `Value cannot be null. Parameter name: value`.

NuGet will do the following for you. If you're upgrading manually make sure to also:

* Delete `bin/Microsoft.Web.Helpers.dll`
* Delete `bin/Microsoft.Web.Mvc.FixedDisplayModes.dll`
* Delete `bin/System.Net.Http.dll`
* Delete `bin/System.Net.Http.*.dll` (all dll files starting with `System.Net.Http`) **except** for `System.Net.Http.Formatting.dll`
* Delete `bin/umbraco.XmlSerializers.dll`
* Add this in the `appSetting` section of your `web.config` file: `<add key="owin:appStartup" value="UmbracoDefaultOwinStartup" />`

Other considerations:

* WebApi has been updated, normally you don’t have to do anything unless you have custom webapi configuration:
  * See this article if you are using `WebApiConfig.Register`: [https://www.asp.net/mvc/overview/releases/how-to-upgrade-an-aspnet-mvc-4-and-web-api-project-to-aspnet-mvc-5-and-web-api-2](https://www.asp.net/mvc/overview/releases/how-to-upgrade-an-aspnet-mvc-4-and-web-api-project-to-aspnet-mvc-5-and-web-api-2)
  * You need to update your `web.config` file to have the correct WebApi version references - this should be done by doing a compare/merge of your `~/web.config` file with the `~/web.config` file in the release
* MVC has been updated to MVC5
  * You need to update your `web.config` file to have the correct MVC version references - this should be done by doing a compare/merge of your `~/web.config` file with the `~/web.config` file in the release
  * The upgrader will take care of updating all other web.config’s (in all other folders, for example, the `Views` and `App_Plugins` folders) to have the correct settings
  * For general ASP.NET MVC 5 upgrade details see: [https://www.asp.net/mvc/overview/releases/how-to-upgrade-an-aspnet-mvc-4-and-web-api-project-to-aspnet-mvc-5-and-web-api-2](https://www.asp.net/mvc/overview/releases/how-to-upgrade-an-aspnet-mvc-4-and-web-api-project-to-aspnet-mvc-5-and-web-api-2)
* It is not required that you merge the changes for the Examine index paths in the ExamineIndex.config file. However, if you do, your indexes will be rebuilt on startup because Examine will detect that they don’t exist at the new location.
* It's highly recommended to clear browser cache - the ClientDependency version is automatically bumped during install which should force browser cache to refresh, however in some edge cases this might not be enough.

</details>

<details>

<summary>7.1.0 to 7.2.0</summary>

* Copy in the `/Views/Partials/Grid` (contains Grid rendering views).

</details>

<details>

<summary>7.0.2 to 7.1.0</summary>

* Remove the `/Install` folder.

</details>

<details>

<summary>7.0.1 to 7.0.2</summary>

* There was an update to the `/umbraco/config/create/ui.xml` which needs to be manually updated. The original element had this text:

```xml
<nodeType alias="users">
    <header>User</header>
    <usercontrol>/create/simple.ascx</usercontrol>
    <tasks>
    <create assembly="umbraco" type="userTasks" />
    <delete assembly="umbraco" type="userTasks" />
    </tasks>
</nodeType>
```

* The `usercontrol` value has changed to: `/create/user.ascx`. This is a required change otherwise creating a new user will not work.
* There is a breaking change to be aware of, full details can be found [here](https://umbraco.com/blog/heads-up-breaking-change-coming-in-702-and-62/).

</details>

<details>

<summary>7.0.0 to 7.0.1</summary>

* Remove all uGoLive dlls from `/bin`
  * These are not compatible with V7
* Move `appSettings/connectionStrings` back to `web.config`
  * If you are on 7.0.0 you should migrate these settings into the web.config instead of having them in separate files in `/config/`
  * The keys in `config/AppSettings.config` need to be moved back to the web.config `<appSettings>` section and similarly, the `config/ConnectionStrings.config` holds the Umbraco database connections in v7.0.0 and they should be moved back to the web.config `<connectionStrings>` section.
  * `/config/AppSettings.config` and `/config/ConnectionString.config` can be removed after the contents have been moved back to `web.config`.
* Delete all files in `~/App_Data/TEMP/Razor/`
  * Related to issues with razor macros

</details>

<details>

<summary>6.latest to 7</summary>

Read and follow [the full v7 upgrade guide](upgrading-to-v7.md)

</details>

<details>

<summary>4.latest to 6</summary>

* If your site was ever a version between 4.10.0 and 4.11.4 and you have upgraded to 6.0.0 install the [fixup package](https://our.umbraco.com/projects/developer-tools/path-fixup) and run it after the upgrade process is finished.
* The DocType Mixins package is **not** compatible with v6+ and will cause problems in your Document Types.

</details>

<details>

<summary>Version 4</summary>

### Version 4.10.x to 4.11.x

* If your site was ever a version between 4.10.0 and 4.11.4 install the [fixup package](https://our.umbraco.com/projects/developer-tools/path-fixup) and run it after the upgrade process is finished.

### Version 4.8.0 to 4.10.0

* Delete the `bin/umbraco.linq.core.dll` file
* Copy the new files and folders from the zip file into your site's folder
  * `/App_Plugins`
  * `/Views`
  * `Global.asax`
* Remove the `Config/formHandlers.config` file

### Version 4.7.2 to 4.8.0

* Delete the `bin/App_Browsers.dll` file
* Delete the `bin/App_global.asax.dll` file
* Delete the `bin/Fizzler.Systems.HtmlAgilityPack.dll` file
* For people using uComponents 3.1.2 or below, 4.8.0 breaks support for it. Either upgrade to a newer version beforehand or follow the workaround [posted here](https://our.umbraco.com/projects/backoffice-extensions/ucomponents/questionssuggestions/33021-Upgrading-to-Umbraco-48-breaks-support-for-uComponents)

### Version 4.7.1.1 to 4.7.2

* Delete the `bin/umbraco.MacroEngines.Legacy.dll` file

### Version 4.6.1 to 4.7.1.1

* Delete `bin/Iron*.dll` (all dll files starting with "Iron")
* Delete `bin/RazorEngine*.dll` (all dll files starting with "RazorEngine")
* Delete `bin/umbraco.MacroEngines.Legacy.dll`
* Delete `bin/Microsoft.Scripting.Debugging.dll`
* Delete `bin/Microsoft.Dynamic.dll`

</details>
