# PanIIT-Online-Round

The ​ **_Preprocessing notebook_** ​ contains code related to data augmentation and the
preprocessing of images. It also illustrates visualizations of the data during the process
(also included in this report).

The ​ **_Model notebook_** ​ contains the code related to the model used to train, ensemble,
and further predict the labels for test data.


## Augmentation

We have used two ways to augment

1. **_Horizontal Flip:_** ​ We observed that, when we flip the image horizontally the class
    of the image will remain same, we doubled our dataset.
2. **_Vertical Flip:_** ​ We observed that, if we flip the 2445 images from class 1,2,3 in
    our dataset vertically,
       a. We obtain more data on classes 6,5,4 respectively and clubbing this with
          horizontal flip we nearly increase the data for these classes by 4 fold.
       b. This also enhances our performance on the hard classes as per our initial
          models.
       c. This does introduce class imbalance but the trade off is completely
          insignificant as our focus is to help the model better learn the curvature of
          mouth as differentiating between classes 1,2,3 and 4,5,6 is very easy for
          our model as per the confusion matrix analysis.


## Preprocessing

The training image used contains several phases of preprocessing.

**_Segmenting Facial Landmarks : Eyes, Nose and Mouth_**

First segment the black region from the grayscale image. This will include eye, nose
and mouth boundaries, along with the boundary of face, and the black dots. The
smallest dots are removed using the “rm_small” function which removes white objects
from the black and white image which have size less than the given value.

**_Getting the Face Mask_**

Face Mask is obtained by segmenting the “colored” region only, i.e. removing the black
and white regions from the face. The outside spots are removed since they are
extremely small than the mask, using the same function “rm_small”. The inner noise is
removed by flipping (inverting) the mask and repeating the step above (using rm_small).
Face mask is thus obtained by flipping the obtained mask.

**_Segmenting Eyes_**

Eyes are segmented using the “white” region and the “face mask” and then using
morphological operations to fill holes and remove smaller objects.


**_Noise Removal_**

Several steps are used to remove the noise. Firstly, the face mask is used to segment
only the inner part of the face.
Secondly, the face obtained finally is diluted and is used as a mask, so than only the
required small objects (i.e. only eye, nose, and mouth boundary) are added. Thus the
small “dashes” which are required are obtained.
Finally, the image is resized to match the corresponding dimension.

**_Vertical Alignment_**

Vertical alignment is done by first obtaining the “minimum area rectangle” for the face
mask. Now the face can be rotated in two ways, which gives one vertically aligned and
one horizontally aligned image.
To check which one of the two is vertical, we segment the eyes for the two images. We
now see the “vertical count” for the two images to check if the white pixels are more
vertically aligned or horizontally.


In case of single eye, we take the whole face and check for white pixel counts on the
whole facial image itself.
Counting of eyes is done by simply labelling the segmented eyes, and removing small
objects followed by diluting the image to fill holes.
Although CNNs are rotation invariant to a great extent by adding this preprocessing step
we observed that our model converged fast thus taking less epochs to fully converge
and we saved a lot of time here.



## Model

**_Data Split_**

We had a total of 14890 images in the training set after applying image augmentation.
Out of these, 20% of the images were taken as validation data.


**_Experiments_**

1. We tried experimenting with the number of layers in the model. We found out that
    increasing the number of hidden layer blocks[deeper models] led to an increase
    in accuracy on the validation set.
2. We tried to experiment with the value of dropout and the number of filters in the
    hidden layers. The value 0.25 for dropout gave the most convincing results.
3. We tried training with images of different sizes. Finally, we went for 128x
    images as 256x256 images didn’t result in the expected performance boost. It
    was not worth the huge amount of time it took to train.
4. We tried adding Batch Normalization and realised that it increased the
    performance of the model by some margin.


**_Ensembling_**

Finally to boost our accuracy we selected 7 of our best models among models trained
by varying:
1. Resolution of data(128x128 and 256x256).
2. Depth of the network.
3. Different model specific hyper-parameters[optimizer, lr, patience etc.].
4. Preprocessing steps, their sequence, their parameters.
We thus applied voting ensembler to obtain the final results.

**_Training Experience_**

System Configuration:
CPU: AMD Ryzen 3.8 GHz
RAM: 16 GB DDR5 RAM
GPU: Nvidia GeForce GTX 1070 8 GB VRAM


We trained the model using RMSprop optimizer with learning rate = 0.001 (decreasing
with epochs) for 30 epochs. The same model was again trained with learning rates
0.0001 for next 10 to 30 epochs.
Model 1 scored a maximum of 99.56 % accuracy of validation set. While model 2
scored a maximum of 99.66 % accuracy on validation set.


