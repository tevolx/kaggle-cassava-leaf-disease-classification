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
    + EfficientNetB3 pretrained with Imagenet with no top.
    + EfficientNetB3 top layer : GlobalAveragePooling + Dropout + Dense
 +  **Optimizers & LR Scheduler**
    + Adam + Lookahead + CosineDecay
    + RectifiedAdam + Lookahead + CosineDecay
    + Adabelief + Lookahead + CosineDecay
 
| Optimizers  | Public Score | Private Score | 5Fold CV(Pubic / Private) |
| ------------- | ------------- | ------------- | ------------- |
| Adam  | 0.8600  | 0.8626  | 0.8607 / 0.8652  |
| RectifiedAdam  | 0.8571  | 0.8627 | - |
| Adabelief  | 0.8464  | 0.8448  | - |

+ **Training**
    + Batch Size: 8
    + 

+ **Inference**
    + Ensemble & TTA(Test Time Augmentation)

# Result & Improvements
+ Private: 0.8652
+ Public: 0.8607
+ Improvements : When I trained my model with single fold 

