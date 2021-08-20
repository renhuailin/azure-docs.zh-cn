---
title: 俄语开放语音转文本
titleSuffix: Azure Open Datasets
description: 了解如何使用 Azure 开放数据集中的俄语开放语音转文本数据集。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 4c0ec200fa3506dd0b251903954240eff5132dd4
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2021
ms.locfileid: "112982503"
---
# <a name="russian-open-speech-to-text"></a>俄语开放语音转文本

来源于各种音频源的语音示例集合。 数据集包含简短的俄语音频剪辑。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

此俄语语音转文本 (STT) 数据集包括：
- 大约 1,600 万条言语
- 大约 20,000 小时
- 2.3 TB（int16 类型的 .wav 非压缩格式），356G opus 格式的数据
- 除验证数据集以外的所有文件均已转换成 opus

数据集的主要目的是训练“语音转文本”模型。

## <a name="dataset-composition"></a>数据集组合

数据集大小是为 .wav 文件指定的。

| 数据集 | 言语 | 小时 | GB | 秒/字符 | COMMENT | 批注 | 质量/噪音 |
|-|-|-|-|-|-|-|-|
| 无线电_v4 (*) | 7,603,192 | 10,430 | 1,195 | 5 秒 / 68 | 单选 | 对齐 | 95% / 清晰 |
| public_speech (*) | 1,700,060 | 2,709 | 301 | 6 秒 / 79 | 公共演讲 | 对齐 | 95% / 清晰 |
| 有声读物_2 | 1,149,404 | 1,511 | 162 | 5 秒 / 56 | 书籍 | 对齐 | 95% / 清晰 |
| radio_2 | 651,645 | 1,439 | 154 | 8 秒 / 110 | 单选 | 对齐 | 95% / 清晰 |
| public_youtube1120 | 1,410,979 | 1,104 | 237 | 3 秒 / 34 | Youtube | 对白字幕 | 95% / ~清晰 |
| public_youtube700 | 759,483 | 701 | 75 | 3 秒 / 43 | Youtube | 对白字幕 | 95% / ~清晰 |
| tts_russian_addresses | 1,741,838 | 754 | 81 | 2 秒 / 20 | 地址 | TTS 4 语音 | 100% / 清晰 |
| asr_public_phone_calls_2 | 603,797 | 601 | 66 | 4 秒 / 37 | 电话呼叫 | ASR | 70% / 嘈杂 |
| public_youtube1120_hq | 369,245 | 291 | 31 | 3 秒 / 37 | YouTube HQ | 对白字幕 | 95% / ~清晰 |
| asr_public_phone_calls_1 | 233,868 | 211 | 23 | 3 秒 / 29 | 电话呼叫 | ASR | 70% / 嘈杂 |
| radio_v4_add (*) | 92,679 | 157 | 18 | 6 秒 / 80 | 单选 | 对齐 | 95% / 清晰 |
| asr_public_stories_2 | 78,186 | 78 | 9 | 4 秒 / 43 | 书籍 | ASR | 80% / 清晰 |
| asr_public_stories_1 | 46,142 | 38 | 4 | 3 秒 / 30 | 书籍 | ASR | 80% / 清晰 |
| public_series_1 | 20,243 | 17 | 2 | 3 秒 / 38 | Youtube | 对白字幕 | 95% / ~清晰 |
| asr_calls_2_val | 12,950 | 7,7 | 2 | 2 秒 / 34 | 电话呼叫 | 手动批注 | 99% / 清晰 |
| public_lecture_1 | 6,803 | 6 | 1 | 3 秒 / 47 | 讲座 | 对白字幕 | 95% / 清晰 |
| buriy_audiobooks_2_val | 7,850 | 4,9 | 1 | 2 秒 / 31 | 书籍 | 手动批注 | 99% / 清晰 |
| public_youtube700_val | 7,311 | 4,5 | 1 | 2 秒 / 35 | Youtube | 手动批注 | 99% / 清晰 |

(*) 只有一个数据示例是采用 txt 文件提供的。

## <a name="annotation-methodology"></a>批注方法

数据集是使用开放源代码编译的。 使用语音活动检测和比对将长序列分割为音频块。 某些音频类型已自动批注，并通过启发式方法以统计方式进行验证。

## <a name="data-volumes-and-update-frequency"></a>数据量和更新频率

数据集的总大小为 350 GB。 带有公共共享标签的数据集的总大小为 130 GB。

数据集本身不太可能更新为后向兼容。 请遵循原始存储库来建立基准并排除文件。

将来可能会添加新的域和语言。

## <a name="audio-normalization"></a>音频规范化

所有文件已规范化，以便更快捷地实现运行时增强。 处理过程如下：

- 必要时转换成 mono；
- 必要时转换为 16-kHz 采样率；
- 存储为 16 位整数；
- 转换为 OPUS；

## <a name="on-disk-db-methodology"></a>磁盘上 DB 方法

每个音频文件（wav、二进制）都经过了哈希处理。 哈希用于创建文件夹层次结构以实现更优化的 fs 操作。

```python
target_format = 'wav'
wavb = wav.tobytes()

f_hash = hashlib.sha1(wavb).hexdigest()

store_path = Path(root_folder,
                  f_hash[0],
                  f_hash[1:3],
                  f_hash[3:15] + '.' + target_format)
```

## <a name="downloads"></a>下载

数据集以两种形式提供：

- 通过 Azure Blob 存储和/或直接链接提供存档；
- 通过 Azure Blob 存储提供原始文件；所有内容都存储在 https://azureopendatastorage.blob.core.windows.net/openstt/ 中

文件夹结构：

```
└── ru_open_stt_opus                                            <= archived folders
│   │
│   ├── archives
│   │    ├── asr_calls_2_val.tar.gz                             <= tar.gz archives with opus and wav files
│   │    │   ...                                                <= see the below table for enumeration
│   │    └── tts_russian_addresses_rhvoice_4voices.tar.gz
│   │
│   └── manifests
│        ├── asr_calls_2_val.csv                                <= csv files with wav_path, text_path, duration (see notebooks)
│        │   ...
│        └── tts_russian_addresses_rhvoice_4voices.csv
│
└── ru_open_stt_opus_unpacked                                   <= a separate folder for each uploaded domain
    ├── public_youtube1120
    │    ├── 0                                                  <= see "On disk DB methodology" for details
    │    ├── 1
    │    │   ├── 00
    │    │   │  ...
    │    │   └── ff
    │    │        ├── *.opus                                   <= actual files
    │    │        └── *.txt
    │    │   ...
    │    └── f
    │
    ├── public_youtube1120_hq
    ├── public_youtube700_val
    ├── asr_calls_2_val
    ├── radio_2
    ├── private_buriy_audiobooks_2
    ├── asr_public_phone_calls_2
    ├── asr_public_stories_2
    ├── asr_public_stories_1
    ├── public_lecture_1
    ├── asr_public_phone_calls_1
    ├── public_series_1
    └── public_youtube700
```

| 数据集 | GB，WAV | GB，存档 | 存档 | 源 | 清单 |
|-|-|-|-|-|-|
| 定型 |  |  |  |  |  |
| 无线电和公共演讲示例 | - | 11.4 | opus+txt | - | manifest |
| 有声读物_2 | 162 | 25.8 | opus+txt | Internet + 比对 | manifest |
| radio_2 | 154 | 24.6 | opus+txt | 单选 | manifest |
| public_youtube1120 | 237 | 19.0 | opus+txt | YouTube 视频 | manifest |
| asr_public_phone_calls_2 | 66 | 9.4 | opus+txt | Internet + ASR | manifest |
| public_youtube1120_hq | 31 | 4.9 | opus+txt | YouTube 视频 | manifest |
| asr_public_stories_2 | 9 | 1.4 | opus+txt | Internet + 比对 | manifest |
| tts_russian_addresses_rhvoice_4voices | 80.9 | 12.9 | opus+txt | TTS | manifest |
| public_youtube700 | 75.0 | 12.2 | opus+txt | YouTube 视频 | manifest |
| asr_public_phone_calls_1 | 22.7 | 3.2 | opus+txt | Internet + ASR | manifest |
| asr_public_stories_1 | 4.1 | 0.7 | opus+txt | 公共情景 | manifest |
| public_series_1 | 1.9 | 0.3 | opus+txt | 公用系列 | manifest |
| public_lecture_1 | 0.7 | 0.1 | opus+txt | Internet + 手动任务 | manifest |
| Val |  |  |  |  |  |
| asr_calls_2_val | 2 | 0.8 | wav+txt | Internet | manifest |
| buriy_audiobooks_2_val | 1 | 0.5 | wav+txt | 书籍 + 手动任务 | manifest |
| public_youtube700_val | 2 | 0.13 | wav+txt | YouTube 视频 + 手动任务 | manifest |

## <a name="download-instructions"></a>下载说明

### <a name="direct-download"></a>直接下载

有关如何直接下载数据集的说明，请参阅 [GitHub 下载说明页](https://github.com/snakers4/open_stt#download-instructions)。

## <a name="additional-information"></a>其他信息

有关数据的帮助或问题，请通过 aveysov@gmail.com 联系数据作者

该许可证准许用户仅出于非商业目的，且仅在归属权属于创作者的情况下，以任何媒体或格式分发、组合、改编采用该资料和基于它进行生成。 它包含以下元素：
* BY - 版权必须归属于创作者
* NC - 仅准许出于非商业目的使用该资料

已与数据集作者达成一致，适用 CC-BY-NC，可用于商业用途。

## <a name="data-access"></a>数据访问

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=open-speech-to-text -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=open-speech-to-text)。

## <a name="helper-functions--dependencies"></a>帮助程序函数/依赖项

### <a name="building-libsndfile"></a>生成 libsndfile

在 Python 中读取 opus 文件且不产生严重开销的有效方法是使用 pysoundfile（围绕 libsoundfile 的 Python CFFI 包装器）。

Opus 支持已在上游实现，但尚未正确发布。 因此，我们选择了自定义生成和猴式修补。

通常，需要在 shell 中使用 sudo 访问权限来运行此命令：

```bash
apt-get update
apt-get install cmake autoconf autogen automake build-essential libasound2-dev \
libflac-dev libogg-dev libtool libvorbis-dev libopus-dev pkg-config -y

cd /usr/local/lib
git clone https://github.com/erikd/libsndfile.git
cd libsndfile
git reset --hard 49b7d61
mkdir -p build && cd build

cmake .. -DBUILD_SHARED_LIBS=ON
make && make install
cmake --build .
```
### <a name="helper-functions--dependencies"></a>帮助程序函数/依赖项

安装以下库：

```
pandas
numpy
scipy
tqdm
soundfile
librosa
```

清单是包含以下列的 csv 文件：

- 音频路径
- 文本文件的路径
- 持续时间

它们已证实为最简单的数据访问格式。

为便于使用，所有清单已重置根目录。 清单中的所有路径都是相对路径，你需要提供根文件夹。

```python
# manifest utils
import os
import numpy as np
import pandas as pd
from tqdm import tqdm
from urllib.request import urlopen


def reroot_manifest(manifest_df,
                    source_path,
                    target_path):
    if source_path != '':
        manifest_df.wav_path = manifest_df.wav_path.apply(lambda x: x.replace(source_path,
                                                                              target_path))
        manifest_df.text_path = manifest_df.text_path.apply(lambda x: x.replace(source_path,
                                                                                target_path))
    else:
        manifest_df.wav_path = manifest_df.wav_path.apply(lambda x: os.path.join(target_path, x))
        manifest_df.text_path = manifest_df.text_path.apply(lambda x: os.path.join(target_path, x))    
    return manifest_df


def save_manifest(manifest_df,
                  path,
                  domain=False):
    if domain:
        assert list(manifest_df.columns) == ['wav_path', 'text_path', 'duration', 'domain']
    else:
        assert list(manifest_df.columns) == ['wav_path', 'text_path', 'duration']

    manifest_df.reset_index(drop=True).sort_values(by='duration',
                                                   ascending=True).to_csv(path,
                                                                          sep=',',
                                                                          header=False,
                                                                          index=False)
    return True


def read_manifest(manifest_path,
                  domain=False):
    if domain:
        return pd.read_csv(manifest_path,
                        names=['wav_path',
                               'text_path',
                               'duration',
                               'domain'])
    else:
        return pd.read_csv(manifest_path,
                        names=['wav_path',
                               'text_path',
                               'duration'])


def check_files(manifest_df,
                domain=False):
    orig_len = len(manifest_df)
    if domain:
        assert list(manifest_df.columns) == ['wav_path', 'text_path', 'duration']
    else:
        assert list(manifest_df.columns) == ['wav_path', 'text_path', 'duration', 'domain']
    wav_paths = list(manifest_df.wav_path.values)
    text_path = list(manifest_df.text_path.values)

    omitted_wavs = []
    omitted_txts = []

    for wav_path, text_path in zip(wav_paths, text_path):
        if not os.path.exists(wav_path):
            print('Dropping {}'.format(wav_path))
            omitted_wavs.append(wav_path)
        if not os.path.exists(text_path):
            print('Dropping {}'.format(text_path))
            omitted_txts.append(text_path)

    manifest_df = manifest_df[~manifest_df.wav_path.isin(omitted_wavs)]
    manifest_df = manifest_df[~manifest_df.text_path.isin(omitted_txts)]
    final_len = len(manifest_df)

    if final_len != orig_len:
        print('Removed {} lines'.format(orig_len-final_len))
    return manifest_df


def plain_merge_manifests(manifest_paths,
                          MIN_DURATION=0.1,
                          MAX_DURATION=100):

    manifest_df = pd.concat([read_manifest(_)
                             for _ in manifest_paths])
    manifest_df = check_files(manifest_df)

    manifest_df_fit = manifest_df[(manifest_df.duration>=MIN_DURATION) &
                                  (manifest_df.duration<=MAX_DURATION)]

    manifest_df_non_fit = manifest_df[(manifest_df.duration<MIN_DURATION) |
                                      (manifest_df.duration>MAX_DURATION)]

    print(f'Good hours: {manifest_df_fit.duration.sum() / 3600:.2f}')
    print(f'Bad hours: {manifest_df_non_fit.duration.sum() / 3600:.2f}')

    return manifest_df_fit


def save_txt_file(wav_path, text):
    txt_path = wav_path.replace('.wav','.txt')
    with open(txt_path, "w") as text_file:
        print(text, file=text_file)
    return txt_path


def read_txt_file(text_path):
    #with open(text_path, 'r') as file:
    response = urlopen(text_path)
    file = response.readlines()
    for i in range(len(file)):
        file[i] = file[i].decode('utf8')
    return file 

def create_manifest_from_df(df, domain=False):
    if domain:
        columns = ['wav_path', 'text_path', 'duration', 'domain']
    else:
        columns = ['wav_path', 'text_path', 'duration']
    manifest = df[columns]
    return manifest


def create_txt_files(manifest_df):
    assert 'text' in manifest_df.columns
    assert 'wav_path' in manifest_df.columns
    wav_paths, texts = list(manifest_df['wav_path'].values), list(manifest_df['text'].values)
    # not using multiprocessing for simplicity
    txt_paths = [save_txt_file(*_) for _ in tqdm(zip(wav_paths, texts), total=len(wav_paths))]
    manifest_df['text_path'] = txt_paths
    return manifest_df


def replace_encoded(text):
    text = text.lower()
    if '2' in text:
        text = list(text)
        _text = []
        for i,char in enumerate(text):
            if char=='2':
                try:
                    _text.extend([_text[-1]])
                except:
                    print(''.join(text))
            else:
                _text.extend([char])
        text = ''.join(_text)
    return text
```

```python
# reading opus files
import os
import soundfile as sf



# Fx for soundfile read/write functions
def fx_seek(self, frames, whence=os.SEEK_SET):
    self._check_if_closed()
    position = sf._snd.sf_seek(self._file, frames, whence)
    return position


def fx_get_format_from_filename(file, mode):
    format = ''
    file = getattr(file, 'name', file)
    try:
        format = os.path.splitext(file)[-1][1:]
        format = format.decode('utf-8', 'replace')
    except Exception:
        pass
    if format == 'opus':
        return 'OGG'
    if format.upper() not in sf._formats and 'r' not in mode:
        raise TypeError("No format specified and unable to get format from "
                        "file extension: {0!r}".format(file))
    return format


#sf._snd = sf._ffi.dlopen('/usr/local/lib/libsndfile/build/libsndfile.so.1.0.29')
sf._subtypes['OPUS'] = 0x0064
sf.SoundFile.seek = fx_seek
sf._get_format_from_filename = fx_get_format_from_filename


def read(file, **kwargs):
    return sf.read(file, **kwargs)


def write(file, data, samplerate, **kwargs):
    return sf.write(file, data, samplerate, **kwargs)
```

```python
# display utils
import gc
from IPython.display import HTML, Audio, display_html
pd.set_option('display.max_colwidth', 3000)
#Prepend_path is set to read directly from Azure. To read from local replace below string with path to the downloaded dataset files
prepend_path = 'https://azureopendatastorage.blob.core.windows.net/openstt/ru_open_stt_opus_unpacked/'


def audio_player(audio_path):
    return '<audio preload="none" controls="controls"><source src="{}" type="audio/wav"></audio>'.format(audio_path)

def display_manifest(manifest_df):
    display_df = manifest_df
    display_df['wav'] = [audio_player(prepend_path+path) for path in display_df.wav_path]
    display_df['txt'] = [read_txt_file(prepend_path+path) for path in tqdm(display_df.text_path)]
    audio_style = '<style>audio {height:44px;border:0;padding:0 20px 0px;margin:-10px -20px -20px;}</style>'
    display_df = display_df[['wav','txt', 'duration']]
    display(HTML(audio_style + display_df.to_html(escape=False)))
    del display_df
    gc.collect()
```

## <a name="play-with-a-dataset"></a>演练数据集

### <a name="play-a-sample-of-files"></a>演练文件示例

大多数平台浏览器支持本机音频播放。 因此我们可以使用 HTML5 音频播放器查看数据。

```python
manifest_df = read_manifest(prepend_path +'/manifests/public_series_1.csv')
#manifest_df = reroot_manifest(manifest_df,
                              #source_path='',
                              #target_path='../../../../../nvme/stt/data/ru_open_stt/')

```

```python
sample = manifest_df.sample(n=20)
display_manifest(sample)
```

### <a name="read-a-file"></a>读取文件

```python
!ls ru_open_stt_opus/manifests/*.csv
```

演示如何以最佳方式读取 wav 和 opus 文件的一些示例。

Scipy 是读取 wav 文件的最快工具。 总体而言，Pysoundfile 是读取 opus 文件的最佳工具。

```python
%matplotlib inline

import librosa
from scipy.io import wavfile
from librosa import display as ldisplay
from matplotlib import pyplot as plt
```

#### <a name="read-a-wav"></a>读取 wav

```python
manifest_df = read_manifest(prepend_path +'manifests/asr_calls_2_val.csv')
#manifest_df = reroot_manifest(manifest_df,
                              #source_path='',
                              #target_path='../../../../../nvme/stt/data/ru_open_stt/')
```

```python
sample = manifest_df.sample(n=5)
display_manifest(sample)
```

```python
from io import BytesIO

wav_path = sample.iloc[0].wav_path
response = urlopen(prepend_path+wav_path)
data = response.read()
sr, wav = wavfile.read(BytesIO(data))
wav.astype('float32')
absmax = np.max(np.abs(wav))
wav =  wav / absmax
```

```python
# shortest way to plot a spectrogram
D = librosa.amplitude_to_db(np.abs(librosa.stft(wav)), ref=np.max)
plt.figure(figsize=(12, 6))
ldisplay.specshow(D, y_axis='log')
plt.colorbar(format='%+2.0f dB')
plt.title('Log-frequency power spectrogram')
# shortest way to plot an envelope
plt.figure(figsize=(12, 6))
ldisplay.waveplot(wav, sr=sr, max_points=50000.0, x_axis='time', offset=0.0, max_sr=1000, ax=None)
```

#### <a name="read-opus"></a>读取 opus

```python
manifest_df = read_manifest(prepend_path +'manifests/asr_public_phone_calls_2.csv')
#manifest_df = reroot_manifest(manifest_df,
                              #source_path='',
                              #target_path='../../../../../nvme/stt/data/ru_open_stt/')
```

```python
sample = manifest_df.sample(n=5)
display_manifest(sample)
```

```python
opus_path = sample.iloc[0].wav_path
response = urlopen(prepend_path+opus_path)
data = response.read()
wav, sr = sf.read(BytesIO(data))
wav.astype('float32')
absmax = np.max(np.abs(wav))
wav =  wav / absmax
```

```python
# shortest way to plot a spectrogram
D = librosa.amplitude_to_db(np.abs(librosa.stft(wav)), ref=np.max)
plt.figure(figsize=(12, 6))
ldisplay.specshow(D, y_axis='log')
plt.colorbar(format='%+2.0f dB')
plt.title('Log-frequency power spectrogram')
# shortest way to plot an envelope
plt.figure(figsize=(12, 6))
ldisplay.waveplot(wav, sr=sr, max_points=50000.0, x_axis='time', offset=0.0, max_sr=1000, ax=None)
```

<!-- nbend -->

---

## <a name="next-steps"></a>后续步骤

查看[开放数据集目录](dataset-catalog.md)中的其余数据集。
