---
layout: post
title: HTML | Visualization
categories: [HTML]
tags: [HTML, python, Visualization]
author: wpsze
date: 2025-01-15 10:35:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/kKLnFZH.png
banner_img: https://i.imgur.com/kKLnFZH.png
---

# html to display figures

To create **a simple HTML and JavaScript code that displays all images from a specified folder**, you can use the following code. This example assumes that your images are stored in a folder named images within the same directory as your HTML file.

To automatically display all images from a folder **without manually specifying each image name**, you can use JavaScript to fetch the list of image files dynamically. However, JavaScript running in the browser does not have direct access to the file system for security reasons.

Done by `Claude-3.5-Sonne vis Poe`, <https://poe.com/s/pKQ5uBUv9PlOfTN4aBB8>

- Python script that you can run to generate a JavaScript file with the image list
- HTML file to use this JavaScript file.

```console
$ python3 get_images_list.py 
$ python3 -m http.server 4007
```

- Open the HTML file in a web browser to see your image gallery.

open <http://localhost:4007/display_images.html> or <http://server-ip:4007/display_images.html>

Features of this solution:

- Displays all image files regardless of filename
- Shows the filename below each image
- Supports multiple image formats (**png, jpg, jpeg, gif, bmp, webp**)
- Responsive grid layout
- Hover effects on images
- Error handling for missing images
- Filename display with overflow handling

```console
/your-project-directory (web_display)
├── display_by_web.sh
├── display_images.html (or whatever you name your HTML file)
├── get_images_list.py
└── /images
    ├── image1.png
    ├── image2.png
    └── ... (other PNG images)
```

{% fold info @display_by_web.sh %}
```sh
#!/bin/bash

# 
# ./display_by_web.sh <image directory path>
# 

export images_dir=${1}
echo "images_dir = ${images_dir}"

cd /home/wpsze/web_display/ 

rm -r images
mkdir -p images
ln -s ${images_dir}/* images

python3 get_images_list.py
python3 -m http.server 4007
```
{% endfold %}

{% fold info @get_images_list.py %}
```python
import os
import json

# Directory containing the images
image_dir = './images/'

#image_dir = os.environ["image_dir"]

# Get all image files
image_files = []
for filename in sorted(os.listdir(image_dir)):
    if filename.lower().endswith(('.png', '.jpg', '.jpeg', '.gif', '.bmp', '.webp')):
        image_files.append(filename)

# Create JavaScript file
with open('image-list.js', 'w') as f:
    f.write('const imageFiles = ')
    json.dump(image_files, f)
    f.write(';')
```
{% endfold %}

{% fold info @display_images.html %}
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Image Gallery</title>
    <!-- Prevent browser caching -->
    <meta http-equiv="Cache-Control" content="no-cache, no-store, must-revalidate">
    <meta http-equiv="Pragma" content="no-cache">
    <meta http-equiv="Expires" content="0">
    <style>
        .gallery {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(400px, 1fr));
            gap: 20px;
            padding: 20px;
        }

        .image-container {
            position: relative;
            aspect-ratio: 1;
            overflow: hidden;
            border-radius: 8px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
            transition: transform 0.3s ease;
        }

        .image-container:hover {
            transform: scale(1.05);
        }

        .image-container img {
            width: 100%;
            height: 100%;
            object-fit: contain;
        }

        .filename {
            position: absolute;
            bottom: 0;
            left: 0;
            right: 0;
            background: rgba(0, 0, 0, 0.7);
            color: white;
            padding: 5px;
            font-size: 12px;
            text-align: center;
            overflow: hidden;
            text-overflow: ellipsis;
            white-space: nowrap;
        }
    </style>
</head>
<body>
    <div class="gallery" id="imageGallery"></div>

    <script src="image-list.js"></script>
    <script>
        const imageFolder = 'images/'; // Change this to your folder name
        const gallery = document.getElementById('imageGallery');

        imageFiles.forEach(filename => {
            const imgContainer = document.createElement('div');
            imgContainer.className = 'image-container';
            
            const img = document.createElement('img');
            img.src = imageFolder + filename;
            img.alt = filename;
            
            // Add filename display
            const filenameDiv = document.createElement('div');
            filenameDiv.className = 'filename';
            filenameDiv.textContent = filename;
            
            // Error handling for images that might not exist
            img.onerror = function() {
                this.parentElement.remove();
            };
            
            imgContainer.appendChild(img);
            imgContainer.appendChild(filenameDiv);
            gallery.appendChild(imgContainer);
        });
    </script>
</body>
</html>
```
{% endfold %}

# Result

![](https://i.imgur.com/kKLnFZH.png)

# Note

To prevent browser caching and always show the latest images, then need to clear the cache of browser.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Image Gallery - No Cache</title>
    <!-- Prevent browser caching -->
    <meta http-equiv="Cache-Control" content="no-cache, no-store, must-revalidate">
    <meta http-equiv="Pragma" content="no-cache">
    <meta http-equiv="Expires" content="0">
    <style>
        .gallery {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
            gap: 20px;
            padding: 20px;
        }
...
```

## More,

### For size of image within box,

```html
.image-container img {
    width: 100%;
    height: 100%;
    object-fit: contain;
}
```

![](https://i.imgur.com/pymFB2Y.png){width=600}