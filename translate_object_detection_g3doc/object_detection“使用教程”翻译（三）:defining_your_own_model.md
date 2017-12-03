原文：https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/defining_your_own_model.md
在本节中，我们将讨论一些用于配置检测模型的抽象概念。如果您想要定义一个新的检测模型并在Tensorflow Detection API中使用它，那么本节应该作为您需要编辑的文件的高级指南，以使您的新模型正常工作。
## DetectionModels(object_detection/core/model.py)
为了使用我们提供的二进制文件进行训练，评估和导出，Tensorflow Object Detection API下的所有模型必须实现DetectionModel接口（请参考object_detection / core / model.py中的完整定义）。这些模型中的每一个都负责实现5个功能：
>* preprocess（预处理）:在对输入的图像应用“detector”之前，对输入值可进行各种预处理（比如：缩放/移动/改变形状）。
>* predict（预测）：产生可以传递给损失函数或后处理函数的“原始”预测张量。
>* postprocess（后期处理）:将预测【译者注：也就是上一步】输出的张量转换为最终检测结果。
>* loss（损失）：【这个大家知道的】
>* restore（恢复）：将checkpoint加载到Tensorflow图形中。

一些需要注意的约定：
>* DetectionModels不应该对输入大小或长宽比做任何假设 - 它们负责（对图像）进行必要的大小调整/形状调整（请参阅预处理函数）
>* 输出类总是[0，num_classes）的整数。这些整数到语义标签的任何映射都要在这个类之外进行处理。我们从来没有明确地emit“背景类”-----因此0是第一个非背景类，任何预测和去除隐式背景类的逻辑都必须由实现内部处理。因此0是第一个非背景类，并且任何预测和去除隐式背景类的逻辑都必须handled internally by the implementation。
>* 检测框被解释为[y_min，x_min，y_max，x_max]格式，并相对于图像窗口进行归一化
>* We do not specifically assume any kind of probabilistic interpretation of the scores --- the only important thing is their relative ordering. Thus implementations of the postprocess function are free to output logits, probabilities, calibrated probabilities, or anything else.

...
TODO
...
注:剩下的都是有关构建并运行一个新模型的建议，感觉有些高阶了，暂时用不到。
