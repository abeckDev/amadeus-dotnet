# Amadeus .NET SDK

Amadeus .NET SDK is a C# library that provides access to the Amadeus Self-Service travel APIs. It targets .NET 8.0 LTS and is distributed as a NuGet package.

Always reference these instructions first and fallback to search or bash commands only when you encounter unexpected information that does not match the info here.

## Working Effectively

### Bootstrap, build, and test the repository:

**IMPORTANT**: The docfx.console package has compatibility issues with mono on Linux. Use the alternative build method below.

### Recommended build process (without documentation generation):
- Restore dependencies: `dotnet restore amadeus-csharp.sln` -- takes 20 seconds initially. NEVER CANCEL. Set timeout to 60+ seconds.
- Build without docfx: Comment out the docfx.console package reference in `amadeus/amadeus.csproj` (see below)
- Build solution: `dotnet build amadeus-csharp.sln --configuration Debug --no-restore` -- takes 3-6 seconds. NEVER CANCEL. Set timeout to 120+ seconds.  
- Run unit tests: `dotnet test amadeus-test/amadeus-test.csproj --configuration Debug --no-build --no-restore` -- takes 2 seconds, 32 tests. NEVER CANCEL. Set timeout to 120+ seconds.

### Disable documentation generation (required for Linux builds):
- Comment out the docfx.console package reference in `amadeus/amadeus.csproj`:
  ```xml
  <!-- <PackageReference Include="docfx.console" Version="2.59.4"><PrivateAssets>all</PrivateAssets>
  <IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>
  </PackageReference> -->
  ```
- Then run standard build commands above

### .NET Runtime Requirements:
- Project targets .NET 8.0 LTS
- All projects have been upgraded to target .NET 8.0 for long-term support
- Build process is optimized for .NET 8.0 runtime

## Validation

### Always run these validation steps after making changes:
1. **Build validation**: Ensure `dotnet build amadeus-csharp.sln --configuration Debug` succeeds
2. **Unit test validation**: Run `dotnet test amadeus-test/amadeus-test.csproj` and verify all 32 tests pass
3. **Sample app validation**: Run `dotnet run --project sample/sample.csproj` - it should start but fail authentication (expected without valid API credentials)
4. **Integration test structure**: Run `dotnet test amadeus-integration-test/amadeus-integration-test.csproj` - tests will fail due to authentication but should not have compilation errors

### Manual validation scenarios:
- **SDK initialization**: Create a test that initializes `Amadeus.builder("test", "test").build()` without throwing compilation errors
- **Basic API structure**: Verify you can access `amadeus.referenceData.locations` and similar API path structures
- **Configuration**: Test environment variable loading with `AMADEUS_CLIENT_ID` and `AMADEUS_CLIENT_SECRET`

### CRITICAL BUILD NOTES:
- **NEVER CANCEL** the `dotnet build` command even if it takes 2-3 minutes - docfx generation can be slow
- **NEVER CANCEL** the `dotnet restore` command - NuGet package resolution can take 30+ seconds
- The docfx.console package requires mono runtime and will fail without it on Linux systems
- Build warnings about XML documentation are normal and expected

## Common Tasks

### Run the sample application:
- Navigate to repository root
- Run: `dotnet run --project sample/sample.csproj`
- Expected behavior: Application starts, attempts API call, fails with authentication error (normal without valid credentials)

### Test API credentials (requires valid Amadeus developer account):
- Set environment variables: `export AMADEUS_CLIENT_ID="your_key"` and `export AMADEUS_CLIENT_SECRET="your_secret"`
- Run sample or integration tests - they should succeed with valid credentials

### Common build issues and solutions:
1. **"mono command not found" OR "docfx.exe exited with code 1"**: Comment out docfx.console package reference in `amadeus/amadeus.csproj` (see instructions above)
2. **Package restore fails**: Ensure internet connectivity and run `dotnet restore` with longer timeout (60+ seconds)
3. **Build warnings about XML documentation**: These are normal and expected, build will succeed
4. **SYSLIB0014 warnings about WebRequest**: These are expected due to legacy API usage and do not affect functionality

### Automated validation script:
For comprehensive validation of all build steps, create and run this script:
```bash
# Create validation script that handles .NET runtime compatibility automatically
# and validates: restore, build, unit tests, and sample app execution
# Available at: /tmp/validate-sdk.sh (created during setup)
```

## Key Projects in this Codebase

### `amadeus/` - Main SDK Library
- Core Amadeus SDK implementation
- Targets .NET 8.0 LTS, builds to `amadeus-dotnet` NuGet package
- Entry point: `Amadeus.cs` with builder pattern
- API structure mirrors Amadeus REST API paths

### `amadeus-test/` - Unit Tests
- NUnit-based unit tests (32 tests total)
- Tests SDK functionality without requiring API credentials
- Focuses on configuration, request building, and SDK initialization

### `amadeus-integration-test/` - Integration Tests  
- xUnit-based integration tests that make real API calls
- Requires valid Amadeus API credentials via environment variables
- Will fail without proper credentials (expected behavior)

### `sample/` - Example Application
- Console application demonstrating SDK usage
- Shows basic API calls and error handling
- Good reference for SDK implementation patterns

## Solution Structure

```
amadeus-csharp.sln          # Main solution file
├── amadeus/                 # Main SDK project
├── amadeus-test/           # Unit tests (NUnit)
├── amadeus-integration-test/ # Integration tests (xUnit)
└── sample/                 # Sample console application
```

## Timing Expectations

- **Restore**: 15-30 seconds initially, <5 seconds on subsequent runs
- **Build**: 3-10 seconds for incremental builds, up to 60 seconds for full rebuild with documentation
- **Unit tests**: 2-5 seconds for full test suite
- **Integration tests**: 1-3 seconds (though they fail without credentials)

Always use timeouts of 120+ seconds for build commands and 60+ seconds for test commands to avoid premature cancellation.