# Convolutional Network
1. Neural Network that share their parameters across space
1. RGB - depth = 3 - feature maps
1. It's like a pyramid - at the bottom you have this big image but very shallow just R, G and B. Applying convolution that progressively squeeze the spatial dimension while increasing the depth which corresponds rougly to the semantic complexity of your representation. At the top it's your classifier.
1. Patches are sometimes called kernels.
1. Each pancake in stack is called feature map - e.g. RGB of an image 
1. Stride - number of pixels you're shifting each time you move your filter
	1. Stride of one makes the output roughly the same as the input, stride of 2 means it's about half the size
	1. Roughly because it depends on padding - valid or same.
	1. Valid padding - doesn't go off the edge of image
	1. Same padding - goes off the edge and pad with zeros in such a way that the output map size is exactly the same size as the input map
1. Volume of next layer: `(W - F + 2P) / S + 1` where `W` is the input layer volume, `F` is the filter volume, `S` is the stride, and `P` is the padding
1. ksize and strides: `[batch, height, width, channels]` with batch and channel typically set to 1.
1. Max-pooling - only take the max value in a stride
1. 
```
# Depth is 20
filter_weights = tf.Variable(tf.truncated_normal((8, 8, 3, 20))) # (height, width, input_depth, output_depth)
filter_bias = tf.Variable(tf.zeros(20))
strides = [1, 2, 2, 1] # (batch, height, width, depth)
padding = 'VALID'
```
1. 