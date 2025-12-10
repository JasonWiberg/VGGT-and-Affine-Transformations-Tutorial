# Using-VGGT and Affine Transformations on Point Clouds 

Within VGGT_Tutorial.ipynb (works in colab: https://colab.research.google.com/drive/1xU7Tdr8WnSlEhEA97wclnj0CLlZR-fZl?usp=sharing), you can learn to use VGGT to generate point clouds from images, and align the point clouds to specified coordinate systems that can correspond to real-world geometry. 

Then, you can also try to use generative AI models like Qwen-Image_edit-2509 and specialized Loras to generate images of a scene from different angles. Use the generated images to improve VGGT inference on the original image (https://colab.research.google.com/drive/18uPhhuQnRy6dLtFFJaKSQtM5YtHu-oMI?usp=sharing).



# Architecture of VGGT (https://arxiv.org/abs/2503.11651, https://github.com/facebookresearch/vggt)

The VGGT (Visual Geometry Grounded Transformer) model is a feed-forward transformer designed to infer full 3D scene structure (camera parameters, depth maps, and 3D point maps) directly from one or multiple images. They breakthrough of the model is that it does this without any iterative optimization or post-processing, which are traditionally the most expensive parts. 


Input & tokenization:

Each input image is first “patchified” into a sequence of tokens using a frozen visual backbone (e.g. a Vision Transformer backbone such as DINOv2). 

In addition to the standard image-patch tokens, VGGT introduces special tokens per frame: a camera token (to encode camera extrinsic/intrinsic parameters) and a small set of register tokens. These help the network perform joint reasoning about geometry and multi‐view relationships. 


Transformer backbone with alternating attention:

The core of VGGT is a deep stack of Transformer blocks. Importantly, it alternates between frame-wise self-attention (looking within the tokens of a single image) and global self-attention (looking across tokens from all input views simultaneously). This design captures both per-image local detail and cross-view geometric correspondences. The transformer depth is on the order of ~ 24 layers. 


Prediction heads / Outputs:

At the top of the Transformer backbone, VGGT attaches dedicated “heads” to give the various outputs.

A camera head predicts for each input image its camera extrinsics and intrinsics (pose, focal length, principal point, etc.). In multi-view settings, the first frame is typically used as world reference. A dense prediction head (DPT head) is used for per-pixel outputs.


Advantages and Future Directions:

Unified multi‐task model that provides many useful outputs in one go. Rather than having separate specialized networks for depth estimation, pose estimation, point cloud reconstruction, etc.

No post-processing / optimization required. Traditional 3D reconstruction pipelines (like Colmap's Structure-from-Motion + Multi-View Stereo + bundle adjustment) involve iterative optimization. Whereas VGGT only requires a single forward pass.

VGGT outputs can serve as a general “3D backbone” for downstream tasks such as novel view synthesis, non-rigid tracking, etc. 

A good project to improve VGGT point clouds further could be to somehow match the colors between images. Since the various images often have different exposure settings, saturation, etc., these differences are often visible in the point clouds. Fix this either using image editing or being able to include the 3D geometry and reference the colors of nearby points in space. 


📄 Reference: Wang et al., "VGGT: Visual Geometry Grounded Transformer", CVPR 2025, https://arxiv.org/abs/2503.11651


