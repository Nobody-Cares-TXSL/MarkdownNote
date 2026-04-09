这份文档基于遇到的**Ubuntu 24.04 在 Intel 8代低压 CPU + AMD 老款独显 + Realtek 网卡**机型上的“死机/黑屏/风扇狂转”问题，总结了一套经过实战验证的完整修复方案。

此文档适用于拥有类似旧款混合显卡笔记本的用户。

---

# Ubuntu 24.04 笔记本死机与风扇狂转修复指南
**——针对 Intel 8th Gen + AMD Radeon (旧款) + Realtek RTL8821CE 硬件环境**

## 1. 故障现象与环境
### 硬件配置
*   **CPU**: Intel Core i5-8250U (8代低压处理器)
*   **显卡**: Intel UHD 620 (核显) + Radeon 500 Series (GCN架构独显)
*   **网卡**: Realtek RTL8821CE 802.11ac PCIe Adapter
*   **系统**: Ubuntu 24.04 LTS (Kernel 6.8+)

### 故障表现
1.  **硬死机 (Hard Lockup)**: 开机进入桌面 1-5 分钟后，画面彻底定格，鼠标键盘无反应。
2.  **内核恐慌 (Kernel Panic)**: 死机后约 30 秒，笔记本风扇突然加速至最大转速（轰鸣声），通常伴随黑屏。
3.  **黑屏**: 在尝试加载显卡驱动时，屏幕背光亮但无图像。

---

## 2. 根因分析 (Root Cause)
经过排查，该故障由两个核心硬件的驱动冲突共同导致：

1.  **主要元凶 (致命死机)**: **Realtek RTL8821CE 网卡**
    *   **原因**: Linux 6.8 内核自带的 `rtw88_8821ce` 驱动对该网卡的电源管理支持极差。当网卡尝试进入 ASPM (L1/L2) 深度节能状态时，会导致 PCIe 总线挂起，引发 CPU 锁死和系统崩溃。
2.  **次要元凶 (黑屏与风扇)**: **AMD Radeon 独显**
    *   **原因**: 旧款 Radeon 显卡与 Ubuntu 24.04 的 Wayland/Xorg 显示协议存在兼容性问题。同时，若为了防死机而禁用显卡驱动，显卡会因缺乏电源管理而全功率空载运行，导致发热和风扇狂转。

---

## 3. 修复方案 (Step-by-Step)

### 准备工作：进入恢复模式
由于正常启动会死机，所有操作建议在**恢复模式**或**断网环境**下进行：
1.  开机狂按 `Esc` 或 `Shift` 进入 GRUB 菜单。
2.  选择 `Advanced options for Ubuntu` -> `(recovery mode)`。
3.  选择 `root (Drop to root shell prompt)` 进入命令行。

### 步骤一：修复 Wi-Fi 致死 Bug (禁止网卡休眠)
这是解决“画面定格死机”的关键。我们需要禁止网卡进入导致崩溃的节能状态。

1.  创建配置文件：
    ```bash
    nano /etc/modprobe.d/rtw88-fix.conf
    ```
2.  写入以下内容（**注意：千万不要开启 powersupersave**）：
    ```text
    options rtw88_core disable_lps_deep=y
    options rtw88_pci disable_aspm=y
    ```
3.  保存并退出 (`Ctrl+O` -> 回车 -> `Ctrl+X`)。

### 步骤二：屏蔽不稳定的 AMD 独显
为了解决“黑屏”和“图形界面卡顿”，我们将系统限制为仅使用 Intel 核显（对于办公和开发已足够，且更稳定）。

1.  创建屏蔽文件：
    ```bash
    nano /etc/modprobe.d/blacklist-amdgpu.conf
    ```
2.  写入以下内容：
    ```text
    blacklist amdgpu
    blacklist radeon
    ```
3.  更新内核引导镜像（**必须执行，否则重启无效**）：
    ```bash
    update-initramfs -u
    ```

### 步骤三：解决屏蔽显卡后的“风扇狂转”问题
屏蔽驱动后，AMD 显卡会处于“无人监管”的通电状态，导致高热。我们需要通过 GRUB 强制主板切断其电源。

1.  编辑 GRUB 配置：
    ```bash
    nano /etc/default/grub
    ```
2.  找到 `GRUB_CMDLINE_LINUX_DEFAULT` 这一行，在引号内追加 `pcie_port_pm=force`。
    修改后应类似：
    ```text
    GRUB_CMDLINE_LINUX_DEFAULT="quiet splash pcie_port_pm=force"
    ```
    *解释：此参数强制开启 PCIe 端口电源管理。当系统发现 AMD 显卡没有驱动加载时，会自动切断该插槽的供电，风扇即可安静。*

3.  更新 GRUB：
    ```bash
    update-grub
    ```

### 步骤四：重启验证
输入 `reboot` 重启电脑。

**预期结果**：
1.  正常进入桌面，不再死机（Wi-Fi 修复生效）。
2.  风扇安静，机身凉爽（PCIe 强制节能生效）。
3.  系统流畅度提升（纯 Intel 核显模式）。

---

## 4. 后续优化 (可选)

### 安装 TLP 电源管理
进一步优化笔记本续航和温度：
```bash
sudo apt install tlp
sudo systemctl enable --now tlp
```

---

## 5. 避坑警告 (Troubleshooting)

*   **不要做的事**：千万不要执行 `options pcie_aspm policy=powersupersave`。这会强制开启最大节能，直接导致 RTL8821CE 网卡掉线甚至再次引发死机。
*   **关于 nomodeset**：不要长期使用 `nomodeset` 参数。虽然它能让你开机，但它会禁用 GPU 硬件加速，导致无法调节亮度、界面撕裂、CPU 负载过高。使用上述“屏蔽独显+保留核显”的方案是更优解。