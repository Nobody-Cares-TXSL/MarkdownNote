# Ubuntu 实用命令

## 排除某个软件包升级
```bash
# 锁定某个软件包，防止升级
sudo apt-mark hold <package-name>

# 解锁某个软件包，允许升级
sudo apt-mark unhold <package-name>

# 查看当前锁定的软件包
sudo apt-mark showhold
```

## 查看端口占用
```bash
# 查看所有端口占用
sudo ss -tuln

# 查看指定端口占用
sudo ss -tuln | grep :<port-number>
```

## 查看显卡硬件和驱动
```bash
# 简洁版：看两张显卡 + 正在用的驱动
sudo lshw -c video -short

# 详细版：看每张卡的内核驱动（推荐）
lspci -k | grep -EA3 'VGA|Display|3D'

# 只看 AMD 和 Intel 的 PCI 位置
lspci -nn | grep -E 'VGA|Display'
```

## 检查驱动是否加载 + 内核日志
```bash
# 查看当前加载的显卡模块（应该看到 i915 和 amdgpu）
lsmod | grep -E "amdgpu|i915"

# 查看 AMD 固件加载情况（重点看有没有 Failed）
sudo dmesg | grep -E "amdgpu.*firmware|Failed to load firmware|amdgpu"

# 查看最近的显卡相关日志（方便排查问题）
sudo journalctl -b -p err | grep -E 'i915|amdgpu|drm'
```
