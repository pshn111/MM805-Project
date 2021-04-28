## Dependecy and Setup

The project is developed on mmg1

#### Create and active a conda virtual environment.

```
conda create -n rignet python=3.7
conda activate rignet
```

Some necessary libraries include:

```
pip install numpy scipy matplotlib tensorboard open3d==0.9.0 opencv-python
pip install "rtree>=0.8,<0.9" 
pip install trimesh[easy]
conda install pytorch==1.6.0 torchvision==0.7.0 cudatoolkit=10.1 -c pytorch
pip install torch-scatter -f https://pytorch-geometric.com/whl/torch-1.6.0+cu101.html
pip install torch-sparse -f https://pytorch-geometric.com/whl/torch-1.6.0+cu101.html
pip install torch-cluster -f https://pytorch-geometric.com/whl/torch-1.6.0+cu101.html
pip install torch-spline-conv -f https://pytorch-geometric.com/whl/torch-1.6.0+cu101.html
pip install torch-geometric
```

## Trained models
You can find the trained models from [here](https://umass.box.com/s/l7dxfayrubf5qzxcyg7can715xnislwm). 


## Quick demo

Check and run quick_start.py. We provide some examples in this script. 

## Data


You need to download the pre-processed data [here](https://umass.box.com/s/9bo643jb2jy6tu9nffoe8zmad8dio0z5), which consists of several sub-folders.

* obj: all meshes in OBJ format.
* rig_info: we store the rigging information into a txt file. Each txt file has four blocks. (1) Lines starting with "joint" define a joint with its 3D position. Each of joint line has four elements, which are joint_name, X, Y, and Z. (2) Line starting with "root" defines the name of root joint. (3) Lines starting with "hier" define the hierarchy of skeleton. Each hierarchy line has two elements, which are parent joint name and its child joint name. One parent joint can have multiple children joints. (4) Lines starting with "skin" define the skinning weights. Each skinning line follows the format as vertex_id, bind_joint_name_1, bind_weight_1, bind_joint_name_2, bind_weight_2 ... The vertex_id follows the vertice order in obj files in the above obj folder.
* obj_remesh: This folder contains the obj files of the remeshed models. Meshes with fewer than 1K vertices were subdivided, and those with more than 5K vertices were simplified; as a result all training and test meshes contained between 1K and 5K vertices.
* rig_info_remesh: Rigging information files corresponding to the remeshed obj. Joints, hierarchy and root are the same. The skinning is recalculated based on nearest neighbor from each remeshed vertex to original vertices.
* pretrain_attention: Pre-calculated supervision to pretrin the attention module, which are calculated by the script geometric_proc/compute_pretrain_attn.py. Each file is a N-by-1 text where N is the number of vertices corresponding to remeshed OBJ file, the i-th row stores the surpervision for vertex i.  
* volumetric_geodesic: Pre-calculated volumetric geodesic distance between each vertex-bones pair. The algorithm is an approaximation, which is implemented in geometric_proc/compute_volumetric_geodesic.py. Each file is an N-by-B numpy array where N is the number of vertices corresponding to remeshed OBJ file, B is the number of bones, and (i, j) stores the volumetric geodesic distance between vertex i and bone j. 
* vox: voxelized models used for inside/outside check. Obtained with [binvox](https://www.patrickmin.com/binvox/). The resolution of the grid is 88x88x88.

After downloading the pre-processed data, one needs to create the data directly used for training/testing, please check and run our script: 

`python gen_dataset.py`

Remember to change the root_folder to the directory you uncompress the pre-processed data.

## Training

### Skinning prediction:
Run the following command to start training the model.

    `python -u run_skinning.py --train_folder='DATASET_DIR/train/' --val_folder='DATASET_DIR/val/' --test_folder='DATASET_DIR/test/' --checkpoint='checkpoints/skinnet' --logdir='logs/skinnet' --train_batch=4 --test_batch=4 --lr=1e-4 --Dg --Lf`

Run the following command to start training the model if the path of train_folder, val_folder and test_folder have been hard code in run_skinning.py
    `python -u run_skinning.py --checkpoint='checkpoints/skinnet' --logdir='logs/skinnet' --train_batch=4 --test_batch=4 --lr=1e-4 --Dg --Lf`
