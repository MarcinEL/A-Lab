[![Donate](https://img.shields.io/badge/Donate-PayPal-green.svg)](https://www.paypal.com/donate?hosted_button_id=BLP3R6VGYJB4Q) 
[![Donate](https://img.shields.io/badge/Donate-Ko--fi-brightgreen?color=ff5f5f)](https://ko-fi.com/jmrplens) 
[![Sponsor](https://img.shields.io/badge/Sponsor-GitHub-brightgreen?color=ea4aaa)](https://github.com/sponsors/jmrplens) 

[![View A|Lab on File Exchange](https://www.mathworks.com/matlabcentral/images/matlab-file-exchange.svg)](https://www.mathworks.com/matlabcentral/fileexchange/93180-a-lab)

#### GitHub and code info

[![Lines of code](https://img.shields.io/tokei/lines/github/jmrplens/A-Lab?label=Lines%20of%20code&style=plastic)](https://github.com/jmrplens/A-Lab/graphs/code-frequency)
[![GitHub release (latest by date)](https://img.shields.io/github/v/release/jmrplens/A-Lab)](https://github.com/jmrplens/A-Lab/releases)
[![GitHub](https://img.shields.io/github/license/jmrplens/A-Lab)](https://github.com/jmrplens/A-Lab/blob/9708e9ecb9b033e2b26cbf3849685fe69a16ad9f/LICENSE)
![GitHub top language](https://img.shields.io/github/languages/top/jmrplens/A-Lab)
[![GitHub issues](https://img.shields.io/github/issues/jmrplens/A-Lab)](https://github.com/jmrplens/A-Lab/issues)
[![GitHub Repo stars](https://img.shields.io/github/stars/jmrplens/A-Lab?style=social)](https://github.com/jmrplens/A-Lab/stargazers)

# A|Lab
Matlab-based software for measuring acoustic parameters and experiments (**A**coustic **Lab**oratory).

This software is made up of different types of measurement (impedance tube, transmission tube, general, etc.) and expansion modules for different specific projects. The software is under development and will be adding more measure methods.

**If you find any bug in the interface or in the calculations, please inform and I will fix it as soon as possible. To feature requests or bug reports go to [Issues](https://github.com/jmrplens/A-Lab/issues).**

The software has been made with the collaboration of the [Waves in Complex Media group](https://igic.webs.upv.es/index.php?option=com_content&view=article&id=22)  located in the [Higher Polytechnic School of Gandia](https://www.upv.es/contenidos/CGANDIA/index-en.html) which is part of the [Polytechnic University of Valencia](https://www.upv.es/index-en.html), mainly giving access to equipment and instrumentation and data to be able to validate the results.

## The heart of the app
The software uses the MATLAB audio libraries, specifically, to transmit and receive simultaneously, the MATLAB <a href="https://www.mathworks.com/help/audio/ref/audioplayerrecorder-system-object.html">audioPlayerRecorder</a> object is used.

In order to make use of the <a href="https://www.mathworks.com/help/audio/ref/audioplayerrecorder-system-object.html">audioPlayerRecorder</a> object, it is necessary to have a sound card that supports 'Full-Duplex'. All external audio cards have 'Full-Duplex', but the audio cards integrated in computers, especially laptops, do not usually have support for this object. A|Lab will notify you with a window if your hardware is not compatible (image below), if you have several sound cards and one that is valid, enter the configuration and select it.

<p align="center">
<img src="https://github.com/jmrplens/A-Lab/blob/e86de2b313cba96f422deaba3747a0959e8e51cc/.github/images/noFullDuplex.png" width="40%"></img> 
</p>

If you are only opening a previous project, you can ignore the warning.

## Examples

The software allows you to save the entire measurement project to be able to open it again at any time and view or export the results. To be able to test the software quickly, some examples of impedance tube and transmission tube measurements have been included, open them from the top menu `File->Load project`.

# Table of contents

---
- [General](#general)
  - [Open the software](#open-and-use)
  - [Device setup and calibration](#device-setup-and-calibration)
  - [Signal panel](#signal-panel)
  - [Information panel](#information-panel)
- [Methods](#methods)
  - [General](#general-1)
  - [Impedance Tube \(ISO 10534-2\)](#impedance-tube-iso-10534-2)
  - [Transmission Tube \(ASTM 2611\)](#transmission-tube-astm-2611)
  - [Robot measurement \(Polytechnic University of Valencia\)](#robot-measurement-upv)
- [Standards and theory](#standards-and-theory)
- [Supports](#supports)
- [Author](#author)
---

<a id="general"></a>
# General

<a id="open-and-use"></a>
## Open the software

To open the software you have two options:

- **Source code**: download the source code and run the ALab.mlapp file with MATLAB.

- **Installable package**: to install the software within matlab download the package from [here](https://github.com/jmrplens/A-Lab/releases).

<a id="device-setup-and-calibration"></a>
## Device setup and calibration

In this panel you can configure the parameters of the audio device and calibrate the system latency (useful for correcting delays in the impulse response). Also, if you have a microphone calibrator, you can calibrate each input channel.

<p align="center">
<img src="https://github.com/jmrplens/A-Lab/blob/e86de2b313cba96f422deaba3747a0959e8e51cc/.github/images/devicepanelc.png" width="70%"></img> 
</p>

Options:
  - **Harware configuration**: Sample rate, Samples per Frame, Device driver, Audio device and Bit depth.
  - **Calibrate latency**: Selects the input and output channel and calibrates automatically.
  - **Calibrate inputs**: Select calibrator signal level, input channel and run calibration.
  - **Air parameters**: Sound speed and air density.

<a id="calibrate-latency"></a>
### Calibrate latency

All systems have an internal latency from the moment the signal is emitted until it is received. With this calibration, the latency of the system is obtained and the correction is applied in all measurements, the delay added by the system in the received signal is eliminated.

To calibrate the latency you must connect a cable directly from an output of the sound card to an input. Once connected, choose the channels where the cable is connected and press `Calibrate latency`. The software will make 5 measurements to calculate the latency of the system.

```matlab
% Latency calculation
inCalibration;  % Input signal from latency calibration measure
outCalibration; % Output signal from latency calibration measure

[cross,time] = xcorr(inCalibration,outCalibration);
[~,Midx]     = max(abs(cross)); % Find peak
Latency      = time(Midx)/fs;   % Latency time value in seconds
```

```matlab
% Latency correction
Latency;     % Latency time in seconds
sigReceived; % Signal received in a measurement from general method, impedance tube, ...

DelaySamples = floor(Latency*SampleRate);
sigReceived = [sigReceived(DelaySamples+1:end);zeros(DelaySamples,1)];
```

<a id="calibrate-inputs"></a>
### Calibrate inputs

To obtain the correct level in the measurements calculations, it is necessary to obtain a factor that applies a dimension to the input signal. This is the calibration factor and it is obtained from a source that has a known acoustic pressure. Usually a calibrator emits 94dB but if you have another type of calibrator you can indicate the reference level.

You must place the calibrator on the microphone you want to calibrate and click on `Calibrate channel N`, the calibration factor will automatically be calculated. This calibration factor is automatically applied to all measured signals.

```matlab
% Calculate calibration factor
Lcal;   % Calibrator reference value in dB (Typical: 94)
sigCal; % Measured signal with calibrator placed on the microphone

Pcal = 10^(Lcal/20) * 2e-5;                 % Calibration reference in Pa
Xrms = sqrt(sum(sigCal.^2)/length(sigCal)); % = rms(sigCal)
K = Xrms / Pcal;                            % Calibration factor
```

```matlab
% Apply calibration factor to a measured signal
K;            % Calibration factor
sigReceived;  % Signal received in a measurement from general method, impedance tube, ...

sigReceived = sigReceived ./  K; % Calibrated signal
```


<a id="signal-panel"></a>
## Signal panel
The signal selection panel appears in all types of measurements. 

<img width="225" src="https://github.com/jmrplens/A-Lab/blob/e86de2b313cba96f422deaba3747a0959e8e51cc/.github/images/sweepPanel.png"></img>

The available signals depend on the chosen measurement method. The following signals are available:
  * MLS
    ```matlab
          fs; % Sample rate
    duration; % Duration in seconds of a sequence
        nseq; % Number of sequences
        
        signal = mls(round(fs*duration+1));
        signal = repmat(signal,nseq,1);
        signal = [signal;zeros(round(fs*duration+1),1)];
    ```
  * Sweep
    ```matlab
          fs; % Sample rate
    duration; % Duration in seconds
     dur_sil; % Silence duration in seconds
        fini; % Initial frequency in hertz
        fend; % End frequency in hertz
        
        signal = sweeptone(duration,dur_sil,fs,'SweepFrequencyRange',[fini,fend]);
    ```
  * White Noise
    ```matlab
          fs; % Sample rate
    duration; % Duration in seconds
    
        signal = randn(round(duration*fs),1);
    ```
  * Pink Noise
    ```matlab
          fs; % Sample rate
    duration; % Duration in seconds
    
        signal = pinknoise(round(duration*fs));
    ```
  * Pure Tone
    ```matlab
          fs; % Sample rate
    duration; % Duration in seconds
        freq; % Tone frequency in hertz
        
        signal = cos(2*pi*freq*(0:1/fs:duration));
    ```
  * Custom signal

    If you want to use a different signal you can import it by selecting it within this panel. The signal must be in a text file with a single column, the sampling frequency will be the one defined in the software configuration.

All signals are normalized between 0.5 and -0.5: `signal = (signal/max(abs(signal)))*0.5;`.

<a id="information-panel"></a>
## Information panel
 <img align="left" height="150" src="https://github.com/jmrplens/A-Lab/blob/e86de2b313cba96f422deaba3747a0959e8e51cc/.github/images/infoPanelc.png"></img> This panel always appears in the bar on the right. It displays the relevant information of the audio device.
 If latency calibration has not been performed it shows the approximate value (`SamplesPerFrame/SampleRate*2`) but this value is not applied to signals.
 If at least one input of the device has been calibrated, the word 'Yes' and the channels numbers will be displayed in the input calibration.

<p><br /><br /></p>

---

<a id="methods"></a>
# Methods

<a id="general-1"></a>
## General

This method is only to perform some tests and see some options that MATLAB offers us, such as the calculation of common parameters in sound level meters.
**Remember: only for test.**

**Tip**: If you have calibrated the input of a microphone, take a measurement with the calibrator attached to the microphone. You can see the measured value in the 'Sound Pressure Level' panel, it must be the same as the reference value of the calibrator. This way you will check that everything is working correctly.

<p align="center">
<img src="https://github.com/jmrplens/A-Lab/blob/e86de2b313cba96f422deaba3747a0959e8e51cc/.github/images/GeneralMethod.png" width="49%"></img>
</p>

<br>

<a id="impedance-tube-iso-10534-2"></a>
## Impedance Tube (ISO 10534-2)

---
##### Table of contents
- [Tube parameters](#tube-parameters)
- [One-Mic measurement](#one-mic-measurement)
- [Two-Mic measurement](#two-mic-measurement)
  - [Calibrate the impedance tube system](#calibrate-the-impedance-tube-system)
- [Information panel](#information-panel-1)
- [Results visualization](#results-visualization)
---

The impedance tube method is detailed in <a href="https://www.iso.org/standard/22851.html">ISO 10534-2</a>. In this software the measure has been implemented in full compliance with the standard.

The steps to follow (also indicated in the standard) are:
  1. Calibrate the inputs to be used (<a href="#device-setup-and-calibration">configuration panel</a>). (_Recommended_)
  1. Enter tube parameters (distances and shape).
  1. Calibrate the impedance tube system (if you use two microphones). (_Recommended_)
  1. Measure.
 
The results obtained are: reflection factor, sound absorption coefficient, specific acoustic impedance, impulse response (two inputs) and frequency response (two inputs).

The side panels that appear in this measurement method are:
* **Left panel**: Here you can open the tube parameter configuration panel, measure with one or two microphones or review the information of the measurement method.
* **Right panel**: Here you can choose the type of signal and its parameters. You will also have information about the tube and the audio hardware.

|Left panel|Right panel|
|:-------:|:------:|
|<img src="https://github.com/jmrplens/A-Lab/blob/e86de2b313cba96f422deaba3747a0959e8e51cc/.github/images/ImpedanceTubeLeftc.png" width="50%"></img>|<img src="https://github.com/jmrplens/A-Lab/blob/e86de2b313cba96f422deaba3747a0959e8e51cc/.github/images/ImpedanceTubeRightc.png" width="50%"></img>|


<a id="tube-parameters"></a>
### Tube parameters

Enter in this panel the geometric parameters of the tube: shape, distance between microphones and distance to the sample. Make sure all distances are measured correctly. Also do not forget to indicate the shape of the tube.

<p align="center">
<img src="https://github.com/jmrplens/A-Lab/blob/e86de2b313cba96f422deaba3747a0959e8e51cc/.github/images/ImpedanceTubeParametersc.png" width="49%"></img>
</p>

<a id="one-mic-measurement"></a>
### One-Mic measurement

If you only have one microphone, you can perform the measurement through this panel.
First place the microphone in position [A] and press `Measure [A]`, then move the microphone to position [B] and press `Measure [B]`. Finally, click on the button `Calculate results` in the right panel.

<p align="center">
<img src="https://github.com/jmrplens/A-Lab/blob/e86de2b313cba96f422deaba3747a0959e8e51cc/.github/images/ImpedanceTubeOneMicc.png" width="49%"></img>
</p>

<a id="two-mic-measurement"></a>
### Two-Mic measurement

To measure with two microphones simply place the microphones in position and press `Measure`.
You can also calibrate the system by pressing `Calibrate (optional)`.
Once the measurement is finished, press the button `Calculate results` in the right panel. If you have calibrated the tube, the software will ask you if you want to use the calibration.

<p align="center">
<img src="https://github.com/jmrplens/A-Lab/blob/e86de2b313cba96f422deaba3747a0959e8e51cc/.github/images/ImpedanceTubeTwoMicc.png" width="49%"></img>
</p>

<a id="calibrate-the-impedance-tube-system"></a>
#### Calibrate the impedance tube system

To calibrate the system, two measurements must be made, one with the normal microphone position, and other one with the swapped position.
It is also advisable to have an absorbent material to slightly reduce reflections.
The figures indicate the position of the microphones for each measurement, make sure they are located correctly before measuring. Once the two measurements are finished, click on `Return`.

<p align="center">
<img src="https://github.com/jmrplens/A-Lab/blob/dbb199b9b21f8d9c1c09294dc0a1d02a9d33d509/.github/images/ImpedanceTubeTwoMicCalc.png" width="49%"></img>
</p>

<a id="information-panel-1"></a>
### Information panel
In this panel you will see a summary of the measurement method and the limitations according to the tube parameters.

<p align="center">
<img src="https://github.com/jmrplens/A-Lab/blob/dbb199b9b21f8d9c1c09294dc0a1d02a9d33d509/.github/images/ImpedanceTubeInformationc.png" width="49%"></img>
</p>

<a id="results-visualization"></a>
### Results visualization

In the results panel you can choose the resolution of the FFT and see the standard results.
You can also export the results in text file to use it in another software, also export the graphs (PNG, JPG, PDF, EPS or FIG) or see the impulse and frequency responses of the measured signals (`Other results` button).

<p align="center">
<img src="https://github.com/jmrplens/A-Lab/blob/dbb199b9b21f8d9c1c09294dc0a1d02a9d33d509/.github/images/ImpedanceTubeResultsc.png" width="49%"></img>
</p>

<br>

<a id="transmission-tube-astm-2611"></a>
## Transmission Tube (ASTM 2611)

---
##### Table of contents
- [Tube parameters](#tube-parameters-1)
- [One-Mic measurement](#one-mic-measurement-1)
- [Two-Mic measurement](#two-mic-measurement-1)
- [Four-Mic measurement](#four-mic-measurement)
  - [Calibrate the transmission tube system](#calibrate-the-transmission-tube-system)
- [Measure information](#measure-information)
- [Results visualization](#results-visualization-1)
  - [Single results visualization](#single-results-visualization-1)
---

The transmission tube method is detailed in <a href="https://www.astm.org/Standards/E2611.htm">ASTM 2611</a>. In the software the method is implemented to be able to use one, two or four microphones. It is necessary to have a transmission tube with 4 microphone positions, two on the upstream side and two on the downstream side.

If the sample to be analyzed is symmetrical, only an anechoic end of the tube is necessary. If it is not symmetric, it will be necessary to measure with anechoic end and with rigid end.

The steps to follow (also indicated in the standard) are:
  1. Calibrate the inputs to be used (<a href="#device-setup-and-calibration">configuration panel</a>). (_Recommended_)
  1. Enter tube parameters (distances and shape).
  1. Calibrate the impedance tube system (if you use four microphones). (_Recommended_)
  1. Measure with anechoic end and, if not symmetrical sample, repeat measure with rigid end.
 
The results obtained are: transmission loss, characteristic impedance, transmission factor, reflection factor, propagation wavenamber in material, sound absorption coefficient, impulse response and frequency response (four inputs) for anechoic and rigid end measure (if not symmetrical).

The side panels that appear in this measurement method are:
* **Left panel**: Here you can open the tube parameter configuration panel, measure with one, two or four microphones or review the information of the measurement method.
* **Right panel**: Here you can choose the type of signal and its parameters. You will also have information about the tube and the audio hardware.

|Left panel|Right panel|
|:-------:|:------:|
|<img src="https://github.com/jmrplens/A-Lab/blob/dbb199b9b21f8d9c1c09294dc0a1d02a9d33d509/.github/images/TransmissionTubeLeft.png" width="50%"></img>|<img src="https://github.com/jmrplens/A-Lab/blob/dbb199b9b21f8d9c1c09294dc0a1d02a9d33d509/.github/images/TransmissionTubeRightc.png" width="50%"></img>|

<a id="tube-parameters-1"></a>
### Tube parameters
The panel shown here is used to enter the geometric parameters of the tube. Make sure all distances are measured correctly. Also do not forget to indicate the shape of the tube.

<p align="center">
<img src="https://github.com/jmrplens/A-Lab/blob/dbb199b9b21f8d9c1c09294dc0a1d02a9d33d509/.github/images/TransmissionTubeParams.png" width="49%"></img>
</p>

<a id="one-mic-measurement-1"></a>
### One-Mic measurement
If you only have one microphone, it is possible to perform the measurement by placing the microphone in each of the measurement positions.

Every time a measurement is made the graph is updated to show which parts have already been measured. Also, if you are not sure that you have carried out the measurement correctly, you can delete it and do it again.

<p align="center">
<img src="https://github.com/jmrplens/A-Lab/blob/dbb199b9b21f8d9c1c09294dc0a1d02a9d33d509/.github/images/TransmissionTube1mic.png" width="49%"></img>
</p>

<a id="two-mic-measurement-1"></a>
### Two-Mic measurement
If you have two microphones you can perform the measurement faster than the one microphone option. The operation is the same, except that now two input channels are chosen, one for each microphone.

The first measurement is made with the microphones in positions `A` and `B` (upstream) and the second in `C` and `D` (downstream), that is why microphone 1 corresponds to position `A` and `C`, and microphone 2 to position `B` and `D`.

<p align="center">
<img src="https://github.com/jmrplens/A-Lab/blob/dbb199b9b21f8d9c1c09294dc0a1d02a9d33d509/.github/images/TransmissionTube2mic.png" width="49%"></img>
</p>

<a id="four-mic-measurement"></a>
### Four-Mic measurement
This is the best option, it reduces errors when manipulating the position of the microphones obtaining better results (although it is not a big difference).

This option also allows you to calibrate the microphones before making the measurement.
<p align="center">
<img src="https://github.com/jmrplens/A-Lab/blob/dbb199b9b21f8d9c1c09294dc0a1d02a9d33d509/.github/images/TransmissionTube4mic.png" width="49%"></img>
</p>

<a id="calibrate-the-transmission-tube-system"></a>
#### Calibrate the transmission tube system

To calibrate the transmission tube, it is necessary to perform 4 measurements:
1. Take a measurement first with all four microphones in their normal positions. (Microphones order: [1,2,3,4])
2. Swap the position of microphone 1 with microphone 2 (leave the other microphones in their normal position) and measure. (Microphones order: [2,1,3,4])
3. Put the microphone 2 back to its position, put the microphone 1 in the position of the microphone 3 and the microphone 3 in the position of the microphone 1. Measure. (Microphones order: [3,2,1,4])
4. Put the microphone 3 back to its position, put the microphone 1 in the position of the microphone 4 and the microphone 4 in the position of the microphone 1. Measure. (Microphones order: [4,2,3,1])

Once all the measurements are finished, click on `Done`.

<p align="center">
<img src="https://github.com/jmrplens/A-Lab/blob/dbb199b9b21f8d9c1c09294dc0a1d02a9d33d509/.github/images/TransmissionTube4micCal.png" width="49%"></img>
</p>

<a id="measure-information"></a>
### Measure information
In this panel you will see some information about the measurement method.

<p align="center">
<img src="https://github.com/jmrplens/A-Lab/blob/dbb199b9b21f8d9c1c09294dc0a1d02a9d33d509/.github/images/TransmissionTubeInformation.png" width="49%"></img>
</p>

<a id="results-visualization-1"></a>
### Results visualization
When you press the button to calculate results, you will see this panel with the different results defined by the standard. If you click on any of the graphs you can see it larger.

Also, if you press the button for other results, you will be able to see the impulse and frequency response of all the measurements.

In the main results panel you can choose to export all the results in text file, export the **transfer matrix** (T11,T12,T21,T22) in text file for your own calculations or if you click on any graph you go to: [Single result](#single-results-visualization-1).

<p align="center">
<img src="https://github.com/jmrplens/A-Lab/blob/dbb199b9b21f8d9c1c09294dc0a1d02a9d33d509/.github/images/TransmissionTubeResults.png" width="49%"></img>
</p>

<a id="single-results-visualization-1"></a>
#### Single results visualization

In this panel you will be able to see the larger graph and you will also be able to export only that result to a text file or graphic file. 
In addition, in the case of absorption and reflection, by clicking on their graphs you will see both the hard-backed calculation that defines the ASTM 2611 standard and the anechoic-backed calculation (from transfer matrix method).

<img src="https://github.com/jmrplens/A-Lab/blob/dbb199b9b21f8d9c1c09294dc0a1d02a9d33d509/.github/images/TransmissionTubeSingleTrans.png" width="32%"></img>
<img src="https://github.com/jmrplens/A-Lab/blob/dbb199b9b21f8d9c1c09294dc0a1d02a9d33d509/.github/images/TransmissionTubeSingleReflection.png" width="32%"></img>
<img src="https://github.com/jmrplens/A-Lab/blob/dbb199b9b21f8d9c1c09294dc0a1d02a9d33d509/.github/images/TransmissionTubeSingleAbsorption.png" width="32%"></img>

<br>

<a id="robot-measurement-upv"></a>
## Robot measurement (Polytechnic University of Valencia)

This measurement method is exclusive to the [Polytechnic University of Valencia](https://www.upv.es/index-en.html). It performs acoustic measurements by using a three-axis robot for automated measurements in a plane or volume. A page dedicated to this method will be included soon to facilitate use by students and researchers at the [Polytechnic University of Valencia](https://www.upv.es/index-en.html).

---

<a id="standards-and-theory"></a>
# Standards and theory

Here is a table of the sources used for the implementation of some of the measures developed in the software.

| Measure | Source | M-File |
|:-------:|:------:|:------:|
| Impedance tube | <a href="https://www.iso.org/standard/22851.html">ISO 10534-2:1998</a>   | [`calculationsTwoMicISO105342.m`](https://github.com/jmrplens/A-Lab/blob/a1b295f4ee735112b9b5fa40cdbf009617be4e14/include/Measurements/Impedance%20Tube%20-%20ISO%2010534-2/calculationsTwoMicISO105342.m)|
| Transmission tube | <a href="https://www.astm.org/Standards/E2611.htm">ASTM 2611 - 19</a> | [`calculationsFourMicASTM2611.m`](https://github.com/jmrplens/A-Lab/blob/a1b295f4ee735112b9b5fa40cdbf009617be4e14/include/Measurements/Transmission%20Tube%20-%20ASTM%202611/calculationsFourMicASTM2611.m) |

---

<a id="supports"></a>
# Supports

<a href="https://www.upv.es"><img align="Left" height="100" src="https://github.com/jmrplens/A-Lab/blob/dbb199b9b21f8d9c1c09294dc0a1d02a9d33d509/.github/images/LogoUPV2.png"></img></a><a href="https://www.gandia.upv.es/"><img align="Left" height="100" src="https://github.com/jmrplens/A-Lab/blob/dbb199b9b21f8d9c1c09294dc0a1d02a9d33d509/.github/images/LogoEPSG2.png"></img></a><a href="https://igic.webs.upv.es/"><img align="Left" height="100" src="https://github.com/jmrplens/A-Lab/blob/dbb199b9b21f8d9c1c09294dc0a1d02a9d33d509/.github/images/LogoIGICb.png"></img></a>***Thanks to the [Waves in Complex Media group](https://igic.webs.upv.es/index.php?option=com_content&view=article&id=22), [Higher Polytechnic School of Gandia](https://www.upv.es/contenidos/CGANDIA/index-en.html) and [Polytechnic University of Valencia](https://www.upv.es/index-en.html) for providing the necessary equipment to develop some of the measures implemented in the software and for all the support in time, meet, talk and discussions.***

<p><br /></p>
Special mention for <a href="https://www.linkedin.com/in/gabrielebunkheila/">Gabriele Bunkheila</a> and <a href="https://www.linkedin.com/in/carsanbo/">Carlos Sanchis</a> for helping me and answer my questions with MATLAB, those long email threads are very valuable.

---

<a id="author"></a>
# Author
Jose M. Requena Plens, 2021. (<a href="https://jmrplens.github.io/">jmrplens.github.io</a>)

[![Donate](https://img.shields.io/badge/Donate-PayPal-green.svg)](https://www.paypal.com/donate?hosted_button_id=BLP3R6VGYJB4Q)
[![Donate](https://img.shields.io/badge/Donate-Ko--fi-brightgreen?color=ff5f5f)](https://ko-fi.com/jmrplens)
[![Sponsor](https://img.shields.io/badge/Sponsor-GitHub-brightgreen?color=ea4aaa)](https://github.com/sponsors/jmrplens) 
