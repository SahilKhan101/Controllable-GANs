# Controllable-GANs
This repo contains the codes for work done till now during the project

1) Generator:
Before buiding any model for controlling the attributes of images, the first we need is Generator.  So here We designed and trained a Generator-Discriminator architecture using celebA dataset over 90k+ images with a training time of nearly 2 hours, and finally achieved good images as a result of passing and randomly generated latent vector into Generator model.

2)	GAN inversion:-
Generative adversarial networks (GANs) learn a deep generative model that is able to synthesize novel, high dimensional data samples. New data samples are synthesized by passing latent samples, drawn from a chosen prior distribution, through the generative model. Once trained, the latent space exhibits interesting properties that may be useful for downstream
tasks such as classification or retrieval. Unfortunately, GANs do not offer an “inverse model,” a mapping from data space back to latent space, making it difficult to infer a latent representation for a given data sample. In this section, we utilised some techniques to Model and Train an Encoder used for Inversion. 
First of all designed a model for encoder with img_size*img_size inputs and out size of desired latent vector (100 is used here). Used perceptual loss for calculating loss, and followed  these step to train the model:
- Using images: <br>
	Input -> image(x) <br>
	z = E(x)  <br>
	x_n = G(z)  <br>
	loss = perceptual(x, x_n) <br>
	loss.backward() <br>
- Using Randomly generated latent vectors:  <br>
Generate Random vector ->  z  <br>
x=G(z)  <br>
z_n = E(x)  <br>
x_n = G(z_n)  <br>
loss = perceptual_loss(x, x_n) + L2_loss(z, z_n)  <br>
loss.backward() <br>

3)	Final Architecture and training logic: 
Working on linking image with attributes. Initially take 2 attributes for simplicity Make a binary type dataset, either blondHair=1 and blackHair=-1 or blondHair=-1 and blackHair=1. <br>
Used vgg16 network to predicting the attribute of newly generated image after translation of latent vector z by setting. For training chose following steps: 
	Take real image (summer, in the paper) (x)  <br>
	z = E(x)  <br>
	Randomly initialize d and take ϵ as high value (1). Then z’ = d_transpose*ϵ.  <br>
	x' = G(z’)  <br>
	loss1 = perceptual(x, x’) <br>
	loss2 = Discriminator(x’) <br>
	get the actual attribute of x => α, and using regressor find attribute of x’ => α’  <br>
	loss3 = || α+ ϵ - α’||  <br>
	loss = loss1 + loss2 + loss3  <br>
	loss.backward() <br>
  
  implementing these steps ‘d’ get trained, allows us to navigate in desired direction, for getting desired outputs.
  
4) Image Translation with using latent space navigation
finally for Translation of a new image, follow these path: <br>
x = image <br>
z = E(x)  <br>
z_new = z + d_transpose*epsilon <br>
new_image = G(z_new)  <br>

