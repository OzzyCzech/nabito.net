---
title: Convert GIF to WebM with ffmpeg
date: 2020-03-17
tags: [bash, ffmpeg]
---

# Convert GIF to WebM with ffmpeg

```shell script
ffmpeg -i my-animation.gif -c vp9 -b:v 0 -crf 41 my-animation.webm
```

```html
<video autoplay loop muted playsinline>
  <source src="my-animation.webm" type="video/webm">
</video>
```