
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>net8.0</TargetFramework>
    <AzureFunctionsVersion>v4</AzureFunctionsVersion>
    <OutputType>Exe</OutputType>
    <SelfContained>false</SelfContained>
    <UseAppHost>false</UseAppHost>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.23.0" />
    <PackageReference Include="Microsoft.Azure.Functions.Worker" Version="1.24.0" />
    <PackageReference Include="Microsoft.Azure.Functions.Worker.ApplicationInsights" Version="1.4.0" />
    <PackageReference Include="Microsoft.Azure.Functions.Worker.Extensions.Http" Version="3.3.0" />
    <PackageReference Include="Microsoft.Azure.Functions.Worker.Sdk" Version="1.18.1" />
  </ItemGroup>

  <ItemGroup>
    <Compile Include="..\FunctionApp2\Function1.cs" Link="HelloFunction2.cs" />
  </ItemGroup>

</Project>







<TargetFramework>net8.0</TargetFramework>



FunctionsDebugger.Enable();