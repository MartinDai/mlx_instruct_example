# 使用LoRA微调

本案例提供了使用LoRA进行微调一个具有自我认知的模型，数据集来源：https://www.modelscope.cn/datasets/swift/self-cognition

## 选择基础模型

微调需要基于一个基础模型进行操作，支持huggingface的safetensors格式，以通义千问2.5-0.5B-Instruct为例，先下载该模型项目文件到本地

```shell
git clone https://www.modelscope.cn/models/Qwen/Qwen2.5-0.5B-Instruct
```

## 修改数据集内容

替换data目录中的3个jsonl文件的内容，`{{NAME}}`为微调后模型对自身认知的名字，`{{AUTHOR}}`为作者名字

## 训练模型

进入本目录，执行如下命令，Qwen2.5-0.5B-Instruct的位置替换为前面下载的模型项目目录，其他训练参数可以参阅文档按需自行调整

```shell
mlx_lm.lora --train --model /Users/martin/develop/projects/modelscope/Qwen2.5-0.5B-Instruct --batch-size 1 --num-layers 4 --iters 1000 --data ./data
```

执行完成以后，会有如下类似的输出

```
Iter 990: Train loss 0.484, Learning Rate 1.000e-05, It/sec 9.852, Tokens/sec 707.364, Trained Tokens 69614, Peak mem 1.740 GB
Iter 1000: Val loss 0.578, Val took 1.063s
Iter 1000: Train loss 0.421, Learning Rate 1.000e-05, It/sec 92.432, Tokens/sec 5638.353, Trained Tokens 70224, Peak mem 1.740 GB
Iter 1000: Saved adapter weights to adapters/adapters.safetensors and adapters/0001000_adapters.safetensors.
Saved final weights to adapters/adapters.safetensors.
```

## 评估模型

执行下面的命令，可以对训练完的模型临时成果进行评估

```shell
mlx_lm.lora --test --model /Users/martin/develop/projects/modelscope/Qwen2.5-0.5B-Instruct --data ./data --adapter-path adapters 
```

得到类似如下输出结果

```
Loading pretrained model
Loading datasets
Testing
Test loss 0.540, Test ppl 1.716.
```

Test loss是损失值，越低越好，表示模型预测与实际结果的偏差程度

Test ppl是困惑度，越接近1越好

如果对这两个值不满意，可以修改数据集或者训练参数，重新执行训练

## 合并模型

执行下面的命令，生成最终模型，其中models/Qwen2.5-0.5B-Instruct-Martin是合并后模型的目录

```shell
mlx_lm.fuse --model /Users/martin/develop/projects/modelscope/Qwen2.5-0.5B-Instruct --adapter-path adapters --save-path models/Qwen2.5-0.5B-Instruct-Martin
```
