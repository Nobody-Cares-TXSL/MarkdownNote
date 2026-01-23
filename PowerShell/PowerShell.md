# PowerShell
## 修改 PowerShell 编码为 UTF-8
```pwsh
# 打开$PROFILE 文件(如果文件不存在，会自动创建)
notepad $PROFILE

# 统一输入输出编码为 UTF-8，解决中文乱码
$OutputEncoding = [console]::InputEncoding = [console]::OutputEncoding = New-Object System.Text.UTF8Encoding
```
保存并重启 PowerShell
- `chcp` 查看当前编码
> 当编码为 65001 时，PowerShell 编码为 UTF-8  
> 当编码为 936 时，PowerShell 编码为 GBK