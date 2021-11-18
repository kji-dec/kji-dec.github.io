---
layout: post
title: Pytorch Tutorial (1)
subheading: Training a Classifier
categories: Pytorch
tags: Pytorch CV
sidebar: []
---





[참고사이트](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html#sphx-glr-beginner-blitz-cifar10-tutorial-py)

### Data

→ image, text, audio, video data를 주로 다루게 되면, 표준 Python 패키지를 사용하여 불러온 후 NumPy 배열로 변환하고, 이를 torch.*Tensor로 변환하면 됨

- 데이터 별 패키지
  - 이미지: Pillow, OpenCV
  - 오디오: SciPy, LibROSA
  - 텍스트: NLTK, SpaCy
  - 영상: torchvision

CIFAR10 dataset을 이용해 실습

1. CIFAR10의 train/test용 dataset을 torchvision으로 불러오고 normalizing

2. CNN 정의

3. loss function 정의

4. training data를 이용해 신경망 학습

5. test data를 이용해 신경망 검사

   <br>

아래 부터는 실습 진행한 코드와 각 셀 별 실행 화면



```python
import torch
import torchvision
import torchvision.transforms as transforms

transform = transforms.Compose(
    [transforms.ToTensor(),
     transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5))])

batch_size = 4

trainset = torchvision.datasets.CIFAR10(root='./data', train=True,
                                        download=True, transform=transform)
trainloader = torch.utils.data.DataLoader(trainset, batch_size=batch_size,
                                          shuffle=True, num_workers=2)

testset = torchvision.datasets.CIFAR10(root='./data', train=False,
                                       download=True, transform=transform)
testloader = torch.utils.data.DataLoader(testset, batch_size=batch_size,
                                         shuffle=False, num_workers=2)

classes = ('plane', 'car', 'bird', 'cat',
           'deer', 'dog', 'frog', 'horse', 'ship', 'truck')
```

```python
import matplotlib.pyplot as plt
import numpy as np

#이미지 보여주는 함수
def imshow(img):
    img = img / 2 + 0.5     # unnormalize
    npimg = img.numpy()
    plt.imshow(np.transpose(npimg, (1, 2, 0)))
    plt.show()

#train dataset을 무작위로 가져오기
dataiter = iter(trainloader)
images, labels = dataiter.next()

#image 보여주기
imshow(torchvision.utils.make_grid(images))
#label 출력
print(' '.join('%5s' % classes[labels[j]] for j in range(batch_size)))
```

![result](https://user-images.githubusercontent.com/71377968/142339532-2858117a-af5b-44cb-9aac-4205b2c11335.png)

```python
#CNN 정의
import torch.nn as nn
import torch.nn.functional as F


class Net(nn.Module):
    def __init__(self):
        super().__init__()
        self.conv1 = nn.Conv2d(3, 6, 5)
        self.pool = nn.MaxPool2d(2, 2)
        self.conv2 = nn.Conv2d(6, 16, 5)
        self.fc1 = nn.Linear(16 * 5 * 5, 120)
        self.fc2 = nn.Linear(120, 84)
        self.fc3 = nn.Linear(84, 10)

    def forward(self, x):
        x = self.pool(F.relu(self.conv1(x)))
        x = self.pool(F.relu(self.conv2(x)))
        x = torch.flatten(x, 1) # flatten all dimensions except batch
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        x = self.fc3(x)
        return x


net = Net()
```

```python
#loss function, optimizer 정의
import torch.optim as optim

criterion = nn.CrossEntropyLoss()
optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)
```

```python
#train NN --> data를 반복해서 input으로 넣고 optimize함
for epoch in range(2):

    running_loss = 0.0
    for i, data in enumerate(trainloader, 0):
        #input 받음
        inputs, labels = data

        #param gradient 0으로
        optimizer.zero_grad()

        # forward, backward, optimize
        outputs = net(inputs)
        loss = criterion(outputs, labels)
        loss.backward()
        optimizer.step()

        # print statistics
        running_loss += loss.item()
        if i % 2000 == 1999:    #2000 minibatch마다 출력
            print('[%d, %5d] loss: %.3f' %
                  (epoch + 1, i + 1, running_loss / 2000))
            running_loss = 0.0

print('Finished Training')
```

![image](https://user-images.githubusercontent.com/71377968/142339852-3773ddb4-1eae-442e-b815-fea97b3aee7c.png)

```python
#trained model 저장
PATH = './cifar_net.pth'
torch.save(net.state_dict(), PATH)
```

```python
#test data로 test network
dataiter = iter(testloader)
images, labels = dataiter.next()

#image 출력
imshow(torchvision.utils.make_grid(images))
print('GroundTruth: ', ' '.join('%5s' % classes[labels[j]] for j in range(4)))
```

![image](https://user-images.githubusercontent.com/71377968/142339929-8a6cfdf4-a338-4ce3-a8cf-f19982df0af6.png)

```python
#학습된 NN 보기
outputs = net(images)

_, predicted = torch.max(outputs, 1)

print('Predicted: ', ' '.join('%5s' % classes[predicted[j]]
                              for j in range(4)))
```

![image](https://user-images.githubusercontent.com/71377968/142339989-e1ad406c-bef6-4291-9277-b2848827a172.png)

해당 결과를 보면 알 수 있듯, 학습된 모델은 대체로 괜찮은 성능을 보이고 있다.



```python
#전체 데이터셋에 대한 결과 보기
correct = 0
total = 0

with torch.no_grad():
    for data in testloader:
        images, labels = data
        outputs = net(images)
        _, predicted = torch.max(outputs.data, 1)
        total += labels.size(0)
        correct += (predicted == labels).sum().item()

print('Accuracy of the network on the 10000 test images: %d %%' % (
    100 * correct / total))
```

![image](https://user-images.githubusercontent.com/71377968/142340055-aff499f3-cd94-4f05-a8b4-707b4a56c216.png)

```python
#각 class 별 정확도 확인하기
correct_pred = {classname: 0 for classname in classes}
total_pred = {classname: 0 for classname in classes}

with torch.no_grad():
    for data in testloader:
        images, labels = data
        outputs = net(images)
        _, predictions = torch.max(outputs, 1)
        # collect the correct predictions for each class
        for label, prediction in zip(labels, predictions):
            if label == prediction:
                correct_pred[classes[label]] += 1
            total_pred[classes[label]] += 1

for classname, correct_count in correct_pred.items():
    accuracy = 100 * float(correct_count) / total_pred[classname]
    print("Accuracy for class {:5s} is: {:.1f} %".format(classname,
                                                   accuracy))
```

![image](https://user-images.githubusercontent.com/71377968/142340115-e2356b43-31fd-4993-a282-9bfc17c89c13.png)

위의 결과를 보았을 때, 해당 model이 일부 class에서 높은 정확도를 보이는 것을 알 수 있다.

<br>

아래는 neural network를 GPU로 옮기는 방법에 대한 설명이다.

우선 디바이스가 CUDA 를 사용할 수 있어야 한다.

```python
device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")

print(device)
```

<br>

이제부터, 디바이스가 CUDA 디바이스라 가정하고 GPU에 옮기면 된다.

```python
net.to(device)
#CUDA tensor로 변환

inputs, labels = data[0].to(device), data[1].to(device)
#이때, input과 label도 GPU로
```

실제로 CPU와 GPU의 처리 속도가 크지 않은데, 이는 neural network가 작기 때문이다.  

<br>

<strike> 노션에 기록용으로 쓰던 글들을 하나씩 블로그로 올리고 있다. 조금 귀찮지만... 이것저것 테스트해보니 깃허브 블로그에도 적응 완료했다! 꽤나 재미있다 :-)  얼른 남은 글들도 하나씩 올려야겠다! </strike>
