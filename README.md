## DIHARD-2019-baseline-alltracks

Diarization system used in all the tracks is [_JHU's Kaldi_](https://github.com/kaldi-asr/kaldi/tree/master/egs/dihard_2018/v2) x-vector based implementation, which is closely based on the JHU's DIHARD 2018 submission, as explained in the paper ['_Diarization is Hard: Some Experiences and Lessons Learned for the JHU Team in the Inaugural DIHARD Challenge_'](http://www.danielpovey.com/files/2018_interspeech_dihard.pdf).


Download the DIHARD development and evaluation data.

#### Prerequisites

**0.** Python (no version requirements)

**1.** Kaldi (the basepath where kaldi is cloned will be refered as  \<k\> )
```
git clone https://github.com/kaldi-asr/kaldi.git 
cd kaldi
cd tools; make;
cd ../src; ./configure; make
```
Knowledge in Kaldi will be beneficial. Links below might be of some help

- [Kaldi tutorial](http://kaldi-asr.org/doc/tutorial.html "http://kaldi-asr.org/doc/tutorial.html")
- [Kaldi for Dummies tutorial](http://kaldi-asr.org/doc/kaldi_for_dummies.html "http://kaldi-asr.org/doc/kaldi_for_dummies.html")

 **2.** Webrtc (only for track 2)

https://github.com/wiseman/py-webrtcvad (download from the above page or the following command should work) 
```
pip install webrtcvad
```

**3.** Optional Denoising 

Requires cntk installation. 
(https://github.com/mmmaat/denoising_DIHARD18)


#### Instructions for all tracks:
**1.** Move to a directory to clone this baseline repository (called \<mod\> here after) and execute the following commands.
```
git clone https://github.com/iiscleap/DIHARD_2019_baseline_alltracks.git
```

```
cd <mod>/DIHARD_2019_baseline_alltracks
cp alltracksrun.sh <k>/kaldi/egs/dihard_2018/v2
cp {make_dihard_2019_dev_eval_alltracks.py,make_dihard_2019_dev_eval_alltracks.sh} <k>/kaldi/egs/dihard_2018/v2/local     
mkdir -p <k>/kaldi/egs/dihard_2018/v2/exp/xvector_nnet_1a
cp {final.raw,max_chunk_size,min_chunk_size,extract.config} <k>/kaldi/egs/dihard_2018/v2/exp/xvector_nnet_1a
cp md_eval.pl <k>/kaldi/egs/dihard_2018/v2
```

**Note.** \<dev\> and \<eval\> will refer to DIHARD 2019 single channel development and evaluation datasets respectively. 

#### Track 1 instructions :

**1.**  Data preparation of DIHARD 2019 dev and eval for Track 1.
```
cd <k>/kaldi/egs/dihard_2018/v2/
local/make_dihard_2019_dev_eval_alltracks.sh --devoreval dev --tracknum 1 <path of DIHARD 2019 dev> data/dihard_dev_2019_track1
local/make_dihard_2019_dev_eval_alltracks.sh --devoreval eval --tracknum 1 <path of DIHARD 2019 eval> data/dihard_eval_2019_track1
```

**2.** Execute the alltracks.sh file (example for running track 1) .. Requires track number and plda path of plda_track1 file: 
```
bash alltracksrun.sh --tracknum 1 --plda_path <mod>/DIHARD_2019_baseline_alltracks/plda_track1
```

##### Running the above command generates rttm file for dev and eval in \<k\>/kaldi/egs/dihard_2018/v2/exp/xvector_nnet_1a/xvectors_dihard_{dev/eval}_2019_track1/plda_scores/rttm
 The script will also display DER on dev.

##### Baseline results for DIHARD_DEV_2019 Track1 is in \<mod\>/DIHARD_2019_baseline_alltracks/performance_metrics_dev_track1.txt

#### Track 2 instructions :

**Note**: webrtc expects .wav files but DIHARD 2019 dataset has .flac files. Convert .flac files to .wav files.

**1.** Run the \<mod\>/DIHARD_2019_baseline_alltracks/flac_to_wav_usingsox.sh (this file uses [sox](http://sox.sourceforge.net/) command for the conversion) as follows : 

```
cd <mod>/DIHARD_2019_baseline_alltracks
bash flac_to_wav_usingsox.sh dihard_2019_dev.list <dev>/flac <dev>/wav
bash flac_to_wav_usingsox.sh dihard_2019_eval.list <eval>/flac <eval>/wav 
```
**2.** Execute the run_vad.sh in \<mod\>/DIHARD_2019_baseline_alltracks to create SAD files for DIHARD 2019 dev and eval single channel datasets. 
```
cd <mod>/DIHARD_2019_baseline_alltracks
bash run_vad.sh <dev>/wav
bash run_vad.sh <eval>/wav  
```
**3.** Copy all such .sad files into a folder named sad_webrtc in <dev/eval>
```
mkdir <dev>/sad_webrtc
cp <dev>/wav/*.sad <dev>/sad_webrtc
mkdir <eval>/sad_webrtc
cp <eval>/wav/*.sad <eval>/sad_webrtc
```
**4.** Data preparation of DIHARD 2019 dev and eval for Track 2.
```
cd <k>/kaldi/egs/dihard_2018/v2/
local/make_dihard_2019_dev_eval_alltracks.sh --devoreval dev --tracknum 2 <dev> data/dihard_dev_2019_track2
local/make_dihard_2019_dev_eval_alltracks.sh --devoreval eval --tracknum 2 <eval> dihard_eval_2019_track2
```
**5.** Execute the alltracks.sh file as shown below (requires track number option and plda path of plda_track2 file ) :  
```
bash alltracksrun.sh --tracknum 2 --plda_path <mod>/DIHARD_2019_baseline_alltracks/plda_track2
```

##### Running the above command generates rttm file for dev and eval in \<k\>/kaldi/egs/dihard_2018/v2/exp/xvector_nnet_1a/xvectors_dihard_{dev/eval}_2019_track2/plda_scores/rttm
The script will also display DER on dev.

##### Baseline results for DIHARD_DEV_2019 Track2 is in \<mod\>/DIHARD_2019_baseline_alltracks/performance_metrics_dev_track2.txt
  
-------------------------------------------------

**Note :** Filewise performance metrics of DER, Jaccard Error Rate(JER), Mutual Information (MI) ... computed using the scoring script in [dscore](https://github.com/nryant/dscore "https://github.com/nryant/dscore")

**Note :** The readme of this repository uses DIHARD 2019 dataset as an example, but the scripts here will work on any dataset, provided the dataset structure is maintained as shown above and the dataset's list files are present in <mod>.
All you need is the that the dataset directory path passed to the data preparation files expects the contents within the directory to be structured as the example shown below
```
<path of dataset passed>
|-- flac
|   |-- DH_0001.flac
|   |-- DH_0002.flac
|   |-- DH_0003.flac
|-- sad
|   |-- DH_0001.lab
|   |-- DH_0002.lab
|   |-- DH_0003.lab
|-- rttm
|   |-- DH_0001.rttm
|   |-- DH_0002.rttm
|   |-- DH_0003.rttm
```
