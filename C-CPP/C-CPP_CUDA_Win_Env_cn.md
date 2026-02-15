# Windows 上的 C/C++/CUDA 程序编译环境

感谢 Gemini、Gork、GPT、Mistral 的支持。

开始之前请确保电脑所有硬件的驱动已经全部装好，一般来说能够正常使用就没多大问题。

## Visual Studio、MSVC

想要在 Windows 上编译 C/C++，个人建议直接安装 Visual Studio.

在 Visual Studio installer（[https://visualstudio.microsoft.com/downloads](https://visualstudio.microsoft.com/downloads)）中先安装 Visual Studio Community 2026（或其他 VS 版本），然后在 Modify 中勾选并安装：

- MSVC Build Tools for x64/x86 (Latest)
- C++ CMake tools for Windows
- Windows 11 SDK (10.0.22621.0)

MSVC：Microsoft Visual C++，简称 MSVC 或 VC++，是微软开发的在 Windows 上的 C/C++ 编译工具链（compiler toolchain）.

注意：Visual Studio 自带的 cl、cmake 不在系统 PATH 中。

接着你应该能看到 Visual Studio 提供的：

- Developer Command Prompt for VS
- Developer PowerShell for VS
- x64 Native Tools Command Prompt for VS
- x64_x86 Cross Tools Command Prompt for VS
- x86 Native Tools Command Prompt for VS
- x86_x64 Cross Tools Command Prompt for VS

这其中一般来说使用 `x64 Native Tools Command Prompt for VS` 进行编译，因为它指定的 cl **编译器位数** 和 **生成程序位数** 都是 64 位。

请注意 `Developer Command Prompt for VS` 和 `Developer PowerShell for VS` 中指定的 cl **编译器位数** 和 **生成程序位数** 都是 32 位：

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

具体来说这是因为 VS 针对不同的开发需求提供了四种组合（架构交叉编译）：

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

| 路径        | 编译器位数 (Host) | 生成程序位数 (Target) |
|-------------|------------------|----------------------|
| HostX86\x86 | 32-bit (x86)     | 32-bit (x86)         |
| HostX64\x64 | 64-bit (x64)     | 64-bit (x64)         |
| HostX64\x86 | 64-bit (x86)     | 32-bit (x86)         |
| HostX86\x64 | 32-bit (x64)     | 64-bit (x64)         |

如果你想通过 PowerShell 使用 HostX64\x64 版的 cl 怎么办？打开终端窗口顶部的下箭头中两个 Visual Studio 自动添加的配置：

- Developer Command Prompt for VS 18
- Developer PowerShell for VS 18

这两个终端配置是使用的 HostX64\x64 版 cl，如果你没有，那可以手动添加：

在终端新建的配置中的“命令行”选项中输入：

```plaintext
powershell.exe -NoExit -Command "&{Import-Module """C:\Program Files\Microsoft Visual Studio\18\Community\Common7\Tools\Microsoft.VisualStudio.DevShell.dll"""; Enter-VsDevShell 07e99a8b -SkipAutomaticLocation -DevCmdArguments """-arch=x64 -host_arch=x64"""}"
```

或使用 CMD 的：

```plaintext
cmd.exe /k "C:\Program Files\Microsoft Visual Studio\18\Community\Common7\Tools\VsDevCmd.bat" -startdir=none -arch=x64 -host_arch=x64
```

启动目录无所谓，官方给的是：

```plaintext
C:\Program Files\Microsoft Visual Studio\18\Community
```

在一台机器上可以存在多个版本的 VS，它们彼此相互独立，比如：

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

但是一般没有必要使用多个版本的 Visual Studio IDE，需要旧版的 VS 一般只是需要旧版的 MSVC，那么这个时候只需要在 VS Installer 中安装旧版的 MSVC 即可，并且也是和其他版本相互独立的。

## 其他编译工具链

GCC 和 LLVM 都是跨平台 C/C++ 工具链，它们都兼容 Windows.

但是如果你的程序需要用到 Windows SDK，或需要与 MSVC ABI 兼容，那就不要用 GCC（MinGW-w64），因为 GCC 主要是代码的全平台一致，而不是全平台兼容。

Clang 也一样，但是 Clang 有专门的 Clang-cl（其使用 MSVC 的链接器）

| 项目        | clang-cl              | clang、gcc    |
| ----------- | --------------------- | ------------- |
| ABI         | MSVC ABI              | GCC ABI       |
| STL         | MSVC STL (`<vector>`) | libstdc++     |
| MSVC `.lib` | ✅ 完全兼容           | ❌            |
| 预编译库     | VS 生态可直接用        | 基本不行       |

安装 LLVM 可以直接通过 VS Installer 安装：

- C++ Clang Compiler for Windows (20.1.8)
- MSBuild support for LLVM (clang-cl) toolset

最好提前有：

- MSVC Build Tools for x64/x86 (Latest)
- C++ CMake tools for Windows
- Windows 11 SDK (10.0.22621.0)

安装 GCC（MinGW-w64）可以通过 MSYS2 等。

MinGW 是 32 位版本，并在 2016 年左右已经基本停止维护，所以目前一般使用其衍生项目 MinGW-w64.

## NVIDIA GPU 驱动

一般使用 `nvidia-smi` 来检查版本，这通常会显示：

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

NVIDIA-SMI 为 NVIDIA-SMI 这个工具的版本，Driver Version 为 NVIDIA GPU 的驱动版本。

一般来说 NVIDIA-SMI 版本和 Driver Version 应该是一致的，因为 NVIDIA-SMI 是由驱动一起发布的工具。

请注意这里的 CUDA Version: 13.1 并不代表你安装了 13.1 版的 CUDA Toolkit，而是指当前驱动支持的最高 CUDA Toolkit 版本。

NVIDIA GPU 驱动建议使用最新版，在 NVIDIA APP 中即可更新，开发建议选择 Studio 版。

## CUDA Toolkit

CUDA Toolkit 的官方下载地址为：[https://developer.nvidia.com/cuda-downloads](https://developer.nvidia.com/cuda-downloads)

CUDA Toolkit 具体安装在 `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v13.1` 中，如果没有自动添加系统 PATH，可能需要手动添加。

NVCC 是 CUDA Toolkit 中的核心编译工具之一，全称 NVIDIA CUDA Compiler Driver（NVIDIA CUDA 编译器驱动）。

NVCC 主要负责把 `.cu` / `.cpp` 文件拆成：

- Host 代码（在 CPU 上跑的部分）：普通的 C/C++ 代码 + 调用 CUDA API 的部分
- Device 代码（在 GPU 上跑的 kernel）：`__global__`、`__device__` 等修饰的函数

然后分别交给不同的后端编译：

- Host 代码 → 转发给系统已有的 C++ 编译器（Windows 上通常是 cl.exe / MSVC，Linux、Mac 上通常是 g++ / clang）
- Device 代码 → 交给 NVIDIA 的专有编译器链（包括前端解析 → PTX 生成 → ptxas 汇编成 cubin / fatbinary）

使用 `nvcc --version` 检查 NVCC 版本，这通常会显示：

```plaintext
nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2025 NVIDIA Corporation
Built on Fri_Nov__7_19:25:04_Pacific_Standard_Time_2025
Cuda compilation tools, release 13.1, V13.1.80
Build cuda_13.1.r13.1/compiler.36836380_0
```

具体版本信息可看：`C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v13.1\version.txt`

一台机器上可以安装多个 CUDA Toolkit 版本，它们彼此独立，比如：

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

## CUDA 与 MSVC

安装好 CUDA Toolkit 后，如果你使用最新的 Visual Studio 18 2026（以及最新的 MSVC），编译时便会遇到：

No CUDA toolset found. 和 -- Configuring incomplete, errors occurred! 这样的找不到 CUDA 工具集和配置不完整的报错。

这是因为虽然你的系统已经安装了 CUDA Toolkit 13.1，但 VS 还需要特定的项目文件模板（`.targets` 和 `.props`）来识别并编译 `.cu` 文件，而 CUDA Toolkit 13.1 还不支持 VS 18 2026（v180），所以我们需要手动拷贝 CUDA Toolkit 的 MSBuild 整合文件：

1. 先进入 `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v13.1\extras\visual_studio_integration\MSBuildExtensions` 目录，其中应该有：

   ```plaintext
   CUDA 13.1.props
   CUDA 13.1.targets
   CUDA 13.1.Version.props
   CUDA 13.1.xml
   Nvda.Build.CudaTasks.v13.1.dll
   ```

2. 复制这些文件

3. 将其粘贴到 `C:\Program Files\Microsoft Visual Studio\18\Community\MSBuild\Microsoft\VC\v180\BuildCustomizations` 中

## CUDA 代码编译

一个简单的编译含有 CUDA 代码的 C/C++ 项目示例。

首先创建并进入 `build` 目录：

```powershell
mdir build

cd build
```

然后先让 Cmake 输出项目构建配置（Project Configuration）：

```powershell
cmake -DGGML_CUDA=ON -DCMAKE_CUDA_ARCHITECTURES=120 -DCMAKE_CUDA_FLAGS="-allow-unsupported-compiler" -DCMAKE_BUILD_TYPE=Release ..
```

- `-DGGML_CUDA=ON`：开启 CUDA 支持
- `-DCMAKE_CUDA_ARCHITECTURES=120`：指定 CUDA 目标架构，具体请参考 [https://developer.nvidia.com/cuda/gpus](https://developer.nvidia.com/cuda/gpus)
- `-DCMAKE_CUDA_FLAGS="-allow-unsupported-compiler"`：允许不受支持的 host 编译器，CUDA 对 GCC / LLVM / MSVC（And VS）版本有严格限制，一般来说绕过是没有问题的，但是需谨慎用于生产环境
- `-DCMAKE_BUILD_TYPE=Release`：开启优化编译，CMake 中常见模式有：

  | 类型           | 含义              |
  | -------------- | ---------------- |
  | Debug          | 带调试符号，无优化 |
  | Release        | 高优化，无调试信息 |
  | RelWithDebInfo | 优化 + 调试信息   |

最后进行编译：

```powershell
cmake --build . --config Release --parallel
```

- `--build`：启动编译
- `--config Release`：该参数对单配置生成器（Single-config）无效，但是多配置生成器（Multi-config）需要，为了方便起见可以带上，如果不需要会自动忽略
- `--parallel`：使用最大可用 CPU 核心数进行编译

## PyTorch

使用 PyTorch 不需要 CUDA Toolkit，只需要 NVIDIA GPU 驱动即可。

不要直接使用 `pip install torch`，否则只会安装 CPU 版的 PyTorch.

安装时需要手动指定 whl：

```bash
pip install torch --index-url https://download.pytorch.org/whl/cu130
```

这里的 CUDA 版本通常 ≤ 你的 `nvidia-smi` 输出的 CUDA 版本。

具体参考 PyTorch 官网：[https://pytorch.org/get-started/locally](https://pytorch.org/get-started/locally)

然后可以验证一下：

```python
import torch
print(torch.cuda.is_available())     # 确认 CUDA 是否可用，应输出 True
print(torch.version.cuda)            # 显示你的 pytorch 编译时的 cuda 版本
print(torch.__version__)             # 显示 PyTorch 的完整版本信息
print(torch.cuda.device_count())     # 显示当前机器上可被 PyTorch 识别并使用的 CUDA 设备（GPU）数量
if torch.cuda.is_available():        # 循环显示所有被 PyTorch 识别的 GPU 的信息
    for i in range(torch.cuda.device_count()):
        print(f"GPU {i}:", torch.cuda.get_device_name(i))   # 显示你的 GPU 名称
        props = torch.cuda.get_device_properties(i)
        print(f"    {props.total_memory / 1024**3:.2f} GB") # 显示你的 GPU 内存总量
        print(f"    {props.major}.{props.minor}")           # 显示你的 GPU 架构版本号（Compute Capability）
```
