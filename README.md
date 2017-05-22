# Robot-Car
# '上汽杯'汽车软件挑战赛复赛技术文档
> 参赛队员：管俊杰，江政，谢云璟
## 视频文件说明：
- 从给定`$shangqi2.wmv$`中截取四段具有代表性的视频，进行实验测试，分别得到`$result00-01.avi$`, `$result02-03.avi$`, `$result19-21.avi$`, `$result00-01.avi$`,四个时间段的视频。
## 针对初赛的算法改进:
1. 针对测试视频整体环境复杂，且较暗的特点，我们在滤波环节将形态学滤波改为高斯滤波。高斯滤波器是一种低通过滤器，能够抑制图片中的高频部分，而让低频部分顺利通过。

```
GaussianBlur( src, dst, Size( i, i ), 0, 0 );
```
- `$src:$`输入图像
- `$dst:$`输入图像
- `$Size(w,h):$`定义内核的大小(需要考虑的邻域范围),`$w$`和 `$h$`必须是正奇数。
- `$x$`和`$y:$`方向标准方差,如果是`$0$`  则使用内核大小计算得到。
2. 对采集图像阈值化处理
- 像素值高于阈值时，我们给这个像素赋予一个新值（eg:白色），否则我们给它赋予另外一种颜色（eg: 黑色）

```python
img=cv2.imread('self_line.jpg',0)

#第二个参数就是用来对像素值进行分类的阈值、第三个参数就是当像素值高于阈值时应该被赋予的新的像素值
#二值化处理，函数返回值有两个，第二个为阈值化后的结果图像，第一个暂时用不到
ret, thresh1=cv2.threshold(img,127,255,cv2.THRESH_BINARY) 
ret, thresh2=cv2.threshold(img,127,255,cv2.THRESH_BINARY_INV)
```
3. 基于ROI的边缘滤波

- 改进Region of Interes(ROI)的选取部分，过滤掉ROI之外的edges。

```python
def roi_mask(img, vertices):
  mask = np.zeros_like(img)
  mask_color = 255
  cv2.fillPoly(mask, vertices, mask_color)
  masked_img = cv2.bitwise_and(img, mask)
  return masked_img
roi_vtx = np.array([[(0, img.shape[0] - 200), (800, 480), (500, 450), (img.shape[1] -160  , img.shape[0] - 170 )]])
roi_edges = roi_mask(edges, roi_vtx)
```
4. 在车辆识别方面将样本的HOG特征改为提取Haar特征，训练采用离散AdaBoost分类器。

```
graph TD
    A[车辆训练样本] -->B[图像预处理]
    C[非车辆训练样本] -->B[图像预处理]
    B[图像预处理] -->D[计算积分图]
    D[计算积分图] -->E[提取类Haar特征]
    E[提取Haar特征] -->F[训练离散AdaBoost分类器]
    F[训练离散AdaBoost分类器] -->G[类特征信息和离散Adaboost分类器]
  
```





