<div align="center">

### 【CVPR'2025 Highlight🔥】Learning Phase Distortion with Selective State Space Models for Video Turbulence Mitigation
</div>

## [Project Page](xg416.github.io/MambaTM/) | [Paper](https://arxiv.org/abs/2504.02697)

## 🧩 Prepare Training Datasets
Please refer to https://github.com/xg416/DATUM

## 🛠️ Training 
### Learn the Latent Phase Distortion (LPD)


### Train the MambaTM

For the training on dynamic scene data, run the following:
```
python train_MambaTM_dynamic.py --train_path ${your_training_data_path} --train_info ${the associated train_info.json} --val_path ${your_testing_data_path} --val_info ${the associated test_info.json} -f ${loaded_model_path (if you want to resume a pre-trained checkpoint)} 
```

We also provide support for DDP training for systems with Slurm Workload Manager, if you are using multiple GPUs, you can run with the following:
```
srun --ntasks={number of GPUs} --gpus-per-task=1  python train_MambaTM_dynamic_DDP.py --train_path ${your_training_data_path} --train_info ${the associated train_info.json} --val_path ${your_testing_data_path} --val_info ${the associated test_info.json} -f ${loaded_model_path (if you want to resume a pre-trained checkpoint)} 
```

Other arguments and hyperparameters for training are described in the *train_MambaTM_dynamic.py* file and *train_MambaTM_dynamic_DDP.py*, please refer to them for more flexible training. Use smaller *patch_size* and *num_frames* in the beginning phase of training can accelerate the entire process.

Later, you can start finetuning on the static scene images for the static scene model by running the following:
```
python train_MambaTM_static.py --train_path ${your_training_data_path} --val_path ${your_testing_data_path} -f ${pretrained_dynamic_scene_model_path} 
```
We injected a certain level of Gaussian noise during training in both modalities for better generalization on real-world data.

## 🚀 Performance Evaluation
Dynamic scene model on ATSyn_dynamic dataset:
```
python test_MambaTM_dynamic.py --data_path ${your_testing_data_path} --info_path ${the associated test_info.json} -result ${path_for_stored_output} -mp ${testing_model_path} 
```
Static scene model on ATSyn_static dataset:
```
python test_MambaTM_static.py --val_path ${your_testing_data_path} -result ${path_for_stored_output} -f ${testing_model_path} 
```
Inference on Turbulence Text dataset, we generate the central 4 frames for the text recognition:
```
python inference_MambaTM_text.py -f ${testing_static_scene_model_path} --n_frames 60 --resize 360
```
Please modify the path of the input and output images in the *inference_MambaTM_text.py* 


## 👍 Useful Links
### [Turbulence @ Purdue i2Lab](https://engineering.purdue.edu/ChanGroup/project_turbulence.html) 

### Restoration:
[Link](https://ieeexplore.ieee.org/abstract/document/10400926) Zhang, Xingguang, Zhiyuan Mao, Nicholas Chimitt, and Stanley H. Chan. "Imaging through the atmosphere using turbulence mitigation transformer." IEEE Transactions on Computational Imaging (2024).

[Link](https://openaccess.thecvf.com/content/ICCV2023/html/Jaiswal_Physics-Driven_Turbulence_Image_Restoration_with_Stochastic_Refinement_ICCV_2023_paper.html) Ajay Jaiswal*, Xingguang Zhang*, Stanley H. Chan, Zhangyang Wang. "Physics-Driven Turbulence Image Restoration with Stochastic Refinement." Proceedings of the IEEE/CVF International Conference on Computer Vision (ICCV), 2023, pp. 12170-12181 

[Link](https://openaccess.thecvf.com/content/CVPR2024/html/Zhang_Spatio-Temporal_Turbulence_Mitigation_A_Translational_Perspective_CVPR_2024_paper.html) Xingguang Zhang, Nicholas Chimitt, Yiheng Chi, Zhiyuan Mao, Stanley H. Chan. "Spatio-Temporal Turbulence Mitigation: A Translational Perspective." Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR), 2024, pp. 2889-2899

[Link](https://www.nature.com/articles/s42256-021-00392-1) Jin, Darui, Ying Chen, Yi Lu, Junzhang Chen, Peng Wang, Zichao Liu, Sheng Guo, and Xiangzhi Bai. "Neutralizing the impact of atmospheric turbulence on complex scene imaging via deep learning." Nature Machine Intelligence 3, no. 10 (2021): 876-884.

### Datasets:
[OTIS dataset](https://zenodo.org/records/161439) | [TSRWGAN data](https://zenodo.org/records/5101910) | [Turbulence Text](https://drive.google.com/file/d/1QWvQfPM-lJwGqK_Wm6lDbi-tYBu-Uopq/view?usp=sharing) | [Heat Chamber](https://drive.google.com/file/d/14iVachB95bCCtke8ONPD9CCH20JO75v2/view?usp=sharing) | [TMT dataset](https://github.com/xg416/TMT) | [BRIAR](https://arxiv.org/abs/2211.01917) (Not public yet)


## 📘 Citation
Please consider citing our work as follows if it is helpful.
```
@InProceedings{zhang2025MambaTM,
    author={Zhang, Xingguang and Chimitt, Nicholas and Wang, Xijun and Yuan, Yu and Chan, Stanley H}, 
    title={Learning Phase Distortion with Selective State Space Models for Video Turbulence Mitigation},
    booktitle={Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR)},
    month={June},
    year={2025}
}
```