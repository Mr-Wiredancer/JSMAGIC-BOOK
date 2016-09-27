<video style="width:100%" controls>
  <source src="http://7xslga.com1.z0.glb.clouddn.com/nitrous.mp4" type="video/mp4">
  Your browser does not support HTML5 video.
</video>

思客的教程都是基于 MacOS 创建的，在大部分 Linux 系统上面的差异并不大，不过 Windows 的环境就会相差很多。除非你很熟悉 Windows 下的命令行和 Node.js 开发环境，否则我们会建议你先使用一个云端的开发环境，比如下面我们要介绍的 [Nitrous.io](https://www.nitrous.io)。

首先去 nitrous.io 上面注册一个帐户，可以直接使用你的 github 帐户。

然后创建一个新的 "Work Station"，当然是选择 Node.js 环境了。

进去之后你就会看到上方的编辑器和下方的命令行，这是一个全功能的云端操作系统

Nitrous.io 上面默认使用的是 Node.js 6.0 版本。我们的课程使用的是 6.6，有强迫证的我当然是要安装一下 6.6 版本：

```bash
$ nvm install 6.6
$ nvm alias default 6.6
$ node --version
6.6.0
```

然后你就可以跟随 JS 黑魔法的教程继续学习啦。

很重要的一点要记住，每次学习完之后，要把工作台停止。点击右上角的图像，然后选择 "Stop Workstation"。因为 Nitrous.io 是有每月使用额度的，所以要节省一点用。你可以在下一次学习的时候再次打开。

最后一个要提醒的是，Nitrous.io 里面使用的是 Zsh，所以在尝试修改命令行配置的时候，你要修改的文件是 `~/.zshrc`