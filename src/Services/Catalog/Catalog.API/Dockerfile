# Use the official ASP.NET Core runtime as base image
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
USER app
WORKDIR /app
EXPOSE 8080
EXPOSE 8081

# Use the SDK image to build the application
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src

# Copy the project file and restore dependencies
COPY ["src/Services/Catalog/Catalog.API/Catalog.API.csproj", "src/Services/Catalog/Catalog.API/"]
RUN dotnet restore "src/Services/Catalog/Catalog.API/Catalog.API.csproj"

# Copy the rest of the source code
COPY . .
WORKDIR "/src/src/Services/Catalog/Catalog.API"

# Build the application
RUN dotnet build "Catalog.API.csproj" -c $BUILD_CONFIGURATION -o /app/build

# Publish the application
FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "Catalog.API.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

# Final stage: runtime image
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .

ENTRYPOINT ["dotnet", "Catalog.API.dll"]