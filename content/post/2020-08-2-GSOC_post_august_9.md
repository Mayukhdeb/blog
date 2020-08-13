+++


title = "Publishing a PyPI package"
description = "Google summer of code week 12"
date = "2020-08-09"
tags = ["GSoC", "OpenWorm", "PyPI"]
slug = "gsoc-2020-august-9"

summary = "Google Summer of Code | Coding period week 10"


+++

First off, this blog post is definitely not an official guide, if you're looking for an official guide, have a look [here](https://packaging.python.org/tutorials/packaging-projects/).

This blog post is pretty specific to the PyPI package that I authored along with [Bradly](https://github.com/balicea) and [Ujjwall](https://github.com/ujjwalll).

## The project structure:

{{< figure src="https://raw.githubusercontent.com/DevoLearn/devolearn/master/images/project_structure.jpg" width="80%">}}

Each of the classes under `devolearn` have their own folder which would contain all the utils required for that class including the model files. 

For example, the folder `embryo_generator_model` would look like:

```
├── embryo_generator_model
    ├── embryo_generator_model.py
    ├── embryo_generator.pt
    ├── __init__.py
```

## The repo structure for PyPI

```
devolearn
├── LICENSE
├── README.md
├── devolearn
│   ├── __init__.py
│   ├── model_1
│   │    ├── model_1.py
│   │    ├── model_1.pt
│   │    ├── __init__.py
│   ├── model_2
│         ├── model_2.py
│         ├── model_2.pt
│         ├── __init__.py
│
├── setup.py

```

## Generating a distribution archive

Once the folder was structured, The next objective was to generate distribution packages for the package. These are archives that are uploaded to the Package Index and can be installed by pip. 

For this I had to install the latest versions of setuptools and wheel with:
```
python3 -m pip install --user --upgrade setuptools wheel

```

Then we had to run the following command  in the same directory as `setup.py`:

```
python3 setup.py sdist bdist_wheel
```
This command should output a lot of text and once completed should generate two files in the dist directory:

```
dist/
    devolearn-0.1.9.tar.gz
    devolearn-0.1.9-py3-none-any.whl
```

## Uploading the archive

First, we'd have to upload the package to test.pypi.org, and for that we'd have to [make](https://test.pypi.org/account/register/) an account. Then go to [this url](https://test.pypi.org/manage/account/#api-tokens) and create a new API token. 

Then we upload the package using the twine package:

```
python3 -m pip install --user --upgrade twine
```

And then:

```
python3 -m twine upload --repository testpypi dist/*
```

When prompted for a username, use `__token__` and then enter the token value as the password;

```
Uploading distributions to https://test.pypi.org/legacy/
Enter your username: __token__
Enter your password: your_token_value
```

## The problem with package size 

In order  to upload a package to test.pypi.org, it had to be under 100 mb, but devolearn was above 150 mb because of the model files. So the workaround was to not include the models in the `MANIFEST.in` file and downloading the models from GitHub directly using `wget` from within the package. The models would get downloaded when the user calls the model class for the first time. 

**Note:**  `MANIFEST.in` is a file that can be used to specify the **non** `.py` files that you'd want to include in the python package to be uploaded, for example, I included the `scaler.gz` file in our manifest file. 

## Uploading to PyPI

When you are ready to upload a real package to the Python Package Index you can do much the same as you did above,

```
python3 -m twine upload dist/*
```
## Done and dusted
This post was basically my own informal guide for uploading a package to PyPI. If you've read this far, you need to take a break from stalking me so hard. 