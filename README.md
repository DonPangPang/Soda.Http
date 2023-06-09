# Soda.Http

[![NuGet version (Soda.Http)](https://img.shields.io/nuget/v/Soda.Http.svg)](https://www.nuget.org/packages/Soda.Http/)
[![NuGet downloads (Soda.Http)](https://img.shields.io/nuget/dt/Soda.Http.svg)](https://www.nuget.org/packages/Soda.Http/) 

<a href="https://jb.gg/OpenSourceSupport">
    <img height="80px" src="https://resources.jetbrains.com/storage/products/company/brand/logos/jb_beam.png" alt="JetBrains Logo (Main) logo.">
<a/>

基于`HttpClient`封装的 Http 请求库。**如果有什么好想法，可以提 Issue 或者 Pr。**

* 2023/04/30 新增`Download/DownloadAsync`方法支持文件下载

## 用法

### 1 预载

预载配置并不是必须的，但是有助于我们进行一些通用基础设置，例如Headers、Accept、BaseUrl等等。

配置`BaseUrl`之后，可以直接使用`QSodaHttp.Uri()`代替`QSodaHttp.Url()`，直接填写接口即可。

在`AspNetCore`中：

```csharp
services.AddSodaHttp(opts =>
{
    opts.BaseUrl = "http://localhost:8080/";
    opts.Accept = new[]
    {
        "application/json",
        "text/plain",
        "*/*"
    };
    opts.EnableCompress = false;
    opts.Headers = new[]{
        ("X-Ca-Test", "key")
    };
});
```

较为通用的写法，程序构建时：

```csharp
QSodaHttp.AddSodaHttp(opts =>
{
    opts.BaseUrl = "http://localhost:8080/";
    opts.Accept = new[]
    {
        "application/json",
        "text/plain",
        "*/*"
    };
    opts.EnableCompress = false;
    opts.Headers = new[]{
        ("X-Ca-Test", "key")
    };
})
```

### 2 全局配置 Authentication

有时需要全局配置 Authentication，如果在代码中请求中独立配置了 Authentication 则会覆盖全局 Authentication

```csharp
QSodaHttp.InitAuthentication("Bearer", "Values");
```

如果你是塞到 Header 里的这种做法

```csharp
QSodaHttp.AddHeader("X-Ca-Key", "Values");
```

### 3 Http 请求

#### 3.1 QSodaHttp

API 示例：

```csharp
var result = await QSodaHttp.Url("https://www.baidu.com/")
    .Header("X-Ca-Key", "XXX")
    .Authentication("Bearer", "XXX")
    .Params(new { Id = "123456" })
    .Body(new { })
    // .Form(...)
    // .File(...)
    .PostAsync<string>();
```

简单示例：

```csharp
// 配置BaseUrl
var services = new ServiceCollection();
services.AddSodaHttp(opts =>
{
    opts.EnableCompress = false;
    opts.BaseUrl = "http://localhost:5050/";
});
```

```csharp
var res = await QSodaHttp.Uri("/Test/Get").Params(new { Id = "123456" }).GetAsync<object>();
```

```csharp
var res = await QSodaHttp.Uri("/Test/TestGetResult").Params(new { Id = "123456", Ids = new[] { "123", "456" } }).GetAsync<object>();
```

```csharp
var res = await QSodaHttp.Uri("/Test/Post").Body(new { Id = "123456", Ids = new[] { "123", "456" } }).PostAsync<object>();
```

```csharp
var res = await QSodaHttp.Uri("/Test/PostResult")
        .Params(new { Id = "123456", Ids = new[] { "123", "456" } })
        .Body(new { Id = "123456", Ids = new[] { "123", "456" } })
        .PostAsync<object>();
```

```csharp
var res = await QSodaHttp.Uri("/Test/Delete").Params(new { Id = "123456" }).DeleteAsync<object>();
```

```csharp
var res = await QSodaHttp.Uri("/Test/DeleteResult").Params(new { Id = "123456", Ids = new[] { "123", "456" } }).DeleteAsync<object>();
```

```csharp
var res = await QSodaHttp.Uri("/Test/Put").Params(new { Id = "123456" }).PutAsync<object>();
```

```csharp
var res = await QSodaHttp.Uri("/Test/PutResult")
        .Body(new { Id = "123456", Ids = new[] { "123", "456" } })
        .PutAsync<object>();
```

```csharp
var res = await QSodaHttp.Uri("Patch").Params(new { Id = "123456" }).PatchAsync<object>();
```

```csharp
var res = await QSodaHttp.Uri("PatchResult")
        .Body(new { Id = "123456", Ids = new[] { "123", "456" } })
        .PatchAsync<object>();
```

```csharp
var res = await QSodaHttp.Url("https://cdn.bootcdn.net/ajax/libs/react/18.2.0/cjs/react.development.js")
        .DownloadAsync(filename: "react");
```
