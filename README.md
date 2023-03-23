# Webcam_pulse
Estimate pulse rate from video captured by webcam

We try measure pulse rate from webcam of our laptop with reasonable accuracy. I got inspiration for the project from a YouTube video by [Steve Mould](https://www.youtube.com/watch?v=BFZxlauizx0) where he discussed how smart watches measure various biometics. Pressing your thumb in front of the webcam, gives a similar video feed to what a smart watch sensor might sense. We divide the project into 3 parts:

```{mermaid}
flowchart LR
  A(Capture) --> B(Clean)--> C(Count)
```
## Capture

We simply use `OpenCV` library to capture the webcam output. Each frame that `OpenCV` captures is a numpy array of shape (length,breadth,3),length and breadth corresponds to resolution of your webcam and 3 corresponds to the color space (BRG default). Every time blood is pumped, the intensity of light, passing through the thumb reaching the camera sensor changes. This change is detected by the camera as slight periodic change in pixel intensity values. Of the 3 color channels, 
- The blue light gets scattered and absorbed easily, and the intensity changes is not very clear and image output is noisy. 
- On the other hand red light is least scattered and absorbed which leads to much cleaner image but intensity changes are little.
- Green performs the best. producing cleaner image with significant intensity changes.

This is the reason why most smart watches have a green flashing light.

Once we capture the frame, we average out the pixel intensity for each color for each frame. 

## Clean

We have a time series of pixel intensity values. We remove the trend from this series to get a clear view of the periodic intensity changes. We remove trend by subtracting a rolling average from the intensities. We ignore first few frames (`ndead_frames`) because the camera takes some time to auto adjust to the light level, thus the pixel intensity in the first few frames are way off.

## Count

Looking at above figure we see local minimas occurring at periodic intervals. This is the pulse rate we want to extract. To do this we perform a discrete Fourier transform on the time series. Doing so gives us the frequencies that constitute the time series. Then we extract the frequency of maximum amplitude which gives the pulse rate. 

## References
- [YouTube video by Steve Mould](https://www.youtube.com/watch?v=BFZxlauizx0)
- [A Fourier transform (FT) is a mathematical transform that decomposes functions into frequency components, which are represented by the output of the transform as a function of frequency.](https://en.wikipedia.org/wiki/Fourier_transform)
