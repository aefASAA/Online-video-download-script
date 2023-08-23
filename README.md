# script
Online video download script based on Tampermonkey</br>
</br>
</br>
将脚本中的代码复制到浏览器的Tampermonkey插件中即可</br>
![Alt Text](https://github.com/aefASAA/Online-video-download-script/blob/main/x5.png)</br>

<h2>一定记得要点击保存才有效<h2>
# 目录

- [安装方法](#安装方法)
- [使用方法](#使用方法)
- [脚本的运行原理](#脚本的运行原理)

## 安装方法
将脚本中的代码复制到浏览器的[Tampermonkey](https://www.tampermonkey.net/)
插件中即可</br>
![Alt Text](https://github.com/aefASAA/Online-video-download-script/blob/main/x5.png)</br>
<h4>一定记得要点击保存才有效<h4>

## 使用方法
打开任意视频网站，启用脚本</br>
示例：[bilibili.com](https://www.bilibili.com/video/BV1ak4y1479b/?spm_id_from=333.1007.top_right_bar_window_history.content.click&vd_source=a5e7e92452c5fb5e7ffe4e8360b808e5)</br>
等待视频完成播放，或者缓存完成</br>
之后浏览器会开始自动下载，部分浏览器会有下载提示，同意即可</br>
【注意：由于本脚本采取的是缓存的方式下载，所以下载的文件由两部分组成——音频与视频】</br>
## 脚本的运行原理
1. 首先，脚本会在全局作用域中创建一些变量，如 autoDownload、isComplete、audio、video、downloadAll 和 quickPlay。

2. 然后，脚本会修改 MediaSource 对象的 endOfStream 和 addSourceBuffer 方法。MediaSource 对象是 HTML5 中用于处理媒体流的对象。

- endOfStream 方法被修改为在调用原始方法后将 isComplete 变量设置为 1。
- addSourceBuffer 方法被修改为在调用原始方法后，根据 MIME 类型清空 audio 或 video 数组，并修改 SourceBuffer 对象的 appendBuffer 方法，使其在调用原始方法后将缓冲区的数据添加到 audio 或 video 数组。

3. download 函数会创建一个新的 a 元素，并设置其 href 属性为 audio 或 video 数组的 Blob 对象的 URL，然后设置其 download 属性为 'audio_' 或 'video_' 加上网页的标题，最后调用 click 方法来下载文件。

4. checkAndDownload 函数会在 downloadAll 或 isComplete 变量为 1 时调用 download 函数。

5. 脚本会每 2 秒调用一次 checkAndDownload 函数，如果 autoDownload 变量为 1，那么还会额外每 2 秒调用一次 checkAndDownload 函数。

6. 最后，脚本会移除所有 iframe 元素的 'sandbox' 属性，并在完成后清除定时器。
