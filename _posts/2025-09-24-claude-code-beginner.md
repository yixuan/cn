---
date: '2025-09-24 22:22:35'
layout: post
slug: claude-code-beginner
status: publish
title: 小白上手 Claude Code
categories:
- 学习中
tags:
- AI
- 大语言模型
- 编程
- 程序
- Claude Code
- VS Code
- 服务器
- 开源
---

AI 辅助编程已经流行了好一阵子了，但一直没找到合适的机会去体验。正好最近在学习 Megatron 这个大模型训练框架，第一眼看来代码量大，而且逻辑关系复杂，所以就准备尝试一下用 AI 来辅助代码理解。目前市面上有不少可用的工具，像是 [Cursor](https://cursor.com/)，[Gemini CLI](https://github.com/google-gemini/gemini-cli)，[Claude Code](https://github.com/anthropics/claude-code) 等等。因为不想和开发环境做太多绑定，所以就暂时不考虑 Cursor 这样自带编辑器的工具了。这样下来主要对比的就是 Gemini CLI 和 Claude Code。

一开始我确实尝试了一下 Gemini CLI，但它目前只支持使用 Gemini 模型，而且国内访问比较麻烦，所以一番折腾之后就决定换成 Claude Code。Claude Code 的好处是能更换基座模型，而且国内的大模型好几家都支持 Anthropic 的 API，例如 [GLM](https://docs.bigmodel.cn/cn/guide/develop/claude)、[DeepSeek V3.1](https://api-docs.deepseek.com/zh-cn/guides/anthropic_api)，[Qwen3-Coder](https://qwenlm.github.io/zh/blog/qwen3-coder/) 等。这里就简单记录一下配置的过程，当作是学习笔记。

<div align="center">
    <img src="https://upload.yixuan.blog/cn/2025/09/claude_logo.png" alt="Claude Code" width="70%" />
</div>

<!-- more -->

### 第1步：远程服务器配置

我总体的设想是把代码仓库放在远程 Linux 服务器上（其实就是我办公室的工作站），然后本地用 VS Code 访问服务器，同时运行 Claude Code 的交互式命令行工具。这一步主要是设置好 SSH 访问的密钥，否则 VS Code 每次连接服务器都要输密码，不太方便。

以 Windows 为例，在命令行中输入

```bash
ssh-keygen -t rsa
```

并遵循提示，就会在 `C:/Users/<用户名>/.ssh` 文件夹下创建 `id_rsa` 和 `id_rsa.pub` 两个文件，其中 `id_rsa` 是私钥，只应该存放在本地机器上，而 `id_rsa.pub` 是公钥，可以用文本编辑器打开并查看其中的内容。公钥中的字符串将被复制到远程服务器的 `~/.ssh/authorized_keys` 文件中。

在 VS Code 中，首先安装好 Remote - SSH 插件，然后点击左下角的连接按钮，就可以进入远程服务器的配置。

<div align="center">
    <img src="https://upload.yixuan.blog/cn/2025/09/remote.png" alt="远程服务器连接" />
</div>

依次选择弹出窗口中的“Connect to Host...”和“Add New SSH Host...”选项，然后输入连接的命令，如下图所示，包括用户名（`qyx`）、服务器 IP 地址（`192.168.192.38`）和端口号（`22`）：

<div align="center">
    <img src="https://upload.yixuan.blog/cn/2025/09/remote_address.png" alt="输入服务器信息" />
</div>

之后 VS Code 会提示要将配置存放在何处，一般用第一项就行（通常是 `C:/Users/<用户名>/.ssh/config`）。为了之后能让 VS Code 通过密钥进行自动连接，我们可以再次点击左下角的连接按钮，依次选择“Connect to Host...”和“Configure SSH Hosts...”，打开之前选择的配置文件，此时可以看到若干配置选项。其中第一行的 `Host` 栏是你自己给服务器的备注名称，可以更改成其他更容易分辨的字符串，而我们还需要另外加上一行 `IdentityFile`（注意缩进），用来给出本地机器上私钥的文件位置。大概的格式如下：

<div align="center">
    <img src="https://upload.yixuan.blog/cn/2025/09/remote_config.png" alt="服务器配置" width="70%" />
</div>

设置好后，在 VS Code 侧边栏找到远程访问标签，然后在刚才添加的服务器处点击按钮“Connect in Current Window...”，即可进行连接。

<div align="center">
    <img src="https://upload.yixuan.blog/cn/2025/09/remote_connect.png" alt="连接服务器" width="50%" />
</div>

第一次连接时，VS Code 可能会让你选择服务器的操作系统，并提示输入密码。完成之后，就可以在侧边栏选择打开远程文件夹，默认是用户目录，此时可能需要再输一次密码。

<div align="center">
    <img src="https://upload.yixuan.blog/cn/2025/09/remote_open.png" alt="打开远程文件夹" width="50%" />
</div>

这一次登录的主要目的是打开并编辑 `~/.ssh/authorized_keys` 这个文件。如果文件还不存在，就直接建立一个新的，然后在这个文件的某一新行输入之前公钥 `id_rsa.pub` 中的字符串，通常是以 `ssh-rsa` 开头，大概的格式如下。

<div align="center">
    <img src="https://upload.yixuan.blog/cn/2025/09/remote_keys.png" alt="输入公钥内容" width="70%" />
</div>

为了增强安全性，最好对这个文件再设置访问权限。打开 VS Code 的远程终端（菜单栏 Terminal → New Terminal），然后输入命令更改权限：

```bash
chmod 600 ~/.ssh/authorized_keys
```

完成之后，可以尝试退出远程服务器再重新连接，如果不再需要输入密码，就说明基于密钥的登录方式配置成功了。

### 第2步：本地安装 Node.js 和 Claude Code

把远程服务器配置好以后，就可以开始安装 Clause Code 了。按照官方教程，最终需要通过 Node.js 将 Claude Code 的可执行文件全局安装到系统中，但我不是特别希望使用 root 权限更改系统文件，所以就选择了本地安装 Node.js 和 Claude Code。具体方法如下。

首先，本地安装 Node.js 的核心是使用 [nvm](github.com/nvm-sh/nvm) 这个 Node.js 管理器。在终端中输入如下命令，无需 root 权限：

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
```

它会在用户目录下创建一个名为 `.nvm` 的文件夹，并在 `~/.bashrc` 中加入一段脚本：

```bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
```

重启终端后，就可以自动识别到 `nvm` 程序了，可以运行如下命令查看是否配置成功：

```bash
nvm --version
```

接下来即可安装 Node.js，并检查版本：

```bash
nvm install --lts
npm --version
```

最后全局安装 Claude Code：

```bash
npm install -g @anthropic-ai/claude-code
```

虽然是全局安装，但 Claude Code 的可执行文件实际上是存放在 `~/.nvm/versions/node/v22.19.0/bin/` 中的（路径中的版本号可能不同），所以完全没有依赖 root 权限。此时我们先不要直接运行 Claude Code，而是去配置一下相关的环境变量。

### 第3步：配置 Claude Code

由于 Claude Code 本身只是一个客户端，它主要的分析和编写代码的能力都是通过调用大语言模型 API 来实现的。为了方便访问，我选择的是智谱 GLM 提供的 API，它需要在 `~/.bashrc` 中加入如下两个环境变量定义：

```bash
## For Claude Code with GLM 
export ANTHROPIC_BASE_URL="https://open.bigmodel.cn/api/anthropic"
export ANTHROPIC_AUTH_TOKEN="Your API Key"
```

其中第一个 URL 的取值是 GLM 官方提供的，第二个 API Key 可以从 GLM 的[相关页面](https://open.bigmodel.cn/usercenter/proj-mgmt/apikeys)获取，其他几家大模型厂商也有相应的 URL 和 API Key 设置。

重启终端，输入 `claude` 命令，再经过确认访问权限后，就可以看到 Claude Code 的主界面了：

<div align="center">
    <img src="https://upload.yixuan.blog/cn/2025/09/claude_1.png" alt="Claude Code 界面" />
</div>

几个与程序本身有关的命令如下：

- `/config`：若干针对 Claude Code 的全局配置，例如更改主题颜色。
- `/status`：查看版本号、工作目录、API 配置等信息。
- `/help`：查看帮助，打印出所有可用的命令。
- `/exit` 或 `/quit`：退出 Claude Code。

目前我们还没有涉及到具体的代码项目，因此可以先简单问一些问题，测试大语言模型是否正常工作：

<div align="center">
    <img src="https://upload.yixuan.blog/cn/2025/09/claude_2.png" alt="测试大语言模型功能" />
</div>

为了更好地让 Claude Code 与 VS Code 交互，推荐在 VS Code 中安装 Claude Code for VS Code 这个插件。安装完成之后，就可以通过右上角的按钮随时启动 Claude Code。此外，下一节会展示通过这个插件来让 Claude Code 和 VS Code 进行联动的一些功能。

<div align="center">
    <img src="https://upload.yixuan.blog/cn/2025/09/claude_extension.png" alt="Claude Code for VS Code 插件" />
</div>

### 第4步：使用 Claude Code 分析代码项目

接下来就结合一个具体的代码项目来真正发挥 Claude Code 的功能。我近期在关注的一个项目叫 [Megatron](https://github.com/NVIDIA/Megatron-LM/)，它是一个分布式训练大语言模型的框架，由 Nvidia 开发。此外，阿里也在 Megatron 的基础上做了一个补充版本 [Pai-Megatron-Patch](https://github.com/alibaba/Pai-Megatron-Patch)，实际训练模型的时候一般会同时用到这两个库。

为了理解这两个库的代码逻辑和相互之间的关系，我尝试使用 Claude Code 来进行代码分析。首先将两个库的源代码分别下载到一个名为 `Megatron` 的文件夹中，然后以这个文件夹作为项目的根目录（启动 Claude Code 时也应该从这里进入）。

在正式和 Claude Code 对话前可能最重要的一步就是运行 `/init` 这个命令，它会对代码的结构和内容进行分析，并将分析结果存放到一个名为 `CLAUDE.md` 的 Markdown 文件中。这个文件包含了 Claude Code 对于项目的总结，之后在这个项目中与 Claude Code 对话时，它都会将 `CLAUDE.md` 里的内容加入到上下文中，因此 `CLAUDE.md` 文件也被称为 Claude Code 的记忆。

<div align="center">
    <img src="https://upload.yixuan.blog/cn/2025/09/claude_init.png" alt="初始化项目" />
</div>

`CLAUDE.md` 是可以自由编辑的。使用 Claude Code 自带的 `/memory` 命令就会打开这个文件，此外在 Claude Code 命令框中输入 `#` 号也可以将文字添加到记忆中。事实上，这个文件也是可以手动编辑的，有任何能帮助理解项目的内容都可以添加进这个文件中。

接下来我们可以开始实际做一些代码分析的工作。例如我提问“本项目中哪些文件与 Attention 的核心实现相关？”，Claude Code 便给我列举出了若干相关的代码实现。

<div align="center">
    <img src="https://upload.yixuan.blog/cn/2025/09/claude_attention.png" alt="搜索 Attention 相关代码文件" />
</div>

我们也可以利用文字指令来操作文件。例如输入“请帮我将上面提及的代码文件都用 VS Code 打开。”，Claude Code 就会执行相应的系统命令来打开文件。因为涉及命令行操作，因此在执行前 Claude Code 会向你申请授权，这个时候选择对后续所有文件都同意，就可以批量打开文件进行阅读了。

<div align="center">
    <img src="https://upload.yixuan.blog/cn/2025/09/claude_open.png" alt="打开文件指令" />
    <img src="https://upload.yixuan.blog/cn/2025/09/claude_open_files.png" alt="执行打开文件指令" />
</div>

对于一些更深入的问题，例如代码理解，只需要像用其他大语言模型一样提问即可。例如我让 Claude Code 对比两种 MLA 实现的区别：

<div align="center">
    <img src="https://upload.yixuan.blog/cn/2025/09/claude_diff.png" alt="总结两种 MLA 实现的区别" />
</div>

注意到 Claude Code 回答中的一些元素在命令行中没有得到很好的渲染（例如表格），因此我们可以让 Claude Code 将上述回答保存为一个 Markdown 文件，然后利用 VS Code 的预览功能就能更清晰地查看回答内容：

<div align="center">
    <img src="https://upload.yixuan.blog/cn/2025/09/claude_diff_md.png" alt="将结果保存为 Markdown 文件" />
</div>

在安装了 VS Code 插件后，有一个特性非常重要，就是 Claude Code 能够感知当前正在查看和编辑的是哪个文件，因此可以直接针对它进行提问。

<div align="center">
    <img src="https://upload.yixuan.blog/cn/2025/09/claude_locate.png" alt="Claude Code 感知当前文件" />
</div>

此外，如果你在 VS Code 中选中了某些代码，Claude Code 也会捕获到：

<div align="center">
    <img src="https://upload.yixuan.blog/cn/2025/09/claude_selected.png" alt="Claude Code 感知选中内容" />
</div>

这个功能一个常见的用法就是提问选中的部分在代码中有什么作用，例如下面的操作：

<div align="center">
    <img src="https://upload.yixuan.blog/cn/2025/09/claude_selected_function.png" alt="基于选中内容提问" />
</div>

### 一些额外的小技巧

在退出 Claude Code 之前，可以运行一下 `/cost` 这个命令，它会告诉你本次会话 Claude Code 消耗了多少大语言模型的输入和输出，并依此估计调用 API 的费用（仅对使用 Claude 官方 API 的场景有意义）。**这都是白花花的银子啊。**

另外，可以让 Claude Code 将本次会话的内容进行总结，并保存为日志文件。当然，这种方式下 Claude Code 不会把会话中的所有细节都记录下来。如果想要完整地把对话的所有输入输出都保存下来，可以在启动 Claude Code 之前运行 `script` 命令，如下所示：

```bash
script claude_code_2025xxxx.log
claude
```

这样退出 Claude Code 之后再在终端中运行 `exit` 命令，就会将会话内容保存到指定的日志文件中，便于之后进行回顾。

### 总体感受

以上这些大概就是我觉得比较核心的功能了。由于我也是刚刚入门 Claude Code，因此有很多使用技巧还在慢慢摸索中。目前我也只是使用 Claude Code 来理解和分析项目，还没有大规模尝试让它来编写和改动代码。这部分的经验和技巧总结就等未来充分体验以后再来写了。

总体来说，上面这些简单的使用和操作还是给我留下了深刻的印象的，我也确实体会到 AI 工具可以加速阅读代码、理解代码的过程，而这放在以前可能是一段非常枯燥且痛苦的经历。我感觉 Claude Code，或者说更广泛的 AI 辅助编程工具，以后应该会变得越来越普遍。虽然我目前还是尽可能不去让 AI 来代替我自己进行思考和代码编写，但我也不敢说未来的趋势是什么样。就当前而言，我是非常愿意使用 Claude Code 这样的工具来帮助我进行代码的梳理和理解的，因为某种程度上这件事“不需要做得很精确”，这也是我认为**当前的** AI 工具最擅长的领域。但涉及到真正的代码编写这种需要一定精确性保证的场景，我还是会犹豫一下，至少利用 AI 工具编写之后我还是会要求自己从头读一遍，并大致验证一下代码逻辑。

提到 AI 编程，我总会联想到 [xkcd 上的一张梗图](https://xkcd.com/2347/)：

<div align="center">
    <img src="https://upload.yixuan.blog/cn/2025/09/xkcd.png" alt="xkcd 漫画" width="50%" />
</div>

我对于 AI 编程是没有太多偏见的，但我不喜欢的是人为将 AI 编程神化或傲慢化。我始终觉得，众多开源软件的作者对于大语言模型的贡献是不可估量的，但绝大多数已经商业化的大语言模型厂商并没有相应地对开源软件及其作者进行必要的回馈。AI 编程取得如今的成就，离不开“祂们”基于一大批高质量代码和数据的“原始积累”。而在完成“工业化”之后，如果还用 AI 编程去打击曾经的那批原始贡献者（例如裁员），就有些本末倒置了。

当然，AI 工具的发展可能不会因个人的意愿而有大的改变。目前能做的更多是让自己去尽快适应新工具的使用逻辑，最好是还能切实提升工作的效率。等未来哪天自己写不动代码的时候，我一定第一时间向 AI 投降！

