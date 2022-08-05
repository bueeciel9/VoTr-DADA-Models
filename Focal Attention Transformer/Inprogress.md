
Theoretically, the focal attention mechanism enables global interaction with much less time and memory cost, because it attends a much smaller number of surrounding (summarized) tokens. In practice, however, extracting the surrounding tokens for each query position could incur high time cost since we need to duplicate the extraction of each token for all queries that the token surrounds. This issue had been extensively discussed in [56, 76, 43] and a common solution is to partition the input feature map into windows. Thus, in our Focal Transformers, we resort to performing focal attention at the window level. We elaborate the window-wise focal attention in the following.

-> 이 논문은 image based이기 때문에 이렇게 window-wise로 처리를 하는데, 나는 voxel based모델을 처리 할거기 때문에, 흠.. 어떻게 하면 좋을까? 생각 좀..
