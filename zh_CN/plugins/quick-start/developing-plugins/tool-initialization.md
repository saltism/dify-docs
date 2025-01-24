# 初始化开发工具

开发 Dify 插件需要进行以下准备。

* Dify 插件脚手架工具
* Python 环境，版本号 ≥ 3.12

### **1. 安装 Dify 插件开发脚手架工具**

访问脚手架工具的 [GitHub 项目地址](https://github.com/langgenius/dify-plugin-daemon/releases)，推荐下载并安装最新版本号和对应操作系统的工具。

本文**以装载 M 系列芯片的 macOS** 为例。下载 `dify-plugin-darwin-arm64` 文件后，赋予其执行权限。

```
chmod +x dify-plugin-darwin-arm64
```

运行以下命令检查安装是否成功。若终端返回类似 `v0.0.1-beta.15` 的版本号信息，则说明安装成功。

```
./dify-plugin-darwin-arm64 version
```

> 若提示 “Apple 无法验证” 错误，请前&#x5F80;**“设置 → 隐私与安全性 → 安全性”**，轻点 “仍要打开” 按钮。

运行命令后，终端若返回类似 `v0.0.1-beta.15` 的版本号信息，则说明安装成功。

{% hint style="info" %}
**Tips:**&#x20;

如果想要在系统全局使用 `dify` 命令运行脚手架工具，建议将该二进制文件重命名为 `dify` 并拷贝至 `/usr/local/bin` 系统路径内。

配置完成后，在终端输入 `dify version` 命令后将输出版本号信息。

<img src="https://assets-docs.dify.ai/2025/01/74e57a57c1ae1cc70f4a45084cbbb37e.png" alt="" data-size="original">
{% endhint %}

### **2. 初始化 Python 环境**

详细说明请参考 [Python 安装教程](https://pythontest.com/python/installing-python-3-11/)，或询问 LLM 获取完整的安装教程。

### 3. 开发插件

请参考以下内容查看不同类型的插件开发示例。

{% content-ref url="tool-type-plugin.md" %}
[tool-type-plugin.md](tool-type-plugin.md)
{% endcontent-ref %}

{% content-ref url="model/" %}
[model](model/)
{% endcontent-ref %}

{% content-ref url="agent-strategy.md" %}
[agent-strategy.md](agent-strategy.md)
{% endcontent-ref %}

{% content-ref url="extension.md" %}
[extension.md](extension.md)
{% endcontent-ref %}

{% content-ref url="bundle.md" %}
[bundle.md](bundle.md)
{% endcontent-ref %}



