# Changelog

## 4.0.0 - 2025-08-22

### Framework upgrade summary 


Completed the upgrade to .NET 8.0 LTS across all projects and updated the copilot instructions accordingly. All projects now target .NET 8.0:

- **Main SDK** (`amadeus/amadeus.csproj`): ✅ .NET 8.0
- **Unit Tests** (`amadeus-test/amadeus-test.csproj`): ✅ .NET 8.0  
- **Integration Tests** (`amadeus-integration-test/amadeus-integration-test.csproj`): ✅ .NET 8.0
- **Sample App** (`sample/sample.csproj`): ✅ .NET 8.0

**Updated Instructions:**
- Removed .NET 6.0 compatibility workarounds
- Updated framework references to .NET 8.0 LTS throughout
- Added note about SYSLIB0014 warnings (expected with legacy WebRequest usage)
- Streamlined build process documentation for .NET 8.0

The project is now fully upgraded to .NET 8.0 LTS with comprehensive development instructions. Commit: fe19777

### Build & Test Modernization Summary

#### Library (`amadeus.csproj`)
- Removed `docfx.console` PackageReference (eliminated Mono/docfx build failure).
- Set `DebugType` to `portable` to resolve PDB/sourcelink errors.
- Upgraded target framework from `net6.0` to `net8.0`.
- Reverted experimental multi-targeting back to single `net8.0` after `_GetBuildOutputFilesWithTfm` errors.

#### Test Project (`amadeus-test.csproj`)
- Removed legacy NUnit 2 DLL reference and `packages.config`.
- Removed `PlatformTarget x64` (now AnyCPU) to support container architecture.
- Trimmed dependencies to essentials: `Microsoft.NET.Test.Sdk`, `NUnit`, `NUnit3TestAdapter`, `Moq`, `coverlet.collector`.
- Removed unused xUnit packages.
- Unified to single `<TargetFramework>net8.0</TargetFramework>` after installing .NET 8 runtime.

#### Outcomes
- Solution builds successfully (warnings only; mainly XML doc + obsolete API notices).
- NUnit tests discover and run; Live Unit Testing works.
- Eliminated causes of prior silent test discovery failures (conflicting NUnit versions, architecture mismatch, missing runtime).

#### Recommended Next Improvements (Optional)
- Clean malformed XML documentation comments (CS1570/CS1591).
- Replace obsolete `WebRequest` usages (SYSLIB0014) with `HttpClient`.
- Add contributor note about required .NET 8 runtime.





## 3.0.0 - 2023-09-12

- Update frameworks to .Net 6
- Removed Support for end of life COVID-19 Travel Restrictions API

## 2.0.0 - 2022-01-27

- Update frameworks to .Net Core 3.1
- Add support for the APIs in categories Travel Safety and Trip
- Add support for the Flight Offers Search/Price, Flight Create Orders, Flight Order Management and SeatMap Display APIs

## 1.0.0 - 2021-12-16

- The first stable version of the Amadeus for Developers C# SDK
