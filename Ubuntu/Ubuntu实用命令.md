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

