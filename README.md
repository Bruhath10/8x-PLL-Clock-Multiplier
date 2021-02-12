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
