# PowerShell
## 修改 Windows PowerShell 编码为 UTF-8
1. 打开注册表
`Win + R` 打开运行窗口，输入 `regedit` 打开注册表
2. 定位到：`HKEY_CURRENT_USER\Console\%SystemRoot%_system32_cmd.exe`
3. 修改 `CodePage` 十进制值为 `65001`
没有`CodePage`项时，新建一个 `鼠标右键-> 新建 -> DWORD(32位)值` 项，命名为 `CodePage`，十进制值为 `65001`
## 修改 PowerShell 7.x 编码为 UTF-8
```powershell
# 打开$PROFILE 文件
notepad $PROFILE

# 1. 存在 $PROFILE 文件
# 统一输入输出编码为 UTF-8，解决中文乱码
$OutputEncoding = [console]::InputEncoding = [console]::OutputEncoding = New-Object System.Text.UTF8Encoding

# 2. 不存在 $PROFILE 文件
# 查看$PROFILE 文件路径
$PROFILE
# 根据路径创建 $PROFILE 文件
# 或者直接使用 New-Item 命令创建 $PROFILE 文件
New-Item -Path $PROFILE -ItemType File -Force
# 然后在 $PROFILE 文件中添加以下内容
$OutputEncoding = [console]::InputEncoding = [console]::OutputEncoding = New-Object System.Text.UTF8Encoding
```
保存并重启 PowerShell
- `chcp` 查看当前编码
> 当编码为 65001 时，PowerShell 编码为 UTF-8  
> 当编码为 936 时，PowerShell 编码为 GBK

## 在 PowerShell 中 启动 git bash 终端
```powershell
# 确认是否存在配置
Test-Path $PROFILE
# 如果返回 False，输入 New-Item -Type File -Force $PROFILE 创建它。

# 确认 Git 的安装位置
Get-Command git | Select-Object Source
# 比如输出: D:\Git\Git\cmd\git.exe
# 则可确认bash的执行文件在: D:\Git\Git\bin\bash.exe

# 打开$PROFILE 文件
notepad $PROFILE

# 在$PROFILE 文件中添加以下内容
# 双引号内填确认的bash执行文件的路径
function bash {
    & "D:\Git\Git\bin\bash.exe" $args
}

# 重新加载配置
. $PROFILE
```
以后直接在 PowerShell 中输入 `bash` 即可启动 git bash 终端