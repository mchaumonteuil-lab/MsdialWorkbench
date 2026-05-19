# Parallel file processing for CLI

Date: 2026-05-18

## Summary

Added a dedicated console configuration parameter to control how many analysis files are processed in parallel before alignment.

## Configuration

Use this line in the MSDIAL console parameter file:

```txt
Number of parallel files: 4
```

`Number of threads` is still available for internal per-file processing:

```txt
Number of threads: 8
```

## Behavior

- `Number of parallel files` controls the number of samples processed concurrently by the CLI `ProcessRunner`.
- The previous CLI behavior divided `NumThreads` by 2 to decide file-level parallelism.
- The CLI now uses the dedicated `ParallelFileCount` setting directly.
- Values are clamped to at least 1 and no more than the machine processor count.

## Files changed

- `.gitignore`
- `src/MSDIAL5/MsdialCore/Algorithm/ProcessRunner.cs`
- `src/MSDIAL5/MsdialCore/Parameter/ParameterBase.cs`
- `tests/MSDIAL5/MsdialCoreTestApp/Parser/ConfigParser.cs`
- `tests/MSDIAL5/MsdialCoreTestApp/Process/DimsProcess.cs`
- `tests/MSDIAL5/MsdialCoreTestApp/Process/GcmsProcess.cs`
- `tests/MSDIAL5/MsdialCoreTestApp/Process/ImmsProcess.cs`
- `tests/MSDIAL5/MsdialCoreTestApp/Process/LcimmsProcess.cs`
- `tests/MSDIAL5/MsdialCoreTestApp/Process/LcmsProcess.cs`

## Build output

The release build is intended to be published under:

```txt
compilation/bin
```

The `compilation/` folder is ignored by Git.

Published successfully with:

```txt
dotnet publish tests\MSDIAL5\MsdialCoreTestApp\MsdialCoreTestApp.csproj -c "Release vendor unsupported" -f net8 -o compilation\bin
```

The plain `Release` configuration was not usable in this checkout because only the local `RawDataHandler-Vendor-UnSupported` package is present under `Assemblies/`.

## Linux build

Published a Linux x64 framework-dependent build with:

```txt
dotnet publish tests\MSDIAL5\MsdialCoreTestApp\MsdialCoreTestApp.csproj -c "Release vendor unsupported" -f net8 -r linux-x64 --self-contained false -o compilation\bin\linux-x64
```

Linux entrypoint:

```txt
compilation/bin/linux-x64/MSDIALCUI
```

This build requires the .NET 8 runtime on the Linux server.

## Linux self-contained build

The `compilation/bin/linux-x64` folder was cleared and republished as a self-contained Linux x64 build:

```txt
dotnet publish tests\MSDIAL5\MsdialCoreTestApp\MsdialCoreTestApp.csproj -c "Release vendor unsupported" -f net8 -r linux-x64 --self-contained true -o compilation\bin\linux-x64
```

Linux entrypoint:

```txt
compilation/bin/linux-x64/MSDIALCUI
```

This build includes the .NET runtime, so the server does not need a separate .NET 8 runtime installation.
