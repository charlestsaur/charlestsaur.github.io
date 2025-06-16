# iPhone photo backup

[< Index >](/index.md)

---

使用 Image Capture（图像捕捉）

用数据线将 iPhone 连接到 Mac，并解锁 iPhone。

打开 Image Capture（图像捕捉）（可在“应用程序” > “实用工具”找到）。

选择你的 iPhone 作为设备，在右下角的“导入到”选择 外置硬盘 作为目标文件夹。

点击 “全部导入” 或 选择性导入。

在导入完成后可以统计一下文件夹里有多少图片和视频

1. 统计所有图片和视频数量

输入以下命令（把 /路径/到/文件夹 替换成你的目标文件夹路径）：

```bash

find "/路径/到/文件夹" \( -iname "*.jpg" -o -iname "*.png" -o -iname "*.gif" -o -iname "*.bmp" -o -iname "*.tiff" -o -iname "*.heic" -o -iname "*.mp4" -o -iname "*.mov" -o -iname "*.avi" -o -iname "*.mkv" -o -iname "*.flv" \) | wc -l

```

回车，它会返回 图片+视频的总数。

2. 分别统计图片和视频的数量

如果你想 分开统计，可以使用：

```bash

echo "图片数量：" && find "/路径/到/文件夹" \( -iname "*.jpg" -o -iname "*.png" -o -iname "*.gif" -o -iname "*.bmp" -o -iname "*.tiff" -o -iname "*.heic" \) | wc -l
echo "视频数量：" && find "/路径/到/文件夹" \( -iname "*.mp4" -o -iname "*.mov" -o -iname "*.avi" -o -iname "*.mkv" -o -iname "*.flv" \) | wc -l


```

这个命令会分别统计 图片 和 视频 的数量。

[< Index >](/index.md)