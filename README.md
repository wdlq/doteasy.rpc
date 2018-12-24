# Introduction
With the continuous development of the website system, the complexity of the architecture will change from MVC->SOA->microservices, from simple to complex, from centralized to distributed.
The introduction of the service framework is SOA->microservice process The problem that must be solved.
In the face of the increase in services, the deployment of service distribution, the mutual call between services and services, have to use the service framework to solve.
Based on NET Core 2.0 Standard 2 development, DotEasy.RPC supports transparent calls from the client to the server, just as simple as an implementation call to an interface.


# Features and dependence
1. Automate assembly and construction of related component types using Microsoft.Extensions.Dependency Injection (future considerations modified to Autofac).
2. Serialization of byte streams using [protobuf-net](https://github.com/mgravell/protobuf-net)
3. Generated by [Roslyn](https://github.com/dotnet/roslyn)'s runtime client proxy 
4. Communication pipeline and host built on [DotNetty](https://github.com/Azure/DotNetty)


# Related extension library
#### DotEasy.Rpc.Consul
Based conusl register center service find and discover. [https://github.com/steveleeCN87/doteasy.rpc.consul](https://github.com/steveleeCN87/doteasy.rpc.consul)
#### DotEasy.Rpc.Entry
Lazy dedicated library to further hide the customizable service build entry. [https://github.com/steveleeCN87/doteasy.rpc.entry](https://github.com/steveleeCN87/doteasy.rpc.entry)

# Demo
A code example that implements a simple interface and implements separation, with extensions based on asp.net core middleware and console applications
[link](https://github.com/steveleeCN87/doteasy.rpc.demo)


# More
[http://www.cnblogs.com/SteveLee/](http://www.cnblogs.com/SteveLee/)

# How to use
**step 1:**
Download the nuget package and install you project, input the code: 
```
Install-Package DotEasy.Rpc -Version 1.0.1
```
or clone the doteasy.rpc source code package.
* Note the project dependent installation

**step 2:**
* Separate your interface and implementation. For example, the interface class name is 'ISample', which contains an interface method 'string SayHello(string name)', the code is as follows：
```
[RpcTagBundle]
public interface ISample
{
    string SayHello(string name);
    ...n
}
```
_**At the 'ISample' interface project or other interface, you must ensure that only the interface definition is in the project.**_
_**Must be on the interface add property name 'RpcTagBundle'.**_
* And in the implementation class name 'Sample'
```
public Sample : ISample
{
    string SayHello(string name)
    {
        return $"hello {name}";
    }
}
```

**step 3:**

Applications were established server and client, the server recommended asp.net core to create a client at random, you can use asp.net core, or use the console application, or even the use of .net winform and so support
* At the asp.net core use middlware extend the package before reference the 'Isample' project and 'DotEasy.Rpc.Entry' dependency package, of course, you can also write your own build method. 
For example in the server:
```
public static IApplicationBuilder UseConsulServerExtensions(this IApplicationBuilder app, IConfiguration configuration)
{
    if (app == null) throw new ArgumentNullException(nameof(app));
    BaseServer baseServer = new BaseServer(configuration);
    baseServer.RegisterEvent += collection => collection.AddTransient<ISample, Sample>();
    baseServer.Start();
    return app;
}
```
for example in the console appliaction, you need to quote the 'Entry' lazy package, or you can also write your own build method:
```
class Program
{
    static void Main()
    {
        new TestClient();
    }
}

public class TestClient : BaseClient
{
    public TestClient()
    {
        var sample = Proxy<ISample>();
        var name = "world";
        Console.WriteLine($"{sample.SayHello(name)}");
        Console.ReadKey();
    }
}
```
_**Just need to reference the interface project, not the implementation project**_

**step 4:**

you can start register service 'consul' or 'etcd' completed, and start client program before start you asp.net core web server, running the result is 'hello world'.

# *so easy!* 

# Change log
## 1.0.2
1. Added precompiled synchronous and asynchronous remote invocation methods, unforcing the use of Task as asynchronous calls and precompiled builds.

## 1.0.1
1. Added Consul registration and callback to implement the configuration of the Consul registry.
2. Added Entry lazy entry class library package to implement Asp.net middleware extension and host based on Console application.