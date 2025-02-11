# **Privacy-Preserving Invisible Image Watermarking System**

Invisible image watermarking is a technique to embed watermarking information into an image such that it is inperceptible to the human eye. Other than being inperceptible to human eye, it must be **undetecable**(not easily recoverable by adversary), **tamper-proof**(can't be easily modifed by the adversary) and **robust**(should be recoverable even after disortions).

Most of the invisible watermarking techniques could be broadly classified into 3 categories :-
* **Spatial Domain**:- In these techniques, the watermarking information is directly stored in the pixel values of the image channels(i.e RGB, YCrCb, etc.). The LSB (Least Significant Bit) embedding is one such example.
* **Frequency Domain**:- In these techniques, the watermarking information is stored in the co-efficients after transforming the image using any frequency transformation technique. DCT, DWT and DFT embedding are few such examples.
* **Latent Space**:- Most of the deep-learning based techniques would fall into this category. The idea here is to map an image to a latent space and add watermarking information in that space. RivaGan, LDM, LaWa are few such examples.

It has been shown that spatial domain techniques are not robust to disortions(compression, adding noise) and could be tampered easily by an adversary as they work directly with pixel values. On the other hand, frequency domain and latent space techniques are more robust to disortions because they add watermarking information after transforming the image.

## Overview
In this notebook, we attempt to develop an privacy-preserving invisible image watermarking system. The basic requirements that need to satisfied:-
* The input(image to watermarked) must be encrypted.
* The watermarking information must be string or bytes.
* The watermarking information may or may not be encrypted.

Keeping the above requirements in mind, we have used a FHE scheme(TFHE) to develop our system using the Concrete library. 

### Approach
We have skipped the latent space(deep-learning based) watermarking techniques. We initially started with a frequency-domain technique(DFT-based embedding) but since Concrete library doesn't work well with floating-point numbers, we had to skip it as well. In this notebook we have taken an **spatial domain** approach to watermark an image in a privacy-preserving way. In our approach, we embed the watermarking information in the 4th bit of the pixel value in RGB channel. 

#### Watermarking Approach Details
In this project, we would be using spatial domain technique to embed watermarking information into the image. We :-
* Convert the watermark message into bits.
* Use repetition code to repeat the bits by repetition factor.
* Append 0's to make the watermark bit string length same as that of number of pixels in the image.
* Create a permuation map and inverse permutation map.
* Permute the watermark bits to distribute the bits across the image.
* To embed the watermark, set the watermark bits in the 4th bit of each pixel in one of the RGB channel.
* To extract the watermark, retreive the 4th bit of each pixel, apply inverse permutation map and decode the watermark bits by majority voting.