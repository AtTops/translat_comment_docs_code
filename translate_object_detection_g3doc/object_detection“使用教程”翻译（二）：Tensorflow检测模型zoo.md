我们提供了在COCO数据集上预训练好的检测模型的集合【***注：针对COCO数据集，已经开源了好几个检测模型，具体可以到项目中查看***】。如果您对COCO数据集中已经存在的类别（例如，人类，汽车等）感兴趣，那么<mark>这些</mark>模型可以开箱即用。在训练新数据集时，它们也可用于初始化模型。 在下表中，我们列出了这样的预先训练好的模型，包括：
>* 与samples / configs目录中的配置文件的文件名相对应的模型
>* 一个包含预先训练好的模型的tar.gz文件的下载链接
>* 模型的速度（{slow，medium，fast}之一）
>* 检测器的性能——COCO数据测量的mAP值。在这里，越高越好，我们只报告四舍五入到最接近的整数的bounding boxes的mAP值
>* 输出类型（目前只有Boxes）
><mark>【注：链接在本文末尾】</mark>
![这里写图片描述](http://img.blog.csdn.net/20171108223145575?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSGFpeFdhbmc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

您可以通过以下方式解压每个tar.gz文件
>tar -xzvf ssd_mobilenet_v1_coco.tar.gz

在解压后的目录里，你会发现：
>* 一个graph proto (graph.pbtxt)
>* 一个checkpoint (model.ckpt.data-00000-of-00001,model.ckpt.index, model.ckpt.meta)
>* 一个各种权重已经作为常量baked到图中的冻结了的graph proto (frozen_inference_graph.pb) 用于开箱既用 inference (在Jupyter notebook试一试!)

[注：以下是各个模型的下载链接]：
1. [ssd_mobilenet_v1_coco](http://download.tensorflow.org/models/object_detection/ssd_mobilenet_v1_coco_11_06_2017.tar.gz)
2. [ssd_inception_v2_coco](http://download.tensorflow.org/models/object_detection/ssd_inception_v2_coco_11_06_2017.tar.gz)
3. [rfcn_resnet101_coco](http://download.tensorflow.org/models/object_detection/rfcn_resnet101_coco_11_06_2017.tar.gz)
4. [faster_rcnn_resnet101_coco](http://download.tensorflow.org/models/object_detection/faster_rcnn_resnet101_coco_11_06_2017.tar.gz)
5. [faster_rcnn_inception_resnet_v2_atrous_coco](http://download.tensorflow.org/models/object_detection/faster_rcnn_inception_resnet_v2_atrous_coco_11_06_2017.tar.gz)
6. [faster_rcnn_nas_coco](http://download.tensorflow.org/models/object_detection/faster_rcnn_nas_coco_24_10_2017.tar.gz)