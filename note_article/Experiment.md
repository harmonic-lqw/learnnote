# 各论文提供的预训练模型：

## pSp

+ 人脸
+ （虽然没提供其他域，但是可以自己训练）

## e4e

+ 人脸
+ 车
+ 马
+ 教堂

## restyle

+ 人脸：FFHQ70000——CelebA_HQ2824
+ 车：Stanford Cars8144——1000
+ 马：LSUN_Horse10000——2215
+ 教堂：LSUN_Church126227——300
+ 动物：AFHQ wild4739——500

## E2style

+ 人脸
+ （论文做了猫、马、车的实验，但是没有提供预训练模型）

## HFGI

+ 

# 记录

+ pSp有很大伪影
+ 尝试用Restyle，如果效果好还可以借鉴E2style修改map2style模块（用平均池化替代卷积）试试