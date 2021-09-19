
<p align="center">
 <img src="https://user-images.githubusercontent.com/52507285/133930875-da812f23-5c2d-44b7-aa9f-d35738cd0161.png" alt="The Documentation Compendium"></a>
</p>


<h3 align="center">Charge Pump Phase Locked Loop </h3>

<div align="center">

</div>

## Table of Contents

- [What is a PLL ?](#PLL)
- [Specifications](#Specs)
- [Tools Used](#Tools)
- [PDK Used](#PDK)
- [SPICE Netlists and Pre Layout Simulations](#Netlist)
  - [Phase Frequency Detector](#PFD)
  - [Charge Pump](#CP)
  - [Voltage Controlled Oscillator](#VCO)
  - [Frequency Divider](#FD)
  - [Complete PLL](#CPLL)
  
- [Layout Designs](#layout)
  - [Phase Frequency Detector](#PFDL)
  - [Charge Pump](#CPL)
  - [Voltage Controlled Oscillator](#VCOL)
  - [Frequency Divider](#FDL)
  - [Complete PLL](#CPLLL)

- [Acknowledgements](#acknowledgements)

## What is PLL? <a name = "PLL"></a>
PLL is a feedback system which synchronises the output oscillations of the VCO to that of the reference (Generally Crystal Oscillator). It does so by minimizing the rate of change of phase error between the two oscillators. The phase detector compares the phase error between the two oscillators and convert it into a voltage proportional to it. This voltage is further fed to a Low pass filter (Charge Pump + Loop filter in our case) which averages it out and send it to the VCO’s control input. The VCO changes its output frequency in accordance to it, thus in turn minimizing the phase error. This process goes on iteratively till the frequencies of the two oscillators are matched. If we want to obtain a multiple of reference frequency, A divide by N circuit is placed in the feedback loop. If we do so the output will be N* ωref.
## Specifications <a name = "Specs"></a>
| Parameter | Value|
| --- | --- |
| VDD | 1.8 v | 
| F<sub>CLKREF</sub> | 5 MHz- 12.5 MHz|
| F<sub>CLKOUT</sub> | 40 MHz- 100 MHz |  
| J<sub>RMS</sub> | < 20ns  |
| Duty Cycle | 50% | 
| Modes| PLL mode and VCO mode |
| Corner | TT | 
| Temperature | Room Temperature | 

## Tools Used <a name = "Tools"></a>
- [Ngspice](http://ngspice.sourceforge.net/download.html) : Simulation
- [Magic](http://opencircuitdesign.com/magic/) : Layout Design
## PDK Used <a name = "PDK"></a>
[Google_skywater](https://github.com/google/skywater-pdk) was used in this project.
## SPICE Netlists and Pre Layout Simulations <a name = "Netlist"></a>
### Phase Frequency Detector <a name = "PFD"></a>
```
*PD_10T
.include spice_lib/sky130.lib

xm1 1 clk1 3 1 sky130_fd_pr__pfet_01v8 l=150n w=640n
xm2 3 clk1 4 0 sky130_fd_pr__nfet_01v8 l=150n w=1800n
xm3 4 clk2 0 0 sky130_fd_pr__nfet_01v8 l=150n w=360n

xm4 1 clk2 6 1 sky130_fd_pr__pfet_01v8 l=150n w=640n
xm5 6 clk2 7 0 sky130_fd_pr__nfet_01v8 l=150n w=1800n
xm6 7 clk1 0 0 sky130_fd_pr__nfet_01v8 l=150n w=360n

xm7 8 clk1 3 0 sky130_fd_pr__nfet_01v8 l=150n w=840n
xm8 clk1 clk1 8 1 sky130_fd_pr__pfet_01v8 l=150n w=640n

xm11 upb 8 1 1 sky130_fd_pr__pfet_01v8 l=150n w=720n
xm12 upb 8 0 0 sky130_fd_pr__nfet_01v8 l=150n w=360n

xm15 up upb 1 1 sky130_fd_pr__pfet_01v8 l=150n w=960n
xm16 up upb 0 0 sky130_fd_pr__nfet_01v8 l=150n w=480n

xm9 9 clk2 6 0 sky130_fd_pr__nfet_01v8 l=150n w=840n
xm10 clk2 clk2 9 1 sky130_fd_pr__pfet_01v8 l=150n w=640n

xm13 downb 9 1 1 sky130_fd_pr__pfet_01v8 l=150n w=720n
xm14 downb 9 0 0 sky130_fd_pr__nfet_01v8 l=150n w=360n

xm17 down downb 1 1 sky130_fd_pr__pfet_01v8 l=150n w=960n
xm18 down downb 0 0 sky130_fd_pr__nfet_01v8 l=150n w=480n


*output cap
c1 up 0 3f
c2 down 0 3f

*sources
v1 1 0 1.8v
v2 clk1 0 pulse(0 1.8 0 6p 6p 5ns 10ns)
v3 clk2 0 pulse(0 1.8 6ns 6p 6p 5ns 10ns)

*simulation
.control
tran 10ns 800ns 120ns
plot v(clk2)+4 v(clk1)+4 v(up)+2 v(down)
.endc
.end
```
#### Output
<p align="center">
 <img src="https://user-images.githubusercontent.com/52507285/133932662-77b52704-61d5-4c20-aa89-6592bb24b09a.png" >
</p>
                                                                                                                   

### Charge Pump<a name = "CP"></a>
                             
```
*CP

.include spice_lib/sky130.lib

xm43 3 2 1 1 sky130_fd_pr__pfet_01v8 l=150n w= 5.4u
xm44 out downb 3 1 sky130_fd_pr__pfet_01v8 l=150n w=420n
xm31 out up 7 0 sky130_fd_pr__nfet_01v8 l=150n w=420n
xm32 7 8 0 0 sky130_fd_pr__nfet_01v8 l=150n w=5.4u

xm33 2 2 1 1 sky130_fd_pr__pfet_01v8 l=150n w=420n
xm34 8 8 0 0 sky130_fd_pr__nfet_01v8 l=150n w=420n

xm35 9 down 3 1 sky130_fd_pr__pfet_01v8 l=150n w=5400n
xm36 9 9 0 0 sky130_fd_pr__nfet_01v8 l=150n w=420n

xm37 10 10 1 1 sky130_fd_pr__pfet_01v8 l=150n w=420n
xm38 10 upb 7 0 sky130_fd_pr__nfet_01v8 l=150n w=5400n

xm39 1 down downb 1 sky130_fd_pr__pfet_01v8 l=150n w=720n
xm40 0 down downb 0 sky130_fd_pr__nfet_01v8 l=150n w=360n

xm41 1 up upb 1 sky130_fd_pr__pfet_01v8 l=150n w=720n
xm42 0 up upb 0 sky130_fd_pr__nfet_01v8 l=150n w=360n
v1 1 0 1.8
v2 up 0 PULSE 0 1.8 1n 6p 6p 100ns 200ns
v3 down 0 0

r1 out rc 200
c1 rc 0 64f
c2 out 0 10f

.ic v(out)=0
.ic c(out)=0
.control
tran 1ns 20us
plot v(out) c(out)
*plot v(6) V(Clk)+2
*v(d) v(Clk) v(6)
.endc
```
#### Output
<p align="center">
 <img src="https://user-images.githubusercontent.com/52507285/133932737-c3bde9d6-42f7-42eb-8731-a6780adba7e9.png" >
</p>
   
 
### Voltage Controlled Oscillator<a name = "VCO"></a>

```
*Current starved 3 stage VCo
.include spice_lib/sky130.lib

xm1 10 16 3 10 sky130_fd_pr__pfet_01v8 l=150n w=420n
xm2 3 16 9 9 sky130_fd_pr__nfet_01v8 l=150n w=420n

xm3 10 3 4 10 sky130_fd_pr__pfet_01v8 l=150n w=420n
xm4 4 3 9 9 sky130_fd_pr__nfet_01v8 l=150n w=420n

xm5 10 4 12 10 sky130_fd_pr__pfet_01v8 l=150n w=420n
xm6 12 4 9 9 sky130_fd_pr__nfet_01v8 l=150n w=420n

xm11 10 12 13 10 sky130_fd_pr__pfet_01v8 l=150n w=420n
xm12 13 12 9 9 sky130_fd_pr__nfet_01v8 l=150n w=420n

xm13 10 13 14 10 sky130_fd_pr__pfet_01v8 l=150n w=420n
xm14 14 13 9 9 sky130_fd_pr__nfet_01v8 l=150n w=420n

xm15 10 14 15 10 sky130_fd_pr__pfet_01v8 l=150n w=420n
xm16 15 14 9 9 sky130_fd_pr__nfet_01v8 l=150n w=420n

xm17 10 15 16 10 sky130_fd_pr__pfet_01v8 l=150n w=420n
xm18 16 15 9 9 sky130_fd_pr__nfet_01v8 l=150n w=420n

xm7 10 5 1 1 sky130_fd_pr__pfet_01v8 l=150n w=1080n
xm8 5 5 1 1 sky130_fd_pr__pfet_01v8 l=150n w=840n
xm9 5 in 0 0 sky130_fd_pr__nfet_01v8 l=150n w=840n
xm10 9 in 0 0 sky130_fd_pr__nfet_01v8 l=150n w=1080n

xm19 1 16 11 1 sky130_fd_pr__pfet_01v8 l=150n w=1080n
xm20 11 16 0 0 sky130_fd_pr__nfet_01v8 l=150n w=540n

*c1 11 0 10f
v1 1 0 1.8
v2 in 0 0.6

.control
tran 0.1ns 0.5us
plot v(in) v(11)
*setplot tran1
*linearize v(14)
*set specwindow=blackman
*fft v(14)
*dc v2 0 1.2 0.01
*plot mag(v(14))
.endc
.end 
```
#### Output
<p align="center">
 <img src="https://user-images.githubusercontent.com/52507285/133932925-739311b2-8046-4695-bb32-e7915f54ea2a.png" >
</p>


### Frequency Divider<a name = "FD"></a>
```
*FD
.include spice_lib/sky130.lib

xm1 1 2 3 1 sky130_fd_pr__pfet_01v8 l=150n w=720n
xm2 0 2 3 0 sky130_fd_pr__nfet_01v8 l=150n w=360n

xm3 3 Clkb 4 1 sky130_fd_pr__pfet_01v8 l=150n w=420n
xm4 3 Clk 4 0 sky130_fd_pr__nfet_01v8 l=150n w=840n

xm7 1 4 5 1 sky130_fd_pr__pfet_01v8 l=150n w=720n
xm8 0 4 5 0 sky130_fd_pr__nfet_01v8 l=150n w=360n

xm9 5 Clk 6 1 sky130_fd_pr__pfet_01v8 l=150n w=420n
xm10 5 Clkb 6 0 sky130_fd_pr__nfet_01v8 l=150n w=640n

xm11 1 6 2 1 sky130_fd_pr__pfet_01v8 l=150n w=720n
xm12 0 6 2 0 sky130_fd_pr__nfet_01v8 l=150n w=360n

xm13 1 Clk Clkb 1 sky130_fd_pr__pfet_01v8 l=150n w=720n
xm14 0 Clk Clkb 0 sky130_fd_pr__nfet_01v8 l=150n w=360n

v1 1 0 1.8
v2 Clk 0 PULSE 0 1.8 1n 6p 6p 5ns 10ns

c1 6 0 10f
.control
tran 0.1ns 0.2us
plot v(6) v(clk)+2
.endc

.end
```
#### Output
<p align="center">
 <img src="https://user-images.githubusercontent.com/52507285/133932774-52c90389-2868-4789-8f57-b772413b2e0a.png" >
</p>

### Complete PLL <a name = "CPLL"></a>

```
*PLL
.include spice_lib/sky130.lib

xx1 Clk_Ref Clk_Out_by_8 up down pd
xx2 up down VCtrl cp

*Loop Filter
r1 VCtrl rc1 490
c1 rc1 0 355f
r2 rc1 rc2 490
c2 rc2 0 350f
r3 rc2 rc3 490
c3 rc3 0 345f

xx3 rc3 Clk_Out vco

xx4 Clk_Out Clk_Out_by_2 fd
xx5 Clk_Out_by_2 Clk_Out_by_4 fd
xx6 Clk_Out_by_4 Clk_Out_by_8 fd

v1 Clk_Ref 0 PULSE 0 1.8 0 6ps 6ps 40ns 80ns

.ic v(VCtrl) = 0
.ic v(Clk_Out_by_2) = 0
.ic v(Clk_Out_by_4) = 1.8
.ic v(Clk_Out_by_8) = 0
.control
tran 0.2ns 180us
plot v(Clk_Ref) v(Clk_Out_by_8) v(Clk_Out_by_4)+2 v(Clk_Out_by_2)+4 v(Clk_Out)+6 v(up)+8 v(down)+10 v(VCtrl)+12
.endc

*PFD
.subckt pd Clk1 Clk2 up down 
xm1 1 clk1 3 1 sky130_fd_pr__pfet_01v8 l=150n w=640n 
xm2 3 clk1 4 0 sky130_fd_pr__nfet_01v8 l=150n w=1800n
xm3 4 clk2 0 0 sky130_fd_pr__nfet_01v8 l=150n w=360n

xm4 1 clk2 6 1 sky130_fd_pr__pfet_01v8 l=150n w=640n 
xm5 6 clk2 7 0 sky130_fd_pr__nfet_01v8 l=150n w=1800n
xm6 7 clk1 0 0 sky130_fd_pr__nfet_01v8 l=150n w=360n

xm7 8 clk1 3 0 sky130_fd_pr__nfet_01v8 l=150n w=2400n 
xm8 clk1 clk1 8 1 sky130_fd_pr__pfet_01v8 l=150n w=640n

xm11 upb 8 1 1 sky130_fd_pr__pfet_01v8 l=150n w=720n
xm12 upb 8 0 0 sky130_fd_pr__nfet_01v8 l=150n w=360n

xm15 up upb 1 1 sky130_fd_pr__pfet_01v8 l=150n w=720n
xm16 up upb 0 0 sky130_fd_pr__nfet_01v8 l=150n w=360n
  
xm9 9 clk2 6 0 sky130_fd_pr__nfet_01v8 l=150n w=2400n
xm10 clk2 clk2 9 1 sky130_fd_pr__pfet_01v8 l=150n w=640n

xm13 downb 9 1 1 sky130_fd_pr__pfet_01v8 l=150n w=720n
xm14 downb 9 0 0 sky130_fd_pr__nfet_01v8 l=150n w=360n

xm17 down downb 1 1 sky130_fd_pr__pfet_01v8 l=150n w=720n
xm18 down downb 0 0 sky130_fd_pr__nfet_01v8 l=150n w=360n


*output cap
*c1 up 0 6f
*c2 down 0 6f

v1 1 0 1.8
.ends pd


*CP
.subckt cp up down out
xm43 3 2 1 1 sky130_fd_pr__pfet_01v8 l=150n w=18u 
xm44 out downb 3 1 sky130_fd_pr__pfet_01v8 l=150n w=420n 
xm31 out up 7 0 sky130_fd_pr__nfet_01v8 l=150n w=420n
xm32 7 8 0 0 sky130_fd_pr__nfet_01v8 l=150n w=4.8u

xm33 2 2 1 1 sky130_fd_pr__pfet_01v8 l=150n w=420n 
xm34 8 8 0 0 sky130_fd_pr__nfet_01v8 l=150n w=420n

xm35 9 down 3 1 sky130_fd_pr__pfet_01v8 l=150n w=5400n 
xm36 9 9 0 0 sky130_fd_pr__nfet_01v8 l=150n w=420n

xm37 10 10 1 1 sky130_fd_pr__pfet_01v8 l=150n w=420n 
xm38 10 upb 7 0 sky130_fd_pr__nfet_01v8 l=150n w=5400n

xm39 1 down downb 1 sky130_fd_pr__pfet_01v8 l=150n w=720n 
xm40 0 down downb 0 sky130_fd_pr__nfet_01v8 l=150n w=360n 

xm41 1 up upb 1 sky130_fd_pr__pfet_01v8 l=150n w=720n 
xm42 0 up upb 0 sky130_fd_pr__nfet_01v8 l=150n w=360n 

*r1 out rc 200
*c1 rc 0 8f

v1 1 0 1.8
.ends cp


*VCO
.subckt vco in 17
xm1 10 16 3 10 sky130_fd_pr__pfet_01v8 l=150n w=420n
xm2 3 16 9 9  sky130_fd_pr__nfet_01v8 l=150n w=420n

xm3 10 3 4 10 sky130_fd_pr__pfet_01v8 l=150n w=420n
xm4 4 3 9 9 sky130_fd_pr__nfet_01v8 l=150n w=420n

xm5 10 4 12 10 sky130_fd_pr__pfet_01v8 l=150n w=420n
xm6 12 4 9 9 sky130_fd_pr__nfet_01v8 l=150n w=420n

xm11 10 12 13 10 sky130_fd_pr__pfet_01v8 l=150n w=420n
xm12 13 12 9 9 sky130_fd_pr__nfet_01v8 l=150n w=420n

xm13 10 13 14 10 sky130_fd_pr__pfet_01v8 l=150n w=420n
xm14 14 13 9 9 sky130_fd_pr__nfet_01v8 l=150n w=420n

xm15 10 14 15 10 sky130_fd_pr__pfet_01v8 l=150n w=420n
xm16 15 14 9 9 sky130_fd_pr__nfet_01v8 l=150n w=420n

xm17 10 15 16 10 sky130_fd_pr__pfet_01v8 l=150n w=2400n
xm18 16 15 9 9 sky130_fd_pr__nfet_01v8 l=150n w=1200n

xm7 10 5 1 1 sky130_fd_pr__pfet_01v8 l=150n w=1080n
xm8 5 5 1 1 sky130_fd_pr__pfet_01v8 l=150n w=840n
xm9 5 in 0 0 sky130_fd_pr__nfet_01v8 l=150n w=840n
xm10 9 in 0 0 sky130_fd_pr__nfet_01v8 l=150n w=1080n

xm19 1 16 11 1 sky130_fd_pr__pfet_01v8 l=150n w=720n
xm20 11 16 0 0 sky130_fd_pr__nfet_01v8 l=150n w=360n

xm21 1 11 17 1 sky130_fd_pr__pfet_01v8 l=150n w=720n
xm22 17 11 0 0 sky130_fd_pr__nfet_01v8 l=150n w=360n

*c1 11 0 24f
v1 1 0 1.8
.ends vco


*FD
.subckt fd Clk 10

xm1 1 2 3 1 sky130_fd_pr__pfet_01v8 l=150n w=720n 
xm2 0 2 3 0 sky130_fd_pr__nfet_01v8 l=150n w=360n 

xm3 3 Clkb 4 1 sky130_fd_pr__pfet_01v8 l=150n w=420n 
xm4 3 Clk 4 0 sky130_fd_pr__nfet_01v8 l=150n w=840n 

xm7 1 4 5 1 sky130_fd_pr__pfet_01v8 l=150n w=720n 
xm8 0 4 5 0 sky130_fd_pr__nfet_01v8 l=150n w=360n 

xm9 5 Clk 6 1 sky130_fd_pr__pfet_01v8 l=150n w=420n 
xm10 5 Clkb 6 0 sky130_fd_pr__nfet_01v8 l=150n w=640n 

xm11 1 6 2 1 sky130_fd_pr__pfet_01v8 l=150n w=720n 
xm12 0 6 2 0 sky130_fd_pr__nfet_01v8 l=150n w=360n 

xm13 1 Clk Clkb 1 sky130_fd_pr__pfet_01v8 l=150n w=720n 
xm14 0 Clk Clkb 0 sky130_fd_pr__nfet_01v8 l=150n w=360n 

xm15 7 6 1 1 sky130_fd_pr__pfet_01v8 l=150n w=720n 
xm16 7 6 0 0 sky130_fd_pr__nfet_01v8 l=150n w=360n

xm19 1 7 10 1 sky130_fd_pr__pfet_01v8 l=150n w=720n
xm20 10 7 0 0 sky130_fd_pr__nfet_01v8 l=150n w=360n 


*c1 7 0 18f
v1 1 0 1.8
.ends fd

```
#### Output
<p align="center">
 <img src="https://user-images.githubusercontent.com/52507285/133933067-dae08113-afe8-4c1d-a18e-eaceae1d221d.png" >
</p>

## Layout Designs <a name = "layout"></a>

### PFD <a name = "PFDL"></a>
<p align="center">
 <img src="https://user-images.githubusercontent.com/52507285/133935870-fe68de64-4844-44d2-9139-053f926da5a9.jpg" >
</p>

### Charge Pump <a name = "CPL"></a>
<p align="center">
 <img src="https://user-images.githubusercontent.com/52507285/133935870-fe68de64-4844-44d2-9139-053f926da5a9.jpg" >
</p>


### Voltage Controlled Oscillator <a name = "VCOL"></a>
<p align="center">
 <img src="https://user-images.githubusercontent.com/52507285/133935922-c8c33991-591a-4199-b18d-9c39a42f5645.jpg" >
</p>

### Frequency Divider <a name = "FDL"></a>
<p align="center">
 <img src="https://user-images.githubusercontent.com/52507285/133935938-e8b6aa65-d172-4ff4-9cdf-7554f6b1322c.jpg" >
</p>

### Complete PLL <a name = "CPLLL"></a>
<p align="center">
 <img src="https://user-images.githubusercontent.com/52507285/133935975-a3ad5f0b-90ed-4e38-8547-eca6225ece82.jpg" >
</p>




 
 

 
 
 
 
 
 
