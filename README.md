# 8x-PLL-Clock-Multiplier
 
## Contents
- Introduction
- Pre-layout simulations

## Introduction

#### PLL

A PLL (Phase Locked Loop) is a control system which outputs a signal whose frequency and phase are locked in a constant relation to that of a reference signal, using a feedback
mechanism. PLLs are mainly used in radio, telecommunications and computers as frequency synthesizers, FM and AM demodulators etc.

![000](https://user-images.githubusercontent.com/44549567/109112763-29197780-7761-11eb-965b-0a0ddd23fac4.JPG)

A PLL consists of a PFD (Phase Frequency Detector), a CP (Charge Pump), a Low pass filter, a VCO (Voltage Controlled Oscillator), and a Frequency divider.

A PFD is a circuit made of transistors which takes the reference signal and the feedback output signal as inputs and compares them for phase difference. It generates signals UP (if output lags ref. signal) and DN (if output leads ref. signal), proportional to the phase difference which are then given as inputs to the Charge Pump (CP).

The Charge Pump is a three way switching circuit with a capacitor, which takes the UP and DN signals as inputs, and depending on which is high among UP and DN, corresponding switch is turned on and the capacitor is either charged or discharged. The voltage accross the capacitor is the output or the control signal to the VCO.

The Low Pass Filter (LPF) is used in Phase Locked Loops (PLL) to get rid of the high frequency components from the output of the phase detector. It also removes the high frequency noise and helps control the dynamic characteristics of the whole circuit.

The VCO is a circuit which takes a control voltage signal as input and generates a waveform whose frequency can be controlled by changing the control voltage. The relation between the output frequency and the voltage is approximately linear for small ranges.

The frequency divider converts one frequency waveform to a waveform whose frequency is a multiple of the input frequency. This can be done using the concepts of feedback along with a D-flip flop for even dividers and an additional use of a mod-n counter for odd dividers.

#### Process Corners:

In VLSI system design, a process corner refers to variation of parameters from nominal values of a transistor on a silicon wafer as a part of DoE (Design of Experiments). The wafers are skewed to different corners to check the robustness of the design in the extremes.

The process corners are generally characterized with two-letter notation, where 1st letter corresponds to the NMOS character and the second letter corresponds to the PMOS character. There are five corners: typical-typical (TT), fast-fast (FF), slow-slow (SS), slow-fast (SF), and fast-slow (FS). Here, in the two letter notation the first letter characterizes the NMOS behaviour and the second letter characterizes the PMOS behaviour. Ex. 'FS' denotes NMOS is Fast and PMOS is Slow. The TT, FF, SS corners are even corners as both the MOS are affected evenly, and the circuit is not adversely affected by them. The FS and SF corners are Skewed corners and affect the performance.

#### Review of the existing PLL:

An 8x PLL clock multiplier IP with an input frequency range of 5Mhz to 12.5Mhz and output frequency range of 40Mhz to 100Mhz was designed and implemented on TT corners by Lakshmi Sathi with the help of Google / SkyWater 130nm technology.

The PFD used was designed using only 8 transistors, minimum area, and no dead-zone (the range of phase difference which the PFD cannot differentiate - considers it as zero). In regular CPs, the drain of NMOS tries to change the voltage, resulting in different Up and Down currents, hence an extra Op-Amp was added, which forces the voltage to be constant and regulates the manipulation of drain, resulting in equal currents (an ideal CP). The VCO was realized by the concept of ring oscillator, chain of inverters connected in series. The frequency divider was realized by using the concept of D-flip flop with negation of output as feedback to input. A frequency divider of 8 can be achieved by cascading multiple dividers. An effective circuit of D-flip flop with lesser transistors to reduce the delay was used.


## Pre-layout simulations

Taking up an existing PLL (made by Lakshmi Sathi), which was implemented on 'tt' corners, I have replaced the 'tt' transistors with 'fs' transistors.
The PLL_prelay.cir file (the pre-layout spice/circuit file) contains the details of the instances of the PFD (Phase Frequency Detector), CP (Charge Pump), VCO (Voltage Controlled Oscillator), FD (Frequency Divider), transistors used and their W, L values, simulation commands, and the liberty file (Sky130nm.lib)

The Sky130nm.lib contained the spice files for 'tt' transistors.

![0](https://user-images.githubusercontent.com/44549567/107802602-9273b080-6d87-11eb-952d-a1bb08166d02.JPG)

I changed the 'tt' transistor spice files to 'fs' transistor spice files

![1](https://user-images.githubusercontent.com/44549567/107802854-df578700-6d87-11eb-819b-570ae1a1076b.JPG)

Now the transistor sizes (W and L values) must be modified as per the 'fs' transistors. The corner spice models are present in the following directory of the Sky130 primitives

![3](https://user-images.githubusercontent.com/44549567/107804234-ac15f780-6d89-11eb-8a98-54c46f9d73ac.JPG)

All the nfet cells in fs.corner.spice whose W and L values are close to the current 'tt' transistors in PLL_prelay.cir

![4](https://user-images.githubusercontent.com/44549567/107804666-28a8d600-6d8a-11eb-891e-6d736e967eb9.JPG)

Replacing the W and L values and running the simulation in ngspice.
Ref. clock - 12.5 MHz

![5](https://user-images.githubusercontent.com/44549567/107806290-a372f080-6d8c-11eb-8a2f-76903d62dd64.JPG)

The frequency of the ref. clock equals the frequency of the clock output divided by 8

![6](https://user-images.githubusercontent.com/44549567/107808170-272ddc80-6d8f-11eb-81e7-0c61be91d743.JPG)

zoomed

![7](https://user-images.githubusercontent.com/44549567/107808999-76c0d800-6d90-11eb-91d0-1b20e2a5bf2a.JPG)

PFD Output

![PD_mod1 cir output](https://user-images.githubusercontent.com/44549567/108028652-2ed0d800-7052-11eb-93bd-fd1a65b758ac.JPG)

Charge Pump output

![CP_mod1 cir output](https://user-images.githubusercontent.com/44549567/108028884-88390700-7052-11eb-88fa-599bd994fe4c.JPG)

VCO Output

![vco_mod1 cir output](https://user-images.githubusercontent.com/44549567/108028934-9c7d0400-7052-11eb-9c0b-7300844cc8c8.JPG)

Frequency divider output

![FD_mod1 cir output](https://user-images.githubusercontent.com/44549567/108028962-a999f300-7052-11eb-82b9-887b6d777ca8.JPG)

## Analysis and next steps
From the results which I got above, I can say that after the control voltage rises and starts saturating, the clk_out and outputs of the Phase detector (UP and DN signals) are getting terminated or becoming zero, and the 'clk_out by 8' signal is becoming a sharp triangular waveform unlike the square pulse we require.

From the outputs of the individual blocks (PD.cir, VCO.cir, CP.cir, and FD.cir), we can notice that the Phase differentiator output is not correctly detecting the difference between the phases of the reference and the output feedback signal. Hence the problem lies in the PD.cir.

![PD_mod1 cir output](https://user-images.githubusercontent.com/44549567/108028652-2ed0d800-7052-11eb-93bd-fd1a65b758ac.JPG)

We need to modify the PD.cir, i.e we need to change the sizes of the transistors in PD.cir as larger slew might be an issue. We keep the length of the PMOs same and increase the width of PMOS and see whether we get correct results.

The pfets and nfets in the PD.cir are as shown:

![0](https://user-images.githubusercontent.com/44549567/108541670-bfd0d900-7308-11eb-94f7-b3e6aecd3c0b.JPG)

The models of pfets are present in sky130_fd_pr__pfet_01v8__fs_discrete.corner.spice

The maximum width available for a length of 0.15u is W = 7.0u or 7000n

![1](https://user-images.githubusercontent.com/44549567/108542110-543b3b80-7309-11eb-8501-8b213120aa7a.JPG)

Replacing the 'XM4' pfet instance width from w=640n to w=7000n

![2](https://user-images.githubusercontent.com/44549567/108543356-f7408500-730a-11eb-99d3-1d4bc365d402.JPG)

The output of the PD.cir is as follows:

![3](https://user-images.githubusercontent.com/44549567/108543586-48507900-730b-11eb-8a0d-f43b97fde834.JPG)

![4](https://user-images.githubusercontent.com/44549567/108543753-7d5ccb80-730b-11eb-8bb7-df7232a84cbc.JPG)

After increasing the width of each instance and checking, the correct output was achieved with the increase in the width of instance 'XM4' pfet. Hence the PD output (UP and DN signals) showed significantly better results after the pfet width was increased.

The PLL output after modification in the PD part

![5](https://user-images.githubusercontent.com/44549567/108555929-34614300-731c-11eb-9d37-b875d764ff72.JPG)

![6](https://user-images.githubusercontent.com/44549567/108556351-e39e1a00-731c-11eb-87b1-ab4f4db3dc00.JPG)

The output is much better than the output before the modification of PD. It still needs some changes to get the correct output. 

According to the above result, the charge pump needs to be modified to get the proper control voltage.






