<properties 
	pageTitle="Release notes for Application Insights for .NET" 
	description="The latest updates for .NET SDK." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/12/2016" 
	ms.author="abaranch"/>
 
# Release Notes for Application Insights SDK for .NET

The [Application Insights SDK for .NET](app-insights-asp-net.md) sends telemetry about your live app to [Application Insights](https://azure.microsoft.com/services/application-insights/), where you can analyze its usage and performance.


#### To install the SDK in your application

See [Get started with Application Insights for .NET](app-insights-asp-net.md).

#### To upgrade to the latest SDK 

* After you upgrade, you'll need to merge back any customizations you made to ApplicationInsights.config. If you're unsure whether you customized it, create a new project, add Application Insights to it, and compare your .config file with the one in the new project. Make a note of any differences.
* In Solution Explorer, right-click your project and choose **Manage NuGet packages**.
* Set the filter to show installed packages. 
* Select **Microsoft.ApplicationInsights.Web** and choose **Upgrade**. (This will also upgrade all the dependent packages.)
* Compare ApplicationInsights.config with the old copy. Most of the changes you'll see are because we removed some modules and made others parameterizable. Reinstate any customizations you made to the old file.
* Rebuild your solution.

## Version 2.0.0-beta4

- UseSampling and UseAdaptiveSampling extension methods were moved to Microsoft.ApplicationInsights.Extensibility
- Removed support for Universal Windows Phone and Store applications
- Updated ```DependencyTelemetry``` to have new properties ```ResultCode``` and ```Id```. ```ResultCode``` will be used to provide HTTP response code for HTTP dependencies and error code for SQL dependencies. ```Id``` will be used for cross-component correlation. 
- If ```ServerTelemetryChannel``` is initialized programmatically it is now required to call ```ServerTelemetryChannel.Initialize()``` method. Otherwise persistent storage will not be initialized (that means that if telemetry cannot be sent in the event of temporary connectivity issues, it will be dropped).
- ```ServerTelemetryChannel``` has new property ```StorageFolder``` that can be set either through code or through configuration. If this property is set, ApplicationInsights uses provided location to store telemetry that was not sent in the event of temporary connectivity issues. If property is not set, or provided folder is inaccessible, ApplicationInsights will try to use LocalAppData or Temp folders as it was done before.
- ```TelemetryConfiguration.GetTelemetryProcessorChainBuilder``` extension method is removed. Instead of this method use ```TelemetryConfiguration.TelemetryProcessorChainBuilder``` instance method.
- ```TelemetryConfiguration``` class has a new property ```TelemetryProcessors``` that gives readonly access to ```TelemetryProcessors``` collection.
- ```Use```, ```UseSampling``` and ```UseAdaptiveSampling``` preserves ```TelemetryProcessors``` loaded from configuration.
- Two telemetry processors are provided out of the box in the configuration file - user agent filter telemetry processor and request handler telemetry processor. Their behavior can be customized. You can append a user agent string that you want to be filtered out in the AI.config file. By default we filtering out ```AllwaysOn``` user agent string.  Current behavior compares strings in the config file against user agent string using a full match case insensitive comparison. You can also customize the list of handlers for which you want requests to be filtered out. 
- Dependent Microsoft.ApplicationInsights.Agent.Intercept nuget version was updated to 1.2.1. It has SQL dependency collection bug fixes.

## Version 2.0.0-beta3

- [Adaptive sampling](app-insights-sampling.md) turned on by default in server telemetry channel. 
- Fixed signature of ```UseSampling``` to allow chaining with other calls to ```Use``` of telemetry processors.  
- Property ```Request.ID``` returned back. ```OperationContext``` now has a property ```ParentId``` for end-to-end coorrelation.
- ```TimestampTelemetryInitializer``` is removed. Timestamp will be added automatically by ```TelemetryClient```.
- ```OperationCorrelationTelemetryInitializer``` is added by default to enable operaitons correlation.
- ```OperationCorrelationTelemetryInitializer``` is used instead of ```OperationIdTelemetryInitializer```.
- User Agent will not be collected by default. User Agent telemetry initializer was removed.
- ```DependencyTelemetry.Async``` field will not be collected by dependency collector telemetry module. 
- Static content and diagnostics requests will not be collected by request telemetry module. Use ```HandlersToFilter``` of ```RequestTrackingTelemetryModule``` collection to filter out requests generated by certain http handlers. 
- Autogenerated request telemetry is accessible though HttpContext extension method: System.Web.HttpContextExtension.GetRequestTelemetry  


## Version 2.0.0-beta2
- Added support for ITelemetryProcessor and ability to configure via code or config. [Enables custom filtering in the SDK](app-insights-api-filtering-sampling/#filtering)
- Removed context initializers. Use [Telemetry Initializers]( https://azure.microsoft.com/documentation/articles/app-insights-api-filtering-sampling/#filtering) instead.
- Updated Application Insights for .Net framework 4.6. 
- Custom event names can now be up to 512 characters.
- Property ```OperationContext.Name``` renamed to ```RootName```.
- Property ```RequestTelemetry.Id``` removed.
- Property ```Id``` and ```Context.Operation.Id``` of RequestTelemetry would not be initialized when creating new RequestTelemetry.
- ```RequestTelemetry.Name``` is not initialized any longer. ```RequestTelemetry.Context.Operation.Name``` will be used instead.
- In request monitoring, response code 401 is part of the normal authentication handshake and will result in a successful request.
- Fix UI thread locking when initializing InMemoryChannel (default channel) from UI thread. This fixes UI freezing issues with WPF applications.
 
## Version 2.0.0-beta1
- TrackDependency will produce valid JSON when not all required fields were specified.
- Redundant property ```RequestTelemetry.ID``` is now just a proxy for ```RequestTelemetry.Operation.Id```.
- New interface ```ISupportSampling``` and explicit implementation of it by most of data item types.
- ```Count``` property on DependencyTelemetry marked as Obsolete. Use ```SamplingPercentage``` instead.
- New ```CloudContext``` introduced and properties ```RoleName``` and ```RoleInstance``` moved to it from ```DeviceContext```.
- New property ```AuthenticatedUserId``` on ```UserContext``` to specify authenticated user identity.
- Added `Microsoft.ApplicationInsights.Web.AccountIdTelemetryInitializer`, `Microsoft.ApplicationInsights.Web.AuthenticatedUserIdTelemetryInitializer` that initialize authenticated user context as set by Javascript SDK.
- Added `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ITelemetryProcessor` and fixed-rate Sampling support as an implementation of it.
- Added `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.TelemetryChannelBuilder` that allows creation of a `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` with a set of `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ITelemetryProcessor`.

## Version 1.2

- Telemetry initializers that do not have dependencies on ASP.NET libraries were moved from `Microsoft.ApplicationInsights.Web` to the new dependency nuget `Microsoft.ApplicationInsights.WindowsServer`
- `Microsoft.ApplicationInsights.Web.dll` was renamed on `Microsoft.AI.Web.dll`
- `Microsoft.ApplicationInsights.Web.TelemetryChannel` nuget was renamed on `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel`. `Microsoft.ApplicationInsights.Extensibility.Web.TelemetryChannel` assembly was renamed on `Microsoft.AI.ServerTelemetryChannel.dll`. `Microsoft.ApplicationInsights.Extensibility.Web.TelemetryChannel` class was renamed on `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel`.
- All namespaces that are part of Web SDK were changed to exclude `Extensibility` part. That includes all telemetry initializers in ApplicationInsights.config and `ApplicationInsightsWebTracking` module in web.config.
- Dependencies collected using runtime instrumentation agent (enabled via Status Monitor or Azure WebSite extension) will not be marked as asynchronous if there are no HttpContext.Current on the thread.
- Property `SamplingRatio` of `DependencyTrackingTelemetryModule` does nothing and marked as obsolete.
- `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector` assembly was renamed on `Microsoft.AI.PerfCounterCollector`
- Several minor bug fixes in Web and Devices SDKs


## Version 1.1

- New telemetry type `DependencyTelemetry` was added which can be used to send information about dependency calls from application (like SQL, HTTP calls etc).
- New overload method `TelemetryClient.TrackDependency` was added that allows you to send information about dependency calls.
- Fixed NullReferenceException thrown by diagnostics module when TelemetryConfiguration.CreateDefault is used.

## Version 1.0

- Moved telemetry initializers and telemetry modules from separate sub-namespaces to the root `Microsoft.ApplicationInsights.Extensibility.Web` namespace.
- Removed "Web" prefix from names of telemetry initializers and telemetry modules because it is already included in the `Microsoft.ApplicationInsights.Extensibility.Web` namespace name.
- Moved `DeviceContextInitializer` from the `Microsoft.ApplicationInsights` assembly to the `Microsoft.ApplicationInsights.Extensibility.Web` assembly and converted it to an `ITelemetryInitializer`.
- Change namespace and assembly names from `Microsoft.ApplicationInsights.Extensibility.RuntimeTelemetry` to `Microsoft.ApplicationInsights.Extensibility.DependencyCollector` for consistency with the name of the NuGet package.
- Rename `RemoteDependencyModule` to `DependencyTrackingTelemetryModule`.
- Rename `CustomPerformanceCounterCollectionRequest` to `PerformanceCounterCollectionRequest`.

## Version 0.17
- Removed dependency to EventSource NuGet for the framework 4.5 applications.
- Anonymous User and Session cookies will not be generated on server side. To implement user and session tracking for web apps, instrumentation with JS SDK is now required – cookies from JavaScript SDK are still respected. Telemetry modules ```WebSessionTrackingTelemetryModule``` and ```WebUserTrackingTelemetryModule``` are no longer supported and were removed from ApplicationInsights.config file. Note that this change may cause a significant restatement of user and session counts as only user-originated sessions are being counted now.
- OSVersion is no longer populuated by SDK by default. When empty, OS and OSVersion is calculated by Application Insights pipeline, based on the user agent. 
- Persistence channel optimized for high-load scenarios is used for web SDK. "Spiral of death" issue fixed. Spiral of death is a condition when spike in telemetry items count that greatly exceeds throttling limit on endpoint will lead to retry after certain time and will be throttled during retry again.
- Developer Mode is optimized for production. If left by mistake it will not cause as big overhead as before attempting to output additional information.
- Developer Mode by default will only be enabled when application is under debugger. You can override it using ```DeveloperMode``` property of  ```ITelemetryChannel``` interface.

## Version 0.16 

2015-04-28 preview

- SDK now supports DNX target platform to enable monitoring of [.NET Core framework](http://www.dotnetfoundation.org/NETCore5) applications.
- Instances of ```TelemetryClient``` do not cache Instrumentation Key anymore. Now if instrumentation key wasn't set in ```TelemetryClient``` explicitly ```InstrumentationKey``` will return null. It fixes an issue when you set ```TelemetryConfiguration.Active.InstrumentationKey``` after some telemetry was already collected, telemetry modules like dependency collector, web requests data collection and performance counters collector will use new instrumentation key.

## Version 0.15

- New property ```Operation.SyntheticSource``` now available on ```TelemetryContext```. Now you can mark your telemetry items as "not a real user traffic" and specify how this traffic was generated. As an example by setting this property you can distinguish traffic from your test automation from load test traffic.
- Channel logic was moved to the separate NuGet called Microsoft.ApplicationInsights.PersistenceChannel. Default channel is now called InMemoryChannel
- New method ```TelemetryClient.Flush``` allows to flush telemetry items from the buffer synchronously

## Version 0.13

No release notes for older versions available.

 

