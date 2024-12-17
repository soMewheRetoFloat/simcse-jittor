# SimCSE-Jittor

by Fl0at9973

来自 Princeton-NLP 的 SimCSE 计图版本复现。

## 代码创新点

+ 参考[bert官方]("[google-research/bert: TensorFlow code and pre-trained models for BERT](https://github.com/google-research/bert)")以及[初版计图bert]("[LetianLee/BERT-Jittor: A BERT model built with Jittor | 计图版 BERT 模型 | 计图 NLP 教程](https://github.com/LetianLee/BERT-Jittor)")，自定义实现了可以支持 bert_base 和 bert_large 的计图 bert：
  + 可以支持从文件导入config以及输入config，config支持修改层；
  + 可以隔离最终的pool层；
+ 数据集缓存以及读取的支持；
+ checkpoint 保存，提取，核验；

## 环境相关

推荐版本：

> python3
>
> gcc <= 9.4.0

关于 jittor 以外的包管理可以使用：

```bash
pip install -r ./requirements.txt
```

关于 jittor 请参考：[计图官网]("[Jittor(计图): 即时编译深度学习框架 — Jittor](https://cg.cs.tsinghua.edu.cn/jittor/)")

~~社区真的很久没人疑难解答和修复现有bug了~~

## 数据集准备

先 clone，之后：

```bash
cd script
bash ./download_nli.sh
bash ./download_wiki.sh
```

有关预训练模型，请将预训练的模型导入 `pretrain_model` 文件夹下，之后修改运行脚本即可。

## 运行

请处在仓库根目录下。

无监督：

```bash
bash ./script/run_unsup_train.sh
```

有监督：

```bash
bash ./script/run_sup_train.sh
```

## 目前发现的 jittor 问题

+ AdamW优化器在导入 pretrained bert 参数的情况下，不能有过长的 evaluate 周期（在我们的模型下，实测是<=14即可正常训练），否则会爆显存（尝试过强制同步和强制清理cuda cache，但是由于计图连自己的cudnn测试都过不了，所以只要调用多线程就会出错）；但是如果不导入 pretrained 就没有问题，但不初始化 bert 根本无法训练出好的结果（论文原文就是使用的 pretrained bert），目前根本无法解决这个问题；
+ 计图在不同的显卡和不同的 gcc 版本下会报不同的错；
+ 在创建随机数组时，类型字段 `dtype` 未被使用，希望官方能注意到。
