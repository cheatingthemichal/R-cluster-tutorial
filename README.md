# How to cluster... in Python

The c2b2 cluster have two principal directories we are going to use. Here ehs stands for Environmental Health Sciences cluster and jc5647 is the Columbia user, replace them with your department and your username.

For all the examples in this guide **department** is **ehs** and **username** is **jc5647**. Change them to yours.

    1. Home directory:     /ifs/home/msph/ehs/jc5647
        - Here we are going to store all the scripts.

    2. Scratch directory: /ifs/scratch/msph/ehs/jc5647
       - Here we are going to store all the data and results.
## Steps and cooking book

1. Connect to the cluster... due to security or something idk we need to connect via using the port 59922, or using CyberDuck. The password should be the same one of your email. By default when you login you're going to be at your home directory.

   -  ssh -p 59922 jc5647@login.c2b2.columbia.edu


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
