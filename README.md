# Run the app

Test if the source works

```
dotnet run --urls "http://localhost:5000"
```

Point your browser to:

```
http://localhost:5000/WeatherForecast
```

# Build the app for OCP

## Dotnet core for rhel.7

To deploy a binary standalone version of our app we need to compile for target architecture and environment:

```
dotnet clean
dotnet restore -r rhel.7-x64
dotnet publish -f netcoreapp3.1 -c Release -r rhel.7-x64 --self-contained false /p:MicrosoftNETPlatformLibrary=Microsoft.NETCore.App
```

At the end of the process you'll get something like:

```
Microsoft (R) Build Engine version 16.6.0+5ff7b0c9e for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

Build started 8/4/2020 10:14:22 AM.
     1>Project "[PATH_TO]/ocp-dotnet-binary-delploy/src/RestAPP/RestAPP.csproj" on node 1 (Clean target(s)).
     1>CoreClean:
        [...]
        [...]        
     1>Done Building Project "[PATH_TO]/ocp-dotnet-binary-delploy/src/RestAPP/RestAPP.csproj" (Clean target(s)).

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:00.58
host:~ $ dotnet restore -r rhel.7-x64
  Determining projects to restore...
  Restored [PATH_TO]/ocp-dotnet-binary-delploy/src/RestAPP/RestAPP.csproj (in 9.3 sec).
host:~ $ dotnet publish -f netcoreapp3.1 -c Release -r rhel.7-x64 --self-contained false /p:MicrosoftNETPlatformLibrary=Microsoft.NETCore.App
Microsoft (R) Build Engine version 16.6.0+5ff7b0c9e for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Determining projects to restore...
  Restored [PATH_TO]/ocp-dotnet-binary-delploy/src/RestAPP/RestAPP.csproj (in 180 ms).
  RestAPP -> [PATH_TO]/ocp-dotnet-binary-delploy/src/RestAPP/bin/Release/netcoreapp3.1/rhel.7-x64/RestAPP.dll
  RestAPP -> [PATH_TO]/ocp-dotnet-binary-delploy/src/RestAPP/bin/Release/netcoreapp3.1/rhel.7-x64/publish/
  ```

# Binary deploy .netcore

Run the commands from the  project root folder ```ocp-dotnet-binary-delploy```.

Create new app:

```
oc new-app openshift/dotnet:3.1 --binary --name=dotnet-rest-app
```

Start the binary build:

```
oc start-build dotnet-rest-app --from-dir=src/RestAPP/bin/Release/netcoreapp3.1/rhel.7-x64/publish
```

Expose the service:

```
oc expose svc/dotnet-rest-app
```

Wait for the pod with the app to be available (oc get pods).

The url of service is:

```
http://<YOUR_HOST>/WeatherForecast
```
