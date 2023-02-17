# How to run R code on the c2b2 cluster.
Based on Jaime's Python tutorial here: https://github.com/ChaosDonkey06/c2b2_cluster_example

## Basic Steps

1. Connect to the cluster. Replace mah2350 with your uni. The password should be the same as to your email.

    -  ssh -p 59922 mah2350@login.c2b2.columbia.edu
    
2. Once you log onto the cluster you will automatically be placed in your home directory.
```
pwd
=>/ifs/home/msph/ehs/mah2350
```
The ending is based on your deparment and uni. Make sure to change it accorindgly throughout the tutorial. Move to your scratch directory, where you will want to store results and data.
```
cd /ifs/scratch/msph/ehs/mah2350
```
Create a folder to store your R libraries.



2. **Installing packages:** Packages are installed in the home director, I prefer using anaconda which is installed in the cluster. Also, as a general good practice I like using envs, see CREATE_ENV.md for how to crete a env.

    To install a python package in your home dir you can follow the step below:
    ```bash
        1) Type qrsh
        2) Type cd /nfs/apps/anaconda/anaconda3/bin
        3) source activate your_env_name # this is optional if you're using or not an environment.
        4) Type pip install  --install-option="--prefix=/path_to_directory" package_name
    ```

### Create python script **hello_world.py**
The hello_world.py script looks like:

```python
print("hello world")
```

## Creat submission script run_script.sh

Of course there're multiple ways of creating this scripts, I like having one script per run so I can track what I've done. The run_script.sh script looks like:

```bash
#!/bin/bash
#$ -cwd -S /bin/bash
#$ -l mem=8G
#$ -l time=18:30:
#$ -pe smp 4
#$ -N name_you_want -j y
#$ -M jc5647@columbia.edu -m aes

module load anaconda/conda3                      # load anaconda.
source activate amr_hospitals                    # activate your environment.
cd /ifs/scratch/msph/ehs/jc5647/your_script_path # move to your project directory.

python hello_world.py
```

In general there are 4 important parameters
