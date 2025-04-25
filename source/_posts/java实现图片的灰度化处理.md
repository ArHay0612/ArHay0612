---
title: java实现图片的灰度化处理
date: 2018-06-12 15:36:11
tags: java
description: 24位彩色图与8位灰度图
---

    摘要:24位彩色图与8位灰度图

    在一个24位彩色图像中，每个像素由三个字节表示，通常表示为RGB。
    通常，许多24位彩色图像存储为32位图像，每个像素多余的字节存储为一个alpha值，表现有特殊影响的信息
    在RGB模型中，如果R=G=B时，则彩色表示一种灰度颜色，其中R=G=B的值叫灰度值，
    因此，灰度图像每个像素只需一个字节存放灰度值（又称强度值、亮度值），灰度范围为0-255.
    这样就得到一幅图片的灰度图

常见的几种灰度化的方法:

    分量法：使用RGB三个分量中的一个作为灰度图的灰度值。
    最值法：使用RGB三个分量中最大值或最小值作为灰度图的灰度值。
    均值法：使用RGB三个分量的平均值作为灰度图的灰度值。
    加权法：由于人眼颜色敏感度不同，按下一定的权值对RGB三分量进行加权平均能得到较合理的灰度图像。
    一般情况按照：Y = 0.30R + 0.59G + 0.11B.加权法实际上是取一幅图片的亮度值
    人眼对绿色的敏感最高，对蓝色敏感最低 ）作为灰度值来计算，用到了YUV模型

java编码实现图片灰度化

1.强制设置灰度化的方法（效果相对就差）

    ```java
    /**
     * 图片灰化（效果不行，不建议。据说：搜索“Java实现灰度化”，十有八九都是一种方法）
     *
     * @param bufferedImage 待处理图片
     * @return
     * @throws Exception
     */
    public static BufferedImage grayImage(BufferedImage bufferedImage) throws Exception {

        int width = bufferedImage.getWidth();  
        int height = bufferedImage.getHeight();  

        BufferedImage grayBufferedImage = new BufferedImage(width, height,
                                        BufferedImage.TYPE_BYTE_GRAY);
        for (int x = 0; x < width; x++) {  
            for(int y = 0 ; y < height; y++) {  
            	grayBufferedImage.setRGB(x, y, bufferedImage.getRGB(x, j));  
            }  
        }  
    }
    ```

2.加权法灰度化（效果较好）

    ```java
    /**
     * 图片灰化（参考：http://www.codeceo.com/article/java-image-gray.html）
     *
     * @param bufferedImage 待处理图片
     * @return
     * @throws Exception
     */
    public static BufferedImage grayImage(BufferedImage bufferedImage) throws
      Exception {
    	int width = bufferedImage.getWidth();
    	int height = bufferedImage.getHeight();
    	BufferedImage grayBufferedImage = new BufferedImage(width, height,
                                        BufferedImage.TYPE_BYTE_GRAY);
    	for (int x = 0; x < width; x++) {
    		for (int y = 0; y < height; y++) {
    			// 计算灰度值
    			final int color = bufferedImage.getRGB(x, y);
    			final int r = (color >> 16) & 0xff;
    			final int g = (color >> 8) & 0xff;
    			final int b = color & 0xff;
    			int gray = (int) (0.3 * r + 0.59 * g + 0.11 * b);
    			int newPixel = colorToRGB(255, gray, gray, gray);
    			grayBufferedImage.setRGB(x, y, newPixel);
    		}
    	}
    	return grayBufferedImage;
    }

    /**
     * 颜色分量转换为RGB值
     *
     * @param alpha
     * @param red
     * @param green
     * @param blue
     * @return
     */
    private static int colorToRGB(int alpha, int red, int green, int blue) {
    	int newPixel = 0;
    	newPixel += alpha;
    	newPixel = newPixel << 8;
    	newPixel += red;
    	newPixel = newPixel << 8;
    	newPixel += green;
    	newPixel = newPixel << 8;
    	newPixel += blue;
    	return newPixel;
    }
    ```
