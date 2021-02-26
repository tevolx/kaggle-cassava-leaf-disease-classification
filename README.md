![image](image/Cassava_Leaf_Classification.JPG)
# Cassava Leaf Diease Classification
Objective: Classify each cassava image into four disease categories or a fifth category indicating a healthy leaf.

# Solution
 + **BaseLine Code** : I used my tensorflow tutorial code as a baseline code for this contest. You can see my code here [Tensorflow-Tutorial](https://github.com/stuart-park/Intern-Tensorflow_Tutorial)
 +  **Preprocessing**
    + Image Size : 512
    + Augmentation :  RandomResizedCrop, Transpose, HorizontalFlip, VerticalFlip, RandomRotate90, RandomBrightnessContrast, HueSaturationValue
 + **Loss**
    + SparseCategoricalCrossEntropy
 +  **Model**
    + EfficientNetB3 pretrained with Imagenet with no top.
    + EfficientNetB3 top layer : GlobalAveragePooling + Dropout + Dense
 +  **Optimizers & LR Scheduler**
    + Adam + Lookahead + CosineDecay
    + Adam + SWA(Stochastic Weight Averaging) + CosineDecay
    + RectifiedAdam + Lookahead + CosineDecay
    + Adabelief + Lookahead + CosineDecay
+ **Training**
    + Batch Size: 8
    + Epochs :10 
    + Callbacks : EarlyStopping, ModelCheckpoint
    + I've also tried training my model twice by first training the newly added top layer, and then unfreeze them and fine-tuning the entire model. But it didn't show much improvements. 

+ **Inference**
    + Ensemble & TTA(Test Time Augmentation)

| Models | TTA | Public Score | Private Score |
| ------------- | ------------- | ------------- | ------------- |
| Adam | 6  | 0.8600  | 0.8626 |
| Adam + SWA | 6  | 0.8548  | 0.8581 |
| Adam(5Fold)  | 6  | 0.8607   | 0.8652 |
| RectifiedAdam  | 6  | 0.8571  | 0.8627 |
| Adabelief  | 6 | 0.8620  | 0.8638 |
| Adam, RectifiedAdam  | 6 | 0.8609  | 0.8661 |
| Adam, RectifiedAdam, Adabelief  | 6  | 0.8600  | 0.8685 |

# Result & Improvements
+ Improvements
    + Ensemble is powerful! I used single model for the final submission because the public score was higher, but in private score ensemble models were higher.
    + I wanted to use CutMix and Mixup with label smoothing but OOM kept occur during training. Should study more about this.
    + Also I wanted to use ResNeXt50 model, but I couldn't find how to use it from keras.
    + Train dataset for this competition had few miss labeled data, and after the competition I've found out that I could have tried different loss, like bi-tempered loss. Also have to study more about this.
+ Results
    + Private: 0.8652
    + Public: 0.8607
    + This was my first competition for image classification and I've learned many things during competition. Thanks for all kagglers and organizers for this competition.
