# Summary

## AlexNet


-----------

## [VGGNet](note/03_VGGNet.md)

1. Concept : small filter & small paramter → More Deeper
2. Parameter 증명 : $27C^2$ vs $49C^2$
3. **Non-Linearty Activation Function 을 많이 쌓아서 Model이 좀 더 표현력이 올라갔다.**

-----------

## [GoogleNet](note/04_GoogleNet.md)

1. Inception Module : 1x1, 3x3, 5x5 conv, 3x3 MaxPooling 하는 layer를 각각 만들어서 ensemble learning 처럼 기준이 다른 layer로 판단하게끔 하는 효과를 가져온다.
2. Local Classification Model (Auxiliary Function) : gradient vanishing을 막기 위해 (솔직히 이건 왜 하는지 잘 모르겠다.)
3. BottleNeck : 중간에 1x1 conv layer를 배치 → parameter를 현저히 줄였다.
4. Global Average Pooling : Fully Connected Layer를 만들지 않고 마지막 1000개 channel을 만들어서 따로 classification 을 하도록 하였다.
5. **Parameter를 획기적으로 줄였다는 것이 제일 큰 장점이다!!**

-----------

## [ResNet](note/05_ResNet.md)

1. Deep Network using Residual Connection
2. 22 layer vs 50 layer를 해보았을 때, training 에서 error가 컸는데, test error도 컸다. 이는 optimize 자체가 안된 것이라 판단.
3. identity를 이용한 방법이다. $x + F(x) = H(x)$
4. $H(x) - x$ 값을 최소화하는 방향으로 해야한다.
5. 처음엔 7x7 conv, 나머지는 모두 3x3 conv
6. activation map size가 반으로 줄어들 때, depth를 2배로 늘렸다.
7. **최대 152 layer 까지 쌓을 수 있었다.**

-----------

## [Semantic Segementation](note/06_Semantic_Segmentation.md)

1. 기존의 단점
   1. sliding window : window 안에 여러가지 class에 대한 정보가 들어있을텐데 결국 그 window는 하나의 class로 분류하기 때문에 나머지 정보들이 소실된다.
   2. Fully Convolutional Networks
2. Encoder & Decoder Network
3. UnPooling
   1. Nearset Neighbor
   2. Bed of Nails : 제일 첫 번째 구역에 숫자 입력하고 나머지는 0
4. MaxUnpooling : 숫자들의 위치를 기억하고 그 전의 자리에 숫자를 배치
5. **Learnable UpSampling : Transpose Convolution**
   1. stride를 1로 하게 되면, 비슷한 수치로 된다.
   2. stride를 2로 하게 되면, Spartial Resolution

-----------

## [Object Detection : ROI, IOU, Superpixel, Selective Search](note/08_Object_Detection.md)

1. Region of Interest : object가 될 것 같은 부분은 border 형태로 만든다.
2. IOU (Integrate of Union) : 2개 border box (True & Predict)가 겹치는 부분 / 2개 border box가 합해지는 부분, 1에 가까울수록 Detection을 잘했다는 수치이다.
3. SuperPixel : 서로 비슷한 수치 (Color, Distance) 끼리 Group 짓는 것을 Superpixel 이라 한다.
4. Selective Search
   1. ROI를 구성해본다.
   2. Superpixel을 진행해서 object들끼리 묶는 과정을 진행한다. greedy하게 진행한다.
   3. 일정이상 진행되면 stop

-----------

## [Object Detection : R-CNN](note/08_Object_Detection(R-CNN_all).md)

1. Trained classification for ImageNet : 이미지에 대한 정보들을 Model에 넣기 위해. 여기서 사용한 model은 AlexNet or VGGNet
2. Fine Tuned Classification part : Pascal VOC의 dataset에 맞게 1000개 class에서 20+1 class로 변경한다.
3. Selective Search가 뽑은 ROI 후보들을 pre-trained model CNN에 input size에 맞게끔 전처리를 한 후 feature들을 뽑는다.
4. 뽑은 feature들을 disk에 저장한다.
5. 뽑은 feature들을 SVM으로 positive negative를 binary classification을 한다.
6. 뽑은 feature들을 Box를 (dx, dy, dw, dh) 로 regression을 진행한다.
7. CPU로는 47초, GPU로는 13초 너무 많은 시간이 걸렸다.

-----------

## [Object Detection : Fast R-CNN](note/08_Object_Detection(R-CNN_all).md)

1. R-CNN 은 모든 ROI에 대한 feature들을 disk에 저장하기 때문에 disk I/O가 굉장히 심하다. 따라서 feature를 저장하기 않고 한 번에 작업하는 방법으로 Fast R-CNN이 등장하였다.
2. Fast R-CNN은 pre-trained model을 쓰는 것은 동일하다.
3. pre-trained model의 CNN을 통해서 activation map을 뽑아낸다.
4. image 상에서의 ROI 를 activation map에 바로 투영한다.
5. activation map에서의 ROI들을 ROI pooling을 통해 7x7x512로 뽑아낸다.
6. 그 다음 regression & Classification을 동시에 진행한다.
7. 실제로 훈련시간을 비교해보면 R-CNN 84h, Fast R-CNN 8h.
8. test 시간은 R-CNN은 47s, Fast R-CNN은 2s

-----------

## [Object Detection : Faster R-CNN](note/08_Object_Detection(R-CNN_all).md)

1. Fast R-CNN은 ROI projection이라는 방법을 통해 feature들을 disk에 모두 저장하지 않아도 된다는 점이 크게 개선점이었지만, 여전히 selective search를 통한 ROI를 찾는 연산을 해야했다.
2. 이번엔 selective search도 NN으로 해결해보자!!
   1. k 값을 정해주면 k-anchor 로 random 한 box를 만들어보자.
   2. pre-trained model을 이용해서 feature map을 뽑는다.
   3. 3x3 conv layer, padding 1을 해서 size가 동일한 intermediate layer를 만든다.
   4. intermediate layer에서 1x1xChannel의 filter를 각각 2xk, 4xk channel 만큼 Fully Convolutional Network를 적용한다.
   5. 그럼 각 k anchor에 대한 positive, negative, regression의 4좌표에 대한 값이 나온다.
3. _**진정한 end to end 구조이다.**_

-----------

## [Object Detection : You Only Look Once, YOLO](note/08_Object_Detection(YOLO).md)

1. grid 하게 나눈다.
2. 2가지를 동시에 진행한다.
   1. grid를 따라서 class probability를 게산한다. $Pr(Class|Object)$
   2. Bounding Box + Confidence를 계산한다. $Pr(Object) * IOU^{truth}$
3. Final Detection
4. _**ROI 없이 NN으로만!!**_

-----------

## [Object Detection : Instance Segmentation](note/08_Object_Detection_instance_segmentation.md)

1. 이제는 ROI Pooling 이 아니라 ROI Align을 해야한다.
   1. ROI Pooling은 대략적인 box regression이기 때문에 소수점이 나와도 반올림을 하였기 때문에 ROI를 Activation map 에 projection해도 크게 바뀌지 않았다. 
   2. Segmentation 같은 경우는 activation map에서 ROI를 projection 하기 위해서는 훨씬 정교하게 해야한다. 따라서 ROI pooling 보다는 ROI Align을 해야한다.
2. Mask R-CNN
3. class를 Classification 하는 branch와 Segmentation 하는 branch를 따로 만들었다.

-----------

## [Generative Adversial Network](note/09_GAN.md)

1. generative model 의 확률론적 접근
   1. seed → x
   2. seed, 해당하는 y → x
   3. seed → x, y
2. GAN의 목적 : train할 때 쓴 image들의 실제 distribution을 파악해서 generative model을 만드는 것이다.
3. GAN의 구조 : Dicriminative Model vs Generative Model
   1. latent vector z를 Generative model 에 넣고, fake image가 생성이 된다.
   2. fake image와 real image를 섞어둔다.
   3. Discriminative Model이 real인지 fake인지 binary classification을 한다.
   4. Loss Function : $min_{\theta_g}max_{\theta_d}[E_{x_{data}}\log(D(x)) + E_{z_{data}}\log(1- D(g(x)))]$
      1. x일 때는 $E_{x_{data}}\log(D(x))$ 가 max 이도록
      2. z일 때는 $E_{z_{data}}\log(1- D(g(z)))$ 가 min 이도록
         1. D(g(x))가 1에 가깝다는 것은 generative가 train이 잘되서 진짜같은 가짜를 만든다는 것이다.
      3. 즉, generative 입장에서는 저 loss 값이 최소로 나와야 좋은 것이고, Discriminative 입장에서는 loss function 값이 max로 나와야 좋은 것이다.
      4. 여기서 saddle point를 발견한다. optimize 하기 힘들다는 증거이다.
   5. optimize가 $\log(1-D(g(x)))$ 그래프에서는 느리게 진행되서 힘들다.
   6. 이를 $\log(1-D(g(x)))$ 에서 $\log(D(g(x)))$ 로 바꿔서 optimize가 빨리 되도록 바꾼다. 즉, $\max_{\theta_g}\max_{\theta_d}[E_{x_{data}}\log(D(x)) + E_{z_{data}}\log(D(g(x)))]$
4. Learning Process
   1. z sampling
   2. Generative model : G(z) → X'
   3. Discriminative model : D(G(z)) = D(X')
   4. Sigmoid(D(X')) → 1 or 0 using Binary Cross Entropy Error
5. Deep Convolutional Network
   1. Dircriminative : Convolutional Network
   2. Generative : like UpSampling Network but Convolution Network
   3. Fully Connected layer를 하지 않고 Convolution 사용
   4. BatchNorm을 썼다. Generator의 output, Discriminative의 input에 사용 안한다.
   5. Generator는 ReLU, 마지막만 Tanh
   6. Discriminative는 LeakyReLU
6. GAN Dissection
   1. latent Unit을 interpret 해서 이 latent unit이 어떤 의미를 가지는지가 관건
   2. segmentation 과 thresold를 동시에 작업
7. GAN Inversion
   1. 기존의 pre-trained generative model을 이용한다.
   2. 자신이 image를 input해서 |G(z)-x|를 최소화 시키는 방향으로 train
   3. 그러면 generative model이 실제 image의 정보를 가지는 distribution neural network로 바뀔 것이다.
   4. 따라서 기존 distribution의 정보와 input image에 대한 distributino 정보를 다 가지고 있는 이미지를 만들어낼 것이다.
8. GAN은 train 시키기 정말 힘든 model이여서 optimize 하는 것만으로도 논문으로 낼 정도이다. 따라서 pre-trained model만 있다면 언제든지 다른 곳에 응용이 가능하다는 의미이다.

-----------

## Representation Learning

1. pretext task
2. Contrastive Learning
   1. SimCLR
   2. MoCo