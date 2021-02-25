![image](image/Cassava_Leaf_Classification.JPG)
# Cassava Leaf Diease Classification
Objective: Classify each cassava image into four disease categories or a fifth category indicating a healthy leaf.

# Solution
 + **BaseLine Code** : I used my tensorflow tutorial code as a baseline code for this contest. You can see my code here: [Tensorflow-Tutorial](https://github.com/stuart-park/Intern-Tensorflow_Tutorial)
 +  **Preprocessing**
    + Image Size : 512
    + Augmentation :  RandomResizedCrop, Transpose, HorizontalFlip, VerticalFlip, RandomRotate90, RandomBrightnessContrast, HueSaturationValue
 + **Loss**
    + SparseCategoricalCrossEntropy
 +  **Model**
    + EfficientNetB3 pretrained with Imagenet.
 +  **Optimizers**
    + Adam
    + RectifiedAdam
    + Adabelief
 
| Optimizers  | Public Score | Private Score |
| ------------- | ------------- | ------------- |
| Adam  | 0.0000  | 0.0000  |
| RectifiedAdam  | 0.0000  | 0.0000  |
| Adabelief  | 0.0000  | 0.0000  |

+ **Learning Rate Scheduler** 
    + CosineDecay
    + CosineDecayRestarts
    + ReduceLROnPlateau

+ **Ensemble & TTA(Test Time Augmentation)**

# Result & Improvements
+ Private: 0.8652
+ Public: 0.8607
+ Improvements : 

