<div align="center">

  <img src="https://github.com/github/explore/blob/main/topics/android/android.png?raw=true" alt="logo" width="120">
  <h1 align="center">ZTC-Kernel-Build (Enhanced)</h1>
  
  <p align="center">
    ✨ <strong>专为第三方内核设计的一键云端编译方案 - 增强版</strong> ✨
  </p>

   <p align="center">
    <a href="https://github.com/zzh20188/ZTC-Kernel-Build/actions/workflows/ztc-kernel-enhanced.yml">
      <img alt="Build Status" src="https://img.shields.io/github/actions/workflow/status/zzh20188/ZTC-Kernel-Build/ztc-kernel-enhanced.yml?branch=main&style=for-the-badge&logo=githubactions&logoColor=white">
    </a>
    <a href="https://github.com/zzh20188/ZTC-Kernel-Build/blob/main/LICENSE">
      <img alt="License" src="https://img.shields.io/github/license/zzh20188/ZTC-Kernel-Build?style=for-the-badge&color=blue">
    </a>
    <a href="https://github.com/zzh20188/ZTC-Kernel-Build/releases/latest">
      <img alt="Latest Release" src="https://img.shields.io/github/v/release/zzh20188/ZTC-Kernel-Build?style=for-the-badge&color=brightgreen">
    </a>
  </p>
  
  <br>

  <img src="assets/build.png" alt="demo">

</div>

---

## 📋 目录

- [新增功能](#-新增功能)
- [项目简介](#-项目简介)
- [快速开始](#-快速开始)
- [配置参数详解](#-配置参数详解)
- [高级配置](#-高级配置)
- [构建产物](#-构建产物)
- [常见问题](#-常见问题)
- [致谢](#-致谢)

---

## 🎉 新增功能

相比原版，增强版添加了以下功能：

### 核心功能
- ✅ **参数化配置** - 通过 GitHub Actions 界面配置所有构建选项
- ✅ **ZRAM 增强** - 支持多种压缩算法（LZO/LZ4/LZ4HC/DEFLATE/ZSTD/842）
- ✅ **网络增强** - IPSet + BBR 拥塞控制算法
- ✅ **NTsync 支持** - Wine 兼容层优化
- ✅ **编译优化** - 可选 LTO 模式（thin/full/none）和优化级别（O2/O3/Os）
- ✅ **版本固定** - 通过配置文件固定特定 commit
- ✅ **自动发布** - 支持 Actions/Pre-Release/Release 三种发布模式

### 配置管理
- 📁 **config/config** - 固定提交版本配置
- 📁 **config/zram.config** - ZRAM 算法配置
- 📁 **config/stock_defconfig** - 官方配置伪装（可选）

---

## 🚀 项目简介

本项目提供一个功能完整的 **GitHub Actions 工作流**，用于全自动编译 ZTC 内核，并使用 AnyKernel3 打包成可直接刷入的 `zip` 文件。

**ZTC 内核特性：**
- 内置 BBG 防格机
- 内置 SUSFS 支持
- 内置 VFS 优化
- 基于 Android 12 GKI 5.10

---

## ✨ 快速开始

### 1. Fork 本仓库

点击仓库右上角的 **`Fork`** 按钮，将此项目复制到你自己的 GitHub 账户下。

### 2. 启动增强版工作流

- 进入你 Fork 后的仓库
- 点击上方的 **`Actions`** 选项卡
- 在左侧边栏选择 **`ZTC Kernel Build (Enhanced)`**
- 点击右侧的 **`Run workflow`** 下拉按钮

### 3. 配置构建参数

在弹出的界面中配置以下参数：

#### 基础配置
| 参数 | 说明 | 默认值 |
|------|------|--------|
| **内核分支** | 选择内核版本 | android12-5.10-lts |
| **自定义版本名** | 可选的版本标识 | 空 |
| **自定义构建时间** | 可选的构建时间 | 当前UTC |

#### KernelSU 配置
| 参数 | 说明 | 默认值 |
|------|------|--------|
| **KernelSU 变体** | Official / Disabled | Official |
| **启用 SUSFS** | 是否启用 SUSFS | false |

#### 功能开关
| 参数 | 说明 | 默认值 |
|------|------|--------|
| **启用 ZRAM 增强算法** | 多种压缩算法支持 | false |
| **启用 BBG 防格机** | ZTC 内置功能 | true |
| **启用 NTsync** | Wine 兼容层 | false |
| **启用网络增强** | IPSet + BBR | false |

#### ZRAM 扩展选项
| 参数 | 说明 | 默认值 |
|------|------|--------|
| **启用完整算法支持** | 所有 ZRAM 算法 | false |
| **自定义算法** | 逗号分隔，如 lzo,lz4,zstd | 空 |

#### 编译优化
| 参数 | 说明 | 默认值 |
|------|------|--------|
| **LTO 优化模式** | thin / full / none | thin |
| **编译优化级别** | O3 / O2 / Os | O3 |

#### 发布配置
| 参数 | 说明 | 默认值 |
|------|------|--------|
| **发布类型** | Actions / Pre-Release / Release | Actions |

### 4. 运行并下载

点击绿色的 **`Run workflow`** 按钮开始构建。

构建完成后：
- **Actions 模式**: 在工作流摘要页面下载 Artifacts
- **Release 模式**: 在 Releases 页面下载发布文件

---

## 🔧 配置参数详解

### ZRAM 配置

#### 完整算法模式
启用后支持以下所有算法：
- LZO / LZ4 / LZ4HC / LZ4K / LZ4KD
- DEFLATE / 842 / ZSTD

#### 自定义算法模式
手动指定需要的算法，格式：
```
lzo,lz4,deflate,zstd
```

**推荐配置：**
- 性能优先: `lz4`
- 压缩率优先: `zstd`
- 平衡: `lz4,zstd`

### LTO 优化

| 模式 | 说明 | 编译时间 | 性能提升 |
|------|------|----------|----------|
| **thin** | 轻量级链接时优化 | 中等 | 中等 |
| **full** | 完整链接时优化 | 较长 | 较高 |
| **none** | 不使用 LTO | 最快 | 无 |

### 优化级别

| 级别 | 说明 | 适用场景 |
|------|------|----------|
| **O3** | 最高性能优化 | 日常使用（推荐） |
| **O2** | 平衡优化 | 稳定性优先 |
| **Os** | 体积优化 | 存储空间受限 |

---

## 🔐 高级配置

### 1. 固定提交版本

编辑 `config/config` 文件：

```ini
# 启用自定义提交
custom=true

# SUSFS 的 commit hash
gki-android12-5.10=abc123def456

# KernelSU 的 commit hash
kernelsu=def789abc123

# ZTC 内核的 commit hash
ztc-kernel=789abcdef012
```

**使用场景：**
- 上游更新导致编译失败
- 需要使用特定稳定版本
- 测试特定功能分支

### 2. ZRAM 算法配置

编辑 `config/zram.config` 添加自定义算法：

```ini
CONFIG_CRYPTO_LZ4HC=y
CONFIG_CRYPTO_LZ4K=y
CONFIG_CRYPTO_LZ4KD=y
CONFIG_CRYPTO_842=y
CONFIG_CRYPTO_LZ4K_OPLUS=y
```

### 3. Stock Config 伪装

将设备官方内核的 defconfig 保存为 `config/stock_defconfig`，构建时会替换 `/proc/config.gz` 内容。

**获取方法：**
```bash
# 在设备上执行
adb shell "zcat /proc/config.gz" > stock_defconfig
```

---

## 📦 构建产物

### AnyKernel3 刷机包

格式：`ZTC-android12-5.10-lts-AnyKernel3.zip`

**刷入方法：**
1. **TWRP Recovery**: 直接刷入 zip 文件
2. **HorizonKernelFlasher**: 授予 ROOT 后刷入
3. **KernelFlasher**: 其他内核刷写工具

### KernelSU 管理器

格式：`KSU-Manager-xxxxx.apk`

**安装说明：**
- 刷入内核后安装 APK
- 首次启动会请求 ROOT 权限
- 通过管理器安装模块

### SUSFS 模块

格式：`susfs-xxxxx.zip`

**安装方法：**
- 通过 KernelSU 管理器安装
- 重启后生效

---

## ❓ 常见问题

### Q1: 构建失败怎么办？

**检查步骤：**
1. 查看 Actions 日志中的错误信息
2. 检查 `config/config` 中的提交是否有效
3. 尝试禁用部分功能（ZRAM/网络增强等）
4. 使用默认配置重新构建

### Q2: 刷入后无法开机？

**解决方法：**
1. 进入 Recovery 刷回原厂 boot.img
2. 检查设备是否支持 GKI
3. 确认 Android 版本匹配（Android 12）
4. 尝试不同的优化级别（O2 代替 O3）

### Q3: KernelSU 无法使用？

**排查步骤：**
1. 确认选择了 "Official" 变体
2. 检查管理器版本是否匹配
3. 查看 `/proc/version` 确认内核版本
4. 重新安装管理器 APK

### Q4: ZRAM 不生效？

**检查方法：**
```bash
# 查看 ZRAM 状态
adb shell "cat /proc/swaps"

# 查看可用算法
adb shell "cat /sys/block/zram0/comp_algorithm"
```

### Q5: 如何回退到旧版本？

**方法一：使用固定提交**
```ini
custom=true
ztc-kernel=<旧版本commit>
```

**方法二：下载历史 Release**
- 在 Releases 页面找到旧版本
- 下载对应的刷机包

---

## 🙏 致谢

本项目基于以下优秀开源项目：

### 核心项目
- **[ZTC 内核](https://github.com/ztc1997/android_gki_kernel_5.10_common)**: ZTC 内核源码
- **[KernelSU](https://github.com/tiann/KernelSU)**: 内核级 Root 方案
- **[SUSFS](https://gitlab.com/simonpunk/susfs4ksu)**: 文件系统隐藏方案
- **[AnyKernel3](https://github.com/osm0sis/AnyKernel3)**: 通用内核刷机包工具

### 工具链
- **[Clang-r563880](https://android.googlesource.com/platform/prebuilts/clang/host/linux-x86)**: Google 官方 Clang 工具链
- **[ABK](https://github.com/xingguangcuican6666/ABK)**: 参数配置功能参考

### 社区贡献
- 感谢所有提供反馈和建议的用户
- 感谢内核开发者的辛勤付出

---

## 📄 许可证

本项目使用 [MIT](LICENSE) 许可证。

---

## 🔗 相关链接

- [原版 README](README.md)
- [Issues](https://github.com/zzh20188/ZTC-Kernel-Build/issues)
- [Discussions](https://github.com/zzh20188/ZTC-Kernel-Build/discussions)
- [ZTC 内核源码](https://github.com/ztc1997/android_gki_kernel_5.10_common)
- [KernelSU 官网](https://kernelsu.org/)

---

<div align="center">
  <p>如果这个项目对你有帮助，请给个 ⭐ Star 支持一下！</p>
</div>
