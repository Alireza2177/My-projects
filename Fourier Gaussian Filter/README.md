# Image Frequency Filtering with DFT and Gaussian Lowpass Filter

This repository contains a Python implementation of image filtering using the Discrete Fourier Transform (DFT) and a Gaussian lowpass filter. The process includes padding the image, transforming it to the frequency domain, applying the filter, and then transforming it back to the spatial domain.

## Overview

The main goal of this project is to apply a Gaussian lowpass filter to an image using frequency domain techniques. This method is efficient and can handle larger kernel sizes than spatial domain convolution.

## Steps

1. **Load the Image**: Read the image from Google Drive.
2. **Padding**: Expand the image size to \( P \times Q \).
3. **Centering**: Multiply the image by \( (-1)^{x+y} \) to shift the zero-frequency component to the center of the spectrum.
4. **DFT**: Compute the Discrete Fourier Transform of the centered image.
5. **Gaussian Filter**: Create a Gaussian lowpass filter in the frequency domain.
6. **Apply Filter**: Multiply the Fourier Transform by the filter.
7. **Inverse DFT**: Transform the filtered image back to the spatial domain.
8. **Cropping**: Extract the original image size from the padded result.
9. **Visualization**: Plot each step for visual inspection.

## Requirements

- Python 3
- NumPy
- OpenCV
- Matplotlib
- Google Colab (optional, for running in the cloud)

## Installation

You can install the required libraries using pip:

```bash
pip install numpy opencv-python-headless matplotlib
