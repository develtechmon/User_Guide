# Getting Started

## This userguide explain how to migrate python package to different python version to ensure neccessary package is installed

## Windows
1. To list down the python version use below command
```
a. py -3.9 --version
b. py -3.11 --version
```

2. To list the pip based on different python version
```
a. py -3.9 -m pip list
b. py -3.11 -m pip list
```

3. To migrate old pip package to new pip environment assuming that my python version change from 3.9 to 3.11. We will need to export `requirement.txt`
that consist of all python libraries
```
a. py -3.9 -m pip freeze > requirements.txt
b. cp ./requirements.txt /path/to/new_project
```

4. To install `requirements.txt` from `python 3.9` to `python 3.11` run below command
```
py -3.11 -m pip install -r ./requirements.txt
```

