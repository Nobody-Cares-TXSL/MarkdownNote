# VUE
## npm
### 配置npm
```bash
# 配置npm镜像源为阿里云镜像源
npm config set registry https://registry.npmmirror.com

# 查看镜像源
npm config get registry
```

### 命令
```bash
# 初始化项目，生成 package.json 文件
npm init

# 安装指定的包
npm install <package-name>

# 卸载指定的包
npm uninstall <package-name>

# 运行 package.json 中定义的脚本命令
npm run <script-name>
```

## Vite

### 创建项目
```bash
# 创建新的 Vite 项目（交互式选择框架）
npm create vite

# 一步创建 Vue 项目
npm create vite@latest my-vue-app -- --template vue
```

### 开发命令
| 命令 | 说明 |
|------|------|
| `npm run dev` | 启动开发服务器（支持热更新） |
| `npm run build` | 构建生产版本（输出到 `dist/` 目录） |
| `npm run preview` | 预览生产构建 |