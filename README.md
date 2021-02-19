# 8x-PLL-Clock-Multiplier
 
## Contents
- Pre-layout simulations

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

## Analysis
From the results which I got above, I can say that after the control voltage rises and starts saturating, the clk_out and outputs of the Phase detector (UP and DN signals) are getting terminated or becoming zero, and the 'clk_out by 8' signal is becoming a sharp triangular waveform unlike the square pulse we require.

From the outputs of the individual blocks (PD.cir, VCO.cir, CP.cir, and FD.cir), we can notice that the Phase differentiator output is not correctly detecting the difference between the phases of the reference and the output feedback signal. Hence the problem lies in the PD.cir.

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

Hence the PD output (UP and DN signals) showed significantly better results after the pfet width was increased.









