- Select a good reference model (e.g., VoxSeT) and think about ideas to improve the attention mechanism 
- Implement the ideas and compare the proposed model with a vanilla VoxSet in terms of accuracy, complexity, memory, training issues, etc. 

![image](https://user-images.githubusercontent.com/65759092/182277766-7fcc122b-52b7-409c-b46e-846bdb1eafe4.png)

Attention modules:
Consists of three parts
VSA modules, MLP lyaers and shallow CNN for BEV feature extraction.
VSA divide the whole scene into non-overlapping 3D voxels and compute the voxel indices of the input point with instant efficiency.
-> with these voxels, determine the attentive region which is analogous to the window attention in Swin Transformer

1. VSA first **transforms the latent codes**, which serve as queries, **to a hidden space** by attention to the projected features.
2. **The transformed hidden features**, which encode the context information of the input points in each voxel, **are enriched by a convolutional feed-forward network**, in which the features across voxels exchange their information in spatial domain.
3. The hidden features are attentively fused with input, producing output features of the input resolution.
-> By leveraging the latent codes, the cross-attention performed in all voxels can be vectorized, making VSA a highly parallel module.

n d-dimensional input features, k latent codes. -> O(nkd)

그렇다면, 여기서 위에 나와있는 간단한 MLP와 2 cross-attention module을 바꿔볼 수 있을 것 같다. + 나의 아이디어..!


Self-attention directily 적용하지 않는 이유: quadratic computational complexity. -> set attention block was proposed
full self-attention -> two reduced cross-attentions induced by a group of latent codes.

![image](https://user-images.githubusercontent.com/65759092/182282302-e1617d49-8a6c-4d33-8854-05f8391f228c.png)

**The first cross attention** transforms the latent features L into hidden features H by attending to the input set. -> O(nkd)
Transformed hidden features contain information about the input set X and they are updated by a point-wise feed forward network.(FFN)

The second cross attention attends the input set to the resulting hidden features. -> O(nkd)


Point clouds are widely distributed and have weak semantic associations in scene level, while they have strong structual details in the local region. -> Partition the scene into a voxel grid and assign a set of latent codes to each voxel.

VSA is highly parallel module. operations across voxels can be vectorized.
**scatter function**: do vectorization which is cuda kernel library that performs symmetric reduction.

VSA encode the region-wise features into a hidden space using latent codes. Hidden features work as a bottleneck, through which we apply a ConvFFN to achieve more flexible and complex information update.










