## **DeepFaceLab** is a tool that utilizes deep learning to recognize and swap faces in pictures and videos.

Based on original FaceSwap repo. **Facesets** of FaceSwap or FakeApp are **not compatible** with this repo. You should to run extract again.

### **Features**:

- new models

- new architecture, easy to experiment with models

- works on 2GB old cards , such as GT730. Example of fake trained on 2GB gtx850m notebook in 18 hours https://www.youtube.com/watch?v=bprVuRxBA34

- face data embedded to png files

- automatic GPU manager, chooses best gpu(s) and supports --multi-gpu (only for identical cards). Warning: dont use cards in SLI mode.

- new preview window

- extractor in parallel

- converter in parallel

- added **--debug** option for all stages

- added **MTCNN extractor** which produce less jittered aligned face than DLIBCNN, but can produce more false faces. Comparison dlib (at left) vs mtcnn on hard case:
![](https://i.imgur.com/5qLiiOV.gif)
MTCNN produces less jitter.

- added **Manual extractor**. You can fix missed faces manually or do full manual extract:
![](https://github.com/iperov/DeepFaceLab/blob/master/doc/manual_extractor_0.jpg)
![Result](https://user-images.githubusercontent.com/8076202/38454756-0fa7a86c-3a7e-11e8-9065-182b4a8a7a43.gif)

- standalone zero dependencies ready to work prebuilt binary for all windows versions, see below

### <font color='00f'>**Model types**:</font>

- **H64 (2GB+)** - half face with 64 resolution. It is as original FakeApp or FaceSwap, but with new TensorFlow 1.8 DSSIM Loss func and separated mask decoder + better ConverterMasked. for 2GB and 3GB VRAM model works in reduced mode.

H64 Robert Downey Jr.:

![](https://github.com/iperov/DeepFaceLab/blob/master/doc/H64_Downey_0.jpg)

![](https://github.com/iperov/DeepFaceLab/blob/master/doc/H64_Downey_1.jpg)

- **H128 (3GB+)** - as H64, but in 128 resolution. Better face details. for 3GB and 4GB VRAM model works in reduced mode.

H128 Cage:

![](https://github.com/iperov/DeepFaceLab/blob/master/doc/H128_Cage_0.jpg)

H128 asian face on blurry target:

![](https://github.com/iperov/DeepFaceLab/blob/master/doc/H128_Asian_0.jpg)

![](https://github.com/iperov/DeepFaceLab/blob/master/doc/H128_Asian_1.jpg)

- **DF (5GB+)** - @dfaker model. As H128, but fullface model. Strongly recommended not to mix various light conditions in src faces.

![](https://github.com/iperov/DeepFaceLab/blob/master/doc/DF_Cage_0.jpg)

- **LIAEF128 (5GB+)** - new model. Result of combining DF, IAE, + experiments. Model tries to morph src face to dst, while keeping facial features of src face, but less agressive morphing. Model has problems with closed eyes recognizing.

LIAEF128 Cage:

![](https://github.com/iperov/DeepFaceLab/blob/master/doc/LIAEF128_Cage_0.jpg)

![](https://github.com/iperov/DeepFaceLab/blob/master/doc/LIAEF128_Cage_1.jpg)

LIAEF128 Cage video:

[![Watch the video](https://img.youtube.com/vi/mRsexePEVco/0.jpg)](https://www.youtube.com/watch?v=mRsexePEVco)

- **LIAEF128YAW (5GB+)** - currently testing. Useful when your src faceset has too many side faces vs dst faceset. It feeds NN by sorted samples by yaw.

- **MIAEF128 (5GB+)** - as LIAEF128, but also it tries to match brightness/color features.

MIAEF128 model diagramm:

![](https://github.com/iperov/DeepFaceLab/blob/master/doc/MIAEF128_diagramm.png)

MIAEF128 Ford success case:

![](https://github.com/iperov/DeepFaceLab/blob/master/doc/MIAEF128_Ford_0.jpg)

MIAEF128 Cage fail case:

![](https://github.com/iperov/DeepFaceLab/blob/master/doc/MIAEF128_Cage_fail.jpg)

- **AVATAR (4GB+)** - non GAN, 256x256 face controlling model. 

![](https://github.com/iperov/DeepFaceLab/blob/master/doc/AVATAR_Navalniy_0.jpg)

Video: 

[![](https://img.youtube.com/vi/3M0E4QnWMqA/0.jpg)](https://www.youtube.com/watch?v=3M0E4QnWMqA)

Usage:

src - controllable face (Cage)

dst - controller face (your face)

converter --input-dir must contains *extracted dst faces* in sequence to be converted, its mean you can train on for example 1500 dst faces, but use for example 100 faces for convert.

![](https://github.com/iperov/DeepFaceLab/blob/master/doc/DeepFaceLab_convertor_overview.png)

- Video comparison of different Cage facesets.
Vertical: 1 - mix of various Cage face shape and light conditions. 2,3,4 - without mix.
Horizontal: 1 - DF, 2 - LIAEF128.

[![](https://img.youtube.com/vi/C1nFgrmtm_o/0.jpg)](https://youtu.be/C1nFgrmtm_o)

Conclusion: **better not to mix and use only same shape faces with same light**

### **Sort tool**:

`hist` groups images by similar content

`hist-dissim` places most similar to each other images to end.

`hist-blur` sort by blur in groups of similar content

`brightness` 

`hue`

`face` and `face-dissim` currently useless

Best practice for gather src faceset:

1) delete first unsorted aligned groups of images what you can to delete. Dont touch target face mixed with others.
2) `blur` -> delete ~half of them
3) `hist` -> delete groups of similar and leave only target face
4) `hist-blur` -> delete blurred at end of groups of similar
5) `hist-dissim` -> leave only first **1000-1500 faces**, because number of src faces can affect result. For YAW feeder model skip this step.
6) `face-yaw` -> just for finalize faceset

Best practice for dst faces:

1) delete first unsorted aligned groups of images what you can to delete. Dont touch target face mixed with others.
2) `hist` -> delete groups of similar and leave only target face

### **Ready to work facesets**:

- Nicolas Cage 4 facesets (1 mix + 3 different)

- Steve Jobs

download from here: https://mega.nz/#F!y1ERHDaL!PPwg01PQZk0FhWLVo5_MaQ

### **Build info**

dlib==19.10.0 from pip compiled without CUDA. Therefore you have to compile DLIB manually.

Command line example for windows: `python setup.py install -G "Visual Studio 14 2015" --yes DLIB_USE_CUDA`

### Prebuilt windows app:

Windows 7,8,8.1,10 zero dependency (just install/update your GeForce Drivers) prebuilt DeepFaceLab can be downloaded from 
1) torrent https://rutracker.org/forum/viewtopic.php?p=75318742 (magnet link inside).
2) https://mega.nz/#F!b9MzCK4B!zEAG9txu7uaRUjXz9PtBqg

Video tutorial: https://www.youtube.com/watch?v=K98nTNjXkq8

### **Windows 10 memory problem:

Windows 10 consumes % of VRAM even if card unused for video output.

### **Problem of the year**:

algorithm of overlaying neural face onto video face located in ConverterMasked.py.
Can someone implement adaptive histogram matching to prevent glares when a dark eyes face merges onto a light eyes face ?
