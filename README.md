<div align="center">
  <h1> MambaTM [CVPR 2025 Highlight🔥] </h1>
  <h2>Learning Phase Distortion with Selective State Space Models for Video Turbulence Mitigation</h2>
</div>

<p align="center">Xingguang Zhang, Nicholas Chimitt, Xijun Wang, Yu Yuan, Stanley H. Chan</p>
<p align="center"> 
   <a href="xg416.github.io/MambaTM/">Project Page</a>  | 
  <a href="https://arxiv.org/abs/2504.02697">Paper</a> 
</p>

## 📑 Contents
- [Environment Installation](#environment-installation)
- [Prepare Dataset](#Datasets-prepare)
- [Training](#Training)
- [Performance Evaluation](#Evaluation)
- [Useful Resources](#links)
- [Citation](#Citation)
- [License](#license)

<h2 id="environment-installation">🔨 Environment Installation</h2>

```shell
conda create -n MambaTM python=3.11
conda activate MambaTM
cd code
pip install -r requirements.txt
```

<h2 id="Datasets-prepare">🧩 Prepare Training Datasets</h2>

First download and prepare the [ATSyn dataset](https://github.com/xg416/DATUM/) first.

To train the ReBlurNet, please download the [LSDIR dataset](https://ofsoundof.github.io/lsdir-data/).


<h2 id="Training">🛠️ Training</h2>
Our model is trained in 2 stages: ReBlurNet and MambaTM

### Train the ReBlurNet
You can directly use our pre-trained ReBlurNet at *code/LPD_learning/model_zoo/NAF_decoder.pth*, if you want to train it from scratch, run the following:
```
cd code/LPD_learning
python train_vae.py --iters ${number_of_iterations} -ps ${image_patch_size} -b ${batch_size} --LSDIR_path ${path_to_LSDIR_dataset} --ATSyn_dynamic_path ${path_to_ATSyn_dynamic_dataset} --ATSyn_static_path ${path_to_ATSyn_static_dataset} --exp_dir ${path_to_save_checkpoints}
```
When the training is finished, run
```
mv ${the_best_checkpoint} code/LPD_learning/model_zoo/NAF_decoder.pth
```

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
python train_MambaTM_static.py --train_path ${your_training_data_path} --val_path ${your_testing_data_path} -f ${pretrained_dynamic_scene_model_path} --start_over
```
Or:
```
srun --ntasks={number of GPUs} --gpus-per-task=1   python train_MambaTM_static.py --train_path ${your_training_data_path} --val_path ${your_testing_data_path} -f ${pretrained_dynamic_scene_model_path} --start_over
```

We injected a certain level of Gaussian noise during training in both modalities for better generalization on real-world data.

<h2 id="Evaluation">🚀 Performance Evaluation</h2>
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
Our pre-trained models are in *code/model_zoo*


<h2 id="links">👍 Useful Links</h2>
### [Turbulence @ Purdue i2Lab](https://engineering.purdue.edu/ChanGroup/project_turbulence.html) 

### Restoration:
[Link](https://ieeexplore.ieee.org/abstract/document/10400926) Zhang, Xingguang, Zhiyuan Mao, Nicholas Chimitt, and Stanley H. Chan. "Imaging through the atmosphere using turbulence mitigation transformer." IEEE Transactions on Computational Imaging (2024).

[Link](https://openaccess.thecvf.com/content/ICCV2023/html/Jaiswal_Physics-Driven_Turbulence_Image_Restoration_with_Stochastic_Refinement_ICCV_2023_paper.html) Ajay Jaiswal*, Xingguang Zhang*, Stanley H. Chan, Zhangyang Wang. "Physics-Driven Turbulence Image Restoration with Stochastic Refinement." Proceedings of the IEEE/CVF International Conference on Computer Vision (ICCV), 2023, pp. 12170-12181 

[Link](https://openaccess.thecvf.com/content/CVPR2024/html/Zhang_Spatio-Temporal_Turbulence_Mitigation_A_Translational_Perspective_CVPR_2024_paper.html) Xingguang Zhang, Nicholas Chimitt, Yiheng Chi, Zhiyuan Mao, Stanley H. Chan. "Spatio-Temporal Turbulence Mitigation: A Translational Perspective." Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR), 2024, pp. 2889-2899

[Link](https://www.nature.com/articles/s42256-021-00392-1) Jin, Darui, Ying Chen, Yi Lu, Junzhang Chen, Peng Wang, Zichao Liu, Sheng Guo, and Xiangzhi Bai. "Neutralizing the impact of atmospheric turbulence on complex scene imaging via deep learning." Nature Machine Intelligence 3, no. 10 (2021): 876-884.

### Datasets:
[OTIS dataset](https://zenodo.org/records/161439) | [TSRWGAN data](https://zenodo.org/records/5101910) | [Turbulence Text](https://drive.google.com/file/d/1QWvQfPM-lJwGqK_Wm6lDbi-tYBu-Uopq/view?usp=sharing) | [Heat Chamber](https://drive.google.com/file/d/14iVachB95bCCtke8ONPD9CCH20JO75v2/view?usp=sharing) | [TMT dataset](https://github.com/xg416/TMT) | [BRIAR](https://arxiv.org/abs/2211.01917) (Not public yet)

<h2 id="Citation">📘 Citation</h2>
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

<h2 id="license">🎫 License</h2>

This project is released under the MIT license. Please refer to the acknowledged repositories for their licenses.