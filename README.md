# PredIctal: An Automated Seizure Prediction Algorithm
Predicting Seizure onset from multichannel EEG Data using a CNN trained on Time-Frequency Representations

## Data
Data was sourced from the American Epilepsy Society Seizure Prediction Challenge hosted on Kaggle, for more detailed information see [here](https://www.kaggle.com/competitions/seizure-prediction/overview).  In brief, the dataset consisted of many 10-minute multichannel intracranial EEG recordings from 2 differnt human patients and 5 different dogs.  Each 10-minute 'segment' was labeled 'preictal' if it preceded a seizure event or 'interictal' if it did not.

## PreProcessing and Manual Feature Extraction
1) Bandpass Filter 0.5Hz-50Hz
2) Morlet Wavelet convolution at band centers for Delta (0-4Hz), Theta (4-8Hz), Alpha (8-12Hz), Beta (12-30Hz), and Gamma (30-50Hz) bands
3) Normalize amplitude and phase data into the range (-1, 1) for each 10-minute segment
4) Apply anti-aliasing filters and downsample to 250Hz for computational efficiency
5) Split each 10-minute segment into 150 non-overlapping 4s subsegments

## Convolutional Neural Network Architecture
1) Convolutional Layer with ten 5x5 filters for the 5 frequency bands
2) ReLU Activation
3) Max Pooling Layer with 1x10 filter size
4) Fully Connected Linear Layer
5) Sigmoid Activation

## Training
- The model was trained for 20 epochs on random batches of 4s subsegments from a balanced dataset split by segments (eg. Patient 1 had 9 interictal and 9 preictal segments for training)
- Separate models were trained for each patient/subject since each has a different number of electrodes and potentially different seizure-like brain activity
- ADAM Optimizer
- Binary Cross Entropy Loss
- L2 Norm Weight Decay for Regularization

## Evaluation
- The model was tested on all 4s subsegments from an equal number of held out segments (50% holdout validation), eg. Patient 1 had 9 interictal and 9 preictal segments for evaluation
- Predictions for each subsegment in a segment were arithmetically averaged to obtain a segment prediction
- Accuracy, Precision, Recall, and F1 score were calculated for both subsegment-wise and segment-wise prediction to ensure class-balanced model performance
- Area under the Receiver Operator Characteristic curve (ROCAUC) for segment-wise prediction was used as the final model evaluation score

## Performance
- The model achieved an aggregate ROCAUC of 0.95, with an accuracy of 86%
- Model performance on each class was equivalent with aggregate f1 scores of 0.86 for both
![image](https://user-images.githubusercontent.com/34703136/192541933-3c79e568-49fe-4732-9585-975157afe841.png)


## Future Directions
- Try Alternative Strategies for imbalanced datasets (oversampling, overweight minority class, etc.)
- Expand Frequency Range to include high gamma (50-100Hz) and include notch filter for powerline
- Improve Frequency Resolution to have multiple Morlet Wavelets per frequency band
- Manually Extract other relevant features: ISPC, non-linear energy, entropy, etc.
- Increase Model complexity with more convolutional and FC layers
