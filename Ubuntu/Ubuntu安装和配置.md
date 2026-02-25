# Ubuntu 安装和配置

## 多系统启动U盘制作

### 下载Ubuntu ISO镜像
[清华大学镜像站](https://mirrors.tuna.tsinghua.edu.cn/ubuntu-releases/)
下载`ubuntu-24.04.4-desktop-amd64.iso`

### 制作步骤
1. 下载 [Ventoy](https://www.ventoy.net/en/download.html)。
2. 解压后运行 Ventoy2Disk.exe
3. 插入 U 盘，点击"安装"（会格式化 U 盘）
4. 在U盘创建一个`ISO文件夹`将 Windows.iso 或者 Ubuntu.iso 复制到 U 盘
5. 运行VentoyPlugin.exe
6. 点击全局控制插件, 更改菜单语言为: `zh_CN`, 设置指定搜索目录为: `ISO文件夹`
7. 退出VentoyPlugin并保存

## 双系统安装Ubuntu

### 磁盘分区
1. 打开磁盘管理工具（Win + X -> 磁盘管理）
2. 找到要安装Ubuntu的磁盘, 右键点击, 选择"压缩卷"
3. 设置压缩空间大小, 点击"压缩", 压缩后会出现一个未分配空间

> 注意: 压缩空间大小要足够大, 足以安装Ubuntu系统以及日常使用, 建议100GB以上

### 关闭快速启动(Fast Boot)和安全启动 (Secure Boot)
- 关闭快速启动
1. 打开控制面板 -> 硬件和声音 -> 电源选项
2. 点击"选择电源按钮的功能"
3. 取消勾选"启用快速启动"
4. 点击"保存更改"
- 关闭安全启动
1. 重启电脑
2. 进入BIOS设置（通常按 F2/F12/Delete 键）
3. 进入 Security → Secure Boot Configuration 
4. 将 Secure Boot 设置为 [Disabled]
5. 按 F10 保存并退出 

### 启动U盘并选择Ventoy开始安装
1. 确保U盘已插入电脑
2. 重启电脑
3. 按 F12/F11/ESC 进入启动菜单
4. 选择从 U 盘启动
5. 看到Ventoy 启动菜单界面说明成功
6. 用方向键选择要启动的系统
7. 按 Enter 确认进入以正常模式启动
8. 进入 GRUB 菜单选择 "Install Ubuntu" 或 "Try Ubuntu without installing" 开始安装流程

### 安装向导 - 基础设置

- **语言选择**：中文（可选）
- **安装类型**：交互式安装
- **应用程序**：默认集合
- **专有软件**：勾选以下选项
  - 为图形和 Wi-Fi 硬件安装第三方软件
  - 下载并安装对其他媒体格式的支持
- **分区设置**: [手动分区](#手动分区)
- **设置用户账户**  
  - 活动目录（不勾选）
- **确认并安装** 
  - 安装完成后重启, 移除U盘

### 手动分区
对未分配空间进行分区:

| 分区 | 大小推荐 | 类型 | 说明 |
|------|----------|------|------|
| EFI | 100~500 MB | 使用现有分区 | 挂载点：`/boot/efi` |
| swap | 内存 2 倍 | 交换分区 | 休眠功能需要 ≥ 内存大小 |
| / | 50~100 GB | Ext4 | 系统分区，挂载点：`/` |
| /home | 剩余空间 | Ext4 | 用户数据，挂载点：`/home` | 

> 新分区勾选"格式化"

分区示例:

![img](https://img2024.cnblogs.com/blog/3471133/202602/3471133-20260218000030232-804104104.jpg)

### 双系统切换

开机 → GRUB 菜单 → 选择系统：

| 选项 | 操作 |
|------|------|
| Ubuntu | 按 Enter 进入 Ubuntu |
| Windows Boot Manager | 按 Enter 进入 Windows |  

## Ubuntu基础配置

1. 更新系统
   ```bash
   sudo apt update && sudo apt upgrade
   ```

2. 设置时间同步
   ```bash
   sudo timedatectl set-local-rtc 1 --adjust-system-clock
   ```

3. 更换软件源
打开“软件与更新” 程序, 选择“Ubuntu软件”选项卡, 更改下载自: 选择其他 -> 中国 -> `mirrors.cloud.tencent.com`

## 全面删除Snap
```bash
# 查看已安装的Snap软件
sudo snap list

# 卸载所有Snap软件
sudo snap remove --purge <package-name>
# 删除顺序: firefox, firmware-updater, snap-store, gnome-42-2204, gtk-common-themes, snapd-desktop-integration, bare, core22, snapd

# 卸载 snapd
sudo apt autoremove --purge snapd

# 清理残留文件
sudo rm -rf /var/cache/snapd /var/lib/snapd ~/snap /snap

# 防止 Snap 自动重新安装
# 创建 apt 偏好设置文件
sudo nano /etc/apt/preferences.d/nosnap.pref
# 添加内容：
Package: snapd
Pin: release a=*
Pin-Priority: -10
# Ctrl + O 并按 Enter 保存
# Ctrl + X 退出

# 检查是否系统有Snap残留
# 检查是否有Snap软件包 (需要自行判断删除 -> 使用 sudo apt remove --purge <package-name>)
dpkg -l | grep snap
# 自动删除不再被任何已安装软件依赖的"孤儿"包
sudo apt autoremove
# 检查snap命令是否存在 (无输出 -> Snap命令行工具已删除)
which snap
# 检查是否有Snap服务 (提示 Unit snapd.service could not be found -> 说明Snap服务已删除)
systemctl status snapd
# 检查apt里的snapd是否彻底删除 (显示已安装:(无); 候选:(无) -> snapd包彻底删除, 且APT已配置不安装Snap)
apt policy snapd
# 检查是否有Snap挂载点 (无输出 -> 没有Snap挂载点)
mount | grep snap
```

