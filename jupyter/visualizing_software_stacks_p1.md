# Visualizing software stacks in Jupyter Notebooks

## Part 1: Introduction

---


### The story behind

Let's approach this the agile way.

    As a data scientist and a Thoth developer,
    I want to be able to visualize software stacks,
    so that I could easily check the results of Thoth resolution
      and consume the observations in Jupyter Notebook more easily
      and customize the software stack visualization

Acceptance criteria are quite obvious -- the result of visualization should be easy to navigate, understandable and clear and should also be compatible with Jupyter Notebook.

We can write down the first point in the checklist:

    1) Make sure visualizations work in Jupyter Notebook

Now we should proceed and start thinking through all the steps that should be on our checklist. First, *what is a software stack?* __Software stack __ is basicaly a hierarchical structure with root node being the package (particularly Python package), and the children are transitive dependencies of this package.

Suppose we are still talking just about package *names*. That should be easy, right? There absolutely *have to* be libraries that can handle that. Well, there are...

![Software stack with transitive dependencies](https://i.stack.imgur.com/xEIVE.png)

<center><i>Software stack with transitive dependencies</i></center>

<br>

That certainly is *NOT* understandable and clear. Unfortunately, most of existing Python libraries do produce output more or less similar to this.

Imagine then, that each of those pretty little bubbles in the graph (think dependency) has a ton of possible versions that we might eventually wanna see as well! Now that is a problem. One that static plots won't solve. Hence we need to go interactive. Second point in the checklist is pinned down, then:

    2) Provide both static and interactive options to visualize the software stack

With that in mind, let's explore are __existing options__.

Among the things to consider, in alphabetical order:

- Community
- Customizability (whatever the word means)
- Ease of use
- Extensibility
- Flexibility
- Performance
- Persistence
- Prettiness
- Suitability (for hierarchical structures)

Why are all those things important for us?

:TODO: elaborate

Please, bear in mind that the following review is highly subjective and biased. It is perfectly OKAY if you don't agree and in fact, I highly encourage you not to take this information as if it was written in stone. Try, see for yourself and make your own opinion.

I will dig a little bit into few libraries that are supposed to be suitable candidates for our software stack visualization.

> *Scroll down to [summary table](#summary) if you want to skip this perhaps over-explaining self-talk*.

<br>

#### Python interactive plotting libraries

##### [plotly]

This is for most people who work in Python the first interactive plotting library which comes to mind. Plotly has been around for a while now and it's getting more and more mature. It has strong community, great developer base and also existing documentation (yay!).

Pros:

🗸 Community<br>
🗸 Prettiness<br>

The plots produced by the library are mature and pretty

Cons:

✗ Extensibility<br>
✗ Performance<br>
✗ Persistence<br>

> ⚠️ *HUGE amount of code needed to produce a plot*

Important thing to note here, and that is something that most of the libraries that I am gonna mention here or have mentioned already, have in common, is that the plots are not persistent. By persistency I mean that they do not disappear on notebook refresh, reload or restore and that they are visible in the published notebook as well.

Something in between:

○ Customizability<br>
○ Ease of use<br>
○ Flexibility<br>
○ Suitable for hierarchical structure<br>


[plotly] is relatively easy to use and quite flexible in the sense that it allows us to plot various kinds of plots, including much wanted hierarchical structures.
In this case, however, we are limited to networks (that is, graph structures) and in order to introduce any kind of layout, we have to step up our game and go into tools like [networkX](https://networkx.github.io/) or [igraph](https://igraph.org/), which add another layer of complexity.

To a certain extent we can customize the graphs, like color, hints and tooltips. But custom actions are not possible. This can be a problem in the future if we wanted to add some custom interaction, buttons, notifications, etc...

:TODO: at least 2 more libraries


##### Summary of interactive plotting libraries

:TODO: table

All of the libraries above use JavaScript frontend to visualize the data. Typicaly this is [d3], [threejs] or [BokehJS]. The common downside of those libraries is that, obviously, you have to learn how to use it. And when you do, you hope that it is the be-all and end-all. And trust me, it is not.

There is one more additional problem I have not mentioned and which definitely *should * be mentioned and that is __safety__. Jupyter by default stores the JavaScript code which produced the plot and runs it on notebook reload. Whether user ran the cell or not!.

> ⚠️ Jupyter by default stores the JavaScript code which produced the plot and runs it on notebook reload. Whether user ran the cell or not!.

<br>

__What other options do we have?__

We could potentially combine a plotting library with [Jupyter Widgets](https://ipywidgets.readthedocs.io/en/stable/) and either reuse existing widgets and actions to interact with our plots produced by one of the libraries above or we could eventually write custom widgets.
The former option, spoken from experience, does not guarantee consistent and flake-free workflow and its performance is not very satisfying, because it usually has to update the whole cell output. This also produces some unpleasant freezes and lags.

The latter one is quite interesting. But you may have noticed that we're already getting very close to **writing our own library**, since this would include learning internals of Jupyter widget and interaction and binding the JavaScript frontend to it. Oh boy, that is a bit terrifying!

Don't worry, we're not gonna do that. At least not yet ... instead, we will introduce a novel approach, which will be described in the following article.

<br>

#### Let's summarize and conclude

:TODO: summarize

[plotly]: https://plot.ly/
