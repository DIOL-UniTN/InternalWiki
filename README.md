# Internal Wiki

### Requirements
0. We reccomend to use a virtual environment to keep everything clean. 
 `venv` or `virtualenv` or even `Anaconda` can be used.
 For example, one can install Anaconda according to its [installation page](https://docs.conda.io/projects/conda/en/latest/user-guide/install/index.html). Or 
1. Clone this repo:
```
git clone https://github.com/DIOL-UniTN/InternalWiki.git
cd InternalWiki
```
2. Create a virtual environment `wiki_env` and install requirements.
``` venv
python -m venv wiki_env
source wiki_env/bin/activate
pip install -r requirements.txt
```

``` virtualenv
virtualenv wiki_env
source wiki_env/bin/activate
pip install -r requirements.txt
```

``` Conda
conda create --name wiki_env
conda activate wiki_env
pip install -r requirements.txt
```

## Run
```
mkdocs serve
```

You'll find the wiki site at `localhost:8000`