# 领域知识：

+ 一般有基于优化和基于学习的方式
+ 基于优化的方法迭代次数多，训练时间长，但是选择合适的空间，重建能力很强，不过因为不容易控制隐变量的分布，所以得到的z不适合编辑（脱离预训练生成器的隐空间）
+ 基于学习的方法速度快，但是重构能力低，需要设计很好的网络结构和损失，以及好的训练Encoer的方法

# In-Domain GAN Inversion for Real Image Editing

+ 提出两部优化，domain-guided和domain-regularized

  + 前者学习一个E
    + 对于重建图像并不是计算和真实图像的距离，而是通过判别器判别哪个是真实图像哪个是重建图像
  + 后者把E当作一个正则项去优化Z，就是将每次生成的图片经过E得到一个Z去和原Z计算损失，作者说可以把充分利用生成器的域的语义信息，通过E把Z限制到与训练的G的隐空间域，使Z带有更多的语义信息。

## Model

+ ![image-20221114191941886](D:\note_file\note_article\GAN_inversion.assets\image-20221114191941886.png)


# (e4e)Designing an Encoder for StyleGAN Image Manipulation

+ 重构图片的效果、可编辑性的强弱，分别用distortion和editability代表distortion低（表示重构图片效果较好）的一般editability能力弱。

## Contribution

+ 作者观察到W空间的重建能力弱，W+空间的可编辑能力弱，作者希望得到训练一个编码器，使图片能映射到隐空间上，同时设计损失保证可编辑的能力

## Model

+ ![image-20221108182349336](D:\note_file\note_article\GAN_inversion.assets\image-20221108182349336.png)
+ ![image-20221108183618107](D:\note_file\note_article\GAN_inversion.assets\image-20221108183618107.png)
+ ![image-20221108183625769](D:\note_file\note_article\GAN_inversion.assets\image-20221108183625769.png)

# (pSp)Encoding in Style: a StyleGAN Encoder for Image-to-Image Translation

## Motivation

+ W+空间比W空间好，但是不容易训练Encoder直接将图片映射到其中，之前都是通过优化的方式找到W+空间的隐向量，这很耗时

## Contribution

+ 提出了一个直接把图片映射到W+空间的Encoder，而不是像Image2Style那样使用优化的方式去找W+空间中的z
+ 提出了一个通用的！端到端的！能解决很多图像领域任务（反演、转正、修复、条件生成、超分、插值）的！框架。

## Model

+ ![image-20221108215203961](D:\note_file\note_article\GAN_inversion.assets\image-20221108215203961.png)
+ ![image-20221109103612046](D:\note_file\note_article\GAN_inversion.assets\image-20221109103612046.png)
+ **map2style**：a set of 2-strided convolutions followed by LeakyReLU activations

# ReStyle: A Residual-Based StyleGAN Encoder via Iterative Refinement

## Model

+ 所提出架构中的Encoder可以是PSP或E4E，所以是一个通用架构
+ ![image-20221109105548115](D:\note_file\note_article\GAN_inversion.assets\image-20221109105548115.png)
+ 作者通过Restyle方式减小了对Encoder的复杂度的要求，所以对PSP和E4E中使用的FPN结构进行简化，取得了近似的结果
+ ![image-20221109105852170](D:\note_file\note_article\GAN_inversion.assets\image-20221109105852170.png)