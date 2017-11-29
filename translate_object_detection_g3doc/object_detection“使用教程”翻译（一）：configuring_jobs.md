（刚接触tensorflow中关于object_detectionAPI时深知其上手之不易，模块的“g3doc”中其实给出了使用指南，这里翻译为中文，方便以后查阅。也希望这一系列的“译文”能够帮助到和我一样“白手起家”的爱好者们。本人翻译能力已经精力有限，有表述得不恰当的地方大家多多指正。）
（一【也就是此译文】）的原文：[Configuring the Object Detection Training Pipeline](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/configuring_jobs.md)

## 概览

Tensorflow目标检测API使用protobuf文件来配置训练和测试相关的东西。文件格式可以参照object_detection/protos/pipeline.proto。在高层次中<mark>【注：注意这个high level，意思是以下的配置文件并不在pipeline.proto中，而在以config结尾的配置文件中。比如\object_detection\samples\configs\ssd_inception_v2_coco.config(大多是嵌套型的map)】</mark>
	配置文件分为5个部分：
		1. model configuration：定义了什么类型的模型（即元meta-architecture将被训练。(比如说选择SSD/FASTER RCNN/RFCN等模型，什么特征提取器<mark>【译者注：简单的说就是我们平常在代码中写的关于模型的配置参数“挪到”了这个配置文件中，让代码不那么"冗余"】</mark>
		2. train_config：决定应使用哪些参数来训练模型参数（即SGD参数，输入的张量需要哪些预处理和怎么初始化特征提取器<mark>【译者注：用咱们的话说就是卷积核】</mark>的值）。
		3. eval_config：决定了什么样的指标将在报告被用来评估（目前只支持PASCAL VOC指标）。
		4. train_input_config：定义了模型中的训练集。
		5. eval_input_config：定义了模型中的测试集。通常这应该不同于训练集。
<br/>
配置文件的框架(格式)如下：
>model {
(... 在这里添加model的配置...)
}
train_config : {
(... Add train_config here...)
}
train_input_reader: {
(... Add train_input configuration here...)
}
eval_config: {
}
eval_input_reader: {
(... Add eval_input configuration here...)
}
## 选择模型参数
>这里有大量的参数需要设置。怎样的设置时最好的取决于您的给定的应用程序。FASTER R-CNN模型更适合于要求高精度不要求速度的情况。相反，如果更看重时间，建议使用SSD模型。请阅读我们的论文【译者注：Speed/accuracy trade-offs for modern convolutional object detectors】，以获得关于速度与准确度之间的更详细讨论。 为帮助新用户入门，在object_detection / samples / model_configs文件夹中提供了示例模型配置。这些配置文件的内容可以粘贴到上面给出的配置文件的框架中。用户应该注意的是，num_classes字段应该改为用户正在训练的类的数量。

## 设置输入(路径)
>Tensorflow目标检测API接受TFRecord文件格式的输入。用户必须指定训练文件和测试文件的位置。此外，用户还应该指定一个标签映射（文件），它定义了类ID和类名之间的映射。训练和评估数据集之间的标签文件应该是相同的。
>
> **示例如下：**
> tf_record_input_reader {
  input_path: "/usr/home/username/data/train.record"
}
label_map_path: "/usr/home/username/data/label_map.pbtxt"
用户应该替换input_path和label_map_path参数，并将配置插入到配置文件中的train_input_reader和eval_input_reader字段。请注意，这些路径也可以指向Google云。（即gs://project_bucket/train.record）
## train_config 的配置
>1. 模型参数初始化。 
2. 输入预处理。 
3. SGD参数。

简单示例如下：
>batch_size: 1
optimizer {
  momentum_optimizer: {
    learning_rate: {
      manual_step_learning_rate {
        initial_learning_rate: 0.0002
        schedule {
          step: 0
          learning_rate: .0002
        }
        schedule {
          step: 900000
          learning_rate: .00002
        }
        schedule {
          step: 1200000
          learning_rate: .000002
        }
      }
    }
    momentum_optimizer_value: 0.9
  }
  use_moving_average: false
}
fine_tune_checkpoint: "/usr/home/username/tmp/model.ckpt-#####"
from_detection_checkpoint: true
gradient_clipping_by_norm: 10.0
data_augmentation_options {
  random_horizontal_flip {
  }
}
## 模型参数初始化
>虽然（这个操作）是可选的，但强烈建议用户使用其他object detection checkpoints【译者注：也就是说，建议我们使用checkpointzhong 已经保存了的参数】。从头开始训练目标检测器可能需要数天时间。为了加快训练过程，建议用户重新使用先前在目标分类或检测的checkpoint保存好的参数。 train_config提供了两个字段来指定已经保存好的检查点：fine_tune_checkpoint和from_detection_checkpoint。 fine_tune_checkpoint应提供一个路径到已经保存好的检查点（即：“/ usr/home/username/checkpoint/model.ckpt - #####”）。 from_detection_checkpoint是一个布尔值。如果为false，则表示检查点来自目标分类检查点。请注意，从detection checkpoint开始（运行）训练过程通常会比classification checkpoint更快。 提供的 checkpoint列表可以在[这里](http://blog.csdn.net/HaixWang/article/details/78484198)找到。
## 输入预处理
>train_config中的data_augmentation_options可用于指定如何修改训练数据。该字段是可选的。
## SGD参数
train_config中的其余参数是作用于梯度下降的参数。请注意，这些配置文件中提供的最佳学习率可能取决于训练中的设置的具体情况（例如，worker数量，GPU类型）
## 设置“评委”【我造的词。。】
目前评估被固定为PASCAL VOC竞赛定义的度量标准。 eval_config的参数设置的默认值比较合理，通常不需要设置。
