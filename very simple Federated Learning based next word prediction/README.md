# Federated Learning with LSTM Text Prediction

This repository contains a simple implementation of federated learning for next-word prediction using LSTM networks in TensorFlow. The code simulates a federated learning setup where multiple user devices train local models on their private data and share only the model weights with a central server, ensuring data privacy.

## Key Components
- **UserDevice**: Represents an individual device. Each device:
  - Initializes with user-specific data.
  - Trains a local LSTM model on the user’s text data.
  - Shares model weights with the central server.
- **FederatedServer**: Aggregates model weights from all devices using the Federated Averaging (FedAvg) algorithm to create a global model, which is then sent back to the devices for further training.

## Features
- **LSTM-based Text Prediction**: Each device trains an LSTM model to predict the next word based on local user data.
- **Federated Learning Simulation**: Models are trained on each device, and only model weights are shared with the server to maintain data privacy.
- **Tokenization and Sequence Preparation**: Text data is tokenized and transformed into sequences for training the LSTM models.

## Running the Code
1. **Prepare User Data**: Place a text file (`user_data.txt`) with sentences, each on a new line, in the project directory. The code assumes that every 5 lines belong to a different user.
2. **Install Dependencies**: Ensure TensorFlow and NumPy are installed:
   ```bash
   pip install tensorflow numpy
   ```
3. **Execute the Script**:
   ```bash
   python script_name.py
   ```
   The simulation will perform multiple federated learning rounds, updating models on each device using the aggregated global model from the server.

## Sample Output
The script ends with a prediction test using a sample text prompt:
```plaintext
Test prediction for 'Hello how are':
Predicted next words: ['you', 'doing', ...]
```

This demonstration is ideal for understanding federated learning in natural language processing tasks and showcases distributed training with LSTM models for next-word prediction.