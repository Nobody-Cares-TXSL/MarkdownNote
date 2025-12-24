# Gemini-CLI 安装教程
本文档介绍如何安装Gemini-CLI 工具。
- [Gemini-CLI Github 地址](https://github.com/google-gemini/gemini-cli)
- [官方故障指南](https://github.com/google-gemini/gemini-cli/blob/main/docs/troubleshooting.md)
# 前提
安装前的准备工作
- **Node.js 版本 20 或更高版本**
- **需要科学上网**  

提示: 以下命令均在 PowerShell 中执行,请自行根据需要修改对应命令。
# 安装
## 临时体验
```
npx https://github.com/google-gemini/gemini-cli
```
## 全局安装
```powershell
npm install -g @google/gemini-cli
```
-- 验证安装
```powershell
gemini version
```
# 身份验证
> 启动 Gemini CLI，然后选择 “使用 Google 登录” ，并按照提示完成浏览器身份验证流程。  
```
gemini
```
## 如果登录失败
- 解决方法
设置网络代理
> 请将 `port` 替换为您的代理端口号。
```
$env:https_proxy="http://127.0.0.1:port"
$env:http_proxy="http://127.0.0.1:port"
```
## 登录Gemini CLI时遇到的 GOOGLE_CLOUD_PROJECT 环境变量错误
访问`https://console.cloud.google.com/`创建Google Cloud项目  
1. 打开项目选择器新建项目
2. 在新建项目的详情页中，找到项目ID，并复制
3. 点击API和服务,再点击库,搜索`Gemini for Google Cloud API`
4. 启用`Gemini for Google Cloud API`


- 设置 GOOGLE_CLOUD_PROJECT 环境变量
```
# 创建 .gemini 目录
mkdir $HOME\.gemini -Force
# 用记事本打开配置文件（如果文件不存在会自动创建）
notepad $HOME\.gemini\.env
# 输入以下内容并保存
$env:GOOGLE_CLOUD_PROJECT="your-project-id"
```

在记事本里按 Ctrl + S 保存，然后关闭记事本。  
关闭 你当前的 PowerShell 窗口。  
重新打开 一个 PowerShell 窗口。  
输入 `gemini`重新启动 Gemini CLI。

- 如果仍然无法登录，请访问[官方故障指南](https://github.com/google-gemini/gemini-cli/blob/main/docs/troubleshooting.md)