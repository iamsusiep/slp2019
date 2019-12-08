# Film Quality Prediction using Acoustic, Prosodic and Lexical Cues



Aeneas: https://github.com/readbeyond/aeneas

sox: `brew install sox`

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

Speech: https://drive.google.com/open?id=1nN_dvH8bVCJ3aAN70zT0MlOz2pZmwDuE

Contains Audio files that after trimming to speech segments with sox. Corresponding transcription done for 

OpenSMILE: https://www.audeering.com/opensmile/, https://siminyang6.wordpress.com/2018/08/05/install-opensmile-on-mac/

ARFF to CSV converter: https://github.com/haloboy777/arfftocsv

Slides: https://docs.google.com/presentation/d/1b9r6CmesxDIpdsGlOkdc4n3NjGh6y2bmzJg2_dmdO0E/edit?usp=sharing

Paper: https://www.overleaf.com/project/5ddde5bd69f06f00014d5e26

Folder Containing Features Extracted: https://drive.google.com/drive/folders/1lsKFIMiyJEkInHC0fZiYB6BtCSGWMAdE?usp=sharing      
