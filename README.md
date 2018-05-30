# Ultrasonic communication by STM32L4's DSP and MEMS microphone

![S](./doc/ChirpFrameS.jpg)

## Preparation: STM32L4 platform and FFT test code on MEMS mic

This project uses STM32L476RG as MCU and MP34ST01-M as MEMS microphone.

![platform](./doc/MEMSMIC_expansion_board.jpg)

![architecture](https://docs.google.com/drawings/d/e/2PACX-1vR1KKp2QeL_SmrnUsTl5zcwddQToPJmnSBHFnxiw78y3_3mjA7EzNl2iNcUA5aOW_jRAQapTNji-eJ7/pub?w=2268&h=567)

==> [Platform](PLATFORM.md)

==> [Test code](./basic)

## Ultrasonic communications experiment (FSK modulation)

==> [Experiment](EXPERIMENT.md)

==> [Test code](./ultracom)

Conclusion: the method (sort of FSK modulation) work very well in a silent room, but did not work in a noisy environment such as a meeting room. I have to come up with another approach, such as spread spectrum.

## Knowles MEMS mic

![Knowles](./doc/Knowles.jpg)

I have bought [this MEMS mic](http://akizukidenshi.com/catalog/g/gM-05577/): Knowles SPM0405HD4H. The spec is similar to the mic on the expansion board from STMicro. Although this one does not support ultrasonic, it should be OK.

## Chirp modulation experiment

### Two kinds of noises

I observed two kinds of noises in a room:

- Constant noises at specific frequencies: noises from motors/inverters???
- Bursty noises in a short period: cough, folding paper etc.

I _guess_ Chirp modulation might be suitable for ultrasonic communications in a noisy environment. No proof yet.

### Chirp modulation

Spectrum is spread out like Mt. Fuji:

![Chirp](./doc/Chirp.jpg)

### Chirp de-modulation

All the frequencies appear in one TQ(Time Quantum). I used [Audacity](https://www.audacityteam.org/) to capture the spectrogram:

![Chirp_Spectrogram](./doc/Chirp_Spectrogram.jpg)

Reference: [Chirp compression (Wikipedia)](https://en.wikipedia.org/wiki/Chirp_compression)

```
If a chirp sequence is a(n) and that for the compression filter is b(n), then the compressed pulse sequence c(n) is given by

c1(n)=IFFT[FFT{a(n)}*FFT{b(n)}]
```

Other references:
- [Chirp A New Radar Technique](http://www.rfcafe.com/references/electronics-world/chirp-new-radar-technique-january-1965-electronics-world.htm)
- [Radar Pulse Compression](https://www.ittc.ku.edu/workshops/Summer2004Lectures/Radar_Pulse_Compression.pdf)

### DFSDM setting

|Parameter    |Value/setting|
|-------------|-----|
|System clock |80MHz|
|Clock divider|25 (3.2MHz over-sampling)|
|Decimation   |32   |
|Filter       |sinc3|
|Sampling rate|100kHz|

### FFT setting

|Parameter    |Value/setting|
|-------------|-----|
|DMA interrupt|2048 samples/interrupt|
|SFFT         | TBD |

### Time Quantum (TQ)

1/100kHz * 2048samples/interrupt = 20.5 msec

### Frame synchronization technique (tentative)

![sync](https://docs.google.com/drawings/d/e/2PACX-1vT0xMhbKVX62nasynSLvbgHrd40IWsxlZk-ngVtTI8NFT8TRtOmFlF54dge_VsReuIUTKtRM1zNQkBn/pub?w=960&h=720)

[Example] Ascii "S" character code (0x53)

![S](./doc/ChirpFrameS.jpg)

### Transmission speed

It is quite slow! I will optimize each parameters to attain faster bit rate.

8bits * 1000(msec) / 615(msec) = 13bps

### FFT output from STM32L4 DSP with MEMS mic

I used a very cheap mic and [Jupyter Notebook to see the output](./agent/chirp_experiment/chirp.ipynb).

Sweep range: 16000Hz - 16000Hz

![16000](./doc/FFT_Chirp_16000.jpg)

Sweep range: 16000Hz - 17000Hz

![16000_17000](./doc/FFT_Chirp_16000_17000.jpg)

Sweep range: 16000Hz - 18000Hz

![16000_18000](./doc/FFT_Chirp_16000_18000.jpg)

### Chirp expriment on May 29, 2018

Regarding S/N ratio and reachability, it achieved a great improvemnt, thanks to chirp modulation.

And sweep range 16000Hz - 18000Hz seemed to show the best result.

==> [Test code](./chirp)
