---
title: 번들추가
date: 2018-02-12 10:59:56
category:
- mvc5
tags:
- mvc5
---

# 스크립트 및 스타일 번들 사용하기

## NuGet 패키지 추가하기
```
Install-Package Microsoft.AspNet.Web.Optimization -version 1.1.1
```

## 번들 정의하기
### BundelConfig.cs
~~~
using System.Web.Optimization;

namespace ClientFeatures
{
    public class BundleConfig
    {
        public static void RegisterBundles(BundleCollection bundles)
        {
            bundles.Add(new StyleBundle("~/Content/css").Include("~/Content/*.css"));
            bundles.Add(new ScriptBundle("~/bundles/angularjs").Include("~/Scripts/angular.js"));
        }
    }
}
~~~

## 번들 설정
### Global.asax
~~~
using System.Web.Mvc;
using System.Web.Optimization;
using System.Web.Routing;
using ClientFeatures;

namespace BookHospital.Web
{
    public class MvcApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
            AreaRegistration.RegisterAllAreas();
            RouteConfig.RegisterRoutes(RouteTable.Routes);
            BundleConfig.RegisterBundles(BundleTable.Bundles);
        }
    }
}
~~~

## 번들 적용하기
### web.config
~~~
<pages pageBaseType="System.Web.Mvc.WebViewPage">
    <namespaces>
        <add namespace="System.Web.Mvc" />
        <add namespace="System.Web.Mvc.Ajax" />
        <add namespace="System.Web.Mvc.Html" />
        <add namespace="System.Web.Routing" />
        <add namespace="System.Web.Optimization" />
        <add namespace="BookHospital.Web" />
    </namespaces>
</pages>
~~~

## _Layout.cshtml에 번들 적용
~~~
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>@ViewBag.Title</title>
    @Styles.Render("~/Content/css")
    <script src="~/Scripts/modernizr-2.6.2.js"></script>
</head>
<body>
    @RenderBody()
    @Scripts.Render("~/bundles/angularjs")
</body>
</html>
~~~