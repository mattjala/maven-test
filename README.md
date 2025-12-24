# Maven Enforcer Plugin Test - Spaces in Paths

This is a minimal test case to verify Maven Enforcer Plugin's `requireFilesExist` rule behavior with spaces in directory paths across different operating systems.

## Test Setup

- **Two test directories:**
  - `test dir with spaces/` - Directory name containing spaces
  - `test-dir-no-spaces/` - Directory name without spaces

- **Configuration:**
  - `pom.xml` - Loads paths from `build.properties` and checks both directories exist
  - `build.properties` - Created dynamically by CI with platform-native path separators

## Platform-Specific Path Formats

- **Linux/macOS**: Forward slashes (`/`) in `build.properties`
- **Windows**: Backslashes (`\`) in `build.properties`

## Expected Behavior

Both directories should be found successfully on all platforms, as:
1. Both directories exist (verified before Maven runs)
2. The paths are read from `build.properties` correctly
3. Spaces in paths are valid on all operating systems

## Running Locally

```bash
# Create build.properties for your platform
# Linux/macOS:
cat > build.properties << EOF
dir.with.spaces=$(pwd)/test dir with spaces
dir.no.spaces=$(pwd)/test-dir-no-spaces
EOF

# Windows PowerShell:
@"
dir.with.spaces=$(pwd -Path)\test dir with spaces
dir.no.spaces=$(pwd -Path)\test-dir-no-spaces
"@ | Out-File -FilePath build.properties -Encoding ASCII

# Run the test
mvn verify
```

## Purpose

This isolates the issue reported in HDFView where Maven's enforcer plugin fails to find directories with spaces in their paths on Windows, even when:
- The directory exists
- The path is correctly formatted
- Forward slashes are used in the configuration

If this test fails on Windows but passes on Linux/macOS, it confirms a platform-specific bug in the enforcer plugin's path handling.
