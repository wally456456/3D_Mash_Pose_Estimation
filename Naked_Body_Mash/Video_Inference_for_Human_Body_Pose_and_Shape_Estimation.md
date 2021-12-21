# Abstract
- Due to the lack of ground truth Data of 3D pose estimation current `3D pose and shape estimation`'s SOTA model is not resulting Accurate and Natural results
- Video Inference for Body Pose and Shape Estimation(VIBE) allows 3D ground truth label in AMASS(large-scale motion capture dataset, Unparied, In the wild, 2D keypoint annotation)
-  `VIBE` : uses self-attention Architecture at the sequence level, possible to generate kinematically plausible ground-truth 3D label

# Introduction
- The problem is the lack of 3D annoted Dataset that describes `diversity` and `complexity` of human's motion
- Auto-Encoding Variational Bayes(IEEE), Human Mesh Recovery from Monocular images via a skeleton-distangled representation(ICCV) uses the data combined with `3D indoor dataset` and `2D ground truth Videos or pseudo-ground truth keypoint annotation`
  BUT!!!
  - Indoor 3D dataset's problem : Number of subject, Range of motion, Complexity of Image is Limited
  - Lack of 2D ground truth Video data
  - Psuedo-ground-truth 2D label is not adequate for 3D human motion
- To solve this problems, authors are inspired by Kanazawa et al (IEEE End-to-end recovery of human shape and pose) -> Uses only 2D keypoints and unpaired dataset of static 3D human shapes and poses using Adversarial training
- Video sequences already have the in-the-wild videos with 2D keypoint annotation -> The problem is how to obtain realistic 3D human motions in sufficient quality for adversarial training -> Facilitate the 3D motion-capture dataset AMASS
- The process of Model Training
  - Uses Two sources of unpaired information by training GAN -> Given Video of a person, predict the parameters of SMPL Body model 	vs    Discriminator : Distinguish between real and regressed sequences  -> The regressor is encouraged to make plausible motions, on the other hand, Discriminator implicitly learn the statics, physics and kinematics(해부학적)of the human body -> Implement both temporal encoder and discriminator Using GRU to capture the sequential motion

# Related Work
- 3D Pose and Shape from a single image
  -  Bottom up for regression approaches, Top down for optimization approaches, multi 	camera setting using keypoints and silhouettes as input -> These are weak, Don’t generalize well
  - SMPLify : Fits the SMPL model to the output of CNN keypoint detector one of first end-to-end approaches
  - Previous DNN are trained by silhouettes. Recently DNN are trained by the pixels <- Because of lack of in-the-wild 3D ground-truth labels, they use weak supervision(the unprecise label data) signals(derived from 2D keypoint reprojection loss)


- 3D Pose and Shape from video
  - Recent deep learning methods that estimate the pose and shape from video focus on joint locations only
  - Two ways of approach
    - 1. two-stage approach : lift off-the-shelf 2D keypoints -> 3D joint location
    - 2. End-to-End methods : Directly regress 3D joint locations -> Good indoor, Poor for outdoor data
- GANs for sequence modeling
  - Recently, GANs and recurrent architectures are combined to translate the sequence to sequence tasks -> Combining these two (GANs + Recurrent architectures) can predict future motion sequences based on previous ones or generate human motion sequences
  - In this paper author focuses on adversarially refining predicted poses conditioned on the sequential input data -> To do this, author employs the discriminator that encodes pose and shape parameters in a latent space using recurrent architecture and adversarial objective taking advantage of 3D mocap data

# Approach!
[image](https://user-images.githubusercontent.com/69032315/146897869-8064e41f-ab4e-4ed4-85f0-a36f7156561a.png)
