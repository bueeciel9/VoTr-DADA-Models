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


![image](https://user-images.githubusercontent.com/65759092/184192968-f7f3d5a4-3d87-450b-a819-a04681130ebd.png)

** Self-attention on sparse voxels:
1.	Define a dense voxel-grid, Ndense voxels in total
2.	Maintain those non-empty voxels with a Nsparse¬ * 3 integer indices array V and Nsparse *d corresponding feature array F,  Nsparse << Ndense
3.	Multi-head self attention is used to build long-range relationships among non-empty voxels.
4.	Given the querying voxel i, the attention range omega is first determined by attention mechanism, and perform multi-head attention on attending voxels j to obtain the feature.
5.	Fi and fj are features of querying and attending voxels respectively, and vi and vj be the integer indices of querying and attending voxels.
6.	Transform the indices vi and vj to the corresponding 3D coordinates of the real voxel centers pi and pj by p = r(v+0.5) where r is the voxel size.
7.	Then, for a single head, compute the query, key, value embedding with the linear projection of query, key, and value. And positional encoding Epos¬ too.
![image](https://user-images.githubusercontent.com/65759092/184199535-aaa5e83e-f796-4f85-9803-cb54a1160161.png)


![image](https://user-images.githubusercontent.com/65759092/184199515-e34d1e8e-8683-4d09-957f-ae1319ab4cac.png)

Self-attention on voxels:
![image](https://user-images.githubusercontent.com/65759092/184199563-b8ff01fc-408e-4e10-b790-f6d0a0ee2787.png)


**Submanifold voxel module:**
The outputs of submanifold voxel modules are exactly at the same locations with the input non-empty voxels. -> keep the original 3D structures of inputs. 
Two sub-layers are designed. 1st sub layer is the self-attention layer that combines all the attention mechanisms, and the 2nd is a simple feed-forward layer. Residual connection s are employed around the sub layers.
Major difference: 
1. Append the additional linear projection layer after the feed-forward layer for channel adjustment of voxel features.
2. Replace layer normalization with batch normalization
3. Remove all the dropout layers in the module, since the number of attending voxels is already small and randomly rejecting some of those voxels hampers the learning process


Sparse voxel module:
Extract features for the empty locations, leading to the expansion of the original non-empty space, typically required in the voxel downsampling process.
There is no feature fi available for the empty voxels, can’t obtain the query embedding Qi from fi. 
-> Approximation of Qi at the empty location from the attending features fj

![image](https://user-images.githubusercontent.com/65759092/184211114-ea6c4585-b507-41e7-b178-a981afb686b9.png)

Function can be interpolation, pooling. In this paper, the function is chosen as the maxpooling.
Self-attention on sparse voxels:
1)	Should cover the neighboring voxels to retrain the fine-grained 3D structure.
2)	Should reach as far as possible to obtain a large context information
3)	The number of attending voxels in self-attention should be small enough.


We define (start; end; stride) as a function that returns the non-empty indices in a closed set [start; end] with the step as stride. In the 3D cases,
for example, ((0; 0; 0), (1; 1; 1), (1; 1; 1)) searches the set {(0; 0; 0), (0; 0; 1), (0; 1; 0),  ... ,(1; 1; 1)} with 8 indices for the non-empty indices. 
In Local Attention, given a querying voxel vi, the local attention range local(i) parameterized by Rlocal can be formulated as: where Rlocal =(1,1,1)

![image](https://user-images.githubusercontent.com/65759092/184236270-2d72e3df-81c2-4e7a-a570-dccebc19dda3.png)

Dilated Attention
![image](https://user-images.githubusercontent.com/65759092/184236714-d303ff3e-17ac-436a-9de3-2c45aa8edb09.png)
![image](https://user-images.githubusercontent.com/65759092/184236902-8963002c-99b4-4af2-9967-eea244648a7b.png)
![image](https://user-images.githubusercontent.com/65759092/184237027-d03027db-14df-4f83-ab94-b6f02f2ef514.png)


we gradually enlarge the querying step R(i) stride when search for the non-empty voxels which are more distant.
we preserve more attending voxels near the query while still maintaining some attending voxels that are far away, 
and R(i) stride > (1; 1; 1) significantly reduces the searching time and memory cost.

음.. 어쨌든 이 두 attention module로 가까운 거리도 효과적으로 하고, 먼 곧까지 잘 훑는다는게 핵심인 것 같은데.. 얘를 어떻게 적용할 수 있을까?








