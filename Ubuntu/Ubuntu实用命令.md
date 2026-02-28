# Ubuntu 实用命令

## 系统管理

### 软件包管理
```bash
# 锁定某个软件包，防止升级
sudo apt-mark hold <package-name>

# 解锁某个软件包，允许升级
sudo apt-mark unhold <package-name>

# 查看当前锁定的软件包
sudo apt-mark showhold
```

### 进程管理
```bash
# 查找运行中的进程
ps aux | grep nginx

# 查找正在运行的 nginx 进程
```

### 磁盘管理
```bash
# 查看磁盘使用情况（以 GB、MB 为单位显示）
df -h
```

## 硬件信息

### 查看显卡硬件和驱动
```bash
# 简洁版：看两张显卡 + 正在用的驱动
sudo lshw -c video -short

# 详细版：看每张卡的内核驱动（推荐）
lspci -k | grep -EA3 'VGA|Display|3D'

# 只看 AMD 和 Intel 的 PCI 位置
lspci -nn | grep -E 'VGA|Display'
```

### 检查驱动是否加载
```bash
# 查看当前加载的显卡模块（应该看到 i915 和 amdgpu）
lsmod | grep -E "amdgpu|i915"

# 查看 AMD 固件加载情况（重点看有没有 Failed）
sudo dmesg | grep -E "amdgpu.*firmware|Failed to load firmware|amdgpu"

# 查看最近的显卡相关日志（方便排查问题）
sudo journalctl -b -p err | grep -E 'i915|amdgpu|drm'
```

## 网络工具

### 查看端口占用
```bash
# 查看所有端口占用
sudo ss -tuln

# 查看指定端口占用
sudo ss -tuln | grep :<port-number>
```

### HTTP 请求
```bash
# 快速查看 HTTP 头信息
curl -I https://www.example.com
```

### 下载工具
```bash
# 断点续传下载（网络中断后可继续）
wget -c https://example.com/file.iso
```

## 文件操作

### 打包压缩
```bash
# 创建 tar.gz 压缩包
tar -czvf backup.tar.gz /home/user/
```

### 查找文件
```bash
# 在指定目录下搜索特定文件
find /home/user/ -name "*.log"
```

### 权限管理
```bash
# 为脚本或文件赋予可执行权限
chmod +x script.sh
```
