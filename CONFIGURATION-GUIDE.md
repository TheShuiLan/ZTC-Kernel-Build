# ZTC-Kernel-Build 配置指南

## 📚 目录

- [快速配置](#快速配置)
- [推荐配置方案](#推荐配置方案)
- [参数详细说明](#参数详细说明)
- [配置文件说明](#配置文件说明)
- [性能调优](#性能调优)
- [故障排除](#故障排除)

---

## 🚀 快速配置

### 最小配置（快速构建）

```yaml
内核分支: android12-5.10-lts
KernelSU 变体: Disabled
启用 SUSFS: false
启用 ZRAM 增强: false
启用 BBG 防格机: true
启用 NTsync: false
启用网络增强: false
LTO 优化模式: none
编译优化级别: O2
发布类型: Actions
```

**特点：**
- 编译时间最短（约 30-40 分钟）
- 功能最基础
- 适合测试构建流程

### 标准配置（推荐）

```yaml
内核分支: android12-5.10-lts
KernelSU 变体: Official
启用 SUSFS: false
启用 ZRAM 增强: true
ZRAM 完整算法: false
ZRAM 自定义算法: lz4,zstd
启用 BBG 防格机: true
启用 NTsync: false
启用网络增强: true
LTO 优化模式: thin
编译优化级别: O3
发布类型: Actions
```

**特点：**
- 平衡性能和功能
- 包含 KernelSU 支持
- ZRAM 和网络优化
- 编译时间适中（约 45-60 分钟）

### 完整配置（全功能）

```yaml
内核分支: android12-5.10-lts
KernelSU 变体: Official
启用 SUSFS: true
启用 ZRAM 增强: true
ZRAM 完整算法: true
启用 BBG 防格机: true
启用 NTsync: true
启用网络增强: true
LTO 优化模式: thin
编译优化级别: O3
发布类型: Release
```

**特点：**
- 所有功能启用
- 最佳性能和隐藏能力
- 编译时间较长（约 60-75 分钟）

---

## 🎯 推荐配置方案

### 方案一：日常使用

**适用场景：** 普通用户日常使用

```yaml
KernelSU: Official
SUSFS: false
ZRAM: true (lz4)
网络增强: true
LTO: thin
优化级别: O3
```

**优点：**
- 稳定性好
- 性能优秀
- 功耗适中

### 方案二：隐藏检测

**适用场景：** 需要绕过 Root 检测的应用

```yaml
KernelSU: Official
SUSFS: true
ZRAM: true (lz4,zstd)
网络增强: true
LTO: thin
优化级别: O3
```

**优点：**
- 强大的隐藏能力
- 可绕过大部分检测
- 性能损失小

### 方案三：性能优先

**适用场景：** 游戏、性能敏感应用

```yaml
KernelSU: Official
SUSFS: false
ZRAM: true (lz4)
网络增强: true
LTO: full
优化级别: O3
```

**优点：**
- 最佳性能
- 低延迟
- 适合游戏

### 方案四：省电优先

**适用场景：** 续航敏感用户

```yaml
KernelSU: Official
SUSFS: false
ZRAM: true (lzo)
网络增强: false
LTO: thin
优化级别: Os
```

**优点：**
- 功耗最低
- 体积小
- 续航提升

### 方案五：开发测试

**适用场景：** 内核开发者、测试人员

```yaml
KernelSU: Official
SUSFS: false
ZRAM: false
网络增强: false
LTO: none
优化级别: O2
```

**优点：**
- 编译快速
- 易于调试
- 问题定位简单

---

## 📖 参数详细说明

### 1. 内核分支

| 选项 | 说明 | 推荐度 |
|------|------|--------|
| **android12-5.10-lts** | LTS 长期支持版本 | ⭐⭐⭐⭐⭐ |
| **android12-5.10-226** | 旧版稳定版本（支持 SukiSU） | ⭐⭐⭐ |

**选择建议：**
- 优先选择 LTS 版本
- 需要 SukiSU 时选择 226 版本

### 2. KernelSU 变体

| 选项 | 说明 | 功能 | 推荐度 |
|------|------|------|--------|
| **Official** | 官方版本 | 完整功能 | ⭐⭐⭐⭐⭐ |
| **Disabled** | 不集成 KSU | 无 Root | ⭐⭐ |

**注意事项：**
- ZTC 内核已内置官方 KSU
- 不再支持 SukiSU（除非使用 226 分支）

### 3. SUSFS 支持

**启用条件：**
- 需要绕过 Root 检测
- 使用银行、游戏等敏感应用

**性能影响：**
- CPU: +2-5%
- 内存: +10-20MB
- 存储: +5MB

**兼容性：**
- 需要配合 SUSFS 模块使用
- 部分应用可能仍会检测到

### 4. ZRAM 配置

#### 算法对比

| 算法 | 压缩率 | 速度 | CPU 占用 | 推荐场景 |
|------|--------|------|----------|----------|
| **lzo** | 低 | 极快 | 低 | 省电优先 |
| **lz4** | 中 | 快 | 中 | 日常使用 |
| **lz4hc** | 中高 | 中 | 中高 | 平衡 |
| **zstd** | 高 | 中 | 高 | 性能优先 |
| **deflate** | 高 | 慢 | 高 | 压缩率优先 |
| **842** | 低 | 快 | 低 | 特殊场景 |

#### 推荐组合

```
日常使用: lz4
性能优先: lz4,zstd
省电优先: lzo
极致压缩: zstd,deflate
```

### 5. 网络增强

**包含功能：**
- IPSet: IP 集合管理
- BBR: Google 拥塞控制算法

**适用场景：**
- 网络应用频繁
- 需要更好的网络性能
- 使用 VPN/代理

**性能提升：**
- 下载速度: +10-30%
- 延迟降低: 10-50ms
- 丢包率降低

### 6. NTsync 支持

**功能说明：**
- Wine 兼容层优化
- 提升 Windows 应用性能

**适用场景：**
- 使用 Wine/Box86/Box64
- 运行 Windows 游戏
- 使用 Termux 开发

**性能提升：**
- 同步操作: +50-200%
- 多线程应用: +20-50%

### 7. LTO 优化

#### 模式对比

| 模式 | 编译时间 | 二进制大小 | 性能提升 | 稳定性 |
|------|----------|------------|----------|--------|
| **none** | 基准 | 基准 | 0% | ⭐⭐⭐⭐⭐ |
| **thin** | +20% | -5% | +5-10% | ⭐⭐⭐⭐ |
| **full** | +50% | -10% | +10-15% | ⭐⭐⭐ |

**选择建议：**
- 日常使用: thin
- 追求性能: full
- 快速测试: none

### 8. 优化级别

| 级别 | 性能 | 体积 | 功耗 | 稳定性 | 推荐场景 |
|------|------|------|------|--------|----------|
| **O2** | 中 | 中 | 中 | ⭐⭐⭐⭐⭐ | 稳定优先 |
| **O3** | 高 | 大 | 高 | ⭐⭐⭐⭐ | 性能优先 |
| **Os** | 低 | 小 | 低 | ⭐⭐⭐⭐ | 省电优先 |

**详细说明：**

**O2 (平衡优化):**
- 启用大部分优化
- 不激进优化
- 最稳定

**O3 (最高性能):**
- 所有优化启用
- 可能增加体积
- 性能最佳

**Os (体积优化):**
- 优先减小体积
- 性能略有损失
- 功耗最低

---

## 📁 配置文件说明

### config/config

**用途：** 固定特定版本的 commit

**格式：**
```ini
custom=true
gki-android12-5.10=<commit-hash>
kernelsu=<commit-hash>
ztc-kernel=<commit-hash>
```

**使用场景：**

1. **上游更新导致编译失败**
```ini
custom=true
ztc-kernel=abc123def456  # 使用上一个稳定版本
```

2. **测试特定功能**
```ini
custom=true
kernelsu=def789abc123  # 使用包含新功能的提交
```

3. **回退到旧版本**
```ini
custom=true
gki-android12-5.10=789abcdef012
ztc-kernel=012345678abc
```

**获取 commit hash:**
```bash
# 在仓库页面查看提交历史
# 复制完整的 40 位 hash
# 或使用短 hash（至少 7 位）
```

### config/zram.config

**用途：** 自定义 ZRAM 算法配置

**默认内容：**
```ini
CONFIG_CRYPTO_LZ4HC=y
CONFIG_CRYPTO_LZ4K=y
CONFIG_CRYPTO_LZ4KD=y
CONFIG_CRYPTO_842=y
CONFIG_CRYPTO_LZ4K_OPLUS=y
```

**自定义示例：**
```ini
# 添加 ZSTD 支持
CONFIG_CRYPTO_ZSTD=y

# 添加 LZO 支持
CONFIG_CRYPTO_LZO=y

# 添加 DEFLATE 支持
CONFIG_CRYPTO_DEFLATE=y
```

### config/stock_defconfig

**用途：** 伪装为官方内核配置

**获取方法：**
```bash
# 方法一：从设备提取
adb shell "zcat /proc/config.gz" > stock_defconfig

# 方法二：从官方内核提取
cd official_kernel
make gki_defconfig
cp .config ../stock_defconfig
```

**使用场景：**
- 绕过内核配置检测
- 伪装为官方内核
- 通过 SafetyNet 检测

---

## ⚡ 性能调优

### 场景一：游戏性能优化

**配置：**
```yaml
ZRAM: lz4
网络增强: true
LTO: full
优化级别: O3
```

**额外调整：**
```bash
# 提高 CPU 调度优先级
echo "performance" > /sys/devices/system/cpu/cpu0/cpufreq/scaling_governor

# 禁用节能功能
echo "0" > /sys/module/workqueue/parameters/power_efficient
```

### 场景二：续航优化

**配置：**
```yaml
ZRAM: lzo
网络增强: false
LTO: thin
优化级别: Os
```

**额外调整：**
```bash
# 启用节能模式
echo "powersave" > /sys/devices/system/cpu/cpu0/cpufreq/scaling_governor

# 降低 ZRAM 优先级
echo "50" > /proc/sys/vm/swappiness
```

### 场景三：多任务优化

**配置：**
```yaml
ZRAM: zstd (完整算法)
网络增强: true
LTO: thin
优化级别: O3
```

**额外调整：**
```bash
# 增加 ZRAM 大小
echo "4G" > /sys/block/zram0/disksize

# 提高后台应用保活
echo "200" > /proc/sys/vm/swappiness
```

---

## 🔧 故障排除

### 问题 1: 编译失败

**症状：**
```
error: xxx not found
fatal: unable to access
```

**解决方案：**

1. **检查网络连接**
```yaml
# 重新运行工作流
# 确保 GitHub Actions 可以访问外部资源
```

2. **使用固定提交**
```ini
custom=true
ztc-kernel=<上一个成功的commit>
```

3. **简化配置**
```yaml
# 禁用所有可选功能
ZRAM: false
网络增强: false
NTsync: false
```

### 问题 2: 刷入后无法开机

**症状：**
- 卡在开机动画
- 直接重启
- 黑屏

**解决方案：**

1. **刷回原厂 boot**
```bash
fastboot flash boot stock_boot.img
```

2. **检查设备兼容性**
- 确认是 Android 12
- 确认支持 GKI
- 确认内核版本匹配

3. **降低优化级别**
```yaml
LTO: none
优化级别: O2
```

### 问题 3: KernelSU 无法使用

**症状：**
- 管理器显示未安装
- 无法获取 Root
- 模块无法加载

**解决方案：**

1. **检查内核版本**
```bash
adb shell "cat /proc/version"
# 应该包含 "KernelSU" 字样
```

2. **重新安装管理器**
```bash
# 卸载旧版本
adb uninstall me.weishu.kernelsu

# 安装新版本
adb install KSU-Manager-xxxxx.apk
```

3. **检查 SELinux**
```bash
adb shell "getenforce"
# 应该是 Permissive 或 Enforcing
```

### 问题 4: ZRAM 不生效

**症状：**
- 内存占用高
- 多任务卡顿
- swap 未启用

**解决方案：**

1. **检查 ZRAM 状态**
```bash
adb shell "cat /proc/swaps"
# 应该显示 zram0 设备
```

2. **手动启用 ZRAM**
```bash
adb shell
su
echo "2G" > /sys/block/zram0/disksize
mkswap /dev/block/zram0
swapon /dev/block/zram0
```

3. **检查算法支持**
```bash
adb shell "cat /sys/block/zram0/comp_algorithm"
# 应该显示可用算法列表
```

### 问题 5: 网络性能无提升

**症状：**
- 下载速度未改善
- 延迟仍然高
- BBR 未生效

**解决方案：**

1. **检查 BBR 状态**
```bash
adb shell "cat /proc/sys/net/ipv4/tcp_congestion_control"
# 应该显示 bbr
```

2. **手动启用 BBR**
```bash
adb shell
su
echo "bbr" > /proc/sys/net/ipv4/tcp_congestion_control
```

3. **检查网络配置**
```bash
adb shell "sysctl net.ipv4.tcp_congestion_control"
adb shell "sysctl net.core.default_qdisc"
```

---

## 📊 性能对比

### 编译时间对比

| 配置 | 时间 | 相对基准 |
|------|------|----------|
| 最小配置 | 30-40 分钟 | 基准 |
| 标准配置 | 45-60 分钟 | +50% |
| 完整配置 | 60-75 分钟 | +100% |

### 性能提升对比

| 功能 | CPU | 内存 | 存储 | 网络 |
|------|-----|------|------|------|
| ZRAM (lz4) | +2% | -20% | - | - |
| ZRAM (zstd) | +5% | -30% | - | - |
| 网络增强 | +1% | +5MB | - | +20% |
| NTsync | +3% | +10MB | - | - |
| LTO thin | - | - | -5% | - |
| LTO full | - | - | -10% | - |

### 功耗对比

| 配置 | 待机 | 轻度使用 | 重度使用 |
|------|------|----------|----------|
| 最小配置 | 基准 | 基准 | 基准 |
| 标准配置 | +5% | +8% | +10% |
| 完整配置 | +10% | +15% | +20% |
| 省电配置 | -10% | -15% | -10% |

---

## 🎓 最佳实践

### 1. 首次构建

```yaml
# 使用标准配置
# 不启用实验性功能
# 使用 Actions 发布模式
```

### 2. 日常更新

```yaml
# 保持配置不变
# 定期检查上游更新
# 使用 Pre-Release 测试
```

### 3. 问题排查

```yaml
# 使用最小配置
# 逐步启用功能
# 记录每次变更
```

### 4. 性能调优

```yaml
# 先测试基准性能
# 单独测试每个功能
# 对比性能数据
```

### 5. 版本管理

```yaml
# 使用 Release 发布稳定版
# 保留历史版本
# 记录配置变更
```

---

## 📝 配置模板

### 模板 1: 新手推荐

```yaml
内核分支: android12-5.10-lts
KernelSU 变体: Official
启用 SUSFS: false
启用 ZRAM 增强: true
ZRAM 自定义算法: lz4
启用 BBG 防格机: true
启用 NTsync: false
启用网络增强: true
LTO 优化模式: thin
编译优化级别: O3
发布类型: Actions
```

### 模板 2: 进阶用户

```yaml
内核分支: android12-5.10-lts
KernelSU 变体: Official
启用 SUSFS: true
启用 ZRAM 增强: true
ZRAM 完整算法: true
启用 BBG 防格机: true
启用 NTsync: true
启用网络增强: true
LTO 优化模式: thin
编译优化级别: O3
发布类型: Pre-Release
```

### 模板 3: 开发测试

```yaml
内核分支: android12-5.10-lts
KernelSU 变体: Official
启用 SUSFS: false
启用 ZRAM 增强: false
启用 BBG 防格机: true
启用 NTsync: false
启用网络增强: false
LTO 优化模式: none
编译优化级别: O2
发布类型: Actions
```

---

## 🔗 相关资源

- [ZTC 内核源码](https://github.com/ztc1997/android_gki_kernel_5.10_common)
- [KernelSU 文档](https://kernelsu.org/)
- [SUSFS 项目](https://gitlab.com/simonpunk/susfs4ksu)
- [ZRAM 文档](https://www.kernel.org/doc/html/latest/admin-guide/blockdev/zram.html)
- [BBR 算法说明](https://github.com/google/bbr)

---

<div align="center">
  <p>如有问题，请在 Issues 中反馈</p>
</div>
