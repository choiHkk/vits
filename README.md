## Introduction
1. vits 오픈 소스와 한국어 데이터셋(KSS)을 사용해 빠르게 학습합니다.
2. KSS 데이터셋은 기본적으로 44kHz인 점을 감안하여 22kHz로 resampling할 수 있도록 utils.load_wav_to_torch()를 수정했습니다.
3. data_utils.py 내부 get_audio method에는 spectrogram을 생성하고 저장한 뒤 다시 읽어오게끔 하는 부분이 있지만 해당 부분을 주석처리했습니다.
4. data_utils.py 내부 get_text method를 kss 스크립트에 맞추어 변경했습니다.
5. utils.py 내부 load_filepaths_and_text func. 를 data_utils.py 입력 형식에 맞게끔 수정했습니다.
6. stft_loss.py 를 추가하고 models.py SynthesizerTrn 부분 speaker embedding if문 일부를 수정했습니다.
7. conda 환경으로 진행해도 무방하지만 본 레포지토리에서는 docker 환경만 제공합니다. 기본적으로 ubuntu에 docker, nvidia-docker가 설치되었다고 가정합니다.
8. GPU, CUDA 종류에 따라 Dockerfile 상단 torch image 수정이 필요할 수도 있습니다.
9. 오픈소스에서 제공하는 영어 문장은 국제음성기호(ipa)를 사용하지만 본 레포지토리에서는 일반적인 한국어 전처리 기법을 사용합니다.
10. single speaker 학습에 해당하는 train.py를 즉시 실행할 경우 에러가 발생합니다. 따라서 본 환경에서 실행할 경우 train_ms.py를 사용합니다.
11. 별도의 pre-processing 과정은 필요하지 않습니다.


## Dataset
1. download dataset - https://www.kaggle.com/datasets/bryanpark/korean-single-speaker-speech-dataset
2. `unzip /path/to/the/kss.zip -d /path/to/the/kss`
3. `mkdir /path/to/the/vits/data/dataset`
4. `mv /path/to/the/kss.zip /path/to/the/vits/data/dataset`

## Docker build
1. `cd /path/to/the/vits`
2. `docker build --tag vits:latest .`

## Training
1. `nvidia-docker run -it --name 'vits' -v /path/to/vits:/home/work/vits --ipc=host --privileged vits:latest`
2. `cd /home/work/vits/monotonic_align`
3. `python setup.py build_ext --inplace`
4. `cd /home/work/vits`
5. `ln -s /path/to/the/vits/data/dataset/kss`
6. `python train_ms.py -c ./config/kss_base.json -m kss_v1`
7. arguments
  * -c : comfig path
  * -m : model output directory
8. (OPTIONAL) `tensorboard --logdir=outdir/logdir`
