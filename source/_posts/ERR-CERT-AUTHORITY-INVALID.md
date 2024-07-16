title: ' MAC chrome 报错 NET::ERR_CERT_AUTHORITY_INVALID '
author: Laiyong Wang
date: 2024-07-10 11:06:35
tags:
---
### 方法一：通过命令行启动Chrome并忽略证书错误（可行）
1. **打开终端**：
   在你的Mac电脑上，打开终端应用程序。

2. **运行Chrome并添加参数**：
   在终端中输入以下命令来启动Chrome，并添加`--test-type`和`--ignore-certificate-errors`参数：
   ```sh
   /Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --test-type --ignore-certificate-errors
   ```

### 方法二：通过chrome://flags设置（个人无效）
1. **打开Chrome**：
   启动Chrome浏览器。

2. **访问chrome://flags**：
   在地址栏中输入 `chrome://flags`，然后按回车键。

3. **搜索和设置标志**：
   搜索 `Allow invalid certificates for resources loaded from localhost`，然后将其设置为 “Enabled”。

4. **重启Chrome**：
   完成设置后，点击页面底部的“Relaunch”按钮重新启动Chrome，使更改生效。

### 方法三：导入自签名证书到系统信任存储（个人证书导入不成功，可自行测试）
1. **获取自签名证书**：
   你需要有自签名证书的`.crt`文件。

2. **打开钥匙串访问工具**：
   在Mac上，打开“钥匙串访问”应用程序（可以通过按`Command + Space`打开Spotlight搜索，然后输入`Keychain Access`找到并打开）。

3. **导入证书**：
   在钥匙串访问中，选择“系统”钥匙串，然后选择“文件” -> “导入项目”，找到你的`.crt`文件并导入。

4. **信任证书**：
   找到导入的证书，双击它，展开“信任”部分，将“使用此证书时”设置为“始终信任”。

5. **重新启动Chrome**：
   重新启动Chrome浏览器，证书应该被信任，不再显示错误。

### 方法四：开发环境使用临时解决方案（可行，但是多项目互相依赖很麻烦，不推荐）
如果你在开发环境中遇到这个问题，可以尝试临时解决方案：

1. **点击高级按钮**：
   在显示错误的页面上，点击“高级”按钮。

2. **继续访问（不安全）**：
   然后点击“继续访问（不安全）”，这会允许你暂时忽略证书错误，继续访问页面。