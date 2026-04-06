# Windows Build Guide – VSCode Spectral Edition

Complete guide for building the Spectral Edition on Windows using MinGW64/GCC, NASM, cmake-js, and WiX 3.14.

---

## Prerequisites

### Required

| Tool | Version | Download | MSYS2 package |
|---|---|---|---|
| [Node.js](https://nodejs.org) | 18 LTS+ | nodejs.org | — |
| [MSYS2](https://www.msys2.org/) | latest | msys2.org | — |
| MinGW64 GCC | 12+ | via MSYS2 | `mingw-w64-x86_64-gcc` |
| NASM | 2.16+ | via MSYS2 or [nasm.us](https://nasm.us) | `mingw-w64-x86_64-nasm` |
| CMake | 3.18+ | via MSYS2 or cmake.org | `mingw-w64-x86_64-cmake` |
| cmake-js | 7+ | `npm install -g cmake-js` | — |

### Optional (for MSI/EXE installer)

| Tool | Version | Download |
|---|---|---|
| [WiX 3.14](https://github.com/wixtoolset/wix3/releases/tag/wix3141rtm) | 3.14.1 | GitHub Releases |
| Windows SDK (signtool) | 10+ | Visual Studio Installer |

---

## MSYS2 Setup

```bash
# 1. Install MSYS2 from https://www.msys2.org/
# 2. Open "MSYS2 MinGW x64" terminal (NOT the MSYS2 MSYS terminal)

# Update package database
pacman -Syu

# Install build tools
pacman -S mingw-w64-x86_64-gcc \
           mingw-w64-x86_64-g++ \
           mingw-w64-x86_64-nasm \
           mingw-w64-x86_64-cmake \
           mingw-w64-x86_64-make \
           git

# Verify
x86_64-w64-mingw32-g++ --version   # should print GCC 12+
nasm --version                      # should print NASM 2.16+
cmake --version                     # should print 3.18+
```

Add `C:\msys64\mingw64\bin` to your Windows `PATH` so PowerShell can find `x86_64-w64-mingw32-g++` and `nasm`.

---

## NASM Standalone (alternative to MSYS2)

1. Download NASM 2.16+ from https://nasm.us/pub/nasm/releasebuilds/
2. Install to `C:\nasm\` (or any path)
3. Add to `PATH` or set `-NasmDir C:\nasm` when calling setup.ps1

---

## cmake-js

```powershell
npm install -g cmake-js
cmake-js --version   # verify
```

---

## Build Everything

```powershell
# Clone
git clone https://github.com/The-Spectral-Operator/VSCode-Spectral-Edition.git
cd VSCode-Spectral-Edition

# Full build (auto-discovers MinGW64/NASM from PATH or common locations)
.\setup.ps1

# With explicit paths
.\setup.ps1 -MingwDir "C:\msys64\mingw64\bin" -NasmDir "C:\nasm"
```

This will:
1. Download VSCode 1.109.5 source
2. Apply Spectral Edition patches
3. Build `lacky_neural_engine.node` via cmake-js + MinGW64 + NASM
4. Compile TypeScript for spectral-extension and stonedrift-mesh
5. Pull all Ollama models (if Ollama is running)

---

## Native Module Build (manual)

```powershell
cd extensions\lacky-neural-engine
npm install --legacy-peer-deps

# Using cmake-js (recommended)
$env:CC  = "x86_64-w64-mingw32-gcc"
$env:CXX = "x86_64-w64-mingw32-g++"
$env:NASM = "nasm"

cmake-js build `
  --arch x64 `
  --CDCMAKE_TOOLCHAIN_FILE="toolchain-mingw64.cmake" `
  --CDCMAKE_BUILD_TYPE=Release `
  --generator "MinGW Makefiles"

# Output: build/Release/lacky_neural_engine.node
```

The `.node` file is a native Windows DLL renamed with the `.node` extension. It is statically linked (`-static-libgcc -static-libstdc++`) so no MSYS2 DLLs need to be distributed.

---

## Build the Installer (MSI + EXE)

### 1. Install WiX 3.14

Download from https://github.com/wixtoolset/wix3/releases/tag/wix3141rtm  
Install or extract to `C:\Program Files (x86)\WiX Toolset v3.14\`

### 2. Build Custom Action DLL

The custom action DLL (`spectral_ca.dll`) must be built before the MSI:

```powershell
cd installer\ca
mkdir build && cd build
cmake .. -G "MinGW Makefiles" `
  -DCMAKE_TOOLCHAIN_FILE="..\..\extensions\lacky-neural-engine\toolchain-mingw64.cmake" `
  -DCMAKE_BUILD_TYPE=Release
cmake --build . --config Release
# Output: build\Release\spectral_ca.dll
```

Or the build script does this automatically.

### 3. Build MSI + EXE

```powershell
cd installer
.\build-installer.ps1

# With explicit WiX path
.\build-installer.ps1 -WixDir "C:\Program Files (x86)\WiX Toolset v3.14\bin"

# MSI only (no EXE bundle)
.\build-installer.ps1 -MsiOnly

# With code signing (requires PFX certificate)
.\build-installer.ps1 -SignCert "cert.pfx"
```

Outputs in `installer\output\`:
- `SpectralEdition-3.7.0.msi`
- `SpectralEditionSetup-3.7.0.exe`

### 4. Test the Installer

```powershell
# Test MSI (verbose log)
msiexec /i installer\output\SpectralEdition-3.7.0.msi /l*v install.log

# Test EXE
installer\output\SpectralEditionSetup-3.7.0.exe /l install.log

# Silent install
msiexec /i SpectralEdition-3.7.0.msi /qn INSTALLFOLDER="C:\SpectralEdition"

# Uninstall
msiexec /x SpectralEdition-3.7.0.msi /qn
```

---

## File Harvesting with Heat (production builds)

For production MSI, use WiX `heat.exe` to automatically harvest all compiled extension files:

```powershell
$WixBin = "C:\Program Files (x86)\WiX Toolset v3.14\bin"

# Harvest spectral-extension dist
& "$WixBin\heat.exe" dir dist\spectral-extension `
  -cg CG_SpectralExtensionFiles `
  -dr SPECTRALEXTDIR `
  -gg -scom -sreg -sfrag -srd -ke `
  -out installer\fragments\SpectralExtensionFiles.wxs

# Harvest stonedrift-mesh dist
& "$WixBin\heat.exe" dir dist\stonedrift-mesh `
  -cg CG_StonedriftMeshFiles `
  -dr STONEEXTDIR `
  -gg -scom -sreg -sfrag -srd -ke `
  -out installer\fragments\StonedriftMeshFiles.wxs
```

The `build-installer.ps1` script automatically picks up any `.wxs` files in `installer\fragments\`.

---

## GUID Management

WiX requires stable GUIDs for upgrade logic. Before distributing publicly:

1. Generate a real UpgradeCode:
   ```powershell
   [guid]::NewGuid()
   ```
2. Replace the placeholder GUID in `installer/wxs/Product.wxs` (UpgradeCode attribute)
3. Replace the placeholder GUID in `installer/wxs/Bundle.wxs` (UpgradeCode attribute)
4. Generate and replace all Component GUIDs in `installer/wxs/Components.wxs`

The `Product Id="*"` auto-generates a new product GUID per build (correct behavior for major upgrades).

---

## Troubleshooting

### `cmake-js: The system cannot find the file specified`
→ cmake-js cannot find cmake. Add CMake to PATH: `C:\msys64\mingw64\bin` or `C:\Program Files\CMake\bin`

### `NASM: command not found`
→ Set `-NasmDir` or add NASM to PATH. Verify: `nasm --version`

### `error: 'node_api.h' not found`
→ cmake-js should provide this automatically. Try: `npm install -g cmake-js` (ensure global install)

### WiX `candle: error CNDL0102: File not found`
→ Check that `dist\` directory is populated before running `build-installer.ps1`. Run `.\setup.ps1 -BuildOnly` first.

### `spectral_ca.dll: undefined reference to MsiGetProperty`
→ Ensure `-lmsi` is in the link command. The `ca/CMakeLists.txt` includes this.

### The `.node` file crashes on load
→ Ensure you're running 64-bit Node.js and the `.node` was built for x64. Check: `node -e "console.log(process.arch)"` → should print `x64`
