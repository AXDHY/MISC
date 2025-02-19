# Windows开发环境配置记录



[TOC]



---

## Windows版本

Windows 11 自2021年发布以来,功能日趋完善,可以成为稳定的开发环境.

目前2025年,鉴于Windows 10以下版本逐渐停止维护,推荐主流电脑安装Windows 11版本.



## Windows快捷键

| 应用           | 快捷键               | 作用                                             |
| -------------- | -------------------- | ------------------------------------------------ |
| 文件资源管理器 | Alt + ↑ 或 backspace | 返回上一级目录                                   |
|                | Alt + ←              | 后退                                             |
|                | Alt + →              | 前进                                             |
| Excel          | Fn + ScrollLock      | 切换，用于方向键是控制屏幕的滚动还是单元格的移动 |
|                | Ctrl + PageUp/Down   | 切换Sheet                                        |
|                | F2                   | 进入焦点单元格的编辑模式                         |



## 命令行工具



### CMD

> CMD（Command Prompt）是 Windows 的传统命令行工具，也叫做“命令提示符”或“控制台”。它源自于 MS-DOS，并且一直保持兼容性，直到今天。CMD 提供了基本的文件和系统操作命令，适合执行简单的`.bat`批处理脚本。
>
> CMD 主要支持文件管理、网络配置、批处理脚本等功能，但其功能较为有限，无法处理复杂的任务，且扩展性较差。



### Powershell 7

> PowerShell 是 Microsoft 于2006年推出的一个更强大的命令行工具，专为系统管理员和开发人员设计。它在 CMD 的基础上引入了更多现代化的功能，包括面向对象的`.ps1`脚本编写能力、支持 .NET 以及更强大的自动化功能。
>
> Powershell 7 是Powershell的现代化跨平台版本(需额外安装,默认版本5),基于.NET 5+构建,继承Powershell的核心功能并进行改进,引入新特性.

**Powershell版本查看**

```powershell
$PSVersionTable.PSVersion
```

**配置文件**

> 默认配置文件路径: `C:\Users\你的用户名\Documents\PowerShell\Microsoft.PowerShell_profile.ps1`
>
> ```powershell
> $PROFILE | Format-List * # 查看默认配置文件路径
> ```

**常见配置**

// todo 引用配置文件

**主题配置**

使用`oh-my-posh`作为主题

> 如果发现主题有乱码,则需要使用特殊字体
>
> ```powershell
>  oh-my-posh font install
> ```
>
> 推荐安装`Nerd`字体



### 命令对比

|                      | CMD    | Powershell                                           |
| -------------------- | ------ | ---------------------------------------------------- |
| 查找可执行文件的位置 | where  | `Get-Command`<br />而where指向的是`Where-Object`命令 |
| 创建符号链接         | mklink | `New-Item -ItemType SymbolicLink`                    |
|                      |        |                                                      |

> 举例
> ```powershell
> cmd /c mklink /D "C:\Path\to\link" "C:\Path\to\target"
> New-Item -ItemType SymbolicLink -Path "C:\Path\to\link" -Target "C:\Path\to\target"
> ```







## 开发环境



### Git

error: There was a problem with the editor '"..." --wait'.

> 之前的git 交互式设置过用其他的编辑器,但是这个编辑器移动了位置.
>
> ```powershell
> git config --global core.editor "\"__path__\" --wait"
> 
> # 或设置 $VSCODE 环境变量
> git config --global core.editor "$VSCODE --wait"
> 
> # 还原为默认编辑器
> git config --global --unset core.editor
> 
> # 网络受限，设置VPN代理
> git config --global http.proxy http://127.0.0.1:7890 # VPN常用端口号
> git config --global https.proxy http://127.0.0.1:7890
> # 取消代理
> git config --global --unset http.proxy
> git config --global --unset https.proxy
> ```
>
> 这些配置都保存在`$HOME/.gitconfig`



### Conda / Anaconda



**Powershell 安装 Anaconda**

如果要将当前的powershell增加conda的功能,需要在安装Anaconda之后,在powershell里手动执行

```powershell
conda init powershell
```

> `no change     D:\Toolkits\Env\Anaconda\Scripts\conda.exe
> no change     D:\Toolkits\Env\Anaconda\Scripts\conda-env.exe
> no change     D:\Toolkits\Env\Anaconda\Scripts\conda-script.py
> no change     D:\Toolkits\Env\Anaconda\Scripts\conda-env-script.py
> no change     D:\Toolkits\Env\Anaconda\condabin\conda.bat`
> `...`



**安装后在 Powershell 中自动执行 activate 指令报错**

> `# >>>>>>>>>>>>>>>>>>>>>> ERROR REPORT <<<<<<<<<<<<<<<<<<<<<<`
>
> `    Traceback (most recent call last):`
> `      File "D:\Toolkits\Env\Anaconda\Lib\site-packages\conda\exception_handler.py", line 18, in __call__`
> `        return func(*args, **kwargs)`
> `               ^^^^^^^^^^^^^^^^^^^^^`
> `      File "D:\Toolkits\Env\Anaconda\Lib\site-packages\conda\cli\main.py", line 87, in main_sourced`
> `        print(activator.execute(), end="")`
> `    UnicodeEncodeError: 'gbk' codec can't encode character '\u202a' in position 3231: illegal multibyte sequence`
>
> `$ D:\Toolkits\Env\Anaconda\Scripts\conda-script.py shell.powershell activate base`

```python
def main_sourced(shell, *args, **kwargs):    
    # ...
	activator = activator_cls(args)
    # 2024.11.29 fix 
    # Anaconda activate command in Powershell throws ERROR
    # print(activator.execute(), end="")
    print(activator.execute().encode('gbk','ignore').decode('gbk'), end ='')
    return 0
```





### CUDA

1. (卸载重装) 通过系统工具将除`NVIDIA 图形驱动程序`和`NVIDIA PhysX 系统软件`以外的各类NVIDIA开头的软件卸载掉.
   如果有卸载不掉的工具,根据提示可能要卸载Visual Stduio.

2. (卸载重装) 使用`DDU`工具做最后的清理

   > 过程中, 显卡驱动将被卸载,仅保留`NVIDIA PhysX 系统软件`

3. 安装对应显卡硬件的CUDA版本安装包.

   > 过程中如果出现报错`NVIDIA安装程序失败`,可能是有软件没有卸载完全,可以在列表中找到导致安装失败的程序先不安装,后续再装.

**常见错误**

1. 找不到导入的项目CUDA xx.props

   - 打开工程配置文件查找props发现电脑没有cuda的属性表: 这是之前安装时没有安装`CUDA/Visual Studio Integration`, 可以通过安装程序只安装这一个软件.

   - 在VS安装路径下,`.\MSBuild\Microsoft\VC\v170\BuildCustomizations`中可以看到

     > CUDA xx.x.xml    CUDA xx.x.targets    CUDA xx.x.props

     问题解决.

2. > fatal error C1189: #error:  -- unsupported Microsoft Visual Studio version! Only the versions between 2017 and 2022 (inclusive) are supported! The nvcc flag '-allow-unsupported-compiler' can be used to override this version check; however, using an unsupported host compiler may cause compilation failure or incorrect run time execution. Use at your own risk.

   用VS2022打开VS2019的解决方案.并提前安装对应的工具.



### Python

换源

```shell
# 法1
pip config set global.index-url https://mirrors.aliyun.com/pypi/simple
pip config set install.trusted-host mirrors.aliyun.com
pip install -r requirements.txt
# 法2
pip install -r .\requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple
```

**Script file '...\conda-script.py' is not present.**

```powershell
conda activate base
python -m ensurepip
python -m pip install --upgrade pip
```

**UnicodeDecodeError: 'gbk' codec can't decode byte 0x9d in position 42362**

```powershell
pipreqs . --encoding=utf-8
```

使用utf-8运行相关命令,可以解决在命令行默认用utf-8执行的问题.

**生成requirements.txt**

```powershell
conda install pipreqs
pipreqs . --encoding=utf-8
```





### WSL

**常见问题整理**

> 由于未安装所需的特性，无法启动操作。
> 错误代码: Wsl/Service/CreateInstance/CreateVm/HCS/HCS_E_SERVICE_NOT_AVAILABLE
>
> > ![image-20241126020228053](res/Windows%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E9%85%8D%E7%BD%AE%E8%AE%B0%E5%BD%95/image-20241126020228053.png)
> >
> > 开启该选项

> > System has not been booted with systemd as init system (PID 1). Can't operate.
> > Failed to connect to bus: Host is down
>
> [使用 systemd 通过 WSL 管理 Linux 服务 | Microsoft Learn](https://learn.microsoft.com/zh-cn/windows/wsl/systemd)
>
> > systemd 是 Linux 系统的基本构建基块套件。 它提供一个系统和服务管理器，该管理器作为 PID 1 运行并启动系统的其余部分。
> >
> > Systemd 主要是一个 init 系统和服务管理器，它包括按需启动守护程序、装载和自动装载点维护、快照支持以及使用 Linux 控制组进行跟踪等功能。
> >
> > Systemd 来源于`Fedora`项目, 用于解决Linux传统内核中init使用的`SysVinit`串行启动效率慢、服务管理复杂、缺少日志管理、缺少标准配置、缺少实时控制等问题.
> >
> > 引入了并行化启动、服务依赖管理、统一的服务管理以及`journald`日志系统. 并在2015年以后在主流Linux发行版中泛用.
>
> 0. WSL版本大于等于0.67.6. (`wsl --version 查看 wsl --update 更新`)
>
> 1. 编辑`/etc/wsl.conf`, 加入如下配置
>
>    > [boot]
>    > systemd=true
>
> 2. ```powershell
>    wsl --shutdown
>    ```
>
>    重启wsl
>
> 3. ```shell
>    systemctl list-unit-files --type=service
>    ```
>
>    验证是否启用`systemctl`





### Julia

1. 先要安装 Julia 环境

2. ```python
   pip install --user julia
   ```

3. ```powershell
   python
   >>> import julia
   >>> julia.install()
   ```

   >   [83775a58] + Zlib_jll v1.2.13+1
   >   [8e850b90] + libblastrampoline_jll v5.11.0+0
   >   [8e850ede] + nghttp2_jll v1.52.0+1
   >     Building Conda ─→ `C:\Users\axdhy\.julia\scratchspaces\44cfe95a-1eb2-52ea-b672-e2afdf69b78f\b19db3927f0db4151cb86d073689f2428e524576\build.log`
   >     Building PyCall → `C:\Users\axdhy\.julia\scratchspaces\44cfe95a-1eb2-52ea-b672-e2afdf69b78f\9816a3826b0ebf49ab4926e2b18842ad8b5c8f04\build.log`
   > Precompiling project...
   >   9 dependencies successfully precompiled in 32 seconds. 1 already precompiled.
   >
   > Precompiling PyCall...
   > Precompiling PyCall... DONE
   > PyCall is installed and built successfully.



### MSYS2

## MSYS2 介绍

### 1. MSYS2 是什么？

MSYS2（Minimal System 2）是一个基于 Cygwin 和 MinGW-w64 的 Windows 终端环境，提供了一个类似于 Unix 的开发环境。它使用 **pacman**（与 Arch Linux 相同的包管理器）来管理软件包，支持开发和构建 Windows 本机应用程序以及 Unix 兼容的工具。

------

### 2. MSYS2 的主要功能

- **提供 POSIX 兼容环境**：可运行 Unix/Linux 工具（如 `grep`, `awk`, `sed`）。
- **支持 MinGW-w64**：可以构建原生 Windows 应用（x86 和 x86_64）。
- **包含 `pacman` 包管理器**：方便安装、更新、删除软件包。
- **兼容 CMake、GCC、Clang、Python、Node.js 等开发工具**。
- **提供 Bash、Zsh、Fish 等 Shell**，适合习惯 Unix 终端的用户。
- **可以使用 `make`、`autotools`、`ninja`、`pkg-config` 进行 C/C++ 项目构建**。

------

### 3. MSYS2 相较于普通 Windows 环境的优势

| 特性           | 普通 Windows 环境        | MSYS2                      |
| -------------- | ------------------------ | -------------------------- |
| **终端支持**   | CMD/Powershell，功能受限 | Bash/Zsh/Fish，功能强大    |
| **包管理**     | 需手动下载安装 EXE/MSI   | `pacman` 自动管理          |
| **编译工具链** | 需手动配置 MinGW/GCC     | 自带 MinGW-w64/GCC/Clang   |
| **POSIX 兼容** | 不兼容 Linux 工具        | 兼容大部分 Unix 工具       |
| **CMake 支持** | 需手动下载 CMake         | `pacman -S cmake` 一键安装 |
| **环境变量**   | 需手动配置 PATH          | 自动管理                   |

------

### 4. MSYS2 的竞品对比

| 方案          | 主要功能                  | 适用场景                        | 是否 POSIX 兼容 | 包管理       |
| ------------- | ------------------------- | ------------------------------- | --------------- | ------------ |
| **Cygwin**    | 提供完整 POSIX 兼容层     | 运行 Unix 软件，移植 Linux 工具 | 是              | `setup.exe`  |
| **WSL(2)**    | 直接运行 Linux 发行版     | 需要完整 Linux 体验             | 是              | `apt`、`dnf` |
| **Git Bash**  | 提供 Git 和部分 Unix 工具 | 主要用于 Git 相关开发           | 部分            | 无包管理     |
| **MinGW-w64** | 提供 Windows 版 GCC       | 只用于编译 Windows 应用         | 否              | 无包管理     |

**MSYS2 的独特优势**：

- **比 Cygwin 更适合开发 Windows 本机应用**（Cygwin 需要 POSIX 兼容 DLL）。
- **比 WSL 轻量**（WSL 是完整的 Linux 子系统）。
- **比 Git Bash 更强大**（Git Bash 仅提供 Git 相关工具）。
- **支持 `pacman`**（Cygwin、MinGW-w64 没有统一的包管理）。

------

### 5. MSYS2 的四个开发环境（Shell 环境）

MSYS2 内部提供了四个不同的 Shell 环境，每个环境的用途不同：

| 环境        | 可执行文件                                 | 主要用途                             | 编译生成的目标           |
| ----------- | ------------------------------------------ | ------------------------------------ | ------------------------ |
| **MSYS**    | `msys2.exe` / `msys2_shell.cmd -msys`      | 运行 Unix/Linux 工具，脚本开发       | 需要 `msys-2.0.dll`      |
| **MINGW32** | `mingw32.exe` / `msys2_shell.cmd -mingw32` | 32 位 Windows 应用开发               | 32 位 Windows 可执行文件 |
| **MINGW64** | `mingw64.exe` / `msys2_shell.cmd -mingw64` | 64 位 Windows 应用开发               | 64 位 Windows 可执行文件 |
| **UCRT64**  | `ucrt64.exe` / `msys2_shell.cmd -ucrt64`   | 64 位 Windows 应用，使用 UCRT 运行时 | 64 位 Windows 可执行文件 |
| **CLANG64** | `clang64.exe` / `msys2_shell.cmd -clang64` | 64 位 Windows 应用，使用 Clang 编译  | 64 位 Windows 可执行文件 |

**选择指南**：

- **编写脚本或运行 Unix 工具**：用 **MSYS** 环境。
- **开发 32 位 Windows 应用**：用 **MINGW32** 环境。
- **开发 64 位 Windows 应用（推荐）**：用 **MINGW64** 环境。
- **使用 MSVC/UCRT**：用 **UCRT64**。
- **使用 Clang 作为编译器**：用 **CLANG64**。

// todo [Windows下使用SDKMAN对JDK（Java）进行多版本管理 - 个人文章 - SegmentFault 思否](https://segmentfault.com/a/1190000044794247)



**安装相关**

1. 使用安装包安装通常直接安装成功,但是在某些具有安全软件的设备上,可能会出现问题

> 使用pacman安装软件时出现`// todo`

```bash
$ pacman-key --init
gpg: starting migration from earlier GnuPG versions
gpg: error running '/usr/bin/gpg-agent': exit status 2
gpg: failed to start gpg-agent '/usr/bin/gpg-agent': General error
gpg: can't connect to the gpg-agent: General error
gpg: error: GnuPG agent unusable. Please check that a GnuPG agent can be started.
gpg: migration aborted
==> Generating pacman master key. This may take some time.
gpg: starting migration from earlier GnuPG versions
gpg: error running '/usr/bin/gpg-agent': exit status 2
gpg: failed to start gpg-agent '/usr/bin/gpg-agent': General error
gpg: can't connect to the gpg-agent: General error
gpg: error: GnuPG agent unusable. Please check that a GnuPG agent can be started.
gpg: migration aborted
gpg: Generating pacman keyring master key...
gpg: error running '/usr/bin/gpg-agent': exit status 2
gpg: failed to start gpg-agent '/usr/bin/gpg-agent': General error
gpg: can't connect to the gpg-agent: General error
gpg: agent_genkey failed: No agent running
gpg: key generation failed: No agent running
gpg: Done
==> Updating trust database...
gpg: no need for a trustdb check
```

> GnuPG agent 不能正常工作.

> 解决方案: 禁用 GPG 验证
> 在某些情况下，如果你只需要进行安装而不关心密钥验证，可以临时禁用 pacman 的 GPG 验证。注意，这会降低系统安全性，不推荐长时间使用此方法：
>
> 编辑 /etc/pacman.conf 文件
>
> 在文件中找到并对以下配置进行注释切换：
>
> ```bash
> # By default, pacman accepts packages signed by keys that its local keyring
> # trusts (see pacman-key and its man page), as well as unsigned packages.
> SigLevel = Never
> # SigLevel    = Required
> ```
>
> 这样可以暂时绕过 GPG 验证，但这仅适用于紧急情况，正常情况下建议使用 GPG 密钥验证。




## 代码编辑器 / IDE



### Emacs

**安装方式**

- 先安装MSYS2

- 然后在MinGW64环境下安装Emacs
  ```bash
  pacman -Syu
  pacman -S mingw-w64-x86_64-emacs
  ```

**常用配置**

- **Spacemacs**

  - 安装方式

    1. 复制配置
       ```bash
       git clone https://github.com/syl20bnr/spacemacs $env:APPDATA/.emacs.d
       ```

       > 注意`$env:APPDATA`目录通常为`C:\Users\axdhy\AppData\Roaming`.Windows的emacs的环境配置在此目录下.注意不同于Linux,emacs在Linux的默认环境配置在`$HOME/.emacs.d`目录下,但是对于windows来说,`$HOME`不同于`$env:APPDATA`,注意区分.

    2. 在MSYS2 MinGW64环境的bin目录下找到`emacs`应用程序,双击启动,也可以创建快捷方式.
       首次启动要选择操作方式(vim/emacs)以及环境配置(默认/精简)
    
    3. 然后直接关闭,修改`.spacemacs`文件,更换源.
       ```lisp
       (defun dotspacemacs/user-init ()
         "Initialization for user code:
       This function is called immediately after `dotspacemacs/init', before layer
       configuration.
       It is mostly for variables that should be set before packages are loaded.
       If you are unsure, try setting them in `dotspacemacs/user-config' first."
         (setq configuration-layer--elpa-archives
         '(("melpa-cn" . "https://mirrors.tuna.tsinghua.edu.cn/elpa/melpa/")
           ("org-cn"   . "https://mirrors.tuna.tsinghua.edu.cn/elpa/org/")
           ("gnu-cn"   . "https://mirrors.tuna.tsinghua.edu.cn/elpa/gnu/")))
         )
       ```
    
    4. 重新运行emacs应用程序,完成后续的安装.
    
       > 如果不更换源,某些文件下载失败将导致插件安装失败.
  



### Visual Studio Code



#### portable便携模式

1. 安装目录下创建data文件夹，安装的扩展都会到这个文件夹内

2. 鼠标右键

   1. 修改注册表，导航到如下路径：`HKEY_CLASSES_ROOT\Directory\Background\shell`

   2. 新建文件夹,命名为`Open with VSCode Insiders`(或任意),创建两个键值

      | Key    | Type          | Value                                                        |
      | ------ | ------------- | ------------------------------------------------------------ |
      | (默认) | REG_SZ        | Visual Studio Code Insiders 打开文件夹                       |
      | Icon   | REG_EXPAND_SZ | `"D:\Toolkits\IDE\Visual_Studio_Code\insiders\Code - Insiders.exe"` |

   3. 在该文件夹下新建子文件夹,命名为`Command`

      | Key    | Type   | Value                                                        |
      | ------ | ------ | ------------------------------------------------------------ |
      | (默认) | REG_SZ | `"D:\Toolkits\IDE\Visual_Studio_Code\insiders\Code - Insiders.exe" "%V"` |



### 设置

- security.workspace.trust: 是否在VSCode内启用工作区信任.关闭减少弹窗



#### 扩展

**C/C++**

> C/C++的官方插件.

该插件有缓存功能,缓存的默认位置为C盘,默认占用空间可达5G以上.
可以通过设置`C_Cpp.intelliSenseCachePath`设置缓存路径,以及`C_Cpp.intelliSenseCacheSize`设置缓存大小.





### Visual Studio



**插件安装**

- **番茄助手(VAssistX)**

  **安装流程**
  
  1. 确保旧版番茄助手插件完全卸载
  
  2. 关闭VS&重启电脑,安装`VA_X_Setupxxxx_0.exe`
  
  3. 在`%USERPROFILE%\AppData\Local\Microsoft\VisualStudio\17.0_e5a362b9`(VS2022,其他版本类似)等路径下找到`VA_X64.dll`,重命名为`VA_X64.dll.bak`(用于意外被迫还原)
  
  4. 将`VA_X64.dll`和`PiaoYun64.dll`复制到该目录下.
  
  5. ```powershell
     "???\2022\Community\Common7\IDE\devenv.exe" /safemode
     ```
  
     以安全模式打开VS2022，然后关闭VS2022(不关闭的话点开扩展发现没有启用选项)
  
  6. 重新打开VS2022，在工具栏打开“扩展→管理扩展”，在已安装中启用VA，再次重启VS2022，即可安装成功
  
  > 安装过程中可能有报错 "未能正确加载"VaMenuPakage"包或其他，忽略继续尝试。
  >
  > ![image-20241119012129457](res/Windows%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E9%85%8D%E7%BD%AE%E8%AE%B0%E5%BD%95/image-20241119012129457.png)

- **QT插件(QT VS Tools)**
  - 首次根据要求要配置QT编译器的路径,也就是要求安装QT环境到本机,而QT环境通常位于QT安装目录下,找到对应qmake或qpath文件选择即可.
  - **常见问题**
    - .ui文件打开后报错: 扩展→QT VS Tools→Options→Qt→General→Qt Designer的`Run in detached window`改成`False`即可.



### Excel



#### VBA

> 1. 启用宏设置
>
>    ![image-20241124011135182](res/Windows%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E9%85%8D%E7%BD%AE%E8%AE%B0%E5%BD%95/image-20241124011135182.png)
>
> 2. 功能区打开开发工具
>
>    ![image-20241124011235901](res/Windows%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E9%85%8D%E7%BD%AE%E8%AE%B0%E5%BD%95/image-20241124011235901.png)
>
> 3. 如果Excel是.xlsx要保存为.xlsm
> 4. 打开VBA编辑器编辑代码，如果函数想要在单元格中直接调用，则需要先插入模块，在模块中编写代码才会生效。
>
> ![image-20241124011812614](res/Windows%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E9%85%8D%E7%BD%AE%E8%AE%B0%E5%BD%95/image-20241124011812614.png)

**VBA调试**

```vb
' 列数据筛选累加.
Public Function EvaluateAndSum(rng As Range) As Double
    Dim cell As Range
    Dim result As Double
    Dim evalResult As Variant
    
    result = 0
    For Each cell In rng
        If cell.Value <> "" Or cell.Value <> "-" Then
        Debug.Print "cell color:" & cell.Interior.Pattern ' Debug调试
            evalResult = Application.Evaluate(cell.Value)
            If IsNumeric(evalResult) Then
                result = result + evalResult
            End If
        End If
    Next cell
    Debug.Print "--Result: " & result
    EvaluateAndSum = result
End Function
```

> Debug.Print结果可以在 **立即窗口** 中查看





### Tesseract-OCR

> Google维护的开源OCR.

1. 安装exe
2. 编辑系统环境变量
   - Path 添加 exe的安装目录
   - `TESSDATA_PREFIX`: exe的安装目录下tessdata路径

```python
pytesseract.pytesseract.tesseract_cmd = r'D:\Toolkits\OCR\Tesseract-OCR\tesseract.exe'
```

> python项目需要先配置Tesseract-OCR的路径.



### QGIS

> QGIS 是一个开源的跨平台 GIS 软件，用于地理数据的查看、编辑和分析。它广泛应用于地理空间数据处理和地图制作.

**QGIS Osgeo4W**

> Osgeo4W 是 **Open Source Geospatial for Windows** 的缩写，是由开源地理空间基金会（OSGeo）开发的一个软件平台。它包含各种开源 GIS 工具和库，能够提供 GIS 软件的一站式安装与管理。

**国内源**

> http://gwmodel.whu.edu.cn/mirrors/osgeo4w



### Qt

**在线安装**

```powershell
./qt-online-installer-windows-x64-4.8.1.exe --mirror https://mirrors.ustc.edu.cn/qtproject
```



## 常用软件



### AI软件



#### Stable Diffusion

> Stable Diffusion是2022年发布的文本到图像的潜在扩散大模型，由CompVis、Stability AI和LAION研发.
> 目前广泛应用于文生图等图片生成应用.

**安装方式**

1. 下载 [AUTOMATIC1111/stable-diffusion-webui: Stable Diffusion web UI](https://github.com/AUTOMATIC1111/stable-diffusion-webui)
2. 运行 `webui-user.bat`即可,首次运行会自动部署venv环境以及下载SD模型

**常见问题**

- `Downloading: "https://huggingface.co/runwayml/stable-diffusion-v1-5/resolve/main/v1-5-pruned-emaonly.safetensors" to D:\__WorkSpace__\stable-diffusion-webui\models\Stable-diffusion\v1-5-pruned-emaonly.safetensors`

  解决方案: 下载大模型的过程中网络异常中断，可以考虑使用有线网络或者使用国内镜像站[HF-Mirror](https://hf-mirror.com/)

- `no module 'xformers'. Processing without...`

  > **xformers** 是一个库，旨在为基于 **注意力机制** 的模型提供更高效的实现，尤其是用于训练和推理时，它能够减少内存占用并提高计算速度。**Stable Diffusion** 是一个扩散模型，但它也使用了 **自注意力** 和类似 **Transformer** 的机制，所以启用 **xformers** 可以提高生成速度.

  解决方案

  1. 在对应python环境下安装`xformers`库, 注意不是`xformer`

  2. 修改`webui.bat`

     ```bat
     :launch
     %PYTHON% launch.py --xformers %*
     ```

     增加 --xformers 参数, 接入xformers库.

**其他配置**

- 在`webui-user.bat`中可以配置Python/git等应用程序的目录.







## 网络问题



### VPN

**常见问题**

- 开启VPN后，UWP应用无法联网

  > UWP应用程序在设计时具有一定的网络访问限制，尤其在回环网络方面，默认情况下不能直接回环网络(localhost), 这是为了增强安全性和防止恶意应用程序利用本地网络进行攻击.
  >
  > 如果开启VPN后,所有网络流量会通过VPN通道,进而影响到UWP应用访问网络.

  解决方案: 在常用的VPN软件中,有UWP应用相关的网络设置

  ![image-20250204220021745](res/Windows%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E9%85%8D%E7%BD%AE%E8%AE%B0%E5%BD%95/image-20250204220021745.png)

  还可以通过`Fiddler Classic`软件的`WinConfig`功能设置.
  点击后都将打开`AppContainer Loopback Exemption Utility`工具,勾选UWP应用解除对它的限制.
  
- 部分域名或地址错误走了VPN代理,导致无法访问

  解决方案: 在设置→系统代理→系统代理绕过的域名/IP网段进行设置

  ```yaml
  bypass:
    - localhost
    - 127.*
    - 10.*
    - 172.16.*
    ...
    - 192.168.*
    - <local>
    - DOMAIN, *.example.net # 支持*通配符,等效于DOMAIN-SUFFIX,clash支持该方式
    - DOMAIN-SUFFIX, example.com # 指定后缀结尾的所有域名
    - DOMAIN-SUFFIX, mydomain.com
    - DOMAIN-KEYWORD, local # 表示包含关键字的所有域名
  ```





## 常用操作



### 磁盘清理

1. 使用**符号链接**将C盘配置映射到其他盘, 减少C盘空间占用.
   可以将一些配置文件或安装目录转移,并且最大程度上不影响软件的功能使用.

   ```bat
   # Docker的安装位置：
   #    C:\Program Files\Docker
   #    C:\ProgramData\Docker
   #    C:\Users\_你的用户名\AppData\Local\Docker
   #    C:\Users\_你的用户名\AppData\Roaming\Docker
   
   mklink /j "C:\Program Files\Docker" "自定义的位置"
   mklink /j "C:\ProgramData\Docker" "自定义的位置"
   mklink /j "C:\Users\_你的用户名\AppData\Local\Docker" "自定义的位置"
   mklink /j "C:\Users\_你的用户名\AppData\Roaming\Docker" "自定义的位置"
   ```

   > 笔者 符号链接 记录
   >
   > ```powershell
   > New-Item -ItemType SymbolicLink -Path “C:\Users\axdhy\AppData\Roaming\.emacs.d” -Target “D:\__WorkSpace__\Config\emacs\.emacs.d”
   > New-Item -ItemType SymbolicLink -Path “C:\Users\axdhy\AppData\Roaming\.spacemacs” -Target “D:\__WorkSpace__\Config\emacs\.spacemacs”
   > New-Item -ItemType SymbolicLink -Path “C:\Users\axdhy\AppData\Roaming\.spacemacs.env” -Target “D:\__WorkSpace__\Config\emacs\.spacemacs.env”
   > ```
   >
   > 常见流程
   >
   > 1. 先创建或先移动完目标路径或文件,确保目标路径存在,源路径不存在.
   > 2. 然后再调用上述语句构建符号链接,将在源路径生成对应符号链接.

   



## 参考资料

1. [power shell 7 autosuggestion 补全设置_powershell 自动补全-CSDN博客](https://blog.csdn.net/TheOneLeaf/article/details/142721318)
2. [Windows 下如何美化 powershell 并显示 conda 环境_windows powershell美化-CSDN博客](https://blog.csdn.net/TheOneLeaf/article/details/142720542)
3. [oh-my-posh3及oh-my-zsh提示prompt出现乱码的原因及使用Nerd字体的解决方法_oh-my-posh 乱码-CSDN博客](https://blog.csdn.net/yihuajack/article/details/111405007)
4. [Qt安装时很慢且经常卡住解决办法_qttabbar安装一直不动-CSDN博客](https://blog.csdn.net/qq_62933419/article/details/136964512)
5. [开启http代理或配置pac后导致win10 MICROSOFT STORE无法联网 · Issue #1083 · 2dust/v2rayN](https://github.com/2dust/v2rayN/issues/1083)
6. [syl20bnr/spacemacs: A community-driven Emacs distribution - The best editor is neither Emacs nor Vim, it's Emacs *and* Vim!](https://github.com/syl20bnr/spacemacs)
7. [elpa | 镜像站使用帮助 | 清华大学开源软件镜像站 | Tsinghua Open Source Mirror](https://mirrors.tuna.tsinghua.edu.cn/help/elpa/)
8. [VS2022安装VisualAssistX番茄助手 - 知乎](https://zhuanlan.zhihu.com/p/661815368)
9. [Rules 规则 | Clash 知识库](https://clash.wiki/configuration/rules.html)