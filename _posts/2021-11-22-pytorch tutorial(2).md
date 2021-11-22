---
layout: post
title: Pytorch Tutorial (2)
subtitle: Transfer Learning for Computer Vision
categories: Pytorch
tags: Pytorch CV
sidebar: []
---



[참고 사이트](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)

### Transfer Learning

매우 큰 dataset에서 ConvNet을 미리 학습한 후, 이 ConvNet을 다른 작업을 위한 초기 설정 혹은 fixed feature extractor로 사용

- finetuning: 무작위 초기화 대신 NN을 ImageNet 100 dataset 등으로 미리 학습한 신경망으로 초기화함. 나머지 과정은 그대로
- fixed feature extractor로의 ConvNet: 마지막 FC layer를 제외한 모든 신경망의 weight을 고정함. 마지막 FC layer는 새로운 random weight을 갖는 layer로 대체되어 이 layer만 학습함



아래의 예제는 개미와 벌을 분류하는 model을 학습하는 것. generalize하기에는 작은 dataset이지만, transfer learning을 이용할 것임 (train - 120장, val - 75)

```python
from __future__ import print_function, division

import torch
import torch.nn as nn
import torch.optim as optim
from torch.optim import lr_scheduler
import numpy as np
import torchvision
from torchvision import datasets, models, transforms
import matplotlib.pyplot as plt
import time
import os
import copy

plt.ion()
```

```python
data_transforms = {
    'train': transforms.Compose([
        transforms.RandomResizedCrop(224),
        transforms.RandomHorizontalFlip(),
        transforms.ToTensor(),
        transforms.Normalize([0.485, 0.456, 0.406], [0.229, 0.224, 0.225])
    ]),
    'val': transforms.Compose([
        transforms.Resize(256),
        transforms.CenterCrop(224),
        transforms.ToTensor(),
        transforms.Normalize([0.485, 0.456, 0.406], [0.229, 0.224, 0.225])
    ]),
}

data_dir = 'data/hymenoptera_data'
image_datasets = {x: datasets.ImageFolder(os.path.join(data_dir, x),
                                          data_transforms[x])
                  for x in ['train', 'val']}
dataloaders = {x: torch.utils.data.DataLoader(image_datasets[x], batch_size=4,
                                             shuffle=True, num_workers=4)
              for x in ['train', 'val']}
dataset_sizes = {x: len(image_datasets[x]) for x in ['train', 'val']}
class_names = image_datasets['train'].classes

device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
```



일부 이미지 시각화

```python
def imshow(inp, title=None):
    """Imshow for Tensor."""
    inp = inp.numpy().transpose((1, 2, 0))
    mean = np.array([0.485, 0.456, 0.406])
    std = np.array([0.229, 0.224, 0.225])
    inp = std * inp + mean
    inp = np.clip(inp, 0, 1)
    plt.imshow(inp)
    if title is not None:
        plt.title(title)
    plt.pause(0.001)  #update를 기다림

# 학습 데이터의 배치를 얻음
inputs, classes = next(iter(dataloaders['train']))

# 배치로부터 격자 형태의 이미지를 만들어냄
out = torchvision.utils.make_grid(inputs)

imshow(out, title=[class_names[x] for x in classes])
```

![image](https://user-images.githubusercontent.com/71377968/142787891-cc40f2fd-10af-47f5-ab73-891bd6423421.png)





model train; learning rate와 scheduling, 최적의 model을 구하는 과정

```python
def train_model(model, criterion, optimizer, scheduler, num_epochs=25):
    since = time.time()

    best_model_wts = copy.deepcopy(model.state_dict())
    best_acc = 0.0

    for epoch in range(num_epochs):
        print('Epoch {}/{}'.format(epoch, num_epochs - 1))
        print('-' * 10)

        # 각 epoch은 학습 단계와 검증 단계를 거침
        for phase in ['train', 'val']:
            if phase == 'train':
                model.train()
            else:
                model.eval()

            running_loss = 0.0
            running_corrects = 0

            for inputs, labels in dataloaders[phase]:
                inputs = inputs.to(device)
                labels = labels.to(device)

                # param gradient를 0으로 설정
                optimizer.zero_grad()

                # foward
                # train phase에만 연산 기록을 추적
                with torch.set_grad_enabled(phase == 'train'):
                    outputs = model(inputs)
                    _, preds = torch.max(outputs, 1)
                    loss = criterion(outputs, labels)

                    # train phase인 경우 backprop, optimize
                    if phase == 'train':
                        loss.backward()
                        optimizer.step()

                # 통계
                running_loss += loss.item() * inputs.size(0)
                running_corrects += torch.sum(preds == labels.data)
            if phase == 'train':
                scheduler.step()

            epoch_loss = running_loss / dataset_sizes[phase]
            epoch_acc = running_corrects.double() / dataset_sizes[phase]

            print('{} Loss: {:.4f} Acc: {:.4f}'.format(
                phase, epoch_loss, epoch_acc))

            # 모델 deep copy
            if phase == 'val' and epoch_acc > best_acc:
                best_acc = epoch_acc
                best_model_wts = copy.deepcopy(model.state_dict())

        print()

    time_elapsed = time.time() - since
    print('Training complete in {:.0f}m {:.0f}s'.format(
        time_elapsed // 60, time_elapsed % 60))
    print('Best val Acc: {:4f}'.format(best_acc))

    # 가장 나은 모델 weight를 불러옴
    model.load_state_dict(best_model_wts)
    return model
```

```python
def visualize_model(model, num_images=6):
    was_training = model.training
    model.eval()
    images_so_far = 0
    fig = plt.figure()

    with torch.no_grad():
        for i, (inputs, labels) in enumerate(dataloaders['val']):
            inputs = inputs.to(device)
            labels = labels.to(device)

            outputs = model(inputs)
            _, preds = torch.max(outputs, 1)

            for j in range(inputs.size()[0]):
                images_so_far += 1
                ax = plt.subplot(num_images//2, 2, images_so_far)
                ax.axis('off')
                ax.set_title('predicted: {}'.format(class_names[preds[j]]))
                imshow(inputs.cpu().data[j])

                if images_so_far == num_images:
                    model.train(mode=was_training)
                    return
        model.train(mode=was_training)
```





finetuning; 미리 train한 model을 불러오고 마지막 FC layer를 초기화 함

```python
model_ft = models.resnet18(pretrained=True)
num_ftrs = model_ft.fc.in_features
# 각 출력 샘플의 크기는 2
# 또는, nn.Linear(num_ftrs, len (class_names))로 generalize
model_ft.fc = nn.Linear(num_ftrs, 2)

model_ft = model_ft.to(device)

criterion = nn.CrossEntropyLoss()

# optimize가 잘 되었는지 확인
optimizer_ft = optim.SGD(model_ft.parameters(), lr=0.001, momentum=0.9)

# 7 epoch마다 0.1씩 lr 감소
exp_lr_scheduler = lr_scheduler.StepLR(optimizer_ft, step_size=7, gamma=0.1)
```

train & validation 과정을 거침

```python
model_ft = train_model(model_ft, criterion, optimizer_ft, exp_lr_scheduler,
                       num_epochs=25)
```



```python
(위 셀의 출력 결과)

Epoch 0/24
----------
train Loss: 0.5960 Acc: 0.6598
val Loss: 0.2069 Acc: 0.9216

Epoch 1/24
----------
train Loss: 0.4832 Acc: 0.8238
val Loss: 0.4045 Acc: 0.8562

Epoch 2/24
----------
train Loss: 0.7341 Acc: 0.7787
val Loss: 0.3405 Acc: 0.8497

Epoch 3/24
----------
train Loss: 0.4445 Acc: 0.8238
val Loss: 0.7291 Acc: 0.7647

Epoch 4/24
----------
train Loss: 0.4463 Acc: 0.8197
val Loss: 0.3453 Acc: 0.8693

Epoch 5/24
----------
train Loss: 0.4851 Acc: 0.8033
val Loss: 0.3764 Acc: 0.8562

Epoch 6/24
----------
train Loss: 0.3701 Acc: 0.8443
val Loss: 0.2430 Acc: 0.9085

Epoch 7/24
----------
train Loss: 0.3545 Acc: 0.8525
val Loss: 0.2691 Acc: 0.9150

Epoch 8/24
----------
train Loss: 0.3670 Acc: 0.8484
val Loss: 0.2494 Acc: 0.9281

Epoch 9/24
----------
train Loss: 0.3110 Acc: 0.8770
val Loss: 0.2704 Acc: 0.9085

Epoch 10/24
----------
train Loss: 0.3862 Acc: 0.8279
val Loss: 0.2526 Acc: 0.9150

Epoch 11/24
----------
train Loss: 0.2445 Acc: 0.9016
val Loss: 0.2312 Acc: 0.9412

Epoch 12/24
----------
train Loss: 0.1822 Acc: 0.9180
val Loss: 0.2516 Acc: 0.9150

Epoch 13/24
----------
train Loss: 0.2778 Acc: 0.8689
val Loss: 0.2712 Acc: 0.9150

Epoch 14/24
----------
train Loss: 0.3723 Acc: 0.8566
val Loss: 0.2308 Acc: 0.9346

Epoch 15/24
----------
train Loss: 0.2530 Acc: 0.8975
val Loss: 0.2270 Acc: 0.9412

Epoch 16/24
----------
train Loss: 0.2656 Acc: 0.8852
val Loss: 0.2254 Acc: 0.9346

Epoch 17/24
----------
train Loss: 0.2753 Acc: 0.8689
val Loss: 0.2250 Acc: 0.9281

Epoch 18/24
----------
train Loss: 0.2366 Acc: 0.8934
val Loss: 0.2211 Acc: 0.9346

Epoch 19/24
----------
train Loss: 0.2573 Acc: 0.9016
val Loss: 0.2392 Acc: 0.9281

Epoch 20/24
----------
train Loss: 0.2666 Acc: 0.8934
val Loss: 0.2681 Acc: 0.9150

Epoch 21/24
----------
train Loss: 0.2915 Acc: 0.8730
val Loss: 0.2282 Acc: 0.9281

Epoch 22/24
----------
train Loss: 0.3191 Acc: 0.8730
val Loss: 0.2266 Acc: 0.9281

Epoch 23/24
----------
train Loss: 0.2335 Acc: 0.9180
val Loss: 0.2358 Acc: 0.9216

Epoch 24/24
----------
train Loss: 0.2557 Acc: 0.8975
Best val Acc: 0.941176

Training complete in 38m 6s
Best val Acc: 0.941176
```

```python
visualize_model(model_ft)
```

![image](https://user-images.githubusercontent.com/71377968/142787994-78f86ed3-f738-4c93-89b2-69bbbd78ea29.png)

fixed feature extractor로의 ConvNet; 마지막 layer를 제외한 network의 모든 부분을 고정해야 함 → gradient가 개선되지 않게 해야 함

```python
model_conv = torchvision.models.resnet18(pretrained=True)
for param in model_conv.parameters():
    param.requires_grad = False

# 새로 생성된 module의 param은 기본값이 requires_grad=True 임
num_ftrs = model_conv.fc.in_features
model_conv.fc = nn.Linear(num_ftrs, 2)

model_conv = model_conv.to(device)

criterion = nn.CrossEntropyLoss()

# 마지막 layer의 param만 optimize되는지 관찰
optimizer_conv = optim.SGD(model_conv.fc.parameters(), lr=0.001, momentum=0.9)

# 7 epoch마다 0.1씩 lr 감소
exp_lr_scheduler = lr_scheduler.StepLR(optimizer_conv, step_size=7, gamma=0.1)
```



train & validation

```python
model_conv = train_model(model_conv, criterion, optimizer_conv,
                         exp_lr_scheduler, num_epochs=25)
```

```python
(위 셀의 출력 결과)

Epoch 0/24
----------
train Loss: 0.6219 Acc: 0.6598
val Loss: 0.2126 Acc: 0.9412

Epoch 1/24
----------
train Loss: 0.4968 Acc: 0.8033
val Loss: 0.6978 Acc: 0.7386

Epoch 2/24
----------
train Loss: 0.4983 Acc: 0.7787
val Loss: 0.2257 Acc: 0.9216

Epoch 3/24
----------
train Loss: 0.4159 Acc: 0.7992
val Loss: 0.1433 Acc: 0.9608

Epoch 4/24
----------
train Loss: 0.5809 Acc: 0.7377
val Loss: 0.2826 Acc: 0.9150

Epoch 5/24
----------
train Loss: 0.4401 Acc: 0.7992
val Loss: 0.3082 Acc: 0.9020

Epoch 6/24
----------
train Loss: 0.4745 Acc: 0.8238
val Loss: 0.1633 Acc: 0.9477

Epoch 7/24
----------
train Loss: 0.4063 Acc: 0.8115
val Loss: 0.1737 Acc: 0.9412

Epoch 8/24
----------
train Loss: 0.2742 Acc: 0.8730
val Loss: 0.1731 Acc: 0.9346

Epoch 9/24
----------
train Loss: 0.3372 Acc: 0.8484
val Loss: 0.1859 Acc: 0.9412

Epoch 10/24
----------
train Loss: 0.3019 Acc: 0.8770
val Loss: 0.1747 Acc: 0.9412

Epoch 11/24
----------
train Loss: 0.4174 Acc: 0.8115
val Loss: 0.1868 Acc: 0.9412

Epoch 12/24
----------
train Loss: 0.4530 Acc: 0.7787
val Loss: 0.1920 Acc: 0.9412

Epoch 13/24
----------
train Loss: 0.3872 Acc: 0.8361
val Loss: 0.1804 Acc: 0.9412

Epoch 14/24
----------
train Loss: 0.3734 Acc: 0.8156
val Loss: 0.1729 Acc: 0.9477

Epoch 15/24
----------
train Loss: 0.3502 Acc: 0.8648
val Loss: 0.1704 Acc: 0.9412

Epoch 16/24
----------
train Loss: 0.3155 Acc: 0.8525
val Loss: 0.1733 Acc: 0.9542

Epoch 17/24
----------
train Loss: 0.2872 Acc: 0.8770
val Loss: 0.1858 Acc: 0.9346

Epoch 18/24
----------
train Loss: 0.3458 Acc: 0.8566
val Loss: 0.1786 Acc: 0.9412

Epoch 19/24
----------
train Loss: 0.3505 Acc: 0.8238
val Loss: 0.1909 Acc: 0.9346

Epoch 20/24
----------
train Loss: 0.3825 Acc: 0.8484
val Loss: 0.1811 Acc: 0.9412

Epoch 21/24
----------
train Loss: 0.3403 Acc: 0.8525
val Loss: 0.1634 Acc: 0.9412

Epoch 22/24
----------
train Loss: 0.3075 Acc: 0.8525
val Loss: 0.1831 Acc: 0.9412

Epoch 23/24
----------
train Loss: 0.2734 Acc: 0.8893
val Loss: 0.1841 Acc: 0.9412

Epoch 24/24
----------
train Loss: 0.3340 Acc: 0.8525
val Loss: 0.1865 Acc: 0.9281

Training complete in 18m 50s
Best val Acc: 0.960784

```

```python
visualize_model(model_conv)

plt.ioff()
plt.show()
```



![image](https://user-images.githubusercontent.com/71377968/142788014-a6138804-53c1-4c94-b57f-8c5b2c2575d2.png)