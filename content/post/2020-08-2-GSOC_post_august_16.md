+++


title = "And then there were tests"
description = "Google summer of code week 12"
date = "2020-08-16"
tags = ["GSoC", "OpenWorm", "Travis", "Tests"]
slug = "gsoc-2020-august-16"

summary = "Google Summer of Code | Coding period week 11"


+++

Setting up [unit tests for devolearn](https://github.com/DevoLearn/devolearn/blob/master/devolearn/tests/test.py) was actually the first time that I was ever touching those testing libraries. While setting up the tests, [this](https://realpython.com/python-testing/) came to be the most useful resource I found.

## Why do we even need tests ? 

It is safe to assume that one day more contributors would come to [devolearn](https://github.com/DevoLearn/devolearn). When a future contributor makes a pull request, these tests would make sure that the pull requests are not breaking the code, and the tests would work as an early warning system if a pull request does break the code. 

In our case, we used [travis CI](https://travis-ci.org/) to run our tests online after each push/pull request. For this we had to set up a `.travis.yml` file on the same diirectory as the `setup.py`. 

A `.travis.yml` file looks like:

```
language: python
python:
  - "3.7"
  - "3.8"

dist: bionic

install: 
  - pip install .
  - pip install -r requirements.txt
script:  
  - python setup.py test 

```

I will break down the file bit by bit:

* `language: python` : Because we'll be running the tests on python. 

* `python:` This section specifies the versions of python on which we'll be running the tests. 

* `install:` Specifies the terminal commands one would need to run in order to install the required libraries. 

* `script:` Specifies the terminal command(s) needed to run the test(s).

On top of that, I had to add this to the `setup()` function in `setup.py`:

```
test_suite='nose.collector',
tests_require=['nose']
```

And as for the test functions themselves, the were pretty easy to set up. It was as simple as calling all the functions within a subclass of `unittest.TestCase`. One of the tests in the devolearn unit tests looks like as follows:

```
centroid_df = segmentor.predict_from_video(<args>)

self.assertTrue(isinstance(centroid_df, pd.DataFrame), "should be a pandas.DataFrame")
```

The snippet above makes sure that the object returned from the function `predict_from_video()` is a `pandas.DataFrame`. If something ever breaks in between, then this test would fail with the message in the 2nd arg of `self.assertTrue`.

After about an hour worth of errors and debugging, I was finally able to make this [weird flex](https://api.travis-ci.org/DevoLearn/devolearn.svg?branch=master&status=passed) on the readme.