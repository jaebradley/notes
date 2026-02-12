# [It's all a blur](https://lcamtuf.substack.com/p/its-all-a-blur)

## One-dimensional moving average
* Blurring is effectively averaging the underlying pixel values
* Simplest averaging algorithm is the moving mean
* Take a fixed-size window and replace each pixel value with the arithmetic mean of `n` pixels in its neighborhood
* Blur value for image 0 = (image - 2, image - 1, image, image + 1, image + 2) / 5
* Blur value for image 1 = (image - 1, image, image + 1, image + 2, image + 3) / 5
* Blur value for image 1 - blur value for image 0 = image 3 - (image - 2)
* If image - 2 = one of the first two images, then there are fixed padding pixels
* Image 3 = blur value for image 1 - blur value for image 0 + fixed padding pixels
  * Can use the same logic for image 4 = (blur value of image 2 - blur value for image 1) + fixed padding pixels

## Right-aligned moving average
* First output value is an average of the four fixed padding pixels and one original image pixel
* Pixel value for image 0 = (5 x blur value for image 0) - (4 x padding pixel)
* Pixel value for image 1 = (5 x blur value for image 1) - (3 x padding pixel) - pixel value for image 0

## Into the second dimension
* The blur algorithm averages pixel value in the x-axis, giving the appearance of motion blur / camera shake
* A naive implementation is to execute the blur in the horizontal direction and then in the vertical direction
  * This ends up blurring the original image so severely that it cannot be easily reconstructted
* Adversarial blur filter will weight the original pixel in the calculated average
  * If the average window has a size of `W` and the current-pixel bias factor is `B`, then the blur formula is
  * Blur of image `n` = `image(n - W) + ... + image(n - 1) + (B x image (n))) / (W + B)`
