<h1 align="center">
  <img src="packaging/flatpak/share/icons/hicolor/128x128/apps/io.github.ladaapp.lada.png" alt="Lada Icon" style="display: block; width: 64px; height: 64px;">
  <br>
  Lada
</h1>

[English](https://github.com/CowanNath/lada/blob/main/README_EN.md)

*Lada* 是一个用于恢复像素化成人视频（JAV）的工具。它帮助恢复此类内容的视觉质量，使其观看起来更加愉悦。

## 功能特点

- **恢复像素化视频**：恢复成人视频中的像素化或马赛克场景。
- **观看/导出视频**：使用CLI或GUI观看或导出恢复后的视频。

## 使用方法

### 图形界面(GUI)

打开文件后，您可以实时观看恢复后的视频，或将其导出到新文件以便稍后观看：

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/screenshot_gui_1_dark.png">
  <source media="(prefers-color-scheme: light)" srcset="assets/screenshot_gui_1_light.png">
  <img alt="视频预览截图" src="assets/screenshot_gui_1_dark.png" width="45%">
</picture>
<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/screenshot_gui_2_dark.png">
  <source media="(prefers-color-scheme: light)" srcset="assets/screenshot_gui_2_light.png">
  <img alt="视频导出截图" src="assets/screenshot_gui_2_dark.png" width="45%">
</picture>

其他设置可以在左侧边栏中找到。

### 命令行界面(CLI)

您也可以使用命令行界面(CLI)来恢复视频：

```shell
lada-cli --input <输入视频路径>
```
<img src="assets/screenshot_cli_1.png" alt="视频导出截图" width="45%">

有关其他选项的更多信息，请使用 `--help` 参数。

> [!TIP]
> Lada会将恢复后的视频写入临时文件，然后将其与原始文件的音频流合并，并保存到选定的目标位置。
> 您可以通过将 `TMPDIR` 环境变量设置为您选择的其他位置来覆盖[默认位置](https://docs.python.org/3/library/tempfile.html#tempfile.gettempdir)。

## 恢复选项

Lada利用专门的模型来处理处理流程的两个主要步骤：检测和恢复。您可以为每个任务选择不同的模型。

**马赛克恢复模型：**

*   **basicvsrpp-v1.2（默认）**：在多样化视频场景上训练的通用模型。大多数情况下提供良好的结果。
*   **deepmosaics**：来自[DeepMosaics](https://github.com/HypoX64/DeepMosaics)项目的恢复模型。质量比basicvsrpp-v1.2差。

> [!NOTE]
> 在大多数/所有情况下，DeepMosaics模型应该表现更差。集成它是因为DeepMosaics项目不再维护，我想提供一种简单的方式来尝试和比较。

**马赛克检测模型：**

*   **v3.1-fast（默认）**：快速高效。
*   **v3.1-accurate**：比v3.1-fast稍微更准确，但更慢。并非总是比v2更好。
*   **v2**：所有模型中最慢的，但通常比v3.1-accurate提供更好的马赛克检测，但结果因人而异。

您可以在侧面板中配置模型，或者在使用CLI时通过指定模型的路径和类型作为参数。

## 性能和硬件要求
不要期望这个工具能完美工作，有些场景可能会相当好且接近真实情况。其他场景可能相当一般，并显示出比原始马赛克更差的伪影。

您需要一块GPU和一些耐心来运行这个应用程序。如果您的显卡至少有4-6GB的显存，那么它应该可以直接工作。

CPU用于编码恢复后的视频，所以也不应该太慢。但您也可以使用GPU编码，在GPU上运行恢复和编码任务。

该应用程序还需要相当多的RAM用于缓冲以提高吞吐量。对于1080p内容，6-8GB RAM应该足够，4K将需要更多。

要实时观看恢复后的视频，您需要一台性能相当强大的机器，否则您会看到播放器暂停和缓冲，直到计算出下一个恢复的帧。
观看视频时不进行编码，但它会使用更多的额外RAM进行缓冲。

如果您的GPU不够快，无法实时观看视频，您将不得不先导出它，然后用您喜欢的媒体播放器稍后观看（GUI和CLI中均可用）。

技术上讲，在CPU上运行应用程序也是支持的，但它会非常慢，实际上不太实用。

以下是在我的可用硬件上使用Lada v0.7.0的一些速度性能数据，让您有一个大致的预期（使用libx264/CPU编解码器和默认设置；RTX 3090结果受CPU编码限制，通过切换到NVENC/GPU编码器可以快得多）：

| 视频名称 | 视频描述                                                                                     | 视频<br>时长 / 分辨率 / FPS | Lada<br>运行时间 / FPS<br>Nvidia RTX 3050<br>(*笔记本GPU*) | Lada<br>运行时间 / FPS<br>Nvidia RTX 3090<br>(台式机GPU) |
|------------|------------------------------------------------------------------------------------------------|--------------------------------------|------------------------------------------------------------|-----------------------------------------------------------|
| vid1       | 所有帧上都存在多个马赛克区域                                                                 | 1分30秒 / 10920x1080 / 30 FPS        | 3分36秒 / 12 FPS                                           | 1分33秒 / 30 FPS                                          |
| vid2       | 所有帧上都存在单个马赛克区域                                                                 | 3分0秒 / 1920x1080 / 30 FPS          | 4分11秒 / 21 FPS                                           | 2分16秒 / 39 FPS                                          |
| vid3       | 视频一半没有任何马赛克，<br>另一半主要是每帧单个马赛克                                       | 41分16秒 / 852x480 / 30 FPS          | 26分30秒 / 46 FPS                                          | 10分20秒 / 119 FPS                                        |

## 安装
### 使用Flatpak
在Linux上安装应用程序（CLI和GUI）的最简单方式是通过Flathub：

<a href='https://flathub.org/apps/details/io.github.ladaapp.lada'><img width='200' alt='从Flathub下载' src='https://flathub.org/api/badge?svg&locale=en'/></a>

> [!NOTE]
> Flatpak只适用于x86_64 CPU和Nvidia/CUDA GPU（Turing或更新版本：RTX 20xx到包括RTX 50xx）。确保您的NVIDIA GPU驱动程序是最新的。
> 它也可以在没有GPU的情况下使用，但会非常慢。

> [!TIP]
> 安装后，您应该在应用程序启动器中找到Lada来启动GUI。您也可以通过`flatpak run io.github.ladaapp.lada`运行它。

> [!TIP]
> 通过Flatpak使用CLI时，我们需要通过授予文件系统权限来使文件/目录可用，以便它可以访问输入/输出文件
>  ```shell
>  flatpak run --filesystem=host --command=lada-cli io.github.ladaapp.lada --input <输入视频路径>
>  ```
> 您可能想要设置一个别名以便于使用
> ```shell
> alias lada-cli="flatpak run --filesystem=host --command=lada-cli io.github.ladaapp.lada"
>  ```
> 您也可以通过[Flatseal](https://flathub.org/apps/com.github.tchx84.Flatseal)永久授予文件系统权限

> [!TIP]
> 如果您从Flathub安装了Lada，而拖放功能不起作用，您的文件浏览器可能不支持[文件传输门户](https://flatpak.github.io/xdg-desktop-portal/docs/doc-org.freedesktop.portal.FileTransfer.html)。
> 您可以通过以下方式解决：
>  1) 切换或更新您的文件浏览器到支持它的版本。
>  2) 授予应用程序文件系统权限（例如，通过[Flatseal](https://flathub.org/apps/com.github.tchx84.Flatseal)），以便它可以直接读取文件。
>  3) 使用"打开"按钮选择文件，而不是拖放。

### 使用Docker

该应用程序也可通过Docker使用（仅CLI）。您可以从[Docker Hub](https://hub.docker.com/r/ladaapp/lada)获取镜像`ladaapp/lada`，使用以下命令：

```shell
docker pull ladaapp/lada:latest
````

> [!NOTE]
> Docker镜像只适用于x86_64 CPU和Nvidia/CUDA GPU（Turing或更新版本：RTX 20xx到包括RTX 50xx）。确保您的NVIDIA GPU驱动程序是最新的。
> 它也可以在没有GPU的情况下使用，但会非常慢。

> [!TIP]
> 确保您已在系统上安装了[NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html)，以便Docker可以传递GPU

> [!TIP]
> 使用Docker时，您需要使文件/目录对容器可用，以及GPU：
>  ```shell
> docker run --rm --gpus all --mount type=bind,src=<输入视频路径>,dst=/mnt ladaapp/lada:latest --input "/mnt/<输入视频文件>"
>  ```

### 使用Windows

对于Windows用户，应用程序（CLI和GUI）被打包为独立的.zip文件。

从[发布页面](https://github.com/ladaapp/lada/releases)获取最新版本。
.zip文件在*Assets*部分中可用。解压缩存档后，您会找到´lada.exe´和´lada-cli.exe´。

> [!NOTE]
> Docker镜像只适用于x86_64 CPU和Nvidia/CUDA GPU（Turing或更新版本：RTX 20xx到包括RTX 50xx）。确保您的NVIDIA GPU驱动程序是最新的。
> 它也可以在没有GPU的情况下使用，但会非常慢。

> [!NOTE]
> 请注意，lada.exe或lada-cli.exe的首次启动可能需要一段时间，直到Windows Defender或您的防病毒软件扫描它。下次打开程序时，它应该会快速启动。

> [!TIP]
> GitHub Releases上的文件每个限制为2GB，所以我不得不分割文件。
> 下载两个文件（´<version>.zip.001´和´<version>.zip.002´）。然后在[7-zip](https://7-zip.org/)中打开第一个文件。
> 您应该能够看到并提取包含.exe文件的*lada*文件夹以及另一个包含应用程序依赖项的子文件夹。

### 其他安装方法

如果上述软件包不适合您，那么您必须按照[构建](#build)步骤来设置项目。

请注意，这些说明主要是为开发人员设置环境以开始处理源代码而设计的。但即使您不是开发人员，您也应该能够按照说明进行操作。

所有软件包目前只适用于Nvidia卡（或CPU），但有报告称按照构建说明，较新的Intel Xe GPU也能正常工作。
AMD GPU可能也能工作，但可能不适用于Windows，因为PyTorch/ROCm构建仅适用于Linux。

如果您能支持为其他操作系统或硬件打包应用程序，请联系我们。

## 构建
如果您想开始修改这个项目，您需要从源代码安装应用程序。查看[Linux](docs/linux_install.md)和[Windows](docs/windows_install.md)的详细安装指南。

## 训练和数据集创建
有关训练您自己的模型和数据集的说明，请参阅[训练和数据集创建](docs/training_and_dataset_creation.md)。

## 致谢
这个项目建立在这些出色的个人和项目所做的工作基础上：

* [DeepMosaics](https://github.com/HypoX64/DeepMosaics)：提供了马赛克数据集创建的代码。也启发我开始这个项目。
* [BasicVSR++](https://ckkelvinchan.github.io/projects/BasicVSR++) / [MMagic](https://github.com/open-mmlab/mmagic)：用作马赛克移除的基础模型。
* [YOLO/Ultralytics](https://github.com/ultralytics/ultralytics)：用于训练马赛克和NSFW检测模型。
* [DOVER](https://github.com/VQAssessment/DOVER)：用于评估数据集创建过程中创建的片段的视频质量，以过滤掉低质量片段。
* [DNN Watermark / PITA Dataset](https://github.com/tgenlis83/dnn-watermark)：使用其大部分代码创建水印检测数据集，用于过滤被文本/水印/标志遮挡的场景。
* [NudeNet](https://github.com/notAI-tech/NudeNet/)：用作额外的NSFW分类器，以过滤我们自己NSFW分割模型的误报。
* [Twitter Emoji](https://github.com/twitter/twemoji)：提供茄子表情符号作为应用程序图标的基础。
* [Real-ESRGAN](https://github.com/xinntao/Real-ESRGAN)：使用他们的图像降质模型设计来设计我们的马赛克检测模型降质流程。
* PyTorch、FFmpeg、GStreamer、GTK和[构建我们生态系统的所有其他人](https://xkcd.com/2347/)
