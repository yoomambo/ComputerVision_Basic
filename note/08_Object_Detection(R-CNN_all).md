# R-CNN to Fast, Faster CNN

## R-CNN

<img src="../image/08/R-CNN_architecture.PNG">

### 1. Procedure

#### 1) Trained Classification model for ImageNet

<img src="../image/08/procedure_1_trained_model.PNG">

기존의 AlexNet, VGGNet 같은 Model을 써서 Model을 학습을 시켜둔다.

#### 2) Fine-Tuned Model for Detection

<img src="../image/08/procedure_2_Fine-tuned_model.PNG">

기존 ImageNet 의 1000개의 class 대신 Pascal VOC dataset의 class 20개와 background 1개, 총 21개의 classification을 하도록 마지막 node 수를 변경한다.

따라서 마지막 FC으로 output을 뱉을 때, _**4096x21**_ 으로 만든다.

#### 3) Extract Features

<img src="../image/08/procedure_3_extract_features.PNG">

1. Selective Search를 ROI(regions of interest)를 통해 region proposal한 부분들을 가지고 온다.
2. 각 지역을 CNN input size에 맞게 Crop&Warp를 한다.
3. CNN을 통과시켜서 feature를 disk에 저장

#### 4) Train binary SVM per class to classify features

<img src="../image/08/procedure_4_binary_classification.PNG">

cached한 region feature들을 class에 대해 positive sample인지 negative sample인지 SVM으로 classification을 해보자.

#### 5) Bounding Box Regression

<img src="../image/08/procedure_5_box_regression.PNG">

(dx, dy, dw, dh) 

---------------

## Fast R-CNN

### 1. R-CNN의 문제점

1. ROI들을 모두 저장하기 때문에 각 ROI에 맞는 CNN parameter들을 모두 저장하고 있어야한다. -> storage huge
2. 처음 ImageNet을 training할 때 필요한 Softmax Classfier, SVM, Box Regressor들을 모두 따로 훈련한다. -> computation expensive
3. training 시간이 84h로 굉장히 오래걸린다.
4. detection도 오래걸린다.

### 2. Fast R-CNN Architecture

<img src="../image/08/Fast_R-CNN_architecture.PNG">

1. 영상에서 CNN을 통해 feature들을 미리 뽑아낸다.
2. 이와 동시에 영상에서 ROI를 Selective Search를 통해 뽑아낸다.
3. CNN으로 뽑아낸 activation map에 ROI를 대입해서 activation map 안에서의 ROI를 새롭게 정의한다.
4. 각 ROI들을 ROI Pooling 작업을 통해 7x7x512을 뽑아낸다.
5. FC를 통과해서 Binary Classfication & Regression을 동시에 진행한다.

<img src="../image/08/Fast_R-CNN_train.PNG">

6. Classification loss와 Bounding box loss를 이용해서 Backpropagation을 적용한다.

-----------

## R-CNN vs SPP vs Fast R-CNN

<img src="../image/08/performance.PNG">