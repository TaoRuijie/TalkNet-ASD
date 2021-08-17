## 1. General Question

### 1.1 Which dataset is used for training and testing ?
'pretrain_AVA.model' is trained on the AVA training set and evaluate on the AVA val/test set, (Has the entire code)
'pretrain_TalkSet.model' is trained on our TalkSet and evaluate on the Columbia ASD set or other raw videos.

### 1.2 How to figure the variable length of data during training ?
We design a scheme to feed the variable-length data into the same mini-batch: we sort all videos by their length and put the videos with similar length into the same batch. We crop all videos into the minimum number of frames in this batch. In this way, we train the TalkNet with videos of different length without losing too much data. 

### 1.3 How to figure multiple faces on the screen ?
In the ASD task, when there are multiple face tracks in the video, we consider one track at a time. The face track of interest is given in each test trial. You can also consider the relationship between the faces on the screen at the same time. There are some papers about that.
### 1.4 Error: RuntimeError: CUDA error: no kernel image is available for execution on the device
Do "pip install --pre torch torchvision -f https://download.pytorch.org/whl/nightly/cu111/torch_nightly.html -U", check this [page](https://github.com/pytorch/pytorch/issues/31285#issuecomment-739139454).

### 1.5 Can not download csv, video data or pretrain model ?
I use google drive to upload the pretrain model and csv files. So you need to make sure you can use google drive under your internet. The error during extract video clips can be ignored.

***

## 2. TalkNet in AVA-Activespeaker dataset

### 2.1 Can not reimplement the result ?
In our experiments, for the result in AVA validation set, for the same code/model, the best training result is 92.6mAP, the worst one is 92.1mAP. So it is reasonable if you get the result little different than this 92.3mAP. Also batchsize might effect the result (not too much).

### 2.2 How to get the result in AVA test set ?
AVA test set did not release the labels. So you need to upload your csv result in their system. Notice that we delete add the first line in the `test_res.csv` file since we modify a bit for the `get_ava_active_speaker_performance.py`. You need to delete the first line when you upload it. For the upload file, you need to set all `label` as `SPEAKING_AUDIBLE`.

### 2.3 What are the labels ? Where is SPEAKING_BUT_NOT_AUDIBLE ？
There are three labels: SPEAKING_AND_AUDIBLE, SPEAKING_BUT_NOT_AUDIBLE, NOT_SPEAKING, but in the finally evaluation, SPEAKING_BUT_NOT_AUDIBLE and NOT_SPEAKING share the same label. So this is a binary classification issue. 

### 2.4 How big your model ? How long for training?
Our model has 15.01M params, in one 22G GPU, each epoch we train 15 mins, evaluate in val set takes 5 mins. Train 25 epochs can get the best result. So at most 7 hours.

***

## 3. TalkNet in TalkSet and Columbia ASD dataset

### 3.1 Why you build TalkSet instead of only use AVA dataset ?
Because we want our model can be used for all videos in the wild. AVA dataset has already provide the face bounding box for each trial, so for the videos not in AVA. If you want to do ASD, you need to do face detection first. In our experiments, the face detection method used in AVA is hard to reimplement. Which means we can hardly get the face area that similar to the detected face in AVA. Due to that, the model trained in AVA can not perform well in videos outside AVA if we use other face detection method.
Due to that, we build TalkSet, the face in TalkSet is all detected by S3FD. So for any raw video (Such as the videos in Col ASD dataset), we can use S3FD to do face detection first, then apply our TalkNet model to get the ASD label.

### 3.2 TalkSet code can not work?
We did not verify this code. Because we just modify LRS3 and VoxCeleb2 to build this set, so we do not (or cannot) upload this set. This `generate_TalkSet.py` is what we used to generate this dataset, and we did not check it later. So it just used for your reference. We have already provide the data list, so you can generate this dataset based on it.

***

## 4. An ASD Demo with pretrained TalkNet model

### 4.1 I try the demo, the performance is not so good ?
You can check the demo video `001.mp4` first and compare your output and our result `001_res.mp4` to make sure what you did is correct. So if you are not statisfied with the performance. We are sorry about that (~cry). We think this model can further improve. For the very short clips (less than 1s), small face and side face, the performance is not so good. 

### 4.2 I try the demo, the face can not be detected ?
That is the reason for the face detection model instead of the ASD part. You can use better face detecion model such as [Insightface](https://github.com/deepinsight/insightface
). Only when the face can be detected, ASD model can perform to get the ASD labels.
