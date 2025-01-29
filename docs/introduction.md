# How to design, implement, test and deploy an image classifier



A machine learning system has three highly related main components: data, software and hardware. 
We want to develop and deploy a neural network for classifying images, assigning a discrete label (from a set of N labels) to a given image.
A neural network is a function 
Consider the following details:
1. Data: a set of properly labelled images, divided in three subsets (training, validation, and testing)
2. Software: at least three functions (train, test, predict) 
3. Hardware:
      how to capture images
      how to train and test
      how to predict
    


/dataset/train
          ├── label_1
          |    ├── 001.jpg
          |    └── 002.jpg
          └── label_2
               ├── 003.jpg
               └── 004.jpg
               
/dataset/validation
          ├── label_1
          |    ├── 005.jpg
          |    └── 006.jpg
          └── label_2
               ├── 007.jpg
               └── 008.jpg
               
/dataset/validation
          ├── label_1
          |    ├── 009.jpg
          |    └── 010.jpg
          └── label_2
               ├── 011.jpg
               └── 012.jpg
