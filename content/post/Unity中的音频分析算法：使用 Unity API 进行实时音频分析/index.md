---
title: Unity中的音频分析算法：使用 Unity API 进行实时音频分析
author: Mingxian Yang
date: 2021-07-02 18:10:00 +0800
description: 对于音频的实时分析，我们通过检测场景中播放的音频，获取其中最接近的节拍。虽然实时分析中会有一些限制，但是它也可以作为适用于多种样用例的解决方案。另外，它也作为了进行音频预处理分析的前置实现。
image: FT.png
categories: [Game Design]
tags: [Unity3D]
---



### 本系列文章： 

[**Unity中的音频分析算法：介绍**]({% post_url 2021-07-01-Unity中的音频分析算法：介绍 %})

[**Unity中的音频分析算法：使用 Unity API 进行实时音频分析**]({% post_url 2021-07-02-Unity中的音频分析算法：使用 Unity API 进行实时音频分析 %})

[**Unity中的音频分析算法：预处理音频分析**]({% post_url 2022-03-21-Unity中的音频分析算法：预处理音频分析 %})

[演示视频链接：https://www.bilibili.com/video/BV1zY4y1h7DL/](https://www.bilibili.com/video/BV1zY4y1h7DL/)


---

## Unity3D 
在Unity引擎中播放声音，我们需要使用 **AudioSource** 组件来播放类型为 AudioClip 的文件。一旦我们将音频导入为 AudioClip ，我们需要将加载类型设置为“Decompress On Load”，以确保我们在运行时访问音频样本数据。
![AudioClip设置](003.png)

我们将 **AudioSource** 组件添加到场景中的游戏物体上，将 AudioClip 拖动到AudioSource中，将播放模式选中为 Play onwake，这样方便一进入到场景就开始播放音频。一旦音频开始播放，Unity API就会提供一些便捷的方法来获取当前正在播放音频的信息。这使得我们对此音频的实时分析变得更简单。

我们使用到Unity中两个重要的API：  
1. [**AudioSource.GetOutputData**](https://docs.unity3d.com/ScriptReference/AudioSource.GetOutputData.html)
2. [**AudioSource.GetSpectrumData**](https://docs.unity3d.com/ScriptReference/AudioSource.GetSpectrumData.html)  

上面的链接为Unity3D的官方文档，可惜的是，文档对于这两个API的解释并不是特别详尽，不过没关系，随着我们的进展，我们会增加对他们的解释以便调用。

**GetOutputData** 将为我们提供一个数组，表示特定通道随时间变化的幅度或响度。我们将其称为“样本数据”。虽然样本数据对于预处理变得更有用，但由于下一个API：**GetSpectrumData**，我们实际上并不需要它来进行实时节拍检测。

**GetSpectrumData** 将给我们一个表示可以表示为相对振幅的数组，在特定通道上的时间样本的频域上。我们将其称为“频谱数据”。频谱数据对于我们的音频分析非常有用，因为它不仅告诉我们在某个时间点音频的数据，而且还可以告诉我们这些音频的频率范围。这可以帮助我们弄清楚不同频率范围内产生的不同的节拍，我们可以粗略地将其转换为音轨内的不同乐器。下面为使用Unity的DrawLine函数将**GetSpectrumData**表示相对振幅的数组进行了可视化的结果。代码和运行结果如下：


```csharp
void Update()
    {
        float[] spectrum = new float[256];
        AudioListener.GetSpectrumData(spectrum, 0, FFTWindow.Rectangular);
        for (int i = 1; i < spectrum.Length - 1; i++)
        {
            Debug.DrawLine(new Vector3(i - 1, spectrum[i] + 10, 0), new Vector3(i, spectrum[i + 1] + 10, 0), Color.red);
            Debug.DrawLine(new Vector3(i - 1, Mathf.Log(spectrum[i - 1]) + 10, 2), new Vector3(i, Mathf.Log(spectrum[i]) + 10, 2), Color.cyan);
            Debug.DrawLine(new Vector3(Mathf.Log(i - 1), spectrum[i - 1] - 10, 1), new Vector3(Mathf.Log(i), spectrum[i] - 10, 1), Color.green);
            Debug.DrawLine(new Vector3(Mathf.Log(i - 1), Mathf.Log(spectrum[i - 1]), 3), new Vector3(Mathf.Log(i), Mathf.Log(spectrum[i]), 3), Color.blue);
        }
    }
```

![GetSpectrumData](004.png)



**GetSpectrumData** 在后台执行快速傅立叶变换 (FFT)，将时域的幅度转换为频域，仅返回从 FFT 返回的复数数据的相对幅度部分。虽然 FFT 通常在从 0Hz 到采样率的整个频率范围内执行（让我们在此示例中使用 48kHz），但 0Hz — 采样率的一半 (24kHz) 是该范围的后一半 (24kHz-48kHz) 的镜像. 这个中点称为奈奎斯特频率。因此，**GetSpectrumData** 和其他一些基于 FFT 的帮助程序仅返回分析的前半部分的相对幅度。这意味着执行 FFT 所需的音频样本数是 **GetSpectrumData** 返回的频率区间数的两倍. 因此，要分析的频率粒度越高，生成该粒度所需的时间范围就越大。如果我们需要 1024 个频率项，每个项的粒度为 23.43Hz，则需要 2048 个音频样本。需要更多的音频样本意味着需要更长的时间来收集这些样本，这会增加不确定性，以准确了解检测到的每个频率值在时域中的确切位置。这是一种权衡取舍。可以进行试验，看看哪种配置最适合当前的项目。我发现 512 和 1024 的频谱数列大小已经足够应对基本的音频检测了。

强烈建议不了解傅里叶变换的读者观看以下有关傅立叶变换的视频，以更好地了解 Unity 中 GetSpectrumData 为我们做了什么。这是国外大佬3Blue1Brown的数学科普视频，形象通俗的介绍了傅里叶变换的复杂概念：Youtube的视频链接：[傅立叶变换](https://www.youtube.com/watch?v=spUNpyF58BY)


对于GetSpectrumData的参数：   

- **samples**: 当前时间播放音频的频谱数据将会存放在我们提供的这个数组里面，数组的大小由我们来指定，数组的长度必须是 2 的幂, 其实理论上FFT不需要强制指定样本大小为2的幂，只不过FFT分析在样本大小为 2 的幂时性能最好，Unity 的 GetSpectrumData 强制指定了这个结果。

- **channel**: 通道 0 在这里是一个通用的选择，因为如果我们有立体声音频，我们将需要单独处理 2 个通道的样本数据。通道 0 包含立体声样本的平均值，将每 2 个立体声样本合并为 1 个单声道样本。这使简化了后续进行音频分析的过程，我们不希望独立分别计算不同声道的音频数据。  

- **window**: 我们可以从多个 [FFT 窗口](https://docs.unity3d.com/ScriptReference/FFTWindow.html) 中进行选择，BlackmanHarris 貌似是一个很不错的选择，使用它减少了跨频带的信号泄漏。

1024 的频谱数组大小能够提供很好的粒度，这意味着 Unity 将在后台采集 2048 个音频样本。如果我们知道音频采样率，我们就可以找到频谱数据支持的频率范围，然后使用我们的数组大小，我们可以快速找出数组的每个索引代表的频率范围。


## 频率的处理

Unity 可以通过静态成员 AudioSettings.outputSampleRate 告诉我们混音器的音频采样率（以赫兹 (Hz) 为单位）。 这将为我们提供 Unity 播放音频的采样率，通常为 48000 或 44100。我们还可以使用 AudioClip.frequency 获得 AudioClip 的采样率。

知道了我们的采样率，我们就可以知道 FFT 的最大支持频率，它是采样率的一半。然后我们可以除以我们的频谱长度，以了解每个 bin（索引）代表哪些频率。

48000 / 2 = 24000Hz 这完全覆盖了我们的音频数据的最高频率。一般来说人能够听到的频率范围在 20Hz-20000Hz 之间。

24000 / 1024 ≈ 23.47Hz/bin。现在，显然我们没有足够的粒度来表示每个单独的频率，但对于大多数用例来说应该足够了。在这个粒度下，我们的第 10 个 bin 将为我们提供 ~234Hz +/- 相邻频率的振幅。

使用用于描述频率范围的[预定义标准](https://www.teachmeaudio.com/mixing/techniques/audio-spectrum)，我们可以开始根据哪些频率在我们的音轨中的某个时间点起作用来做出决定。此外还有一种别的可行的方法是：[根据频率检测不同的音符](https://pages.mtu.edu/~suits/notefreqs.html)。


为了验证上述分析的正确性，我们从Youtube上下载了[音频](https://www.youtube.com/watch?v=H-iCZElJ8m0)，它提供了 10Hz - 20kHz 的频谱。音轨在大约 2:08 的时候达到 234Hz。因此，如果我将此音频文件加载到 AudioSource 并查看在 128 秒时的 GetSpectrumData，我应该会在索引值(bin)为10的附近看到它在起主要作用。

事实确实如此！  
我使用了以下的 Script 做了一个小测试，使用上述的音频，在第128 - 129s 时输出 GetSpectrumData 返回的数组值，我应该会在索引值在10的附近看到显著的结果：
```csharp
if (audioSource.time >= 128f && audioSource.time < 129f) 
    {
        float[] curSpectrum = new float[1024];
        audioSource.GetSpectrumData (curSpectrum, 0, FFTWindow.BlackmanHarris);
        float targetFrequency = 234f;
        float hertzPerBin = (float)AudioSettings.outputSampleRate / 2f / 1024;
        int targetIndex = (int)(targetFrequency / hertzPerBin);
        string outString = "";
        for (int i = targetIndex - 3; i <= targetIndex + 3; i++) 
        {
            outString += string.Format("| Bin {0} : {1}Hz : {2} |   ", i, i * hertzPerBin, curSpectrum[i]);
        }
        Debug.Log (outString);
    }
```
测试的结果如下所示：
![Test](005.png)

方便观察，我将数据结果取3位小数，并做成表格：  

| Bin 6 | Bin 7 | Bin 8 | Bin 9 | Bin 10 | Bin 11 | Bin 12 |
| :---: | :---: | :---: | :---: | :----: | :----: | :----: |
| 141Hz | 164Hz | 188Hz | 211Hz | 234Hz  | 258Hz  | 281Hz  |
| 0.000 | 0.002 | 0.031 | 0.104 | 0.149  | 0.098  | 0.028  |

可以在此处看到频段之间存在一些泄漏，但我们仍然具有足够的粒度，可以看到此时在 234Hz 范围内有显着的结果，这证明我们的分析和计算都是正确的。

到现在为止，我们可以知道音轨中某个时间点的频率分布。这对音频分析提供了数据支持。这意味着我们可以使用这些频率数据进行 **Spectral Flux (频谱流计算)**。[(wikipedia link)](https://en.wikipedia.org/wiki/Spectral_flux#:~:text=Spectral%20flux%20is%20a%20measure,spectrum%20from%20the%20previous%20frame.&text=The%20spectral%20flux%20can%20be,onset%20detection%2C%20among%20other%20things.)

## 使用 Spectral Flux 进行基础检测

很多论文以及CSDN上面都有大量关于使用 Spectral Flux 来检测节拍的工作。在这里我将不会提出什么新的内容，只是单纯将解决方案应用到 C# 语言并在 Unity3D 里面实现一些效果。

频谱流就是在两个接近的时间点找到频谱数据之间每个区间的总差异。对我们来说，这实际上意味着将当前帧中正在播放的音频的频谱数据与上一帧或我们上次检查的数据进行比较。这时候你可能就会意识到，我们面临的几个问题：实时的乐谱分析受音频播放进度的限制，还有就是实时的分析以及帧率之间的优化。

每次我们调用 GetSpectrumData 时，我们应该保留最新的频谱数据以进行比较。如果我们每帧都更新我们的频谱数据，那么这很容易实现



我们使用了两个float数组来存放实时数据 (curSpectrum) 以及保留历史数据 (prevSpectrum) 并进行比较。我们想知道最新频谱数据和当前频谱数据之间的每个频率区间的差异。为了能减少一些空间复杂度，我们只保留一些正的的差异，以便我们能够知道整个频谱是否处于上升趋势。我们将其称为 rectified spectral flux。我们正在分析包含所有所支持频率（0-48000/2 Hz）的整个频谱。如果你想在一个频率子集上运行相同的算法，比如，只是分析次低音和低音范围，只需指定要处理校正后的频谱流的 1024 长度频谱的哪些索引(bin)即可。因为我们已经知道如何确定哪个索引对应哪个频率了，例如上述例子中的索引10对应234Hz附近的频率范围。

有一些更高级的玩法，比如在多个频率范围运行算法，用来消除噪音产生的影响，并且获得不同乐器可能发出的频率范围以及对应的索引值。这些更加复杂的应用可以支持音频分析算法在音乐游戏决策中得到更令人信服的结果。

在这里，我们简单起见一次分析整个频谱。
```csharp

        float sum = 0f;
        for (int i = 0; i < numSamples; i++) 
        {
            sum += Mathf.Max (0f, curSpectrum [i] - prevSpectrum [i]);
        }
        return sum;
    
```

接下来，我们希望能够找到一个阈值，超过阈值的频谱变化足够明显，就会被认为发生了一个新节拍。我们的做法是计算平均光谱通量值，并将其乘以一个指定的灵敏度系数。如果我们正在处理的光谱通量值高于我们计算的平均值，我们就在这个时间点上判定发生了一个新的节拍。 

我们要基于为围绕（过去和未来）特定光谱通量值的时间范围计算的光谱通量值生成阈值。当我们要做到实时分析时，这会变得有点困难，因为我们永远无法真正得到未来几个帧率的频谱流。好在我们可以用一些在Unity中可以实现的小Trick来做到伪实时。我本人的做法是将音频设置为静音，算法处理静音的音频，然后延迟一点时间我们播放相同的音乐给玩家听到。值得注意的是，如果我们使用了一些Output的API，我们不能真的直接Mute音频，而是使用Mixer组件来把音频降低到玩家听不到，然而场景的AudioSource依然可以读取音频的数据。实现很简单，不在这里赘述了。  

我们要做的只是对一些过去的一些频谱流值（我们的帧率/2）进行检测。因此，如果我们的帧大小为 30，一旦我们有 30 个光谱通量值，我们就可以通过对值 1-30 取平均值、将平均值乘以某个灵敏度乘数并检查第 15 个值是否超过阈值。然后继续判断第 16 个值是否超过阈值，此时的阈值计算的平均值为第 2-31 的值，依此类推。根据帧速率，实时延迟约 15 个频谱流值会使我们落后于当前播放的音频大约半秒。下面的代码展示了如何计算频谱流的阈值：

```csharp

    // spectral flux 窗口的值加和
	float sum = 0f;
	for (int i = windowStartIndex; i < windowEndIndex; i++) 
    {
		sum += spectralFluxSamples [i].spectralFlux;
	}

	// 求平均值并乘一个阈值系数
	float avg = sum / (windowEndIndex - windowStartIndex);


```

剩下的功能就变得很直接了。  
我们只需要考虑高于阈值的 flux ，我们把这些 flux 叫做 pruned spectral flux，如果当前 flux 小于阈值那就设置其为 0。

写到这里，突然感觉这个操作和神经网络里面的 ReLU 好像啊哈哈哈。*插个题外话哈，如果有时间我把自己在学校做的关于神经网络激活函数的内容post出来。有学习的需求的可以关注一下我的博客 YMX~*

```csharp

	return Mathf.Max (0f, spectralFluxSamples [spectralFluxIndex].spectralFlux - spectralFluxSamples [spectralFluxIndex].threshold);

```
最后我们需要确定每一个 spectral flux 是不是峰值，我们通过比较样本的 pruned spectral flux 和它旁边的邻居，如果它大于先前的 pruned spectral flux 和 它之后的一pruned spectral flux，那他就是峰值呗。所以我们还需要对他之后的一个 flux 进行比较，所以我们又需要将本来就是伪实时的算法再滞后一个 flux 的时间。  

```csharp
    bool isPeak(int spectralFluxIndex) 
    {
        if (spectralFluxSamples [spectralFluxIndex].prunedSpectralFlux > spectralFluxSamples [spectralFluxIndex + 1].prunedSpectralFlux &&
            spectralFluxSamples [spectralFluxIndex].prunedSpectralFlux > spectralFluxSamples [spectralFluxIndex - 1].prunedSpectralFlux) 
                return true;
            else 
                return false;
    }
    
```

回顾一下整个流程：我们需要不断地收集音频数据(spectrum data)，计算出 rectified spectral flux，然后再看看历史（我们窗口大小的一半），判断现flux是否在根据历史样本计算出的阈值之上，然后再计算下一个 flux，看看当前 flux 是否是一个峰值。  


我们使用一个class类来表示 Spectral Flux 的信息，定义如下：  

```csharp
    public class SpectralFluxInfo 
    {
        public float time;
        public float spectralFlux;
        public float threshold;
        public float prunedSpectralFlux;
        public bool isPeak;
    }
```
将这些信息能够支持实时绘制算法的输出。可视化的数据，他不比枯燥的数据香吗？这里面的 threshold 我们可以通过自行调整，播放音频，然后改变阈值，你会看到一些变化，找到一个最合适的。

我们在Update函数里面调用 analyzeSpectrum 函数，就可以看到结果了。  

```csharp
void Update() 
{
	audioSource.GetSpectrumData(spectrum, 0, FFTWindow.BlackmanHarris);
	spectralFluxAnalyzer.analyzeSpectrum (spectrum, audioSource.time);
}
```


## 结果

![GetSpectrumData](006.png)

每个绿点都是 rectified spectral flux 样本。可以看到一直到绿线都有绿点，绿线代表音轨中的当前播放时间。蓝点是该时间点的阈值。由于计算平均值所需的样本时间范围，我们只能生成比实时晚几个样本的阈值。红点是我们的峰值。由于我们在这里分析的是整个频谱，歌曲中存在其他的干扰因素影响到了特定频率的强度，因此一些频率的节拍并不总是在我们绘图的 y 轴上的同一个位置（rectified spectral flux 的值），相同频率的节拍有可能画出的点的y轴会有区别。    

峰值是我们最看重的因素，它代表了音频中发生了主要的节拍。我们可以根据这个做音乐的可视化，做音游的自动曲谱生成等项目……不过这个算法还存在着一些制约，比如它并不真的是实时的，有一些妥协的办法，例如我上面说的在 Unity 中先处理音频，然后在一定延迟后播放相同的音乐；或者在第一次播放歌曲时存储节拍并缓存结果以备后用。  

不过我们如果不想做一些妥协呢？肯定有方法能实现预先预处理整个音频文件，通过我们相同的 spectral flux 算法运行所有样本，以便我们可以在播放音频之前就已经做完了检测工作。在 Unity 中获得预处理音频需要一些别的处理方式，不过它肯定是可行的。

我将会在下一篇文章讲解到底如何进行预处理操作。那时候我们还会对检测算法做一些别的方面的改进。

下一篇文章：[**Unity中的音频分析算法：预处理音频分析**]({% post_url 2022-03-21-Unity中的音频分析算法：预处理音频分析 %})
