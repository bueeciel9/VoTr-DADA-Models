* Summary

- Conventional 3D convolutional backbones in voxel-based 3D detectors can't efficiently capture large context information.
- Transformer-based architecture that enables long-range relationships between voxels by self-attention.

Sparse voxel module & submanifold voxel module -> can operate on the empty and non-empty voxel positions effectively.

Local Attention & Dilated Attention

Fast Voxel Query -> to accelerate the querying process in multi-head attention.


![image](https://user-images.githubusercontent.com/65759092/183721463-a25aa9e0-1cde-4403-a7f9-7195352b2df4.png)


3D sparse CNN can't capture rich context information with limited receptive fields. 
주로 사용되는 voxel size: (0.05m, 0.05m, 0.1m) -> the maximum receptive field in the last layer is (3.65m, 3.65m, 7.3m)
-> 4m가 넘는 찯르은 cover 할 수 없다.


아이디어: -> focal attention을 써서, 전부 다 voxel size를 키우는 게 아니라, 
VoTr에서 나온 아이디어:
**Maximum receptive field를 결정하는 요소: Voxel size V, Kernel size K, the downsample stride S, and the layer number L.
V를 키우면 high quantization error of point clouds, K를 키우면 cubic growth of convoluted features, S를 키우면 low-resolution BEV map, L을 키우면 add computational overhead.**
-> focal attention을 써서, 전부 다 voxel size를 키우는 게 아니라, 특정 voxel을 여러 개 나눠가지고 크게도 하고, 작게도 해서 이를 focal attention을 수행할 수 있지 않을까?
VoxSeT에서 나온 아이디어:
그 실험적으로, 더 큰 Voxel을 이용할수록, 더 잘 탐지한다고 했다. 

직접적으로 Transformer를 voxel에 대입하면 안 좋은 이유!
1)	Non-empty voxels are sparsely distributed in a voxel-grid.
-> Non-empty voxels only account for a small proportion of total voxels(normally non-empty voxels occupy less than 0.1% of the total voxel space).

2)	The number of non-empty voxels is still large in a scene
-> Applying fully-connected self-attention like the standard Transformer is computationally prohibitive.

