FROM mcr.microsoft.com/dotnet/aspnet:8.0-nanoserver-1809 AS base
WORKDIR /app
EXPOSE 8080
EXPOSE 8081


# This stage is used to build the service project
FROM mcr.microsoft.com/dotnet/sdk:8.0-nanoserver-1809 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["src/TelemetryService/TelemetryService.API/TelemetryService.API.csproj", "src/TelemetryService/TelemetryService.API/"]
RUN dotnet restore "./src/TelemetryService/TelemetryService.API/TelemetryService.API.csproj"
COPY . .
WORKDIR "/src/src/TelemetryService/TelemetryService.API"
RUN dotnet build "./TelemetryService.API.csproj" -c %BUILD_CONFIGURATION% -o /app/build

# This stage is used to publish the service project to be copied to the final stage
FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./TelemetryService.API.csproj" -c %BUILD_CONFIGURATION% -o /app/publish /p:UseAppHost=false

# This stage is used in production or when running from VS in regular mode (Default when not using the Debug configuration)
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "TelemetryService.API.dll"]