# Jupyter Notebooks in production workflows
## Reproducibility and sharing

---

<div align="justify">

Jupyter Notebooks are being increasingly adopted by developers and data scientists for their interactivity and for their ease of use. However, Jupyter notebooks have not yet evolved into a production-ready development tool, although there is not much missing for this to happen.
In this article, I'll focus on the concept of *reproducibility* of Jupyter notebooks as development units with respect to the dependencies they have.

</div>

<div align="justify">

## Notebook requirements

Jupyter notebooks require a running kernel which executes the Python code. That kernel then refers to the user's or system-wide Python environment.
More experienced Pythonistas and Jupyter users then usually install a kernel that is **specific to a virtual environment**. I myself prefer to have a specific kernel for each notebook (unless I am working with a repository of Jupyter notebooks).

In any case, **Jupyter notebooks are by default not self contained units**. Therefore, we need to provide *requirements.txt*, *Pipfile*, or whatever manifest with notebook **dependencies** (requirements) so that we could execute the notebook.
This is quite inconvenient. For example, if we want to share the notebook, we have to include the requirements along with it. A good example is [binder](https://mybinder.org/) -- we actually need to create a GitHub repository in order to turn a notebook into an image (in the next post, I'll demonstrate how this can be done in a much fancier fashion).

So... What if a notebook *was* in fact a self contained unit? That would mean that we could share the notebook by itself, execute it right ahead or build an image from it. We would have a **reproducible piece of code.**

In the following section I'll showcase a library called [jupyter-nbrequirements] which does precisely that -- turns a Jupyter notebook into a self-contained unit by allowing us to **manage the notebook's dependencies** with ease without even leaving the notebook.

<div style="text-align:center">
<img alt="NBRequirements UI" src="https://raw.githubusercontent.com/CermakM/jupyter-nbrequirements/master/assets/ui.png">
<span>Fig. 1: Jupyter NBRequirements UI</span>
</div>

</div>

## Scenario A: Author's perspective

<div align="justify">

The first use case is from the author's perspective. That is, the author of the notebook uses [jupyter-nbrequirements] as a dependency management tool to embed the requirements into the notebook.


#### The 'old-school' approach


We'll start with a classical approach using integrated *jupyter magic commands*. At the end of this section, we'll see that there is also a UI (Fig. 1), but for some use cases, a code can still be desirable.


#### Create the environment for the notebook to run in

Say we want to do an EDA, we will probably need [pandas](https://pandas.pydata.org), a visualization library like [plotly](https://plot.ly) and some additional libraries to make our lives easier, like [sklearn](https://scikit-learn.org/stable/) and [pandas-profiling](https://github.com/pandas-profiling/pandas-profiling).

In a Jupyter notebook cell:

```
%dep add pandas --version ">=0.24.0"
%dep add plotly
%dep add sklearn
%dep add pandas-profiling
```


And perhaps our code would need some refactoring and linter checks later on, so let's add a `dev` dependency.

```
%dep add --dev black
```

You can now check the requirements that your notebook has by issuing `%requirements` (or `%dep`, which is just an alias for it) command:


```
%requirements
```
```
[packages]
pandas = ">=0.24.0"
plotly = "*"
sklearn = "*"
pandas-profiling = "*"

[dev-packages]
black = "*"

[[source]]
url = "https://pypi.org/simple"
verify_ssl = true
name = "pypi"

[requires]
python_version = "3.6"
```

> NOTE: The `%requirements` command is capable of **analysing library usage** and can detect additional dependencies that you notebook might need (still experimental and therefore not 100% reliable). You should check the output carefully and optionally add those dependencies as well.

Up to this point, we've been working only with the metadata. In order to create the environment and actually install the dependencies, you run the `%dep ensure` command (insipired by the golang's [dep](https://github.com/golang/dep), for those familiar with Golang).


```
%dep ensure
```

> The project currently uses `pipenv` for dependency resolution. However, there is an experimental [Thoth] resolution engine which also **optimizes the notebook dependencies** and provides recommendation about possible changes in the software stack. This resolution engine might be of great value for this library once it is stable.
The resolution engine can be specified as an argument to `%dep ensure --engine`.

```
%dep ensure --engine thoth
```

#### NBRequirements UI


Since [v0.4.0](https://github.com/CermakM/jupyter-nbrequirements/releases/tag/v0.4.0), [jupyter-nbrequirements] introduced a user interface (see Fig. 1)! Needless to say more -- check it out, interact with it and see what it can offer you!
The Jupyter magic commands are in sync with the UI, so don't worry old schoolers, you can still run the commands manually and the existing notebooks will work!

The UI will automatically look up the package you're trying to add and the relevant version this package has. That way, the notebook authors **dont't expose themselves to the risk that they specify a non-existing version**.


## Scenario A: User's perspective

</div>

<!-- --- -->

<!-- Resources -->

[jupyter-nbrequirements]: https://github.com/CermakM/jupyter-nbrequirements
[Thoth]: https://github.com/thoth-station

<!-- <blockquote>
<p>Written with <a href="https://stackedit.io/">StackEdit</a>.</p>
</blockquote> -->

