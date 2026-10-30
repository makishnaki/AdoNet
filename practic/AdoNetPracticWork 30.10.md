# 15 билетов по ADO.NET + PostgreSQL

**Общие условия для всех билетов:**
- Время: 1,5 часа
- БД: PostgreSQL (Docker)
- Проект: ASP.NET Core Minimal API (.NET 8), пакеты `Npgsql` и `Dapper`
- В **каждом** билете студент должен реализовать **все темы**: соединение, команды, параметры, транзакции, DataSet, DataAdapter, DataTable, асинхронность, пулы, Dapper
- Каркасы содержат только сигнатуры эндпоинтов и пустые тела — без подсказок и решений

---

## Общие файлы (для всех билетов)

**`MyApp.csproj`**
```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>net8.0</TargetFramework>
    <Nullable>enable</Nullable>
    <ImplicitUsings>enable</ImplicitUsings>
    <AssemblyName>MyApp</AssemblyName>
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Npgsql" Version="8.0.5" />
    <PackageReference Include="Dapper" Version="2.1.35" />
  </ItemGroup>
</Project>
```

**`Dockerfile`**
```dockerfile
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src
COPY *.csproj .
RUN dotnet restore
COPY . .
RUN dotnet publish -c Release -o /app

FROM mcr.microsoft.com/dotnet/aspnet:8.0
WORKDIR /app
COPY --from=build /app .
ENV ASPNETCORE_URLS=http://+:80
EXPOSE 80
ENTRYPOINT ["dotnet", "MyApp.dll"]
```

**`docker-compose.yml`**
```yaml
version: '3.8'
services:
  db:
    image: postgres:16
    environment:
      POSTGRES_DB: shopdb
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
    ports: ["5432:5432"]
    volumes:
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
  app:
    build: .
    environment:
      CONN_STRING: "Host=db;Port=5432;Database=shopdb;Username=postgres;Password=postgres;Pooling=true;Minimum Pool Size=1;Maximum Pool Size=20"
    ports: ["8080:80"]
    depends_on: [db]
```

---

## Билет 1 — «Интернет-магазин: каталог товаров»

