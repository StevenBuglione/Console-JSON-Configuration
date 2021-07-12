
<br />
<p align="center">

  <h3 align="center">Console Json Configuration</h3>
  <h4 align="center"><em>Disclaimer: This repo is for personal use only. I pretty much ripped this project off some blog. But the blog post forgot to mention one step which crashed the application. So I added the step here for next time.</em> </h4>
  <h5> Reference Blogs </h5>
  <ul>
    <li>
      <a href="https://www.damirscorner.com/blog/posts/20210305-ConfiguringEnvironmentsInNetConsoleApp.html"> Damir's Corner. </a>
  </li>
  <li>
    <a href="https://jameshobday.dev/2020/04/22/adding-a-json-configuration-file-to-a-net-core-console-application/"> James Hobday </a>
  </li>
  </ul>
</p>

## 1. Install The Required Nuget Packages

* Microsoft.Extensions.Configuration
* Microsoft.Extensions.Configuration.Binder
* Microsoft.Extensions.Configuration.EnvironmentVariables
* Microsoft.Extensions.Configuration.Json


### 2. Configure Program.cs 
  ```sh
  static void Main(string[] args)
        {
            var environment = Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT");
            var builder = new ConfigurationBuilder()
                .AddJsonFile($"appsettings.json", true, true)
                .AddJsonFile($"appsettings.{environment}.json", true, true)
                .AddEnvironmentVariables();

            var configurationRoot = builder.Build();
            var appConfig = configurationRoot.GetSection(nameof(AppConfig)).Get<AppConfig>();

            Console.WriteLine(appConfig.Environment);
        }
  ```

### 3. Configure appsettings.json, appsettings.Development.json and appsettings.Production.json

1. appsettings.json
  `{"AppConfig": {
    "Environment": "Default"
  }
}`

  
2. appsettings.Development.json
  `{"AppConfig": {
    "Environment": "DEV"
  }
}`

2. appsettings.Production.json
  `{"AppConfig": {
    "Environment": "PROD"
  }
}`


### 4. Configure launchSettings.json
```sh
{
  "profiles": {
    "ConsoleJsonConfigurationExample (Dev)": {
      "commandName": "Project",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "ConsoleJsonConfigurationExample (Prod)": {
      "commandName": "Project",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Production"
      }
    }
  }
}
```

### 5. Configure .csproj File
```sh
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>net5.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <None Remove="appsettings.Development.json" />
    <None Remove="appsettings.json" />
    <None Remove="appsettings.Production.json" />
  </ItemGroup>

  <ItemGroup>
    <Content Include="appsettings.Production.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </Content>
    <Content Include="appsettings.Development.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </Content>
    <Content Include="appsettings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </Content>
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.Extensions.Configuration" Version="5.0.0" />
    <PackageReference Include="Microsoft.Extensions.Configuration.Binder" Version="5.0.0" />
    <PackageReference Include="Microsoft.Extensions.Configuration.EnvironmentVariables" Version="5.0.0" />
    <PackageReference Include="Microsoft.Extensions.Configuration.Json" Version="5.0.0" />
  </ItemGroup>

</Project>
```
We add this configuration becasue we need to make sure that the JSON files are coppied over to the output directory of our application. 


### 6. Run Application 
