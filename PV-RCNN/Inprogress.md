
**PV-RCNN**


2가지 핵심.
![image](https://user-images.githubusercontent.com/65759092/184465452-1f9a12ca-af49-433d-8ba8-008646c49d9d.png)

1. 3D sparse convolution

2D image에 2D convolution을 적용한 것 처럼 3D voxel에 3D convolution을 적용할 수 있습니다.

3D convolution은 inefficient하기 때문에 sparse 데이터에 적합한 3D sparse convolution을 적용할 수 있습니다.

위의 그림은 3D sparse convolution을 구현한 내용입니다. 단순히 sparse한 데이터에 효율적으로 적용가능한 sparse convolution입니다.

 

2. Point set abstraction

pointnet++ 논문의 point set의 feature을 encoding하는 알고리즘입니다.

전체 point에서 keypoint를 sampling하고 샘플링한 keypoint를 중심으로 정해준 반지름 거리 안에 속해있는 point들의 feature을 모아서 pointnet block이라는 MLP와 max pooling으로 이루어진 네트워크를 통과시켜 그 공간의 정보를 나타내는 feature vector를 생성합니다.

 
다시 말해 샘플링된 point가 local feature을 encoding 함으로써 convolution처럼 local feature을 배우고, 이 방법을 iterate하게 하면 point cloud를 적은 수의 point로 encoding 하게 됩니다.

![image](https://user-images.githubusercontent.com/65759092/184465461-cf8cd763-21c3-4c83-9ff0-bda7a0882d1d.png)


![image](https://user-images.githubusercontent.com/65759092/184465409-843af89a-6653-4c75-a216-6ef7393e34fe.png)


2 stage detection 구조로 이루어져있고 첫번째 stage에서 3D box proposal이 이루어지고 두번째 stage에서 각 box proposal에 대해서 confidence를 계산하고 object에 더 잘 맞도록 location refinement를 하게 됩니다

-> 내가 참고할 수 있는 것: Voxel을 이용해서 각 크기별로 Set abstraction을 이용했는데, 음..
이 대신에 Voxel 크기별로 focal attention을 수행할 수 있도록 바꾸는 것이 목표.

근데, 단점이라면 CNN만을 이용했다는 것. 이거를 이제 attention 으로 바꿔주는 작업을 해야한다.
