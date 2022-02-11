# Lane Detection 코드 분석
## 이 글은 LaneDetection.ipynb를 한줄한줄 공부한 글입니다.

```python
from google.colab.patches import cv2_imshow  #최상단에 import 를 추가한다.

cv2_imshow(img) #cv2.imshow 함수를 cv2_imshow로 변경한다.
```

<br>

```python
file_name = 'car3.jpeg'  #이미지 파일 저장한다.
frame = cv2.imread(file_name)  #이미지 파일을 읽는다.
cv2_imshow(frame)  #읽은 이미지 파일 윈도우 창에 보여준다.
height, width, channels = frame.shape  #이미지 파일을 모양을 Y축,X축,채널의 수 순서로 retrun 한다.
print(height, width, channels)
```

![Untitled](https://user-images.githubusercontent.com/98979901/153596303-00234d29-7fa7-46f7-8c38-ef53af6c2c9b.png)
<br>
```python
gray = cv2.cvtColor(frame, cv2.COLOR_RGB2GRAY) #RGB to gray로 이미지 변환한다
cv2_imshow(gray)
height, width = gray.shape
print(height, width)
```

![Untitled 1](https://user-images.githubusercontent.com/98979901/153596319-a9f88630-f2a4-40a1-b5b7-236c67bdb983.png)
<br>

```python
# GaussianBlur for refucing noise
blur = cv2.GaussianBlur(gray, (5, 5), 0) #가우시안 블러를 사용한다. 필터는 5,5
cv2_imshow(blur)
```
<br>

![Untitled 2](https://user-images.githubusercontent.com/98979901/153597142-de1499ad-1757-43bc-ba46-4d4fac998af0.png)


- cv2.GaussianBlur에 관하여

opencv 가 제공하는 Blurring(image smoothing) 종류 4가지 



**1. Averaging**

**2. Gaussian Blurring**

**3. Median Blurring**

**4. Bilateral Filtering**

```python
canny = cv2.Canny(blur, 40, 130)  # blur 인풋 이미지 , 40 최소 임계값, 130 최대 임계값 지정한다.
cv2_imshow(canny)
```
<br>

![Untitled 3](https://user-images.githubusercontent.com/98979901/153596595-ec98f7d4-4909-473e-9312-ef5b72e06e39.png)


### cv2.canny

> img_canny = cv2.Canny(image, threshold1, threshold2, edges=None, apertureSize=None, L2gradient=None)
> 

```python
mask = np.zeros((height,width), dtype='uint8') #[[0,0,0...0,0,0]...[0,0,0,...0]] 0배열 추가
cv2_imshow(mask)
```
![Untitled 4](https://user-images.githubusercontent.com/98979901/153596726-5c2d8034-3190-48d7-8400-4d200143d56c.png)

<br>


```python
poly_heigh = int(0.60 * height) #높이 점 찍기
poly_left = int(0.47 * width) # 좌측 점 찍기
poly_right = int(0.53 * width)# 우측 점 찍기
polygons = np.array([[(0,height), (poly_left, poly_heigh), (poly_right, poly_heigh), (width, height)]]) #하나의 array로 
cv2.fillPoly(mask, polygons, 255)  #0~255 검~흰 ploygons 영역을 흰색으로 채운다
cv2_imshow(mask)
```

![Untitled 5](https://user-images.githubusercontent.com/98979901/153596733-bf39a932-b3f1-44bd-8c9a-ec195ec032d2.png)
```python
# Bitwise operation between poly and mask
masked = cv2.bitwise_and(canny, mask) # canny 와 mask bitwise_and 연산 
cv2_imshow(masked)
```
<br>

![Untitled 6](https://user-images.githubusercontent.com/98979901/153596753-4909475c-272f-40d6-aa8b-617b2b2acab2.png)

#bitwise 연산 참고 블로그

```python
lines = cv2.HoughLinesP(masked, 2, np.pi / 180, 20, np.array([]), 20, 10)
```

- 허프변환은 이미지에서 모양을 찾는 가장 유명한 방법입니다. 이 방법을 이용하면 이미지의 형태를 찾거나 누락되거나 깨진 영역을 복원할 수 있습니다.
- 

```python
image_rgb = cv2.cvtColor(canny, cv2.COLOR_GRAY2RGB)
if lines is not None:
    for line in lines:
        print(line)
        x1, y1, x2, y2 = line.reshape(4)
        cv2.line(image_rgb, (x1, y1), (x2, y2), (0, 0, 255), 1) # 선을 그린다(이미지파일,좌표,좌표,색깔,선두께)
    cv2_imshow(image_rgb)
```

```python
for line in lines:
    for x1, y1, x2, y2 in line:
        cv2.line(frame, (x1, y1), (x2, y2), (0, 255, 255), 5)
cv2_imshow(frame)
```
