## 在windows上编译
- 不要使用WSL系统: https://www.electronjs.org/zh/docs/latest/tutorial/tutorial-first-app#%E5%88%9B%E5%BB%BA%E9%A1%B9%E7%9B%AE
- 主要是借鉴: https://github.com/freelensapp/freelens/wiki/Development
- github action: [release.yaml](.github/workflows/release.yaml)
### 预先安装的软件
#### nodejs
```cmd
node -v
v22.20.0
```
#### 安装pnpm
```cmd
npm install -g pnpm@latest-10
```
#### IDE配置验证
查看IDE的`Languages & Frameworks`->Node.js里的2个设置不为空

### 安装依赖
先执行安装依赖, 在cmd.exe的命令行下, electron_mirror主要是为了electron的安装
```
pnpm config set electron_mirror https://npmmirror.com/mirrors/electron/
pnpm config set registry https://registry.npmmirror.com
pnpm install
```
安装其他依赖,避免`Error: Could not find any Visual Studio installation to use`错误:
- 打开下载页面：https://visualstudio.microsoft.com/visual-cpp-build-tools/
- 下载并安装 “Build Tools for Visual Studio”
- 安装时，勾选：
  - Desktop development with C++（桌面开发 C++）
  - Windows 10 SDK（或者 Windows 11 SDK）
- 安装完成后，重启终端。 验证：
```cmd
vswhere
```
MSB8040的问题: https://www.jianshu.com/p/89243d94f309

### pnpm build
在 freelens/package.json 文件的 config 部分定义了以下二进制文件版本：
- kubectl: v1.34.1
- helm: v3.19.0
- freelens-k8s-proxy: v1.4.0
在 freelens/package.json 文件的 config 部分定义了:
```jsx
  "config": {
    "k8sProxyVersion": "1.4.0",
    "bundledKubectlVersion": "1.34.1",
    "bundledHelmVersion": "3.19.0",
    "contentSecurityPolicy": "script-src 'unsafe-eval' 'self'; frame-src https://*.renderer.freelens.app:*/; img-src * data:",
    "welcomeRoute": "/welcome"
  }
```
在`async ensureBinary(): Promise<void> {}`里设置了环境变量和超时
可以设置LENS_SKIP_DOWNLOAD_BINARIES=true, 就会跳过下载二进制文件
```jsx
set LENS_SKIP_DOWNLOAD_BINARIES=true pnpm build
```
### pnpm build:app dir

### 打包
实际打包
```cmd
pnpm --color=always build:app msi nsis --x64
```
这条命令来自于[release.yaml](.github/workflows/release.yaml)里的这段代码:
```yaml
      - name: Build Electron app (Windows)
        if: runner.os == 'Windows'
        shell: bash
        run: |
          pnpm --color=always build:app \
            msi nsis \
            --${{ matrix.arch }}
```




## 添加功能
命令行关闭的时候, 添加1个选项,`Close tabs to the left`
![close_tab.png](mine/pics/close_tab.png)
