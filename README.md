# Semantic Segmentation

We perform a semantic segmentation of road versus non road. To achieve this we use a [fully convulotional neural network](https://arxiv.org/pdf/1605.06211.pdf). The architecture is based on the network developed by [Berkley researchers](https://people.eecs.berkeley.edu/~jonlong/long_shelhamer_fcn.pdf).

## Architecture
The architecture leverages a pretrained model and builds on that to classify independent pixels to be road or not. As such we perform partial transfer learning by taking the pretrained [VGG network by Long, Shelhame, et al.](https://people.eecs.berkeley.edu/~jonlong/long_shelhamer_fcn.pdf). Since convolutional networks have the shortcoming that they do not store information location we need to introduce skip layers. This leads to the following design. First, we convert the output of the pretrained network to 1x1 convolutional layer. Next, we introduce skip layers on the fourth layer and the third layer of VGG. We transpose after each skip layer so that the ultimate output has the same dimensions as the initial input. Throughout the operations of the network we need to ensure that the right dimensions of the layers is obtained. We achieve this by simple convolutional layers.

To improve the accuracy of the network we initialze the weights of each layer to random values of a normal distribution with standard deviation 0.01. In addition we introduce a L2 regularization on the layers to ensure that certain nodes in the neural network don't get too heigh weights.

As an optimizer we use [Adam Optimizer](https://arxiv.org/abs/1412.6980) with cross entropy as error metric. We found that the learning rate and drop out rate have a significant impact on accuracy of the algorithm.

## Learning Rate and Drop Out Rate
We initially tried a learning rate of 0.004 with drop out of fifty percent. We noted that with this learning rate the first epoch would have an loss around 0.9. In the second epoch it would jump to 1.2 and subsequent epochs even higher before going down. To reduce this oscillation we reduced the learning rate to 0.001. This caused the initial epoch to have a slightly higher average error but subsequent epochs would consistently decrease. This effect became even stronger with a learning rate of 0.0005 and 80 epochs. For this configuration we found a cross entropy loss of 10.81 percent.

We dropped the learning rate to 0.0001 with 150 epochs and found an average cross entropy loss of 11.68 percent. We searched for even a lower loss by decreasing the drop out rate and increasing the number of epochs. The lowest we found is with 300 epochs and thrity percent drop out rate, with which we found an error of 7.21 percent.

We see that with a learning rate of 0.0005 the traffic sign doesn't get recognized as road but some sidewalk gets incorrectly classified.

![image with traffic sign](https://raw.githubusercontent.com/abossenbroek/CarND-Semantic-Segmentation/master/img/lr0_0005_1.png)

This is not the case with a reduced dropout as we can see here,

![image with traffic sign](https://raw.githubusercontent.com/abossenbroek/CarND-Semantic-Segmentation/master/img/dropout_1.png)

We see similar charateristics for an image with considerable amount of shadow. Here the network with learning rate of 0.0005 misses parts of the road,

![image with traffic sign](https://raw.githubusercontent.com/abossenbroek/CarND-Semantic-Segmentation/master/img/lr0_0005_2.png)

By decreasing the learning rate to 0.0001 we get better results as can be seen here,

![image with traffic sign](https://raw.githubusercontent.com/abossenbroek/CarND-Semantic-Segmentation/master/img/lr0_0001_2.png)

The reduction of dropout leads to similar results but also wrongly classifies some parts of the road as can be seen here,

![image with traffic sign](https://raw.githubusercontent.com/abossenbroek/CarND-Semantic-Segmentation/master/img/dropout_2.png)

Finally we run investigate one of the hardest images we found. For this image the learning rate of 0.0005 is clearly inaccurate,

![image with traffic sign](https://raw.githubusercontent.com/abossenbroek/CarND-Semantic-Segmentation/master/img/lr0_0005_3.png)

By decreasing the dropout we get,

![image with traffic sign](https://raw.githubusercontent.com/abossenbroek/CarND-Semantic-Segmentation/master/img/dropout_3.png)

However, increasing the dropout back to fifty percent and decreasing the learning rate leads to the best result as can be seen below,

![image with traffic sign](https://raw.githubusercontent.com/abossenbroek/CarND-Semantic-Segmentation/master/img/lr0_0001_1.png)

So we conclude that despite an approximately 3 percent higher cross entropy the dropout rate of fifty leads to better results as long as the learning rate is reasonably low.

