A USB Class Compliant Audio Interface for the ClearCom CC-110 Headset

# build

Build a cc-110-usb using the included design files:
* Order a set of 3 PCBs from [OSHPark]() or use send the included gerbers to your preferred supplier. This project involves surface mount soldering. Review the PCB layout before proceeding.
* Import the BOM directly into Mouser, parse it with your favorite [BOM tool](), or order parts individually.
* Assemble the PCB according to the schematic.
* Print the case from the STL or order from a 3D-printing service.

You will need the skills and tools for surface mount soldering

Estimated cost for one unit is 35 USD, assuming that you purchase parts from Mouser and discard 2 of the 3 PCBs from OshPark.

# description

The [CC-110](https://www.clearcom.com/product/cc-110/) is a single-ear headset for ClearCom intercom systems. The cc-110-usb converter allows you to connect your CC-110 to a computer or smartphone via class-compliant USB audio.

This project is designed to let you use a headset that you already have on hand. I do not recommend buying a CC-110 just to use with this converter, since there are many headsets that will work without a converter or offer better quality with less effort.

# electrical design

## cc-110 specifications

The CC-110 has the following relevant electrical specifications, which have been extracted from its product page. All values are typical and measured at 1 kHz.
* **Speaker Impedance** 400 ±30% Ohms
* **Microphone Impedance** 200 ±30% Ohms
* **Open Circuit Sensitivity** -64 ±3.5 dB V at 99 dB ±3 dB SPL
* **Connector** 4-Pin XLR, Female (XLR4F), with pinout below

<br/>

XLR4F Pin | Function
------ | ----
1 | `MIC -`
2 | `MIC +`
3 | `SPK -`
4 | `SPK +`

<br/>

A typical conversation causes approximately [60 dB SPL](https://www.cdc.gov/nceh/hearing_loss/what_noises_cause_hearing_loss.html). Assuming a 2 m distance, 1 cm microphone placement, and that sound pressure decreases at *1/r*, a typical human might output 106 db SPL at the microphone, which calculates to **4 mVpp** at the microphone connector. This corresponds to some rough measurements with a noisy oscilloscope.

No sensitivity is given for the headphone. The compatible ClearCom [HMS-4X](https://www.clearcom.com/product/hms-4x/) provides up to 3.1 V (+12 dB uV) output. The minimum headphone impedance of 32 Ohms suggests an internal resistance of approximately the same. Therefore, the power delivered to the headphones is around **20 mW**. At a [representative sensitivity](https://service.shure.com/s/article/understanding-earphone-headphone-specifications?language=en_US) of 100 dB SPL / mW, the headphone would produce 113 dB SPL. This is a loud output that matches qualitative observations about volume from a CC-110 and HMS-4X.

There are a lot of assumptions here, but the results should reasonably inform the requirements for amplifier, ADC, and DAC performance.

## audio codec

The Texas Instruments [PCM2912A](https://www.ti.com/lit/ds/symlink/pcm2912a.pdf) (PDF) is a USB class-class compliant audio codec with integrated stereo DAC and mono ADC. It is well suited to this application. The cc-110-usb approximately mimics the PCM2912A's reference design.

The PCM2912A provides a 20 kOhm microphone preamplifier with fixed 20 dB gain followed by up to 30 dB of USB-programmable gain. The PCM2912A ADC accepts **1.4 Vpp** before clipping. The estimated input voltage of 4 mVpp after 50dB of gain is **1.3 Vpp** and should therefore be a reasonable match for the microphone. In limited breadboard tests, the received level was sufficient with the gain control set between 50% and 100% across a number of speaking volumes.

The PCM2912A provides a per-channel speaker output of 1.3 V, yielding 13 mW into a 32 Ohm load. Ohm's law reveals that the internal resistance is 32 Ohms. Connecting the CC-110 400 Ohm load and summing the left and right channels results in **14 mW** delivered to the headset. This is less than the estimated HMS-4X maximum output but still sufficient to produce about 111 dB SPL from the representative earpiece. The actual output volume will also depend on the level of the media source. In limited breadboard tests, the headset volume was sufficiently loud for listening to music, watching videos, and making calls in an office environment; but it may not be loud enough to amplify quiet media. This is an acceptable compromise for now, and additional amplifiers can be added in a later revision if required.

## history
All designs that interfaced with the computer of smartphone's integrated headset circuit were rejected because of unpredictable and varied performance across devices.
* Each device has unique gain capabilities and microphone impedance requirements, which can affect the ability to cleanly amplify a signal from the CC-110.
* Many devices sense the resistance between `MIC +` and `GND` to detect the presence of a microphone or the activation of an in-line control. The impedance levels are [well-understood](https://source.android.com/devices/accessories/headset/plug-headset-spec) but clash with the CC-110's 200 Ohms. An impedance transformer or terminator would be required for compatibility, further complicating the gain considerations above.
* Some devices apply as bias voltage to detect the presence to in-line controls or to power an electret-style microphone. Bias is not necessary for the CC-110's dynamic microphone and may cause damage.

# pcb design

# mechanical design

# revisions
## 0.0
The breadboard implementation performs well, and validates the design assumptions enough to proceed to a PCB design. Some noise is audible, but it seems to be related to poor grounding. A proper ground plane on the PCB should reduce the noise.

# license
The cc-110-usb is licensed under [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/).
