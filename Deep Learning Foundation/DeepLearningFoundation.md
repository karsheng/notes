# Deep Learning Foundation
## Anaconda
1. `numpy` is a dependency of `pandas`.
1. To install `numpy` and `panda` - `conda install numpy pandas` or `conda install pandas`
1. Cretae environment to isolate projects:
	1. `conda create -n env_name list of packages`
	1. `conda create -n py3 python=3` or `conda create -n py2 python=2`
	1. `source activate my_env` to enter the environment (on MacOS or Linux)
	1. If `conda install package_name` used to install, the packages will only be available when you're in the environment.
1. `conda list` to check packages installed. 
1. `source deactivate` to deactivate the environment.
1. Share environments such that others can install all the packages used in your code with the correct version.
1. Save packages to a YAML file with `conda env export > environment.yaml`.
1. Cretae environment from an environment file used `conda env create -f environment.yaml`
1. Use `conda env list` to list down all the environments created.
1. Default environment - i.e. environment used when you're not in one, is called `root`.
1. To remove an environment, use `conda env remove -n env_name`
1. When sharing code on GitHub, it's good practice to make an environment file and include it in the repository. This will make it easier for people to install all the dependencies for your code.
1. Include a pip `requirement.txt` file using pip freeze for people not using conda.
1. [Conda myths and misconception](https://jakevdp.github.io/blog/2016/08/25/conda-myths-and-misconceptions/)
1. [Conda documentation](https://conda.io/docs/using/index.html)


## Jupyter Notebooks
1. Jupyter notebook is a web application that allows you to combine explanatory text, math equations, code, and visualizations all in one easily sharable document.
1. You could download the data, run the code in the notebook, and repeat the analysis, in effect detecting the gravitational waves yourself!
1. Basically just put everything in one place.
1. 
1. Literate programming - Notebooks are a form of literate programming proposed by Donal Knuth in 1984. In his words:
> "Instad of imagining that our main task is to instruct a computer what to do, let us concentrate rather on explaining to human beings what we want a computer to do."
![notebook-components](img/notebook-components.png)
1. The central point is the notebook server. You connect to the server through your browser and the notebook is rendered as a web app. Code you write in the web app is sent through the server to the kernel. The kernel runs the code and sends it back to the server, then any output is rendered back in the browser. When you save the notebook, it is written to the server as a JSON file with a .ipynb file extension.
1. The great part of this architecture is that the kernel doesn't need to run Python. Since the notebook and the kernel are separate, code in any language can be sent between them. For example, two of the earlier non-Python kernels were for the R and Julia languages. With an R kernel, code written in R will be sent to the R kernel where it is executed, exactly the same as Python code running on a Python kernel. IPython notebooks were renamed because notebooks became language agnostic. The new name Jupyter comes from the combination of Julia, Python, and R. If you're interested, here's a list of available kernels.
1. Another benefit is that the server can be run anywhere and accessed via the internet. Typically you'll be running the server on your own machine where all your data and notebook files are stored. But, you could also set up a server on a remote machine or cloud instance like Amazon's EC2. Then, you can access the notebooks in your browser from anywhere in the world.
1. `shift + enter` to run code and move to the next cell. `ctrl + enter` to run and stay at the same cell.
1. `tab` for auto completion.
1. `shift + tab` to bring up the tooltip, twice for more info (cool stuff!)
1. Magic commands are preceded with one or two percent signs (`%` or `%%`) for line magics and cell magics, respectively. Line magics apply only to the line the magic command is written on, while cell magics apply to the whole cell.
1. These magic keywords are specific to the normal Python kernel. If you are using other kernels, these most likely won't work.
1. Use `timeit` magic command to time how long it taks for a function to run.
1. Use `matplotlib` to create visualizations.
1. On higher resolution screens such as Retina displays, the default images in notebooks can look blurry. Use %config InlineBackend.figure_format = 'retina' after %matplotlib inline to render higher resolution images.
1. Examples
`%timeit`
![magic timeit](img/magic-timeit.png)
`%%timeit`
![magic timeit2](img/magic-timeit2.png)
`%matplotlib`
![magic matplotlib](img/magic-matplotlib.png)
`%pdb` - debugging
![magic pdb](img/magic-pdb.png)
1. [List of available magic commands](http://ipython.readthedocs.io/en/stable/interactive/magics.html)
1. Notebooks are just big JSON files with the extension `.ipynb`
1. To convert a notebook to an HTML file: `jupyter nbconvert --to html notebook.ipynb`
1. Convert notebook to slideshow and serve it with HTTP server to see the presentation immediately: `jupyter nbconvert notebook.ipynb --to slides --post serve`

## Regression
1. There are a lot of machine learning models out there and one of them is called the neural network. When we use the neural network that not just one or two but many layers deep to make a prediction we call that deep learning.
1. Deep learning is a subset of machine learning that has outperformed almost every other type of model almost every time, a huge range of task.
1. Machine learning is classified into three different styles:
	1. Supervised learning - give a model a label data set to get feedback on what's correct and what's not.
	1. Unsupervised learning - give a model a data set without labels. No feedback on what's correct or not and it has to learn by itself while the structure of the data is to solve a given task.
	1. Reinforcement learning - model isn't given feedback right off the bat, it only gets it when it achieve it's goals.
	1. E.g. Reinforcement learning - chess bot only gets feedback when it wins the game. Supervised learning - gets feedback for every move. Unsupervised learning - no feedback even after it won.
1. Sample Code for Linear Regression:
```Python
# TODO: Add import statements
import pandas as pd
from sklearn.linear_model import LinearRegression
# Assign the dataframe to this variable.
# TODO: Load the data
bmi_life_data = pd.read_csv('bmi_and_life_expectancy.csv')
y_values = bmi_life_data[['Life expectancy']]
x_values = bmi_life_data[['BMI']]

# Make and fit the linear regression model
#TODO: Fit the model and Assign it to bmi_life_model
bmi_life_model = LinearRegression()
bmi_life_model.fit(x_values, y_values)

# Mak a prediction using the model
# TODO: Predict life expectancy for a BMI value of 21.07931
laos_life_exp = bmi_life_model.predict([21.07931])
```
1. Linear regression works best when the data is linear.
1. Linear regression is sensitive to outliers.
1.Multiple linear regression:
`y = m1x1 + m2x2 + m3x3 + ... + mnxn +b`

## Neural Network
1. Each neuron has three jobs:
	1. Receive a set of signals from it's dendrites
	1. Integrate those signnals to determine whether the info should be passed on in the cell body(or Soma)
	1. If the sum of the signals passes a certain threshold send this resulting signal i.e. the action potential onwards via it's axon and next set of neurons.
1. First computational model of a neuron (1943) - Neuron receives binary inputs, sum them, and if the sum exceeds a certain threshold value out put a 1 if not output a 0.
1. The perceptron - single layer feedforward neural network - data disclosed in one direction i.e. forward.
1. The perceptron incorporate the idea of weights on the inputs so given some training set of input-output examples it should learn a function from it by increasing or decreasing the weights continuously for each example depending on what it's output was. These weight values are mathematically applied to the input such that after each iteration, the ouput prediction gets more accurate. This process is called training.
1. `