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

# Approach
![plot](https://user-images.githubusercontent.com/69032315/146897869-8064e41f-ab4e-4ed4-85f0-a36f7156561a.png)
- V = input, T = Single Person $I_t$ = Information of Each Frame
- Train a temporal encoder composed of bidirectional GRU -> outputs the latent variables containing information incorporated from past and future frames -> These features are used to regress the parameters of the SMPL body model at each time instance
- <strong> Key Point <\strong> -> Why it is encoder not generator -> `Because it makes the mesh with the latent variable from the Features of pretrained CNN model`

- SMPL
  - Parameters
  - Pose ![plot](https://user-images.githubusercontent.com/69032315/146898571-6b7431ff-18ae-405a-a7ab-22a18eadf254.png) 
    - Consists of global body rotation and relative rotation of 23 joints in axis-angle format
  - Shape ![plot](https://user-images.githubusercontent.com/69032315/146898666-f3ed233d-7c39-4b35-820a-10bcd0487da0.png)
    - 10 Coefficient of a PCA shape space
  - Given these parameters SMPL model is a differentiable function  ![plot](https://user-images.githubusercontent.com/69032315/146898809-f48af0e0-2b0a-499f-9819-9176738b77f3.png)
   that outputs a posed 3D mesh (Because it has the randomness)
  
- With Videos
  - ![plot](https://user-images.githubusercontent.com/69032315/146898867-a34cb07b-d628-4709-810e-d241d7850773.png)
  - ![plot](https://user-images.githubusercontent.com/69032315/146898899-4d553505-a98c-4bb0-8965-9c7881911a54.png) = Pose parameters at time step t
  - ![plot](https://user-images.githubusercontent.com/69032315/146898963-80a8dac0-aaaf-4af9-b148-7e313ec83376.png) = single body shape prediction for the sequence 
    - Made every timesteps but average pooling to get a single shape
  
- Temporal Encoder
  - Temporal Encoder(Generator) from the given ![plot](https://user-images.githubusercontent.com/69032315/146899324-76c66106-6145-445b-a486-d8af36c9639c.png)(extracted feature of each frame by pretrained CNN), it outputs the corresponding pose and shape parameters in each frame -> Sent to GRU to get latent feature ![plot](https://user-images.githubusercontent.com/69032315/146899608-e1c80f58-686c-4d8d-9cac-c10faca3a682.png)
 which is the input to T regressor,The regressor is initialized with mean pose Θ¯ and takes the input of Θk along with the features ![plot](https://user-images.githubusercontent.com/69032315/146899740-1d5a7114-9594-41b3-bc94-84948165f4d6.png) Uses 6D rotation representation instead of axis angle 
  - Encoder's Loss Term : ![plot](https://user-images.githubusercontent.com/69032315/146899856-3f73cf19-3303-45a7-adde-79a3972c71db.png)
    - 2D(x), 3D(X), SMPL(pose(seta), shape(beta)), adv(Discriminator)
    - ![plot](https://user-images.githubusercontent.com/69032315/146899954-d1f06bd5-dfcc-4409-92c8-cfedbfadf35b.png)
    - 2D keypoint loss -> To get this keypoint loss we need to do the orthographic projection(Get several points from the 3D and project it to 2D image) to the  3D joint locations and then compute to the 2D

- Motion Discriminator 
  - It is important to discriminate the sequences not single shot because of the ignorance of temoral continuity of movement
  - To mitigate this author uses `Motion Discriminator` -> discriminate whether the generated sequence of poses corresponds to realistic sequence or not
  - The output of generator is given as input of multi-layer GRU model(estimate latent code hi at each time step i) to aggregate the hidden states hi author uses self-attention 
  - Finally a linear layer predicts a value between 0 and 1 to predict whether the input is real or fake
  - Dicriminator's Loss Function : ![plot](https://user-images.githubusercontent.com/69032315/146900340-29551fe8-568f-4a68-aa0f-c506a2f33cc9.png)
  - PR = real motion sequence
  - PG = generated motion sequence
  
- (Ablation) Motion Prior(MPoser)
  - Mposer = sequenctial VAE on the AMASS dataset to learn latent representation of plausible human motions
  - Use Mposer as a regularizer to penalize implausible sequences
  - Mposer is encoder decoder structure consist of GRU layers that output latent vector of each timestep
  - When Mposer is employed then DM is disabled and add prior loss ![plot](https://user-images.githubusercontent.com/69032315/146900587-816a8567-75d4-4229-90f1-a938beafc57f.png)

- Self-Attention Mechanism
  - The final hidden state holds a summary of the information of sequence
  - Use self-attention to amplify the contribution of the most important frames in final representation
  - r(learned convex combination of the hidden states) is the representation of Θˆ
    - Procedure of producing r -> hi(representing hidden state at each frame) are averaged and max pooled -> Two representation r(avg), r(max) are concatenated to constitute the final static vector r(used fo the Dm fake/real decision)
- Training Procedure
  - Use ResNet-50 as an encoder pretrained on single frame pose and shape estimation task -> outputs   ![plot](https://user-images.githubusercontent.com/69032315/146900857-a2f60794-49b3-4a0b-8636-efe98c22e078.png) -> Do not update ResNet
  - T(sequence length) = 16
  - Temporal encoder = 2-layer GRU with hidden size of 1024
  - SMPL regressor = 2 fully-connected layer with 1024 neurons each -> final layer  ![plot](https://user-images.githubusercontent.com/69032315/146901013-e79ef87e-b6e1-420f-afbf-80b72b23f860.png)->(pose, shape, camera parameters)
  - Self attention = 2MLP layers with 1024 neurons each and tanh activation 
  - Adam optimizer = 0.00005(Generator) , 0.0001(Discriminator)
# Experiments
![plot](https://user-images.githubusercontent.com/69032315/146901273-c5b5b5d6-f3f4-41d4-9ac8-07c7bff86a28.png)
- PA-MPJPE = The differences of size, location, rotation of subject’s contact  
- MPJPE = The differences of Joint(unit = mm)
- PCK(percentage of correct keypoints) = Gives threshold to the difference 

# Conclusion
  - (1) Introducing a recurrent architecture propagates information over time
  - (2) Introducing discriminative training using AMASS
  - (3) Uses self-attention in discriminator to focus on the important temporal structure of human motions
  - (4) Giving Ablation method(MPoser) but it is less effective than Discriminator
