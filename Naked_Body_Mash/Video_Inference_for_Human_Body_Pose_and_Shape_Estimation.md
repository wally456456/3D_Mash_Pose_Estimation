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
