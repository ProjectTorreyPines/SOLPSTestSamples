# TestSamples

## Sample SOLPS cases
Each case should have a top-level folder named:
<device>_<last name of originator/provider>_<shot number or similar identifier>_<time in ms>
for example:
D3D_Ma_184833_03600
is a DIII-D model by Xinxing Ma, based on shot 184833 at 3600 ms

Within the parent folders, there should be one or more run folders, a baserun folder (common to all the runs), and some equilibrium information. If IMAS-style files are available, they can go in an IMAS folder.
EQDSK: The equilibrium data should be in a folder called EQDSK.
baserun: The baserun folder should be called baserun.
IMAS: any IMAS-format files go in this folder (there might not be any)
The run folders have whatever names the original modeler gave them.

## Using dvc
This repo stores test sample data files using [DVC](https://dvc.org/doc). If you want to add or modify data files "pointed by" this repository, you will need to install dvc locally.

### Installing dvc

Preferred method for installing dvc is using conda. Here I give the commands to create a new conda environment just ofr dvc and installing it. This works on all OS.

```
conda create --name dvc
conda activate dvc
conda install -c conda-forge mamba
conda install -c conda-forge dvc dvc-ssh
```

### Setting ssh config

This repo is using nfs mount points in iris or omega for storing the files. So you need access to either of the two. To ensure that the repo does not have to keep your loging information, you should have your local ssh setup such that:


```
ssh iris.gat.com
```
or 
```
ssh omega.gat.com
```

logs you into the computers directly without specifying username or getting password prompts. For this, you will need to add your ssh key to these computers and specify key location and username in your ssh config:

#### Password free loging setup
```
ssh-keygen -t rsa                 # If you do not have this already
ssh-copy-id user@iris.gat.com     # Copies your ssh key to iris with one time password prompt, replace user with your username
ssh-copy-id user@omega.gat.com    # Copies your ssh key to omega with one time password prompt
```

#### Username free ssh login

Following should work for most unix systems, replace <username> with your iris or omega username:

For iris:
```
echo "Host iris iris.gat.com" >> ~/.ssh/config
echo "  Hostname iris.gat.com" >> ~/.ssh/config
echo "  IdentityFile ~/.ssh/id_rsa" >> ~/.ssh/config
echo "  User <username>" >> ~/.ssh/config
```

For omega:
```
echo "Host omega omega.gat.com" >> ~/.ssh/config
echo "  Hostname omega.gat.com" >> ~/.ssh/config
echo "  IdentityFile ~/.ssh/id_rsa" >> ~/.ssh/config
echo "  User <username>" >> ~/.ssh/config
```

### Adding/ new data files or directories or updating existing files

Please follow the format specified above to inser t a enw data file in the correct existing directory or creating a new top-level directory. For sake of keeping data consistent, we hash the top-level directory in dvc directly instead of adding all individual files. For example, for adding D3D_Ma_184833_03600 top level directory, we do:
```
dvc add D3D_Ma_184833_03600
git add D3D_Ma_184833_03600.dvc
git commit -m "Adding DIII-D model by Xinxing Ma, based on shot 184833 at 3600 ms"
git push
dvc push
```

Even if you are updating a file inside the top level directoy, use the above commands to add the top level directory only, dvc will track the changes to the files correctly.

#### So where is the data stored really?

Git keeps the version control information through .dvc files of corresponding directories. These files point to the location in server where the files are stored. The files are currently stored in `/fusion/projects/sparc/detach_ctrl/solps/TestSamples` but the internal file structure is not human readable. Best way to browse through the data is to clone this repository and doing dvc pull.