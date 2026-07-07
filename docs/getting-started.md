# Getting started

## Description

GreenFire is built as both a npm an a nupkg package and can be consumed in any React or ASP.NET projects.
For the moment, the packages are not published, therefore, they have to be consumed through a local folder as a local package.

### **GreenFire.React**

This package, built with Vite, exposes all the controls and processes necessary to operate. The package has the processes made in a way that they can be easily implemented without having to construct them from their individual pieces, like this:

=== "React"

```ts
<CrudProcess processCode="users" />
```

### **GreenFire.Net**

This package, built with ASP.NET, exposes all the endpoints, as well as classes and interfaces that are necessary to output the correct metadata for all of GreenFire needs. The philosophy behind this is to be able to output processes with a simple syntax, like this:

=== "ASP.NET"

```csharp
[ProcessCode("users")]
public class UsersProcess(IUserService service) : CrudProcess<User, Guid, UserDto>(service)
{
}
```

## Installation

### **GreenFire.React**

Once the package is correctly located in a local path in the frontend folder, it can be easily installed via commands. For example, if the package is located inside a folder named **libs** in the root, it will be as simple as executing the next command in the terminal:

`npm install ./libs/greenfire-react.tgz`
  
This should install the package and reflect it inside **packages.json**, like this:

`"@greenfire/react": "file:libs/greenfire-react.tgz"`

### **GreenFire.Net**

For this one, it is necessary to define a local nuget folder, the easiest way to do this is with Visual Studio, in the **NuGet Package Manager Settings**, in the **Sources** menu, where a local folder can be added. Once it is added, the Greenfire package will appear and be able to be installed from the **NuGet Package Manager**.

## First steps
  
### **GreenFire.React**

With GreenFire installed, the first step is adding GreenFire context to your application, usually done in your App file:

=== "React"

```ts
return (
    <>
        <GreenFireProvider
        localization={localization}
        themeMode={theme}
        backend={greenFireBackend}
        dialogs={dialogs}
        >
        <Routes>
            <Route path="/main" element={<MainPage />} />
        </Routes>            
        </GreenFireProvider>
    </>
)
```

Refer to [**Configuration (frontend)**](configuration/frontend/index.md) for detailed information about the provider's configuration.

Once this is done, you are ready to start using any of the elements that the package offers, like, for example, a Crud Process:

=== "React"

```ts
import { CrudProcess } from "@greenfire/react";
import { useNavigate, useParams } from "react-router-dom";

function CrudProcessPage() {
  const { code, id } = useParams<{ code: string, id: string }>();
  const navigate = useNavigate();

  return (
    <CrudProcess
      processCode={code ?? ""}
      id={id ?? undefined}
      onNormalizeRoute={() => navigate(`/process/${code}`, { replace: true })}
      onError={() => navigate("/error")}
    />
  );
}

export default CrudProcessPage;
```

### **GreenFire.Net**

The backend GreenFire initialization is very simple, done in your application's startup configuration.

=== "ASP.NET"

```csharp
builder.Services.AddGreenFire();
```

Once initialized, your API will start exposing GreenFire's own endpoints, as well as automatically discovering any of the definitions you may create for processes, layouts, controls, dashboards and reports.

The extra configuration is minimal, refer to [**Configuration (backend)**](configuration/backend/index.md) to take a look at what is possible.
