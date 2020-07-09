# SciPy 2020 Introduction to Conda

Repo to store notes from Intro to Conda workshop at SciPy 2020

- Tutorial/Intro site: https://carpentries-incubator.github.io/introduction-to-conda-for-data-scientists/index.html

## Managing Environments

Create environment (notice pip at the end - always install pip)
- Note: see "Instructor's recommended environment creation method" below
```
conda create --name test-env package1 package2=1.0 pip
```

Activate/deactivate specific environment
```
conda activate <environment-name>
conda deactivate
```

Activate "base" environment
```
conda activate
```

List all environments
- shows all environments, whether they are installed in alternate directory or not
- If a directory is active, it's indicated with ```*```
```
conda env list
```

Install library available to conda
```
conda install library
```

Search for conda packages
```
conda search package1
```

Show most recent version of a package that is compatible with current environment
- This will show the version of numba that works, as well as dependent packages and versions that will be installed. Can choose to actually install or not
```
conda install numba
```

Install specific package
- If this package is incompatible with current environment it will fail and tell you
```
conda install package1=1.1
```

Prohibit conda from updating existing packages in environment when installing new package
- It will fail if package updates are required for an install
```
conda install package1 --freeze-installed
```

Deleting environments
- ```--all``` removes everything
```
conda remove --name <environment-name> --all
```

Deleting prefixed environments
```
conda remove --prefix ./env --all
```

### Instructor's recommended environment creation method

Create environment (named "env") in current working directory
- Instructor uses "env" since git is pre-configured to ignore "env" folders (you version control the environment creation file, not the actually environment folder)
```
conda create --prefix ./env ipython pandas python pip
```

Active environment created in non-standard directory
```
conda activate ./env
```

- disable auto activation of conda base when opening terminal
```
conda config --set auto_activate_base false
```

### Environment file

- normal name: ```environment.yml```
- standard:
```yml
name: machine-learning-env

dependencies:
	- ipython
	- matplotlib
	- pandas
	- pip
	- python
	- scikit-learn
```

- instructor doesn't use a name, since he uses ```--prefix```
```yml
name: null

dependencies:
	- ipython
	- matplotlib
	- pandas
	- pip
	- python
	- scikit-learn
```

- example that references pip requirements.txt and a pip standalone install
```yml
name: null

dependencies:
	- ipython
	- matplotlib
	- pandas
	- pip
		- -r file:requirements.txt
		- combo
	- python
	- scikit-learn
```

- always version control yml file

Create environment using environment file
```
conda env create --prefix ./env --file environment.yml
conda activate ./env
```

- export environment to environment.yml
- **WARNING**: this really only works if you are moving it to the exact same OS (this export includes packages specific to the OS (different for MacOS, Linus, Windows, etc)
	- Best to always also have the smaller version that specific includes packages you use (instead of dependencies, sub dependencies, etc)
```
conda env export --name machine-learning-env > environment_linux.yml
```

- update current environment
```
conda env update --prefix ./env --file environment.yml  --prune
```

- remove and recreate environment
```
conda env create --prefix ./env --file environment.yml  --force
```

### Add channels for additional packages

- I couldn't find xgboost in the channels setup by conda by default, so I add conda-forge
- After adding this channel I was able to install xgboost
```
conda config --add channels conda-forge
```

### Make Jupyter aware of conda environment through custom kernel

- add ```- ipykernel=5.3``` (version for example) to environment.yml, and force recreate environment (if already created)
- After recreation, ```conda activate ./env``` environment, and run
```
python -m ipykernel install --user --name xgboost-env --display-name "XGBoost"
```
- A new option will be added to "Notebook" and "Console" options in Jupyter tab launch options
- The new option will also be available in the kernel drop down when a notebook is open
- **NOTE:** May need to refresh tab before it shows up


### Conda Channels

- channels are added to environment.yml
- When environment is being created conda looks into the first channel listed for each package
	- In the example below, if package is found in pytorch it's installed; if it's not found, conda then looks into conda-forge; if it's not found, conda then looks into default channels
```yml
name: null

channels:
  - pytorch
  - conda-forge
  - defaults

dependencies:
  - cudatoolkit=10.1
  - pip=20.0
  - python=3.7
  - pytorch=1.5
  - torchvision=0.6
```