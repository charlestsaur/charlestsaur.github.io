# C/C++/CUDA Program Compilation Environment on Windows

Thanks to Gemini, Gork, GPT, and Mistral for their support.

Before you begin, make sure all hardware drivers are properly installed. If everything works properly, you shouldn't run into major issues.

## Visual Studio, MSVC

To compile C/C++ on Windows, I personally recommend installing Visual Studio directly.

First, install Visual Studio Community 2026 (or another VS version) from the Visual Studio installer ([https://visualstudio.microsoft.com/downloads](https://visualstudio.microsoft.com/downloads)). Then, in Modify, select and install:

- MSVC Build Tools for x64/x86 (Latest)
- C++ CMake tools for Windows
- Windows 11 SDK (10.0.22621.0)

MSVC: Microsoft Visual C++, abbreviated as MSVC or VC++, is a C/C++ compilation toolchain developed by Microsoft for Windows.

Note: The cl and cmake tools that come with Visual Studio are not in the system PATH.

Next, you should see the following provided by Visual Studio:

- Developer Command Prompt for VS
- Developer PowerShell for VS
- x64 Native Tools Command Prompt for VS
- x64_x86 Cross Tools Command Prompt for VS
- x86 Native Tools Command Prompt for VS
- x86_x64 Cross Tools Command Prompt for VS

Generally, you should use `x64 Native Tools Command Prompt for VS` for compilation because it specifies both the **compiler bitness** and **generator bitness** as 64-bit.

Please note that the **compiler bitness** and **build bitness** specified in `Developer Command Prompt for VS` and `Developer PowerShell for VS` are both 32-bit:

```powershell
**********************************************************************
** Visual Studio 2026 Developer PowerShell v18.2.1
** Copyright (c) 2025 Microsoft Corporation
**********************************************************************
PS C:\Users\---\source\repos> Get-Command cl.exe

CommandType   Name     Version     Source
-----------   ----     -------     ------
Application   cl.exe   14.50.3...  C:\Program Files\Microsoft Visual Studio\18\Community\VC\Tools\MSVC\14.50.35717\bin\HostX86\x86\cl.exe
```

```cmd
**********************************************************************
** Visual Studio 2026 Developer Command Prompt v18.2.1
** Copyright (c) 2025 Microsoft Corporation
**********************************************************************
[DEBUG:ext\vcvars.bat] Found potential v145 version file: 'Microsoft.VCToolsVersion.VC.14.50.18.0.txt'
[DEBUG:ext\vcvars.bat] Testing v145 version file: 'Microsoft.VCToolsVersion.VC.14.50.18.0.txt'

C:\Program Files\Microsoft Visual Studio\18\Community>where cl.exe
C:\Program Files\Microsoft Visual Studio\18\Community\VC\Tools\MSVC\14.50.35717\bin\Hostx86\x86\cl.exe
```

This is because Visual Studio provides four host/target combinations:

```powershell
PS C:\Program Files\Microsoft Visual Studio\18\Community\VC\Tools\MSVC\14.50.35717\bin> tree
.
├───Hostx64
│   ├───x64
│   └───x86
└───Hostx86
    ├───x64
    └───x86
```

| Path        | Compiler Bitness (Host) | Build Bitness (Target) |
| ----------- | ----------------------- | ---------------------- |
| HostX86\x86 | 32-bit (x86)            | 32-bit (x86)           |
| HostX64\x64 | 64-bit (x64)            | 64-bit (x64)           |
| HostX64\x86 | 64-bit (x86)            | 32-bit (x86)           |
| HostX86\x64 | 32-bit (x64)            | 64-bit (x64)           |

What if you want to use the HostX64\x64 version of cl via PowerShell? Open the two automatically added Visual Studio configurations shown by the down arrow at the top of the terminal window:

- Developer Command Prompt for VS 18
- Developer PowerShell for VS 18

These two terminal configurations use the HostX64/x64 version of cl. If you don't have it, you can add it manually:

In the newly created terminal configuration, enter the following in the "Command Line" option:

```plaintext
powershell.exe -NoExit -Command "&{Import-Module """C:\Program Files\Microsoft Visual Studio\18\Community\Common7\Tools\Microsoft.VisualStudio.DevShell.dll"""; Enter-VsDevShell 07e99a8b -SkipAutomaticLocation -DevCmdArguments """-arch=x64 -host_arch=x64"""}"
```

Or use CMD:

```plaintext
cmd.exe /k "C:\Program Files\Microsoft Visual Studio\18\Community\Common7\Tools\VsDevCmd.bat" -startdir=none -arch=x64 -host_arch=x64
```

The startup directory doesn't matter. The default is:

```plaintext
C:\Program Files\Microsoft Visual Studio\18\Community
```

You can install multiple versions of Visual Studio on the same machine. They operate independently. For example:

```powershell
PS C:\Program Files\Microsoft Visual Studio> tree
.
├───18
|   ├───Community
|   └───Insiders
└───17
    ├───Community
    └───Insiders
```

However, it's generally unnecessary to use multiple versions of Visual Studio IDE. If you need an older version of VS, you usually only need an older version of MSVC. In this case, you only need to install the older version of MSVC through the VS Installer. That's it, and it's also independent of other versions.

## Other Compiler Toolchains

GCC and LLVM are both cross-platform C/C++ toolchains, and they are both compatible with Windows.

However, if your program needs to use the Windows SDK or needs to be compatible with the MSVC ABI, then do not use GCC (MinGW-w64), because GCC primarily focuses on cross-platform code consistency, not cross-platform compatibility.

Clang works similarly, but Clang has its own dedicated Clang-cl (which uses the MSVC linker).

| Project              | clang-cl                                 | clang, gcc               |
| -------------------- | ---------------------------------------- | ------------------------ |
| ABI                  | MSVC ABI                                 | GCC ABI                  |
| STL                  | MSVC STL (`<vector>`)                    | libstdc++                |
| MSVC `.lib`          | ✅ Fully compatible                     | ❌                       |
| Pre-compiled library | Can be used directly in the VS ecosystem | Basically not compatible |

LLVM can be installed directly through the VS Installer:

- C++ Clang Compiler for Windows (20.1.8)
- MSBuild support for LLVM (clang-cl) toolset

It's best to have these beforehand:

- MSVC Build Tools for x64/x86 (Latest)
- C++ CMake tools for Windows
- Windows 11 SDK (10.0.22621.0)

GCC (MinGW-w64) can be installed via MSYS2, etc.

MinGW is a 32-bit version and was largely discontinued around 2016, so its derivative project, MinGW-w64, is generally used now.

## NVIDIA GPU Driver

The version is usually checked using `nvidia-smi`, which typically displays:

```plaintext
--- ---  - --:--:-- 2026
+-----------------------------------------------------------------------------------------+
| NVIDIA-SMI 591.74                 Driver Version: 591.74         CUDA Version: 13.1     |
+-----------------------------------------+------------------------+----------------------+
| GPU  Name                  Driver-Model | Bus-Id          Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |           Memory-Usage | GPU-Util  Compute M. |
|                                         |                        |               MIG M. |
|=========================================+========================+======================|
|   0  NVIDIA GeForce RTX ----      WDDM  |   00000000:02:00.0  On |                  N/A |
|  0%   35C    P0             --W /  ---W |    ----MiB /  -----MiB |      -%      Default |
|                                         |                        |                  N/A |
+-----------------------------------------+------------------------+----------------------+

+-----------------------------------------------------------------------------------------+
| Processes:                                                                              |
|  GPU   GI   CI              PID   Type   Process name                        GPU Memory |
|        ID   ID                                                               Usage      |
|=========================================================================================|
|    0   N/A  N/A            5016    C+G   C:\Windows\explorer.exe               N/A      |
+-----------------------------------------------------------------------------------------+
```

NVIDIA-SMI refers to the version of the NVIDIA-SMI tool, while Driver Version refers to the version of the NVIDIA GPU driver.

Generally, the NVIDIA-SMI version and Driver Version should match, as NVIDIA-SMI is a tool released along with the driver.

Please note that CUDA Version: 13.1 here does not mean you have CUDA Toolkit version 13.1 installed, but rather refers to the highest CUDA Toolkit version currently supported by the driver.

Use the latest NVIDIA driver. You can update it through NVIDIA App. For development, the Studio version is recommended.

## CUDA Toolkit

The official download address for CUDA Toolkit is: [https://developer.nvidia.com/cuda-downloads](https://developer.nvidia.com/cuda-downloads)

CUDA Toolkit is specifically installed in `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v13.1`. If it is not automatically added to the system PATH, you may need to add it manually.

NVCC is one of the core compilation tools in CUDA Toolkit, short for NVIDIA CUDA Compiler Driver.

NVCC is primarily responsible for splitting `.cu` / `.cpp` files into:

- Host code (the part running on the CPU): ordinary C/C++ code + parts calling the CUDA API
- Device code (the kernel running on the GPU): functions decorated with `__global__`, `__device__`, etc.

Then, they are handed over to different backends for compilation:

- Host code → forwarded to the system's existing C++ compiler (usually cl.exe/MSVC on Windows, and g++/clang on Linux and Mac)
- Device code → handed over to NVIDIA's proprietary compiler chain (including frontend parsing → PTX generation → ptxas assembly into cubin/fatbinary)

Using `nvcc --version` to check the NVCC version typically displays:

```plaintext
nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2025 NVIDIA Corporation
Built on Fri_Nov__7_19:25:04_Pacific_Standard_Time_2025
Cuda compilation tools, release 13.1, V13.1.80
Build cuda_13.1.r13.1/compiler.36836380_0
```

For detailed version information, see: `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v13.1\version.txt`

Multiple CUDA Toolkit versions can be installed on a single machine; they are independent of each other. For example:

```powershell
PS C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA> tree
.
├───v13.1
│   ├───bin
|   │   ├───crt
|   │   └───x64
|   ...
└───v12.8
    ...
```

## CUDA With MSVC

After installing the CUDA Toolkit, if you are using the latest Visual Studio 18 2026 (and the latest MSVC), you will encounter the following errors during compilation:

"No CUDA toolset found." and "-- Configuring incomplete, errors occurred!" These are errors indicating that the CUDA toolset is not found and the configuration is incomplete.

Even if CUDA Toolkit 13.1 is installed, Visual Studio still requires specific project file templates
 (`.targets` and `.props`) to recognize and compile `.cu` files. CUDA Toolkit 13.1 does not yet support Visual Studio 18 2026 (v180), so we need to manually copy the CUDA Toolkit's MSBuild integration files:

1. First, go to the directory `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v13.1\extras\visual_studio_integration\MSBuildExtensions`, which should contain:

   ```plaintext
   CUDA 13.1.props
   CUDA 13.1.targets
   CUDA 13.1.Version.props
   CUDA 13.1.xml
   Nvda.Build.CudaTasks.v13.1.dll
   ```

2. Copy these files.

3. Paste them into `C:\Program Files\Microsoft Visual Studio\18\Community\MSBuild\Microsoft\VC\v180\BuildCustomizations`.

## CUDA Code Compilation

A simple example of compiling a C/C++ project containing CUDA code.

First, create and enter the `build` directory:

```powershell
mdir build

cd build
```

Then, let CMake output the project build configuration:

```powershell
cmake -DGGML_CUDA=ON -DCMAKE_CUDA_ARCHITECTURES=120 -DCMAKE_CUDA_FLAGS="-allow-unsupported-compiler" -DCMAKE_BUILD_TYPE=Release ..
```

- `-DGGML_CUDA=ON`: Enables CUDA support
- `-DCMAKE_CUDA_ARCHITECTURES=120`: Specifies the target CUDA architecture. For details, please refer to [https://developer.nvidia.com/cuda/gpus](https://developer.nvidia.com/cuda/gpus)
- `-DCMAKE_CUDA_FLAGS="-allow-unsupported-compiler"`: Allows unsupported host compilers. CUDA has strict limitations on GCC/LLVM/MSVC (and VS) versions. Generally, bypassing this is not a problem, but caution should be exercised when using it in production environments.
- `-DCMAKE_BUILD_TYPE=Release`: Enables optimized compilation. Common modes in CMake include:

| Type           | Meaning                                 |
| -------------- | --------------------------------------- |
| Debug          | With debug symbols, no optimization     |
| Release        | High optimization, no debug information |
| RelWithDebInfo | Optimized + debug information           |

Finally, compile:

```powershell
cmake --build . --config Release --parallel
```

- `--build`: Start compilation
- `--config` `Release`: This parameter is invalid for single-configuration generators, but is required for multi-configuration generators. It can be included for convenience; otherwise, it will be automatically ignored.
- `--parallel`: Compile using the maximum number of available CPU cores.

## PyTorch

Using PyTorch does not require the CUDA Toolkit, only the NVIDIA GPU driver.

Do not directly use `pip install torch`, otherwise it will only install the CPU version of PyTorch.

You need to manually specify the whl during installation:

```bash
pip install torch --index-url https://download.pytorch.org/whl/cu130
```

The CUDA version here is usually ≤ the CUDA version output by your `nvidia-smi`.

For details, please refer to the PyTorch official website: [https://pytorch.org/get-started/locally](https://pytorch.org/get-started/locally)

Then you can verify it:

```python
import torch
print(torch.cuda.is_available())     # Confirm whether CUDA is available, should output True
print(torch.version.cuda)            # Display the CUDA version when your PyTorch was compiled
print(torch.__version__)             # Display the complete version information of PyTorch
print(torch.cuda.device_count())     # Display the number of CUDA devices (GPUs) that can be recognized and used by PyTorch on the current machine
if torch.cuda.is_available():        # Loop through and display information about all GPUs recognized by PyTorch
    for i in range(torch.cuda.device_count()):
        print(f"GPU {i}:", torch.cuda.get_device_name(i))    # Display your GPU name
        props = torch.cuda.get_device_properties(i)
        print(f"    {props.total_memory / 1024**3:.2f} GB")  # Display your GPU's total memory
        print(f"    {props.major}.{props.minor}")            # Display your GPU's architecture version number (Compute Capability)
```
