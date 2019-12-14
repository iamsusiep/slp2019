# Film Quality Prediction using Acoustic, Prosodic and Lexical Cues

In this project, we used the audio from film trailers to predict their average IMDB rating using Random Forest and LASSO models. 

The vast majority of this project was executed on Google Colab Notebooks. Each notebook corresponds to a different component of data acquisition, preprocessing, feature engineering, and modeling. Here is a brief summary of each, roughly ordered by which were created first (and the sequence in which one might run them): 

#### Data Acquisition 
* `get_youtube_data.ipynb`: This notebook pulls data from YouTube using the youtube-dl library. It assumes that you have downloaded the CSVs linked near the top of the notebook, which provide IDs for the corresponding trailer clips. 
* `remove_music.ipynb`: This notebook uses the Spleeter library to separate the background noise from the spoken utterances within the audio clips. 
* `transcription.ipynb`: This notebook uses Google Speech-to-Text API. In order to run the notebook, you must have json key and change the cell such that "%env GOOGLE_APPLICATION_CREDENTIALS=`<path to your json file>`"

#### Data Preprocessing and Feature Engineering 
* `prosodic_embeddings.ipynb`: This notebook trains a Tacotron + prosodic embeddings model. 
* `preprocess_utterances.ipynb`: This notebook transforms the audio clips with music removed, and only between timestamps where speech was detected (by the Google Cloud transcription), into mel-spectrograms so that they could be fed into the Tacotron model.
* `prosody_inference.ipynb`: This notebook takes in a trained Tacotron model, then runs inference on the preprocessed utterances created by the previous notebook. This yields a set of embeddings for each utterance for each trailer.
* `Praat_Extraction.ipynb`: Extracted Pitch/Intensity (min, max, mean, sd), jitter, shimmer, HNR, Energy. Contains code for segmenting speech to IPU. 
* `aeneas.ipynb`: Used Aeneas for forced alignment of transcript to audio. 
https://github.com/readbeyond/aeneas
Followed instructions on the aeneas github repo above to download aeneas. Refer to `aeneas.ipynb` in this repo to process aeneas for our project.
* sox: We used it for extracting speech segments. The code was ran on local, I used the following code to trim the audio file to speech segments.

`brew install sox`

`dir_name` is directory in which you uncompress all the `output_example*.zip`


```python
import pandas as pd
import os

for file_path in glob('dir_name/*/OEBPS/Resources/*.csv'):
    df = pd.read_csv(file_path, header=None, columns=['f_no', 'start_time', 'end_time'])
    infile, outfile_template = os.path.basename(file_path), os.path.basename(file_path).replace('.wav', '_{}.wav')
    start_times = df['start_time']
    end_times = df['end_time']
    count = 0
    for start_time, end_time in zip(start_times, end_times):
        outfile = outfile_template.format(count)
        !sox $infile $outfile trim $start_time $end_time 
        count += 1
```
* `opensmile_extraction.ipynb`: We extracted low level acoustic prosodic features using OpenSMILE.https://www.audeering.com/opensmile/

Setup: 

`brew install audioconf automake libtool m4 gcc`

`tar -zxvf opensmile-2.3.0.tar.gz`

`cd opensmile-2.3.0`

`bash autogen.sh`  (repeat this over 2 times)

`./configure` 

`make -j4 ; make`

`make install`

* ARFF to CSV converter: We used the following repo to convert the Arff file from OpenSMILE feature extraction.
https://github.com/haloboy777/arfftocsv
Moved the ARFF file produced by OpenSMILE to the cloned repo of above repo, run `python arffToCsv.py`

#### Modeling
* `ML_notebook_RF.ipynb`: This notebook first merges all the features in our dataset, then trains a Random Forest model to predict IMDB average rating. 
* `ML_notebook.ipynb`: This notebook first merges all the features in our dataset, then trains a LASSO model to predict IMDB average rating. 




## Other Links
### Slides:
https://docs.google.com/presentation/d/1b9r6CmesxDIpdsGlOkdc4n3NjGh6y2bmzJg2_dmdO0E/edit?usp=sharing

### Speech Files: 
https://drive.google.com/open?id=1nN_dvH8bVCJ3aAN70zT0MlOz2pZmwDuE

Contains Audio files that after trimming to speech segments with sox. Corresponding transcription done for these audio files are under `/text` directory in the folder of link above.
### Paper: 
https://www.overleaf.com/project/5ddde5bd69f06f00014d5e26

### Folder Containing Features Extracted: https://drive.google.com/drive/folders/1lsKFIMiyJEkInHC0fZiYB6BtCSGWMAdE?usp=sharing      


