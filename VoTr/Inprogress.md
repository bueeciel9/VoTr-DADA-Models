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
Non-empty voxels only account for a small proportion of total voxels(normally non-empty voxels occupy less than 0.1% of the total voxel space).
-> Special operations should be designed to only attend to those non-empty voxels efficiently.

2)	The number of non-empty voxels is still large in a scene
-> Applying fully-connected self-attention like the standard Transformer is computationally prohibitive.

Submanifold voxel module operate strictly on the no-empty voxels, to retain the original 3D geometric structure.
Sparse voxel modules can output features at the empty locations, which is more flexible and can further enlarge the non-empty voxel space.

Non-empty voxels are too numerous for self-attention, we further propose two attention mechanisms. -> 음.. 이 아이디어는 나중에 voxel focal attention을 할때도 넣어야할까?
일단, local, dilated attention을 어떻게 수행하는지 확실히 이해하고, 여러 복셀단위들로 나누어서도 이게 실현 가능한지 한번 생각해보자.
1.	Local attention
Local attention focuses on the neighboring region to preserve detailed information.
2.	Dilated Attention -> 얘 살짝 focal attention이랑 비슷하게 생각할수도? 
Dilated attention obtains a large attention range with only a few attending voxels, by gradually increasing the search step.

위 두가지 attention을 further accelerate, Fast Voxel Query which contains a GPU-based has table to efficiently store and lookup the non-empty voxels.

















