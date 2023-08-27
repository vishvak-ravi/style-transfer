# Neural Style Transfer

A quick and dirty implementation of the classic neural style transfer algorithm
used to transform the "style" of an image while maintaining the image's content. I partially followed TensorFlow's
guide to better understand the API and how to effectively construct a model, but the high-level math which I implemented
myself is explained below.

# High-level Overview

Given a style image and a content image, the algorithm outputs an image combining the content
of the content image and the style of the style image. 

The neural style transfer algorithm uses VGG19, a CNN created by the Visual Geometry Group at Cambridge, which extracts
features from an input image. The output image is first initialized (most often with the content image), and then iterated
to minimize some measure of loss composed of "style loss" between the style image and the output image, as well as the
"content loss" between the content image and the output image.

Content loss is obtained by comparing the outputs at certain layers of the VGG19 network when passing in the output image
as well as the content image. The later layers of the network identify buildings, people, and other high-level information
related to the content of the image. The content loss is then trivially computed via MSE between the outputs of the images.
This process can be repeated for multiple outputs in the VGG network.

Style loss is obtained similarly to content loss, however, using earlier outputs in the network helps detect lower-level
details indicative of "style" (e.g. swirls, lines, patterns, etc.). However, unlike content, simply comparing the MSE of the
outputs aren't sufficient to detect style. Rather, we construct a Gram matrix, which effectively computes the correlation
of each feature map, with each entry of the Gram matrix being the dot product of flattened feature maps. Hence, computing
MSE between the output image's Gram matrix and the style image's Gram matrix, we can quantitatively compare style loss.

The total loss is then a weighted average of the content and style loss. This loss is minimized over each iteration via SGD
for each channel for each pixel of the output image. In practice, this algorithm often outputs images over-relying on repetitive
style patterns. Thus, a third loss, variation loss, is a third term in the loss equation which penalizes lack of variety
by comparing differences in neighboring pixel values.

## License

[MIT](https://choosealicense.com/licenses/mit/)