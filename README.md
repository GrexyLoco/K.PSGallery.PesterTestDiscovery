# K.PSGallery.PesterTestDiscovery

[![PowerShell](https://img.shields.io/badge/PowerShell-5.1%2B-blue?logo=powershell)](https://docs.microsoft.com/en-us/powershell/)
[![Pester](https://img.shields.io/badge/Pester-5.0%2B-green?logo=powershell)](https://pester.dev/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

Intelligent PowerShell module for Pester test discovery using strict naming conventions for reliable CI/CD integration.

## 🚀 Key Features

- **🔍 Smart Discovery**: Auto-finds tests using fixed conventions
- **⚡ High Performance**: Early validation prevents hanging on invalid paths  
- **🎯 Multiple Formats**: Object, JSON, and GitHub Actions output
- **🛡️ Robust**: Graceful error handling and detailed reporting
- **📏 Strict**: Non-configurable patterns ensure consistency

## 📋 Conventions & Patterns

### Fixed Naming Rules
| Type | Valid | Invalid |
|------|-------|---------|
| **Directories** | `Test`, `Tests` | `UnitTests`, `TestSuite`, `MyTests` |
| **Files** | `*.Test.ps1`, `*.Tests.ps1` | `*.UnitTest.ps1`, `*Test.ps1` |

### Recommended Structure
```powershell
MyProject/
├── src/MyModule.psm1
└── Tests/                    ✅ Single test directory (recommended)
    ├── MyModule.Tests.ps1    ✅ Valid test file
    └── Feature.Test.ps1      ✅ Valid test file
```

### ⚠️ Multiple Directory Warning
The module detects and warns about multiple test directories:
```powershell
⚠️ Multiple test directories found - consider consolidating
⚠️    Found: C:\MyProject\Tests
⚠️    Found: C:\MyProject\src\Test
```

## 📦 Installation & Quick Start

```powershell
# From source (development)
Import-Module .\K.PSGallery.PesterTestDiscovery\K.PSGallery.PesterTestDiscovery.psd1

# Basic discovery
$result = Invoke-TestDiscovery
Write-Host "Found $($result.TestFilesCount) test files"

# GitHub Actions optimized
$result = Invoke-TestDiscovery -OutputFormat 'GitHubActions'
```

## � Core Functions

| Function | Purpose |
|----------|---------|
| `Invoke-TestDiscovery` | Main discovery with comprehensive options |
| `Get-TestDirectories` | Find test directories (fixed patterns) |
| `Find-TestFiles` | Locate test files in directories |
| `Confirm-ValidTestDirectory` | Validate directory names (`Test`/`Tests` only) |
| `Confirm-ValidTestFile` | Validate file patterns (`.Test.ps1`/`.Tests.ps1` only) |

## 💡 Usage Examples

### Basic Discovery
```powershell
# Auto-discover from current directory
$result = Invoke-TestDiscovery
$result.ValidationResults.ConventionsFollowed  # $true/$false

# Explicit path with custom depth
$result = Invoke-TestDiscovery -TestPath './Tests' -MaxDepth 3

# With exclusions
$result = Invoke-TestDiscovery -ExcludePaths @('bin', 'obj') -Detailed
```

### Component Usage
```powershell
# Individual function usage
Confirm-ValidTestDirectory -DirectoryName 'Tests'        # $true
Confirm-ValidTestFile -FileName 'MyModule.Tests.ps1'     # $true

$testDirs = Get-TestDirectories -MaxDepth 5
$testFiles = Find-TestFiles -TestDirectories $testDirs
```

### CI/CD Integration
```yaml
# GitHub Actions
- name: Discover & Run Tests
  shell: pwsh
  run: |
    $result = Invoke-TestDiscovery -OutputFormat 'GitHubActions'
    if ($env:test-files-count -gt 0) { Invoke-Pester -Path $env:discovered-paths }
```

## 📤 Output Formats & Configuration

### Object Output (Default)
```powershell
@{
    DiscoveryMode = 'AutoDiscovery'|'Explicit'
    TestDirectories = @(...)             # DirectoryInfo objects
    TestFiles = @(...)                   # FileInfo objects  
    TestDirectoriesCount = 2
    TestFilesCount = 5
    DiscoveredPaths = @('Tests', 'src/Test')
    ValidationResults = @{
        HasValidDirectories = $true
        HasValidFiles = $true
        ConventionsFollowed = $true
    }
    Metadata = @{
        SearchDepth = 5
        ValidDirectoryNames = @('Test', 'Tests')      # Fixed
        ValidFilePatterns = @('*.Test.ps1', '*.Tests.ps1')  # Fixed
        Timestamp = '2025-08-16 10:30:45'
    }
}
```

### GitHub Actions Variables
When using `-OutputFormat 'GitHubActions'`:
- `test-path-exists`: 'true'/'false'
- `discovered-paths`: Semicolon-separated paths
- `test-files-count`: Number of test files
- `conventions-followed`: 'true'/'false'

### Configuration Settings
```powershell
# Fixed (non-configurable)
$ValidTestDirectoryNames = @('Test', 'Tests')
$ValidTestFilePatterns = @('*.Test.ps1', '*.Tests.ps1')

# Configurable  
$DefaultMaxDepth = 5
$DefaultExcludePaths = @('bin', 'obj', 'packages', '.github')
```

## 🛡️ Performance & Error Handling

- **Fast Path Validation**: `Test-Path` checks prevent expensive operations on invalid paths
- **No Hanging**: Returns empty results in ~20ms for non-existent paths
- **Memory Efficient**: Streams results without loading entire directory trees
- **Graceful Fallbacks**: Continues when directories are inaccessible

## 🧪 Testing & Contributing

```powershell
# Run tests with coverage
Invoke-Pester -Path './Tests/' -CodeCoverage './K.PSGallery.PesterTestDiscovery.psm1'
```

**Contributing**: Fork → Feature branch → Add tests → PR

## 📄 License & Versioning

**License**: MIT | **Version**: 1.0.0 | **Versioning**: [Semantic](https://semver.org/)

## 🙏 Credits

[Pester](https://pester.dev/) • [PowerShell Community](https://github.com/PowerShell/PowerShell) • [GitHub Actions](https://github.com/features/actions)
