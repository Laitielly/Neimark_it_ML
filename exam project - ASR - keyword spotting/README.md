# Description of the project "Training an acer model based on the MatchboxNet architecture for the keyword spotting task"

This work was done as an examination for the Neymark-ML intensive.

You can download the whole project with all the source and finished files [here](https://disk.yandex.ru/d/9AWP_LVSxaOOgg) (17.4 GB).

## About the task

Keyword detection is a process that helps to detect keywords or phrases used in phone calls or audio recordings. Then these words and phrases can be used to adjust the urgency of the call, assess customer satisfaction, or trigger the activation of the system (an example from life - Alice, Ok, Google!).

## About the dataset

A google speech commands v02 dataset was selected. This dataset consists of audio recordings of the type .wav with a duration of one second. Each audio recording contains one spoken English word or background noise. These words are taken from a small set of commands, and are pronounced by many different native speakers. This dataset is designed to help in teaching simple machine learning models.
Version 2 was released on April 11, 2018 and contains 105,829 audio files.

The distribution of data can be seen in the table below:

|   | train | dev | test |
| ------------- | ------------- | ------------- | ------------- |
| v02 | 84848 | 9982 | 4890 |

## Choosing a trigger word

In this dataset, 2 words/names are suitable for classification - Sheila and Marvin. For reasons of the fact that Marvin is easier to pronounce and, in my opinion, is better distinguishable from other words, it was taken as a target.

|   | train | dev | test |
| ------------- | ------------- | ------------- | ------------- |
| Sheila | 1606 | 204 | 17 |
| Marvin | 1710 | 195 | 11 |

## Dataset analysis

A small duration analysis was carried out to identify “broken” audio recordings.

### Train

So, for the training sample, there is the following distribution. 
The first graph shows the distribution of audio recordings with a duration of more than 1 second. On the second less than 1 second. 
There were slightly more than 73k records equal to 1 second.

![train_distr](https://github.com/Laitielly/Neimark_it_ML/blob/main/exam%20project%20-%20ASR%20-%20keyword%20spotting/images/train_distr.png)

### Dev

Also for validation, there was no audio length longer than a second. Equal to a second - almost 9k.

![dev_distr](https://github.com/Laitielly/Neimark_it_ML/blob/main/exam%20project%20-%20ASR%20-%20keyword%20spotting/images/dev_distr.png)

### Test

For the test, all audio was equal to 1 second without exceptions.

## Augmentation

In this dataset, the data is clearly unbalanced, so the Marvin class had to be supplemented.

I used the following methods:

- Slight slowing down of the voice and its tone
- Reverberation
- White noise
- Adding sounds in the background

Let's go a little bit on what all these words mean:

- A **slight slowing down of the voice and its tone** - I think this is understandable,
it's just that the voice becomes a little bassier, you can say
- **Reverberation** is the process of gradually reducing
the intensity of sound with its multiple reflections. That is a kind of echo.
- **White noise** - noise whose spectral components
evenly distributed over the entire range of frequencies involved. Or simply put: a uniform sound without sudden changes in volume, which contains the entire frequency range.
- **Adding sounds to the background** - I just downloaded the music and split them one second at a time, turned down the volume and added the main audio to the background.

Accordingly, I restored everything to balance. As a result, there were 166930 audio recordings in the training sample.

## Metrics

The following metrics were used:

- Accuracy (basic metric for comparison)
- F1-score (basic metric for comparison)
- Balanced accuracy (unweighted average recall / macro recall)
- False alarm per hour (test metric for understanding false positives)

## MatchboxNet

To solve this problem, I used the MatchboxNet architecture.
MatchboxNet is based on the QuartzNet convolutional neural network for automatic speech recognition.
The MatchboxNet-BxRxC model has B blocks like this. Each block consists of R sub-blocks. All sub-blocks in the block have the same number of output channels C.

![mbn](https://github.com/Laitielly/Neimark_it_ML/blob/main/exam%20project%20-%20ASR%20-%20keyword%20spotting/images/mbn.png)

## Experiments

In total, 6 models with different parameters and different sets of augmented data were trained. All of them are distributed in folders of the same name.

**Summary metrics:**

<table>
    <thead>
        <tr>
            <th></th>
            <th></th>
            <th>Accuracy</th>
            <th>Balanced accuracy</th>
            <th>F1-score</th>
            <th>FAR per hour</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=3>Exp1 train+dev augm</td>
            <td rowspan=1>train</td>
            <td>99.85</td>
            <td>99.85</td>
            <td>99.85</td>
            <td rowspan=3>81</td>
        </tr>
        <tr>
            <td>dev</td>
            <td>99.61</td>
            <td>99.61</td>
            <td>99.62</td>
        </tr>
        <tr>
            <td>test</td>
            <td>99.84</td>
            <td>90.85</td>
            <td>99.92</td>
        </tr>
        <tr>
            <td rowspan=3>Exp2 only train augm</td>
            <td rowspan=1>train</td>
            <td>99.6</td>
            <td>99.6</td>
            <td>99.6</td>
            <td rowspan=3>43</td>
        </tr>
        <tr>
            <td>dev</td>
            <td>99.51</td>
            <td>87.94</td>
            <td>99.75</td>
        </tr>
        <tr>
            <td>test</td>
            <td bgcolor="#D6EEEE">!99.88!</td>
            <td bgcolor="#D6EEEE">!90.87!</td>
            <td bgcolor="#D6EEEE">!99.94!</td>
        </tr>
      <tr>
            <td rowspan=3>Exp3 sr=44100</td>
            <td rowspan=1>train</td>
            <td>98.67</td>
            <td>98.67</td>
            <td>98.68</td>
            <td rowspan=3>!0!</td>
        </tr>
        <tr>
            <td>dev</td>
            <td>98.19</td>
            <td>98.19</td>
            <td>98.21</td>
        </tr>
        <tr>
            <td>test</td>
            <td>99.39</td>
            <td>54.34</td>
            <td>99.69</td>
        </tr>
      <tr>
            <td rowspan=3>Exp4 No effects All augm</td>
            <td rowspan=1>train</td>
            <td>99.65</td>
            <td>99.65</td>
            <td>99.65</td>
            <td rowspan=3>3460</td>
        </tr>
        <tr>
            <td>dev</td>
            <td>99.67</td>
            <td>99.43</td>
            <td>99.43</td>
        </tr>
        <tr>
            <td>test</td>
            <td>99.43</td>
            <td>86.23</td>
            <td>99.84</td>
        </tr>
      <tr>
            <td rowspan=3>Exp5 Not augmented</td>
            <td rowspan=1>train</td>
            <td>99.48</td>
            <td>91.46</td>
            <td>99.73</td>
            <td rowspan=3>13</td>
        </tr>
        <tr>
            <td>dev</td>
            <td>99.84</td>
            <td>91.22</td>
            <td>99.77</td>
        </tr>
        <tr>
            <td>test</td>
            <td>99.54</td>
            <td>81.78</td>
            <td>99.92</td>
        </tr>
      <tr>
            <td rowspan=3>Exp6 No effects train augm</td>
            <td rowspan=1>train</td>
            <td>99.7</td>
            <td>99.7</td>
            <td>99.7</td>
            <td rowspan=3>3534</td>
        </tr>
        <tr>
            <td>dev</td>
            <td>99.73</td>
            <td>90.72</td>
            <td>99.77</td>
        </tr>
        <tr>
            <td>test</td>
            <td>99.56</td>
            <td>86.26</td>
            <td>99.87</td>
        </tr>
    </tbody>
</table>

**Beautiful graphics:**

![allgrphx](https://github.com/Laitielly/Neimark_it_ML/blob/main/exam%20project%20-%20ASR%20-%20keyword%20spotting/images/allgrphx.png)

### [First experiment](https://github.com/Laitielly/Neimark_it_ML/tree/main/exam%20project%20-%20ASR%20-%20keyword%20spotting/exp_1-all_sounds)

![exp1grphx](https://github.com/Laitielly/Neimark_it_ML/blob/main/exam%20project%20-%20ASR%20-%20keyword%20spotting/images/exp1grphx.png)

The experiment was carried out on the basis of fully augmented train and dev data, sampling rate - 22050. Basic configuration.

9/11 Marvin audio has been correctly classified. However, 2 incorrectly classified audio recordings contain the usual "Ma" sound,
which indicates that all of Marvin's recordings were guessed correctly.

### [Second experiment](https://github.com/Laitielly/Neimark_it_ML/tree/main/exam%20project%20-%20ASR%20-%20keyword%20spotting/exp_2%20with%20usual%209982%20dev)

![exp2grphx](https://github.com/Laitielly/Neimark_it_ML/blob/main/exam%20project%20-%20ASR%20-%20keyword%20spotting/images/exp2grphx.png)

The experiment was conducted on the basis of augmented train data, sampling rate - 22050. Configuration: repeat - 3, dropout - 0.2.

### [Third experiment](https://github.com/Laitielly/Neimark_it_ML/tree/main/exam%20project%20-%20ASR%20-%20keyword%20spotting/exp3%20with%2044100%20sr%20full%20aug)

![exp3grphx](https://github.com/Laitielly/Neimark_it_ML/blob/main/exam%20project%20-%20ASR%20-%20keyword%20spotting/images/exp3grphx.png)

The experiment was carried out on the basis of fully augmented train and dev data, sampling rate - 44100. Configuration: repeat - 3, dropout - 0.2.

### [Fourth experiment](https://github.com/Laitielly/Neimark_it_ML/tree/main/exam%20project%20-%20ASR%20-%20keyword%20spotting/exp4_without%20low%20fast%20effects)

![exp4grphx](https://github.com/Laitielly/Neimark_it_ML/blob/main/exam%20project%20-%20ASR%20-%20keyword%20spotting/images/exp4grphx.png)

The experiment was conducted on the basis of fully augmented train and dev data, but the effects of bass voice were removed. The sampling rate is 22050. 
Configuration: repeat - 2, dropout - 0.2.

### [Fifth experiment](https://github.com/Laitielly/Neimark_it_ML/tree/main/exam%20project%20-%20ASR%20-%20keyword%20spotting/exp5%20not%20augmented)

![exp5grphx](https://github.com/Laitielly/Neimark_it_ML/blob/main/exam%20project%20-%20ASR%20-%20keyword%20spotting/images/exp5grphx.png)

The experiment was conducted on the basis of the built-in add-on from MatchboxNet (supplemented only by train using white noise). 
The sampling rate is 22050. Configuration: repeat - 3, dropout - 0.2.

### [Sixth experiment](https://github.com/Laitielly/Neimark_it_ML/tree/main/exam%20project%20-%20ASR%20-%20keyword%20spotting/exp6%20no%20effect%20no%20dev%20augm)

![exp6grphx](https://github.com/Laitielly/Neimark_it_ML/blob/main/exam%20project%20-%20ASR%20-%20keyword%20spotting/images/exp6grphx.png)

The experiment was conducted on the basis of augmented train data, but the effects of bass voice were removed. The sampling rate is 22050. 
Configuration: repeat - 2, dropout - 0.2.


