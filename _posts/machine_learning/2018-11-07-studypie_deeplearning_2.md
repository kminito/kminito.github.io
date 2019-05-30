---
layout: post
title: "코인 가격 예측 스터디노트 - 2. Tensorflow GPU 세팅 "
category: "machine_learning"
tag:
  - "python"
  - "data science"
  - "machine learning"
  - "deep learning"
---
_스터디파이에서 '딥러닝으로 주식/코인 가격 예측하기'를 참여하며 공부한 내용을 정리한 것입니다.._  



  원래 google colab을 사용하려고 했으나, 쓰다보니 계속 불편해서 그냥 데스크탑에서 Jupyter notebook을 돌렸습니다.
  따라서 GPU 사용을 위한 세팅부터 새로 했습니다. google colab은 사용이 익숙치 않아서 그렇지, 세팅이나 속도 같은 걸 생각해보면 Google colab이 훨씬 빠르고 편리한 것 같아요. 무엇보다 학습하면서 게임을 할 수 있다는 게 가장 큰 장점인 것 같습니다.  

  자세한 가이드는 각각의 링크에 있습니다만, 혹시나 한두가지 빼먹어서 헤매는 분들이 계실까봐 나름 정리해서 올립니다. 특히 tensorflow, CUDA, CuDNN의 버전에 유의하세요..!


## **GPU 세팅 및 Jupyter notebook으로 GPU 사용 설정**
_(호환성 때문에 버전에 유의해서 깔았습니다..)_
#### **GPU 세팅**   
  설치 가이드 : https://www.tensorflow.org/install/gpu  

  1. CUDA-enabled GPU card 확인 - GTX 1050 이라 CUDA 가능  
  2. CUDA Toolkit, cuDNN 설치 (저는 CUDA 9.0, CuDNN 7.3 설치)  
  3. Visual Studio 설치 (저는 2017 설치)  

#### **tensorflow-GPU 설치**  
  설치 가이드 : https://www.tensorflow.org/install/pip

  1. 가상환경 생성 (아나콘다라 `conda create -n study pip python=3.6`로 만듦)  
  2. tensorflow gpu 설치 - 위 링크의 가이드를 따라하면 되는데, `pip install --upgrade tensorflow`은 안 했습니다. 최신 버전인 1.12로 깔리면 문제 생길까봐 일부러 1.11로 뒀어요.  
  3. 저기 가이드 맨 마지막에 있는 확인하는 코드를 실행해서 정상적으로 작동하는지 확인.  

#### **Jupyter notebook 에서 GPU 사용 가능하도록 설정**  

참고 : [Create a Jupyter Notebook Kernel for the TensorFlow Environment](https://www.pugetsystems.com/labs/hpc/The-Best-Way-to-Install-TensorFlow-with-GPU-Support-on-Windows-10-Without-Installing-CUDA-1187/#create-a-jupyter-notebook-kernel-for-the-tensorflow-environment)


  1. 가상환경 실행 (저 같은 경우는 `activate study`)  
  2. 가상환경 활성화 상태에서 `conda install ipykernel` 입력  
  3. 그리고 `python -m ipykernel install --user --name tf-gpu --display-name "TensorFlow-GPU"`  
  4. 이렇게 하고 나면 Jupyter notebook에서 새 노트북 만들 때 아래와 같이 TensorFlow-GPU 버튼이 생깁니다.  
    ![ipython-gpu]({{ site.url }}\assets\study_notes\studypie_1\2_1.PNG)


여기까지 다 되었으면 준비 완료...!

#### 추신 ####
이후 필요한 패키지는 가상환경 실행된 상태에서 pip install로 설치해서 쓰시면 됩니다..!
