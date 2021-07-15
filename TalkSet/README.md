### TalkSet Generation

You can check the 'train.txt' and 'test.txt' to generate TalkSet by your own.

This `generate_TalkSet.py` code is just used for your reference, I did not check it recently.

Input the LRS3, VoxCeleb2, 3 list files in `lists_in`
Output TalkSet, train.txt, test.txt (Here the test set is the validation set actually)

### Usage:

Set the following parser based on the location of your data:

`out_path`: the output TalkSet location
`Vox_audio`: Location of the Vox2, training set, audio location
`Vox_video`: Location of the Vox2, training set, video location
`lrs3_audio`: Location of the LRS3, audio location
`lrs3_video`: Location of the LRS3, video location
`task`: The part of the TalkSet you want to generate, eg: TAudio
`num_cpu`: The num of the threads, higher will be faster, based on your PC performance, eg: 10

```
python TalkSet/generate_TalkSet.py --task 'TAudio'
python TalkSet/generate_TalkSet.py --task 'FAudio'
python TalkSet/generate_TalkSet.py --task 'TFAudio'
python TalkSet/generate_TalkSet.py --task 'TSilence'
python TalkSet/generate_TalkSet.py --task 'FSilence'
python TalkSet/generate_TalkSet.py --task 'Fusion'
```

### Description:
For `lists_out\*.txt` files: 
- The 1st row is the face clips data type, 
	- TAudio: audio is active, lip is moving, audio and lip are sync
	- FAudio: audio is active, lip is moving, audio and lip are not sync (Speech from others)
	- TFAudio: one part is 'TAudio', the other part is 'FAudio'
	- TSilence: one part is 'TAudio', in the other part, audio is non-active, lip is not moving
	- FSilence: one part is 'silence'(audio is non-active, lip is not moving), in the other part, audio is active, lip is not moving (Speech from others)
- The 2nd row is the path for the audio file (filename started from 'silence' is the data from LRS3, filename started from 'id.....' is the data from VoxCeleb2)
- The 3rd row is the path for the video file
- The 4th row is the length(seconds) of this data 
- The 5th row is the start of 'active' clip (in FSilence, it presents the 'silence' part)
- The 6th row is the end of 'active' clip
- The 7th row is the start of 'non-active' clip (in FSilence, it presents the 'speech from others' part)
- The 8th row is the end of 'non-active' clip
- The 9th row is the file ID

The dataset generated will not be fixed each time because we randomly select FSlience data, and the change point is the random number. We believe the result will be similar. The whole time to generate the TalkSet will use about 3 to 6 hours in our experiments.