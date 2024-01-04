---
title: "Dual-Tone Multiple Frequency Detector"
excerpt: "Signal Processing Toolkit for a DTMF detector meant for a standard numeric dial pad. <br/><img src='/images/DTMF_cover.png'>"
collection: portfolio
---
*Designed and built for ECE 161A with professor [fred harris](https://web.archive.org/web/20160511170915/http://electrical.sdsu.edu/faculty/frederick_harris.html).*

We designed a digital signals processing system for detecting dial tones representing alphanumeric characters from a given input waveform. Used 11 Chebyshev Type 2 filters to successfully filter and detect signals and separate noise.
I lead and managed a team of 3 people over a period of 3 weeks to write 9000 of lines of code in MATLAB.
In addtition, I wrote testing scripts to ensure validity and stability of digital filters in both time and frequency domain.
We verified correctness of design using both infinite and finite impulse response filters.
The FIR filters were implemented using the Remez algorithm.

![Frequency Response of a Filter](/images/DTMF_cover.png)