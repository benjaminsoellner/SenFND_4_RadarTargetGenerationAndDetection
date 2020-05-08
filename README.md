# Sensor Fusion Engineer Project 4 - Radar Target Generation and Tracking

## Benjamin Söllner

This project is part of the [Udacity Sensor Fusion Nanodegree](https://www.udacity.com/course/sensor-fusion-engineer-nanodegree--nd313) online class and subsequently completed to meet the courses project submission standards. The remaining section of this `README` includes the Reflection which has to be completed as part of this project.

If you want to view the output of this project's MATLAB code a good choice is to open the [exported HTML file](http://benjaminsoellner.github.io/SenFND_4_RadarTargetGenerationDetection/radarlive.html).

## Reflection

Here are the answers to the project [rubric](https://review.udacity.com/#!/rubrics/2548/view).

### Implementation steps for the 2D CFAR process.

2D-CFAR iterates through the 2D-FFT matrix bins in horizontal and vertical direction with two nested for-loops. Since the ``RDM`` matrix has been converted to dB-units before (``10*log10(abs(Mix_fft2))``) the elements summing up all training and guard cells needs to be converted back from dB-unit before (``db2pow``). The noise level is then equal to the sum of training cell values minus the sum of guard cell values.

A threshold can then be computed by normalizing the noise level with the total size (number of cells) of the filter patch. We add a basic ``SNR`` offset of ``6``.

The signal of the CUT-cell itself (again, converted from dBs with ``db2pow``) is then compared to the threshold and the output value set to ``1`` if the signal is above the threshold.

### Selection of Training, Guard cells and offset.

The number of training cells are ``Tr=10`` and ``Td=5`` based on the original resolution of the signal sampling (``Nd=128``, ``Nr=1024``). A higher value could also be chosen, but larger values lead to increased runtime.

The number of guard cells ``Gr=5`` and ``Gd=3`` were chosen by visually looking at the shape / extent of the signal peak in 2D-FFT. They area covered by guard cells should roughly cover the patch that peaks above noise in the signal area so that signal values do not wrongly contribute to the noise level.

The ``SNR`` of ``6`` is visually inspected in the 2D-FFT display as well by looking at the minimum noise level. Values below ``6`` yield unwanted false positives.

### Steps taken to suppress the non-thresholded cells at the edges.

The boundaries of the for loop itself already takes care that only CUT-cells that lie well inside the 2D-FFT matrix edges are processed. Since the target matrix ``Mix_cfar`` is already initialized with zeros, no additional step needs to be taken.