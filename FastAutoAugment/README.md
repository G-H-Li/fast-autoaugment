# FastAutoAugment的解析

## networks
此模块中主要记录了不同网络模型的代码，主要包括：efficientnet、shakeResNet（没有了解过）、pyramidNet、resNet、WideResNet。

学习过程中，没有做主要了解。

## tf_port
此模块包含rmprop参数更新策略、tpu的归一正则化

## FastAutoAugment（核心模块）
在此模块中，几个重点文件如下：
```
train.py
search.py
augmentations.py
```
* `train.py` : 此文件主要是负责训练，包括数据处理、模型评估等等整个训练流程
* `augmeantations.py` : 此文件主要负责常见的各种数据增强的函数实现，我在[VisionAlgorithm](https://github.com/G-H-Li/VisionAlgorithm) 中对其中算法的实现效果进行了相关测试。
* `search.py` : 此文件主要负责对一个模型和数据集最佳变换策略的实验搜索。

## 实现流程
1. 通过 `search.py` 针对某一模型和数据集搜索得到最佳的数据变换策略序列，包括什么策略和每个策略的具体调节参数等等。（其中main会对模型进行有增强和无增强的两次训练）
2. 将搜索得到的最佳策略写入到 `archive.py` 方便调用。
3. 在 `data.py` 中利用torch的数据加载器，在进入训练前，将所有数据使用最佳的transform进行处理。
4. 对处理好的数据进行训练。

## 实现方面
其深度搜索主要是使用了ray分布式增强学习框架，使用HyperOptSearch进行参数搜索，并通过自定义评估算法进行参数调节

## FastAutoAugment VS 数据投喂框架
* 数据处理粒度： 前者主要着眼于整个数据集最合适的数据增强策略的生成；后者着眼于一个batch数据的变换策略，数据粒度更细
* 实时性：前者主要先搜索，确定变换策略后，再进行训练；后者偏向于训练过程中的实时处理，更符合训练过程中的效果，从这点来看，后者可能会带来训练时间的提升

## 后续
* 主要会针对此FastAutoAugment进行修改，实现数据投喂框架
> step1: 调整代码，支持cpu运行
