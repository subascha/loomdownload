# Downloading Loom videos
Needed to download Loom-Videos for schooling purposes

Tried some downloader extensions for both Chrome and Firefox but was this tools were not able to download a loom video. So I did a little research in the loom site sources an finally get it done by the following steps (optimization may be required but it does the job).

## Get the m3u8
Loom uses HLS streams so you first need the m3u8 playlist file.

Use the Chrome developer tools. Reload the loom site and filter in "Network" for m3u8. You will find something similar to the following output:

```
https://cdn.loom.com/sessions/raw/0a5dcee7ffa748759f5656dae5890d69.m3u8?Expires=1607585919&Policy=eyJTdGF0ZW1lbnQiOlt7IlJlc291cmNlIjoiaHR0cHM6Ly9jZG4ubG9vbS5jb20vc2Vzc2lvbnMvcmF3LzBhNWRjZWU3ZmZhNzQ4NzU5ZjU2NTZkYWU1ODkwZDY5Lm0zdTgiLCJDb25kaXRpb24iOnsiRGF0ZUxlc3NUaGFuIjp7IkFXUzpFcG9jaFRpbWUiOjE2MDc1ODU5MTl9fX1dfQ__&Signature=brq4RBUwL~nZqCcb50BoQuVmAji4vMHPZ2qtewNd4a9IwjKF7LGWHp9CKqR9CzkQIfgGZeTeH0H5LJVb51TMRckGEwmdxczfvaYn6yUDqhssMKSXDDc15CcYonhFavFE~GOyUvcQczdI9Nkp-wj7M2sbAzP64ujNkbgUGg5MybULliHnBw3Yrwx-ZO26dV48AXFcfLi5zebCWML2MFF2DQbRyUiH31B72k3dFQct5QR4k5Z5flLZ1u5EcovitPEoOJeXnvKts8iTjQrbv-PlIdi~fiu2LP-btEw9n-i6qADFAfaJPRVsoczL5xDIBj7Iw7SrWZCccWtpq3bFAbObTQ__&Key-Pair-Id=APKAJQIC5BGSW7XXK7FQ
```

Just copy the link address, download an rename so you got the m3u8 file

## Realize the policy part that will allow downloading

The same way you found the download for the m3u8 you need to look up the policy part from the \*.ts-files.
When watching the video and filtering by "ts" you will find multiple ts files e.g.

```
https://cdn.loom.com/sessions/raw/0a5dcee7ffa748759f5656dae5890d69-0.ts?Policy=eyJTdGF0ZW1lbnQiOlt7IlJlc291cmNlIjoiaHR0cHM6Ly9jZG4ubG9vbS5jb20vc2Vzc2lvbnMvcmF3LzBhNWRjZWU3ZmZhNzQ4NzU5ZjU2NTZkYWU1ODkwZDY5KiIsIkNvbmRpdGlvbiI6eyJEYXRlTGVzc1RoYW4iOnsiQVdTOkVwb2NoVGltZSI6MTYwNzU4NTkxOX19fV19&Signature=Sq-5rsjcOXNfpeSGx8VcKuqJ5LCz60R9LjoM5f3EDc4cirFB0tAwNY280yF17BkmESwY296s89JKn0r9JnkaKfU0N5tWe2AeMkUt~TrrOAgPWfpXNgqXOb3y4ucs5FJP-3XZzU0V-LsOLInk5zHSEz4hAXYxoyCN1IXPb5gy-9-xynZ~0LwoNZd~6MsFa4T0Mbrg839RROPj7-~FdwS5NZprcaiNnBJmB7wSPVmjv2jQ4fjnOCyEotEYMYRi2LeuqOOkKkW7PPw4aqNfO4x1Xhpy09eh4OuaSKsslWhOatEuNRZvGDD2ABgPBGE0O5FBnOz78ehwAiaJpIdSMS0V8g__&Key-Pair-Id=APKAJQIC5BGSW7XXK7FQ&Expires=1607585918.898
```

## Wrap it up in a bash script

Next we you this information for batch downloading all needed ts-files.

```
#!/bin/bash
# LOOM DOWNLOAD

M3U8="downloaded.m3u8"
CONTENT=$(cat downloaded.m3u8)
POLICY="?Policy=eyJTdGF0ZW1lbnQiOlt7IlJlc291cmNlIjoiaHR0cHM6Ly9jZG4ubG9vbS5jb20vc2Vzc2lvbnMvcmF3LzBhNWRjZWU3ZmZhNzQ4NzU5ZjU2NTZkYWU1ODkwZDY5KiIsIkNvbmRpdGlvbiI6eyJEYXRlTGVzc1RoYW4iOnsiQVdTOkVwb2NoVGltZSI6MTYwNzU4NTkxOX19fV19&Signature=Sq-5rsjcOXNfpeSGx8VcKuqJ5LCz60R9LjoM5f3EDc4cirFB0tAwNY280yF17BkmESwY296s89JKn0r9JnkaKfU0N5tWe2AeMkUt~TrrOAgPWfpXNgqXOb3y4ucs5FJP-3XZzU0V-LsOLInk5zHSEz4hAXYxoyCN1IXPb5gy-9-xynZ~0LwoNZd~6MsFa4T0Mbrg839RROPj7-~FdwS5NZprcaiNnBJmB7wSPVmjv2jQ4fjnOCyEotEYMYRi2LeuqOOkKkW7PPw4aqNfO4x1Xhpy09eh4OuaSKsslWhOatEuNRZvGDD2ABgPBGE0O5FBnOz78ehwAiaJpIdSMS0V8g__&Key-Pair-Id=APKAJQIC5BGSW7XXK7FQ&Expires=1607585918.898"

dos2unix ${M3U8}

for i in ${CONTENT} ; do
	wget "https://cdn.loom.com/sessions/raw/${i}${POLICY}" -O ${i}
done
```

## Merging
After downloading all the files the last step is to merge the ts-files. 

Just use the following command and you are done:

`grep .*.ts downloaded.m3u8 | xargs cat | ffmpeg  -i pipe: -c:a copy -c:v copy output.mp4`

Used this wrapper to get the correct ordering.

THE END
