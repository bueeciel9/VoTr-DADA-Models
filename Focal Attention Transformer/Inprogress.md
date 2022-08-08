
Theoretically, the focal attention mechanism enables global interaction with much less time and memory cost, because it attends a much smaller number of surrounding (summarized) tokens. In practice, however, **extracting the surrounding tokens for each query position could incur high time cost** since we need to duplicate the extraction of each token for all queries that the token surrounds. This issue had been extensively discussed in [56, 76, 43] and a **common solution is to partition the input feature map into windows**. Thus, in our Focal Transformers, we resort to performing focal attention at the window level. We elaborate the **window-wise focal attention** in the following.

-> 이 논문은 image based이기 때문에 이렇게 window-wise로 처리를 하는데, 나는 voxel based모델을 처리 할거기 때문에, 흠.. 어떻게 하면 좋을까? 생각 좀..

VoTr
![image](https://user-images.githubusercontent.com/65759092/183331702-e2322b95-ce0c-4e59-9b84-2d68e7dea3bb.png)

VoxelNet
![image](https://user-images.githubusercontent.com/65759092/183331714-0ecf2a56-5dfa-4446-8bda-8392f3b09cf6.png)

VoxSeT

![image](https://user-images.githubusercontent.com/65759092/183331754-7e1d73f1-a87a-4bf0-a599-18360de173b4.png)
