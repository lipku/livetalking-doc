## 6. Performance Optimization

### 6.1 Image Caching
When pasting back the digital human, the current full-body image is required. If it is read from the hard disk when needed, due to the limitations of I/O performance, it will greatly affect the running speed of the program. Generally, it is necessary to preload the full-body images into the memory. If the number of images is not large, all of them can be loaded into the memory, that is, in this case, the length of the original training video should not be too long.
If the length of the training video is long and the memory cannot load all the images, then a caching strategy needs to be implemented, and only a segment of images that are recently required should be loaded. The following is a schematic diagram of the cache. It is assumed that only the latest 1,000 images are loaded.
![](./assets/cache.png)
Since the digital human reads the full-body images in a back-and-forth loop, the situation of reading images in the reverse direction also needs to be considered during the actual implementation.
A version of the imgcache code has been implemented, and the code is placed on [Knowledge Planet](https://t.zsxq.com/7NMyO). 