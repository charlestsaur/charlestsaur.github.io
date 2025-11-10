# iPhone Photo Backup

[< Index >](../index.md)

---

Use Image Capture

Connect your iPhone to your Mac with a data cable and unlock your iPhone.

Open Image Capture (found in Applications > Utilities).

Select your iPhone as the device and select External Hard Drive as the destination folder in "Import to" in the lower right corner.

Click "Import All" or Import Selectively.

After the import is complete, you can count how many pictures and videos there are in the folder

1. Count all pictures and videos

Enter the following command (replace /path/to/folder with your target folder path):

```bash

find "/path/to/folder" \( -iname "*.jpg" -o -iname "*.png" -o -iname "*.gif" -o -iname "*.bmp" -o -iname "*.tiff" -o -iname "*.heic" -o -iname "*.mp4" -o -iname "*.mov" -o -iname "*.avi" -o -iname "*.mkv" -o -iname "*.flv" \) | wc -l

```

Press Enter, it will return the total number of pictures + videos.

2. Count the number of images and videos separately

If you want to count them separately, you can use:

```bash

echo "Number of images:" && find "/path/to/folder" \( -iname "*.jpg" -o -iname "*.png" -o -iname "*.gif" -o -iname "*.bmp" -o -iname "*.tiff" -o -iname "*.heic" \) | wc -l
echo "Number of videos:" && find "/path/to/folder" \( -iname "*.mp4" -o -iname "*.mov" -o -iname "*.avi" -o -iname "*.mkv" -o -iname "*.flv" \) | wc -l

```

This command will count the number of images and videos separately.

[< Index >](../index.md)
