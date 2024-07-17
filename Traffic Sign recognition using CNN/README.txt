
Traffic Sign Recognition Using PyTorch and CNN

Overview:
This project implements a traffic sign recognition system using Convolutional Neural Networks (CNN) with PyTorch. The system is trained to identify various traffic signs from images, making it a crucial component for autonomous driving systems.

Requirements:
To run this project, you will need to install the following libraries:
pip install torch torchvision torchsummary tensorboard imbalanced-learn plotly pandas matplotlib

Project Structure:
- labels.csv: Contains metadata on traffic sign labels.
- myData/: Directory containing subfolders for each traffic sign class with respective images.
- traffic-sign-recognition-using-pytorch-and-cnn-original-.ipynb: Jupyter notebook with the complete implementation.

Usage:
1. Clone the Repository:
   git clone https://github.com/yourusername/your-repo-name.git
   cd your-repo-name

2. Install Dependencies:
   pip install -r requirements.txt

3. Run the Jupyter Notebook:
   Open traffic-sign-recognition-using-pytorch-and-cnn-original-.ipynb in Jupyter Notebook or Jupyter Lab and execute the cells step-by-step.

Implementation Details:

Import Libraries and APIs:
import torch
import gc, os, cv2, PIL
import torchvision as tv
import torch.nn as nn
import torchsummary as ts
import numpy as np
import pandas as pd
import plotly.express as px
import matplotlib.pyplot as plt
from imblearn.over_sampling import RandomOverSampler
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score, classification_report

Check CUDA Availability:
cuda_available = torch.cuda.is_available()
print(f"CUDA available: {cuda_available}")

Load and Prepare Data:
labels_df = pd.read_csv('path_to_labels.csv')
x, y = [], []
data_dir = 'path_to_myData'
for folder in range(43):
    folder_path = os.path.join(data_dir, str(folder))
    for i, img in enumerate(os.listdir(folder_path)):
        img_path = os.path.join(folder_path, img)
        img_tensor = tv.transforms.ToTensor()(PIL.Image.open(img_path))
        x.append(img_tensor.tolist())
        y.append(folder)
    print(f'Folder of label {folder} images loaded. Number of samples: {i + 1}')
x = np.array(x)
y = np.array(y)

Model Architecture:
The CNN model architecture used in this project is designed to effectively recognize traffic signs from images. The detailed architecture and layer shapes are as follows:

class TrafficSignNet(nn.Module):
    def __init__(self):
        super(TrafficSignNet, self).__init__()
        self.conv1 = nn.Conv2d(3, 32, kernel_size=3)
        self.conv2 = nn.Conv2d(32, 64, kernel_size=3)
        self.conv3 = nn.Conv2d(64, 128, kernel_size=3)
        self.fc1 = nn.Linear(128 * 6 * 6, 512)
        self.fc2 = nn.Linear(512, 128)
        self.fc3 = nn.Linear(128, 43)
        self.dropout = nn.Dropout(0.5)
    
    def forward(self, x):
        x = F.relu(F.max_pool2d(self.conv1(x), 2))
        x = F.relu(F.max_pool2d(self.conv2(x), 2))
        x = F.relu(F.max_pool2d(self.conv3(x), 2))
        x = x.view(-1, 128 * 6 * 6)
        x = F.relu(self.fc1(x))
        x = self.dropout(x)
        x = F.relu(self.fc2(x))
        x = self.fc3(x)
        return x

Training and Evaluation:
Training and evaluating the model involves defining the loss function, optimizer, and running the training loop. The notebook includes detailed steps and code to perform these tasks.

Results:
The trained model is evaluated on a test dataset, and the results include accuracy and a classification report.

Contributing:
Contributions are welcome! Please open an issue or submit a pull request for any improvements or bug fixes.

License:
This project is licensed under the MIT License.
