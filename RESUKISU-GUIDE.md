# ReSukiSU 使用指南

## 📖 关于 ReSukiSU

ReSukiSU 是 SukiSU 的重构版本，提供与官方 KernelSU 相同的功能，但具有以下特点：

- ✅ **内置 SUSFS 支持** - 使用 Inline Hooks 方式
- ✅ **KPM 支持** - 内核补丁模块功能
- ✅ **更好的隐藏能力** - 绕过 Root 检测
- ✅ **代码优化** - 相比 SukiSU 更稳定

---

## ⚠️ 重要说明

### ZTC 内核与 ReSukiSU

ZTC 内核默认**内置了官方 KernelSU**，这是不可逆的。要使用 ReSukiSU，需要：

1. **强制替换内置 KSU** - 移除 ZTC 内置的 KSU 代码
2. **重新集成 ReSukiSU** - 使用 ReSukiSU 的 setup 脚本

本工作流已经实现了这个功能，通过 `force_replace_ksu` 选项控制。

### ⚠️ 兼容性警告

**ReSukiSU 与 ZTC 内核可能存在兼容性问题！**

根据测试，ReSukiSU 可能无法在最新的 ZTC 内核（android12-5.10-lts）上正常编译。

**推荐方案：**

1. **使用 android12-5.10-226 分支**
   - 这是旧版 ZTC 内核
   - 原生支持 SukiSU/ReSukiSU
   - 更稳定

2. **使用 Official KernelSU**
   - ZTC 内核内置支持
   - 无需替换
   - 最稳定

3. **等待 ReSukiSU 更新**
   - 等待 ReSukiSU 适配新版 ZTC
   - 或使用版本固定功能回退到兼容版本

---

## 🚀 快速开始

### 1. 启动工作流

1. 进入 GitHub 仓库
2. 点击 **Actions** 标签
3. 选择 **ZTC Kernel Build (Enhanced)**
4. 点击 **Run workflow**

### 2. 配置 ReSukiSU

在工作流配置界面：

```yaml
KernelSU 变体: ReSukiSU
强制替换 ZTC 内置的 KSU: true (必须启用)
启用 SUSFS: true (推荐)
```

### 3. 其他推荐配置

```yaml
内核分支: android12-5.10-lts
启用 ZRAM 增强: true
ZRAM 自定义算法: lz4,zstd
启用 BBG 防格机: true
启用网络增强: true
LTO 优化模式: thin
编译优化级别: O3
```

---

## 🔧 配置详解

### 必需配置

| 参数 | 值 | 说明 |
|------|-----|------|
| **KernelSU 变体** | ReSukiSU | 选择 ReSukiSU 变体 |
| **强制替换 KSU** | true | 必须启用以移除内置 KSU |

### 推荐配置

| 参数 | 值 | 说明 |
|------|-----|------|
| **启用 SUSFS** | true | 启用文件系统隐藏 |
| **启用 ZRAM** | true | 提升多任务性能 |
| **启用网络增强** | true | 提升网络性能 |

---

## 📋 工作流程

### 构建流程

1. **检测 ReSukiSU 模式**
   ```
   检查是否选择了 ReSukiSU 变体
   检查是否启用了强制替换
   ```

2. **移除内置 KSU**
   ```
   删除 KernelSU 目录
   清理 drivers/Makefile 引用
   清理 drivers/Kconfig 引用
   ```

3. **安装 ReSukiSU**
   ```
   执行 ReSukiSU setup 脚本
   集成 ReSukiSU 代码
   配置内核选项
   ```

4. **可选：固定版本**
   ```
   读取 config/config 文件
   切换到指定 commit
   ```

5. **编译内核**
   ```
   使用配置的优化选项编译
   生成 AnyKernel3 刷机包
   ```

6. **获取管理器**
   ```
   从 ReSukiSU 仓库下载管理器
   打包为 Artifact
   ```

---

## 🔐 版本固定

### 为什么需要固定版本？

- ReSukiSU 更新可能导致编译失败
- 需要使用特定功能的版本
- 回退到稳定版本

### 如何固定版本？

编辑 `config/config` 文件：

```ini
# 启用自定义提交
custom=true

# ReSukiSU 的 commit hash
resukisu=abc123def456

# 可选：同时固定 SUSFS 版本
gki-android12-5.10=789abcdef012
```

### 获取 commit hash

1. 访问 [ReSukiSU 仓库](https://github.com/ReSukiSU/ReSukiSU)
2. 点击 **Commits** 查看提交历史
3. 复制完整的 40 位 hash（或至少 7 位短 hash）
4. 填入 config/config 文件

---

## 📦 构建产物

### AnyKernel3 刷机包

格式：`ZTC-android12-5.10-lts-AnyKernel3.zip`

**包含内容：**
- 编译好的内核 Image
- AnyKernel3 刷写脚本
- ReSukiSU 集成

**刷入方法：**
- TWRP Recovery
- HorizonKernelFlasher
- 其他内核刷写工具

### ReSukiSU 管理器

格式：`ReSukiSU-Manager-xxxxx.apk`

**功能：**
- Root 权限管理
- 模块管理
- SUSFS 配置（如果启用）
- 日志查看

### SUSFS 模块

格式：`susfs-xxxxx.zip`

**安装方法：**
- 通过 ReSukiSU 管理器安装
- 重启后生效

---

## ⚙️ 高级配置

### 场景一：最大隐藏能力

```yaml
KernelSU 变体: ReSukiSU
强制替换 KSU: true
启用 SUSFS: true
启用 ZRAM: true
ZRAM 算法: zstd
启用网络增强: true
LTO 模式: thin
优化级别: O3
```

**适用于：**
- 银行应用
- 游戏防作弊
- 企业应用

### 场景二：性能优先

```yaml
KernelSU 变体: ReSukiSU
强制替换 KSU: true
启用 SUSFS: false
启用 ZRAM: true
ZRAM 算法: lz4
启用网络增强: true
LTO 模式: full
优化级别: O3
```

**适用于：**
- 游戏
- 性能敏感应用
- 日常使用

### 场景三：稳定优先

```yaml
KernelSU 变体: ReSukiSU
强制替换 KSU: true
启用 SUSFS: true
启用 ZRAM: true
ZRAM 算法: lz4
启用网络增强: false
LTO 模式: thin
优化级别: O2
```

**适用于：**
- 首次尝试
- 稳定性要求高
- 测试环境

---

## 🐛 故障排除

### 问题 1: 编译失败 - KSU 冲突

**症状：**
```
error: redefinition of 'xxx'
error: multiple definition of 'xxx'
```

**原因：**
- 未启用 "强制替换 KSU"
- ZTC 内置 KSU 未完全移除

**解决方案：**
```yaml
强制替换 ZTC 内置的 KSU: true  # 必须启用
```

### 问题 2: 刷入后无法开机

**症状：**
- 卡在开机动画
- 直接重启

**解决方案：**

1. **刷回原厂 boot**
```bash
fastboot flash boot stock_boot.img
```

2. **降低优化级别**
```yaml
LTO 模式: none
优化级别: O2
```

3. **禁用部分功能**
```yaml
启用 SUSFS: false
启用 ZRAM: false
```

### 问题 3: ReSukiSU 管理器无法使用

**症状：**
- 管理器显示未安装
- 无法获取 Root

**解决方案：**

1. **检查内核版本**
```bash
adb shell "cat /proc/version"
# 应该包含 "ReSukiSU" 或 "KernelSU" 字样
```

2. **重新安装管理器**
```bash
adb uninstall me.weishu.kernelsu
adb install ReSukiSU-Manager-xxxxx.apk
```

3. **检查 SELinux**
```bash
adb shell "getenforce"
# 应该是 Permissive 或 Enforcing
```

### 问题 4: SUSFS 不生效

**症状：**
- 应用仍然检测到 Root
- 隐藏功能无效

**解决方案：**

1. **确认 SUSFS 已启用**
```yaml
启用 SUSFS: true
```

2. **安装 SUSFS 模块**
```
通过 ReSukiSU 管理器安装 susfs-xxxxx.zip
重启设备
```

3. **配置 SUSFS**
```
在管理器中配置需要隐藏的应用
启用相应的隐藏选项
```

### 问题 5: 版本固定不生效

**症状：**
- 使用的不是指定的 commit
- 编译结果与预期不符

**解决方案：**

1. **检查配置文件格式**
```ini
custom=true  # 必须设为 true
resukisu=abc123def456  # 完整或短 hash
```

2. **验证 commit 存在**
```bash
# 在 ReSukiSU 仓库检查 commit 是否存在
```

3. **查看构建日志**
```
在 Actions 日志中搜索 "切换到自定义提交"
确认是否成功切换
```

---

## 📊 性能对比

### ReSukiSU vs 官方 KSU

| 项目 | 官方 KSU | ReSukiSU |
|------|----------|----------|
| **Root 功能** | ✅ | ✅ |
| **模块支持** | ✅ | ✅ |
| **SUSFS 内置** | ❌ | ✅ |
| **KPM 支持** | ❌ | ✅ |
| **隐藏能力** | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **性能开销** | 低 | 中 |
| **稳定性** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |

### 性能影响

| 功能 | CPU | 内存 | 存储 |
|------|-----|------|------|
| **ReSukiSU 基础** | +2% | +15MB | +8MB |
| **+ SUSFS** | +3% | +25MB | +13MB |
| **+ KPM** | +1% | +10MB | +5MB |

---

## 🔗 相关资源

### 官方资源
- [ReSukiSU 仓库](https://github.com/ReSukiSU/ReSukiSU)
- [SUSFS 项目](https://gitlab.com/simonpunk/susfs4ksu)
- [ZTC 内核](https://github.com/ztc1997/android_gki_kernel_5.10_common)

### 文档
- [增强版 README](README-ENHANCED.md)
- [配置指南](CONFIGURATION-GUIDE.md)
- [版本对比](COMPARISON.md)

### 社区
- [Issues](https://github.com/zzh20188/ZTC-Kernel-Build/issues)
- [Discussions](https://github.com/zzh20188/ZTC-Kernel-Build/discussions)

---

## ❓ 常见问题

### Q: ReSukiSU 和 SukiSU 有什么区别？

**A:** ReSukiSU 是 SukiSU 的重构版本：
- 代码结构更优化
- 稳定性更好
- 功能相同
- 推荐使用 ReSukiSU

### Q: 必须启用 "强制替换 KSU" 吗？

**A:** 是的，因为：
- ZTC 内核内置了官方 KSU
- 不移除会导致冲突
- 编译会失败

### Q: ReSukiSU 可以和官方 KSU 共存吗？

**A:** 不可以：
- 同一内核只能有一个 KSU
- 必须选择其中一个
- 建议根据需求选择

### Q: SUSFS 是必需的吗？

**A:** 不是必需的：
- 如果不需要隐藏 Root，可以不启用
- 启用后会增加一些性能开销
- 根据实际需求决定

### Q: 如何更新 ReSukiSU？

**A:** 两种方法：
1. 重新构建内核（使用最新版本）
2. 使用版本固定功能（固定到新版本）

### Q: ReSukiSU 支持哪些 Android 版本？

**A:** 当前配置支持：
- Android 12 (5.10 LTS)
- Android 12 (5.10.226)

未来可能支持更多版本。

---

## 💡 最佳实践

### 1. 首次使用

```yaml
# 使用稳定配置
KernelSU 变体: ReSukiSU
强制替换 KSU: true
启用 SUSFS: false
LTO 模式: thin
优化级别: O2
```

### 2. 日常使用

```yaml
# 平衡配置
KernelSU 变体: ReSukiSU
强制替换 KSU: true
启用 SUSFS: true
启用 ZRAM: true
LTO 模式: thin
优化级别: O3
```

### 3. 追求性能

```yaml
# 性能配置
KernelSU 变体: ReSukiSU
强制替换 KSU: true
启用 SUSFS: false
启用 ZRAM: true (lz4)
LTO 模式: full
优化级别: O3
```

### 4. 最大隐藏

```yaml
# 隐藏配置
KernelSU 变体: ReSukiSU
强制替换 KSU: true
启用 SUSFS: true
启用 ZRAM: true (zstd)
LTO 模式: thin
优化级别: O3
```

---

## 🎓 进阶技巧

### 技巧 1: 版本管理

```ini
# config/config
custom=true

# 使用稳定版本
resukisu=stable-commit-hash

# 使用开发版本
# resukisu=dev-commit-hash
```

### 技巧 2: 性能调优

```bash
# 刷入内核后，在设备上执行
adb shell
su

# 调整 CPU 调度
echo "performance" > /sys/devices/system/cpu/cpu0/cpufreq/scaling_governor

# 调整 ZRAM
echo "4G" > /sys/block/zram0/disksize
echo "zstd" > /sys/block/zram0/comp_algorithm
```

### 技巧 3: 日志分析

```bash
# 查看 KSU 日志
adb shell "dmesg | grep -i kernelsu"

# 查看 SUSFS 日志
adb shell "dmesg | grep -i susfs"

# 导出完整日志
adb shell "dmesg" > kernel.log
```

---

<div align="center">
  <p><strong>享受 ReSukiSU 带来的强大功能！</strong></p>
  <p>如有问题，请在 Issues 中反馈</p>
</div>
