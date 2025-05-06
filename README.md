<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup Label="Globals">
    <SccProjectName>SAK</SccProjectName>
    <SccProvider>SAK</SccProvider>
    <SccAuxPath>SAK</SccAuxPath>
    <SccLocalPath>SAK</SccLocalPath>
  </PropertyGroup>

  <PropertyGroup>
    <OutputType>WinExe</OutputType>
    <TargetFramework>net7.0-windows7.0</TargetFramework>
    <Nullable>enable</Nullable>
    <UseWindowsForms>true</UseWindowsForms>
    <ImplicitUsings>enable</ImplicitUsings>
    <PackageIcon>automated.png</PackageIcon>
    <ApplicationIcon>Resources\automated.ico</ApplicationIcon>
  </PropertyGroup>

  <ItemGroup>
    <Compile Remove="NewFolder\**" />
    <EmbeddedResource Remove="NewFolder\**" />
    <None Remove="NewFolder\**" />
  </ItemGroup>

  <ItemGroup>
    <Compile Remove="PDI_Module\Pdi.cs" />
    <Compile Remove="PDI_Module\Pdi.Designer.cs" />
    <Compile Remove="SetPoint_Module\CqGrade.cs" />
    <Compile Remove="SetPoint_Module\CqGrade.Designer.cs" />
  </ItemGroup>

  <ItemGroup>
    <EmbeddedResource Remove="PDI_Module\Pdi.resx" />
    <EmbeddedResource Remove="SetPoint_Module\CqGrade.resx" />
  </ItemGroup>

  <ItemGroup>
    <Content Include="Resources\automated.ico" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="iTextSharp" Version="5.5.13.4" />
    <PackageReference Include="Microsoft.AspNetCore.SignalR.Client" Version="9.0.2" />
    <PackageReference Include="Newtonsoft.Json" Version="13.0.3" />
    <PackageReference Include="Oracle.ManagedDataAccess.Core" Version="23.6.0" />
    <PackageReference Include="QRCoder" Version="1.6.0" />
    <PackageReference Include="WinForms.DataVisualization" Version="1.9.2" />
  </ItemGroup>

  <ItemGroup>
    <Compile Update="Properties\Resources.Designer.cs">
      <DesignTime>True</DesignTime>
      <AutoGen>True</AutoGen>
      <DependentUpon>Resources.resx</DependentUpon>
    </Compile>
  </ItemGroup>

  <ItemGroup>
    <EmbeddedResource Update="Properties\Resources.resx">
      <Generator>ResXFileCodeGenerator</Generator>
      <LastGenOutput>Resources.Designer.cs</LastGenOutput>
    </EmbeddedResource>
  </ItemGroup>

  <ItemGroup>
    <None Update="Resources\automated.ico">
      <Pack>True</Pack>
      <PackagePath>\</PackagePath>
    </None>
    <None Update="Resources\automated.png">
      <Pack>True</Pack>
      <PackagePath>\</PackagePath>
    </None>
  </ItemGroup>

</Project>
