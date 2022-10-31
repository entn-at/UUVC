# UUVC
**This is currently incomplete, will add checkpoints and demo, testing cleaned-up codes.**

Official implementation for the paper [A Unified One-Shot Prosody and Speaker Conversion System with Self-Supervised Discrete Speech Units](...).
Submitted to ICASSP 2023.
**Audio samples/demo for our system can be accessed [here](https://b04901014.github.io/UUVC/)**

## Setup environment
1. [pytorch](https://pytorch.org)
2. [pytorch-lightning](https://pytorch-lightning.readthedocs.io/en/stable/)
3. [huggingface](https://huggingface.co/docs/transformers/index)
4. [textless-lib](https://github.com/facebookresearch/textlesslib)

See `setup.sh` for details of package installation (especially if you have problem on installing textless-lib).
I used 1.12.1 for pytorch and 1.7.7 for pytorch lightning. No guarantee the compatability of other versions.

## Dataset preprocessing
Here we show an example of preprocessing VCTK, which you can adjust to your own dataset.
1. Get VCTK from [here](https://datashare.ed.ac.uk/handle/10283/3443)
2. Run the below commands to preprocess speech units, pitch, 22k melspectrogram, energy, 16k-resampled speech:
```
mkdir -p features/VCTK
python s2u.py --VCTK --datadir VCTK_DIR --outdir features/VCTK --with_pitch_unit
```
3. Run the below command to generate training and validation splits, you can adjust the script to your own dataset:
```
python make_data_vctk.py
```

## Training
We provide the below command as an example, change the arguments according to your dataset:
```
mkdir ckpt
python train.py --saving_path ckpt/ \
                --training_step 75000 \
                --batch_size 200 \
                --check_val_every_n_epoch 5 \
                --traintxt datasets/train_vctk.txt \
                --validtxt datasets/valid_vctk.txt \
                [--distributed]
```
 - `--distributed` if you ar e training with multiple GPUs
 - `--check_val_every_n_epoch`: Eval every n epoch
 - `--training_step`: Total training step (generator + discriminator)

## Inference
We provide examples for synthesis of the system in `inference.py`, you can adjust this script to your own usage.
Example to run `inference.py`:
```
python inference.py --result_dir samples --ckpt CKPT_PATH --config CONFIG_PATH --metapath META_PATH
```
 - `--ckpt`: .ckpt file that is generated during training, or from the pretrained checkpoints
 - `--config`: .json file that is generated at the start of the training, or from the pretrained checkpoints
 - `--result_dir`: Your desired output directory for the samples, will create subdirectory for different conversions
 - `--metapath`: The txt file contains the source and target speech paths, see `eval.txt` for example.
The filenames will be `{source_wav_name}--{target_wav_name}.wav`

## Pretrained checkpoints
We provide checkpoints pretrained sperately on VCTK and (LibriTTS-360h + VCTK + ESD). The model is a little bit large since it contains all the training and optimizer states.
 - [VCTK](...)
 - [LibriTTS-360h + VCTK + ESD](...)