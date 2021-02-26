# 개발일지
+ **01 / 15 (FRI)**  
Tutorial을 통해 생성한 baseline code 에서 pretrained model을 `VGG16`에서 `EfficientNetB3`로 변경하고 학습을 진행시켰지만 OOM(Out Of Memory) 에러 발생
+ **01 / 18 (MON)**<br>
OOM 에러의 원인을 찾다 보니 파일 디렉토리를 통해 이미지를 읽으들어온뒤 shuffle의 buffer size를 학습 데이터 크기인 20000으로 정하였기 때문. 데이터 파일 디렉토리로만 이루어져 있으면 문제가 없었지만 directory를 통해 이미지를 불러와서 array로 만들었기 때문에 shuffle의 buffer size를 1000으로 수정하니 모델이 학습이 되었다. 이렇게 학습된 모델을 통해 submit을 하려고 보니 본 대회는 code competition이라 코드를 제출하였는데 notebook timeout 에러 발생
+ **01 / 19 (TUE)**<br>
notebook timeout에러 발생 해결을 위해 Kaggle을 찾아보니 code Competition 같은 경우 training 부분과 inference 부분을 나누어 submit시 train에서 저장된 모델을 불러와 inference 부분을 통해 추론하는 코드를 제출. 하지만 또 notebook timeout 에러 발생. 알고보니 batch를 잘못주어 발생한 에러. 코드 수정후 다시 submit 했는데 이제는 결과 0점으로 나와버렸다.
+ **01 / 20 (WED)**<br>
0점 원인을 계속 찾다보니까 inference 부분에서 출력층의 activation function이 softmax이다 보니 `predict()`에서 argmax를 붙여줬는데 jupyter에서 찍어보니 각 사진에 대한 것이 아닌 전체 데이터셋에 대한 argmax가 나온것이었다. 결국 test data에서 이미지를 한장씩 불러와서 예측하도록 코드를 수정하였고 그 결과 0.76 정도가 나옴. 결과를 향상시키기 위해 EfficinetB3 모델에서 learning rate, epoch, resolution(img_size) 등의 hyper parameter를 수정하여 학습시켰지만 0.78을 모두 넘지 못하였음. 
+ **01 / 21 (THU)**<br>
EfficientNetB3에서의 HyperParameter(resolution, lr, batch_size)를 바꿔가며 학습을 하였지만 val_acc가 0.78을 넘지 못함. 원인은 base_model을 freeze 시켜놓고 classifier부분만 학습을 시켜 Transfer Learning을 진행하였기 때문이었음. base model를 unfreeze시키고 모든 layer의 weight를 다시 학습(lr=1e-3, epochs=10)시켜 Fine-Tuning을 진행한 결과 val_acc가 0.81 정도로 향상되었음. 하지만 학습시간은 매우 길어짐. Fine-Tuning시 learning rate을 낮게 잡지 않으면 빠르게 overfitting이 될 수 있기 때문에 lr을 낮게 잡고 sceduling을 시켜야겠다. 
+ **01 / 22 (FRI)** <br>
Learning Rate Schedule을 위해 ReduceLRonPleatue를 사용했지만 score가 향상되지는 않음. 때문에 CosineAnnealingWarmRestart Scheduler를 사용하여 학습을 하려 했지만 해당 스케줄러는 파이토치에는 있지만 Tensorflow에서는 cosinedecay로 있어 cosinedecay사용. 해당 부분에 대한 내용은 공부가 필요해 보임
+ **01 / 25 (MON)** <br>
Tensorflow의 CosineDecay와 Pytorch의 CosineAnealingWarmRestart에 대한 내용 확인. 해당 Competition에서 ResneXt50_32x4d가 좋은 결과를 낸다고 하여 해당 모델 논문 확인.
+ **01 / 26 (TUE)** <br>
새로운 optimizer인 Radam과Lookahead를 같이 사용하였고 scheduler는 그대로 CosineDecay를 사용. 10 epoch 동안 학습을 실시하는데 2 epoch 이후 부터 val_loss가 증가하는데 val_acc도 증가하기 시작하였다. Overfitting인지 아닌지 확인필요.
+ **01 / 27 (WEN)** <br>
Radam을 이용한 Lookahead optimizer를 사용하였을 때 val_loss가 증가하는데에 val_acc도 증가하기 시작하였음. 해당 model을 이용하여 submit한 결과 score는 0.803정도로 나옴. 때문에 Radam을 사용하지 않고 Adam을 이용한 Lookahead optimizer와 최근에 나온 Adabelief optimizer를 사용하여 모델을 학습시킴. Adam을 이용한 Lookahead optimizer의 경우 0.837로 향상되었지만 Adabelief optimizer는 Inferecence code를 실행하던 도중 에러가 발생.
+ **01 / 28 (THU)**<br>
Tensorflow 사이트에서 Fine Tuning시 Batch Normalization Layer는 freeze시켜야 한다는 것을 알게 되었음. 때문에 EfficientnetB3를 불러와 위 방법을 통해 학습을 시킴. 모델을 모두 unfreeze하고 학습시키는 과정에서 Overfitting 발생. Submit한 결과 0.800으로 점수가 낮아졌음.
+ **01 / 29 (FRI)**<br>
Adam으로 Freeze된 모델을 학습시킨후 모델을 BN Layer를 제외하고 모두 unfreeze 시킨 후 AdaBelief를 사용하여 학습을 시키려고 했지만 오류가 발생. 다시 같은 방법으로 Cosine Decay를 적용한 Adam을 이용하여 Freeze된 모델을 학습시키고 Adam에 Lookahead을 적용하고 초기 Learning rate를 낮추어서 unfreeze모델을 다시 학습시킴. 이때 초기 learning rate를 너무 작게 잡다 보니 local mnimum에 도달하는데 걸리는 시간이 너무 걸려, 다시 learning rate를 높이고 학습시킴.
+ **02 / 01 (MON)**<br>
저번주에 Learning rate을 1e-4로 학습한 결과 점수가 0.841로 향상되었음. 이제 이 모델에 Cross Validation을 적용해보기로 하고 코드를 구현. 하지만 Kaggle Kernel내에서 GPU를 한번에 9시간만 사용이 가능하여 시간초과로 모델을 모두 돌리지 못함. 때문에 다시 학습을 한번하는것으로 변경. 또한 TTA(Test Time Augmentation)기법을 알게 되어 이를 코드로 구현하고 적용한 결과 점수가 0.457로 하락. TTA를 적용하는 횟수를 너무 높게 잡아서 그런것으로 예상됨.
+ **02 / 02 (TUE)**<br>
TTA를 3으로 줄이고 augmentation 기법들을 줄인 결과 점수가 0.008정도 향상되었음. 또한 KFold를 이용하여 점수가 제일 높았던 모델을 학습시키기 위해 Kaggle Kernel내 GPU가 아닌 TPU를 사용하여 모델을 돌려보기로 계획. 그러기 위해서 tfrecord형식의 데이터를 불러오는 것으로 바꾸고 학습. 이때 모델을 학습시킬 때 1 epoch이 넘어가면 train과 validation data에서 모두 loss값이 nan이 되는 현상이 발생하여 문제의 원인을 찾아본 결과 batch를 적용한 후 마지막 batch를 drop시키는 것으로 해결.
+ **02 / 03 (WEN)**<br>
TPU를 연결하여 GPU에서 돌렸던 모델을 학습해본결과 GPU의 결과와 다르게 나옴.
+ **02 / 04 (THU)**<br>
optimizer를 여러개를 사용해보고 cosine decay와 cosine decay restarts등의 학습 스케줄러를 사용해보았지만, overfitting되거나 갑자기 loss가 폭발해버리는 gradient exploding 등의 문제 발생하고, val_loss는 증가하는데 val_acc가 높아지는 등의 문제점이 계속 발생. 그러던중, 모두 공통적으로 train_loss가 0.3정도 일때 val_loss가 가장 최소가 됨을 발견하였고, 이에 cosine decay에서 decay step을 전체 학습하는 동안으로 하지 않고 train_loss가 0.3 정도 되는 step으로 변경하여, learning rate이 train_loss가 0.3 정도 되었을 때의 값을 이용하도록 함. 그 결과 그 전에 발생했던 overfitting이 해소되었고, val_loss또한 안정적으로 유지 되었음. 그 전에 decay step을 전체 학습하는 동안으로 설정하였을 때 가장 높은 score가 나왔는데 지금 보니 init_lr를 1e-5로 설정하여 1e-5에서 1e-6사이를 cosine decay시키니 val_loss도 큰 변화가 없던 거였음.
+ **02 / 05 (FRI)**<br>
어제 생각한 방법으로 해당 문제가 해결될 것이라 생각되었지만 LB 점수는 그대로 0.81정도의 score를 유지. 모델을 학습시킬 때 val_loss도 안정적이고 acc도 높았지만 정작 test에서는 val_loss가 더 높았던 모델보다 점수가 낮게 나옴. 그 이유를 찾아보고자 점수가 가장 높았던 모델과 비교해 봤을 때 배치크기가 영향을 주었을 것이라고 생각을 하여 이에 대한 자료를 찾아본결과 배치가 크면 모델의 최적화와 일반화가 어렵다는 것을 알게 됨. 특히 학습데이터와 조금이라도 다른 데이터가 들어왔을 때 모델이 제대로 작동하지 않을 가능성이 높다는 점에서 현재 모델과의 문제점과 유사하여 배치크기를 줄이거나, 배치크기를 늘린만큼 learning rate도 증가 시켜 학습시켜보기로함
