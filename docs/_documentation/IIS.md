---
slug: iis
---
## Register ServiceStack ASP.NET HttpHandler

ServiceStack integrates with your existing ASP.NET Web Application by registering an ASP.NET HttpHandler used to route HTTP requests to ServiceStack. The configuration below supports both IIS/6.0 and Mono as well as IIS7+ new handler mappings under `<system.webServer>`:

### Configure ServiceStack at `/` root path

Add this configuration in your `Web.config` to host ServiceStack at the `/` root path:

```xml
<!-- For IIS 6.0/Mono -->
<system.web>
  <httpHandlers>    
    <add path="*" type="ServiceStack.HttpHandlerFactory, ServiceStack" 
         verb="*"/>
  </httpHandlers>
</system.web>

<!-- For IIS 7.0+ -->
<system.webServer>
  <validation validateIntegratedModeConfiguration="false" />
  <handlers>
    <add path="*" name="ServiceStack.Factory" preCondition="integratedMode" 
         type="ServiceStack.HttpHandlerFactory, ServiceStack" 
         verb="*" resourceType="Unspecified" allowPathInfo="true" />
  </handlers>
</system.webServer>
```

> **Tip:** If you want to host your webservice on a custom path to avoid conflicts with another web framework (eg ASP.Net MVC), see https://github.com/ServiceStack/ServiceStack/wiki/Run-servicestack-side-by-side-with-another-web-framework.

> **Note:** Due to limitations in IIS 6 - host [ServiceStack at a /custompath](http://mono.servicestack.net/ServiceStack.Hello/#custompath) which must end with `.ashx`, e.g: `path="api.ashx"`

### Configure ServiceStack at `/api` custom path

If you want to use ServiceStack together with an existing ASP.NET Web Framework, you can instead host ServiceStack at `/api` path with:

```xml
<location path="api">
  <system.web>
    <httpHandlers>
      <add path="*" type="ServiceStack.HttpHandlerFactory, ServiceStack" 
           verb="*"/>
    </httpHandlers>
  </system.web>

  <system.webServer>
    <modules runAllManagedModulesForAllRequests="true"/>
    <validation validateIntegratedModeConfiguration="false" />
    <handlers>
      <add path="*" name="ServiceStack.Factory" 
           type="ServiceStack.HttpHandlerFactory, ServiceStack" 
           verb="*" preCondition="integratedMode" 
           resourceType="Unspecified" allowPathInfo="true" />
    </handlers>
  </system.webServer>
</location>
```

To use ServiceStack together with ASP.NET MVC follow the steps in the [[Mvc integration]] docs.

## Troubleshooting

### Disable WebDAV to enable PUT and DELETE Verbs

If you are running IIS 7.5 you may need to disable the WebDAV module to enable `PUT` and `DELETE` verbs.  You can do this globally through IIS or locally through a web.config.

```xml
<system.webServer>
  <modules runAllManagedModulesForAllRequests="true">
    <remove name="WebDAVModule" />
  </modules>
</system.webServer>
```