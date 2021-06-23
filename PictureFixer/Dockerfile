#See https://aka.ms/containerfastmode to understand how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/nightly/aspnet:6.0 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

#FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
FROM mcr.microsoft.com/dotnet/nightly/sdk:6.0 AS build 
COPY ["nuget.config", "nuget.config"]
RUN apt-get update
RUN apt-get install --no-install-recommends --yes python3
RUN dotnet workload install microsoft-net-sdk-blazorwebassembly-aot --skip-manifest-update
WORKDIR /src
COPY ["Server/PictureFixer.Server.csproj", "Server/"]
COPY ["Shared/PictureFixer.Shared.csproj", "Shared/"]
COPY ["Client/PictureFixer.Client.csproj", "Client/"]
RUN dotnet restore "Server/PictureFixer.Server.csproj"
COPY . .
WORKDIR "/src/Server"
RUN dotnet build "PictureFixer.Server.csproj" -c Release -o /app/build --nologo

FROM build AS publish
RUN dotnet publish "PictureFixer.Server.csproj" -c Release -o /app/publish --nologo

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "PictureFixer.Server.dll"]