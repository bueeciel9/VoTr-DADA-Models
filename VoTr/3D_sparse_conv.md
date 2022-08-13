https://dh87.tistory.com/3
https://hegosumluxmundij.tistory.com/m/112


**Sparse Convolution**

LiDAR 신호 처리에서 필수적인 역할

기존 convolution과는 상당히 다른 개념과 GPU 계산 스키마를 사용하는 방식

 

LiDAR 신호 처리에서 필수적인 역할

기존 convolution과는 상당히 다른 개념과 GPU 계산 스키마를 사용하는 방식

 

동기

CNN이 2D 이미지 신호 처리에 매우 효과적인 것으로 입증됨.

그러나 3D point cloud 신호의 경우 추가적인 차원 Z는 계산을 크게 증가시킴.

반면 일반 이미지와 달리 3D 포인트 클라우드의 대부분 voxel은 비어있기 때문에 3D voxel point cloud 데이터는 종종 sparse 신호가 됨.

![image](https://user-images.githubusercontent.com/65759092/183727547-fd016740-2ccb-4f74-839a-dcf9d83b2bf5.png)

문제는 모든 이미지 픽셀이나 공간 복셀을 스캔하는 대신 **희소 데이터로만 컨볼 루션을 효율적으로 계산할 수 있는지 여부**

sparse convolutional layer can be performed with a few convolution kernels followed by a sparse matrix multiplication. It could be assumed that the sparse matrix formulation naturally leads to highly efficient computation. 
However, computing sparse matrix multiplication can involve severe overhead that makes it difficult to actually achieve attractive acceleration. Thus, we also propose an efficient sparse matrix multiplication algorithm.



