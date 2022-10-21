# Lab 2B RP2040 Expansion Proposal

## Light up LED through GPIO

First, giving a glance on the LED lit up through GPIO:

<img src="assets\ezgif-3-0d3b8d071d.gif" width=25% height=25% />

By assigning the GPIO port and connect the cable to the corresponding port on board can we light the LED up.



## Board Proposal

The PIO and GPIO ports on the RP2040 give us opportunities to communicate with some electrical devices which we do not have chances to access. After wandering around the Detkin Lab serval times, we find some basic instruments to put our passion for music into real-world embedding systems.

First, we find some speakers, whose model is AST-03208MR-R, and some 7-Segment Displays with model number 5641AS, which could be used with the combination of the input from RP2040. 



<center>
    <img src="assets\speaker.jpg" width=25% height=25% />
    <img src="assets\display.jpg" width=25% height=25% />
</center>

In our proposal, the RP2040 would be responsible for receiving input from a serial input, for example, from the keyboard from a PC or Mac, and converting the input into music scales, for example, pressing key 1 represents the first music scale. And the RP2040 would also be responsible to use communication protocols to transfer the music scales to the speaker and also display the current scale playing on the 7-segment displays.

In short, this proposal would turn the RP2040 into an instrument, and if programmed well, it is possible to play any songs on it by a proficient musician.

**Component requirement**:

- RP2040 Board
- AST-03208MR-R Speaker
- 5641AS 7-segment display
- Cables

**Questions about the Proposal**:

The signal transform from digital to analog may be an issue when transferring key pressing signal to speaker signal.