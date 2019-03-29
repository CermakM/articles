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

Now we should proceed and start thinking through all the steps that should be on our checklist. First, *what is a software stack?* __Software stack __ is basically a hierarchical structure with root node being the package (particularly Python package), and the children are transitive dependencies of this package.

Suppose we are still talking just about package *names*. That should be easy, right? There absolutely *have to* be libraries that can handle that. Well, there are...

![Software stack with transitive dependencies](https://i.stack.imgur.com/xEIVE.png)

<center><i>Software stack with transitive dependencies</i></center>

<br>

That certainly is *NOT* understandable and clear. Unfortunately, most of existing Python libraries do produce output more or less similar to this.

Imagine then, that each of these pretty little bubbles in the graph (think dependency) has a ton of possible versions that we might eventually wanna see as well! Now that is a problem. One that static plots won't solve. Hence we need to go interactive. Second point in the checklist is pinned down, then:

    2) Provide both static and interactive options to visualize the software stack

<br>

### Factors to consider when choosing suitable plotting library

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

Why are all these things important for us?

__Community__ is a pleasant benefit whichaalso indicates maturity, stability and developer base. These are things that you appreciate when you search for a solution to a problem or a 'Getting started' tutorial and which can greatly improve and speed up the development process.

By a library being __customizable__, I understand at least the possibility to choose a set of colors, define custom fonts, add annotations or choose layout which I like. Simple things, really.

__Ease of use__ ... ye, don't you too like complicated things which give you coding nightmares and electric shocks whenever you touch the keyboard to implement them? I wonder how it feels not to have these around.

__Extensibility__ will be especially important for us in future. Imagine for example that we wanted to add custom buttons, icons, actions to the plot itself. That'd be cool, right?

<blockquote>
The future belongs to those who believe in the beauty of their dreams.
<br>
<cite>Elanor Roosevelt</cite>
</blockquote>

__Flexibility__ kinda goes hand in hand with Customizability and Extensibility and yet it is not the same thing in my understanding. A library offers flexibility in plotting if I can visualize the same data in multiple ways without changing (or chaning too much) neither the data structure nor the code which produced the plot.

__Performance__ and __Prettiness__ are quite self-explaining in my opinion and by __persistence__ it is meant the capability of the library to preserve the data when we exit the notebook and restore them on notebook reload.


And the last but not least, the overall __suitability to visualize hierarchical structures__. There are libraries that might actually live up to all of the above expectations, but this is probably the most crutial one. If the library is not capable of visualizing hierarchical data, the necessary condition is not met and the library is rejected immediately.

With all that being said, let's explore are __existing options__.

<br>

### Exploring the existing options

Please, bear in mind that the following review is highly subjective and probably also biased. It is perfectly OKAY if you don't agree and in fact, I highly encourage you not to take this information as if it was written in stone. Try, see for yourself and make your own opinion.

I will dig a little bit into some of the libraries that are supposed to be suitable candidates for our software stack visualization.

> *Scroll down to [summary table](#summary) if you want to skip this perhaps over-explaining self-talk*.

<br>

#### [plotly]

This is for most people who work in Python the first interactive plotting library which comes to mind. Plotly has been around for a while now and it's getting more and more mature. It has strong community, great developer base and also existing documentation (yay!).

Pros:

🗸 Community<br>
🗸 Customizability<br>
🗸 Prettiness<br>

The plots produced by the library are very pretty, the interaction is smooth and we can eventually even use the online editor to customize the plot, the legend, add some annotations etc...

Cons:

✗ Extensibility<br>
✗ Performance<br>
✗ Persistence<br>

> ⚠️ *HUGE amount of code needed to produce a plot*

Important thing to note here, and that is something that most of the libraries that I am gonna mention here or have mentioned already, have in common, is that the plots are not persistent. By persistency I mean that they do not disappear on notebook refresh, reload or restore and that they are visible in the published notebook as well.

Something in between:

○ Ease of use<br>
○ Flexibility<br>
○ Suitable for hierarchical structure<br>


[plotly] is relatively easy to use and quite flexible in the sense that it allows us to plot various kinds of plots, including much wanted hierarchical structures.
In this case, however, we are limited to networks (that is, graph structures) and in order to introduce any kind of layout, we have to step up our game and go into tools like [networkX](https://networkx.github.io/) or [igraph](https://igraph.org/), which add another layer of complexity.

To a certain extent we can customize the graphs, like color, hints and tooltips. But custom actions are not possible. This can be a problem in the future if we wanted to add some custom interaction, buttons, notifications, etc...

<br>

#### [Bokeh]

To cite:

> Bokeh is an interactive visualization library that targets modern web browsers for presentation. Its goal is to provide elegant, concise construction of versatile graphics, and to extend this capability with high-performance interactivity over very large or streaming datasets. Bokeh can help anyone who would like to quickly and easily create interactive plots, dashboards, and data applications.

Pros:

🗸 Community<br>
🗸 Performance<br>

The community is amazing. Bokeh is used really extensively, it integrates smoothly with [Holoviews], has great documentation, bunch of tutorials. This is really great!

Cons:

✗ Flexibility<br>

Bokeh really binds the user to use pre-defined models in a defined way. This is usually a good way (less error-prone), but in this case, it doesn't suite our purpose.

Something in between:

○ Customizability<br>
○ Extensibility<br>
○ Ease of use<br>
○ Persistence<br>
○ Prettiness<br>
○ Suitable for hierarchical structure<br>

> I am not a fan of this widget-like approach to plotting at all, I prefer the plotly way or even better, the [plotnine](https://github.com/has2k1/plotnine) way, to output graphs (maybe becouse I started my data visualizaiton journey in R and I absolutely love [ggplot2](https://ggplot2.tidyverse.org/)). That's why I put the `Ease of use` in this section.

The [plots](https://bokeh.pydata.org/en/latest/docs/user_guide/graph.html) produced by [Bokeh] are not as visually pleasing as the ones outputted by [plotly]

Again, similar to the [plotly], if you wish to plot hierarchical structures, you gotta start with [networkX]. The outputs then look something like [this](https://ggplot2.tidyverse.org/), which from my point of view is not satisfying.

Bokeh really focuses on interactivity. There are some neat features liked linked views and it performs a bit better, even with larger amount of data. However, we are limited to the capabilities of pre-built models.

The extensibility is a little bit better here, because every graph has its own model. So theoreticaly, we would just need to either extend the existing models or write a new model.

<br>

#### [holoviews]

With HoloViews, you can usually express what you want to do in very few lines of code, letting you focus on what you are trying to explore and convey, not on the process of plotting.

It specializes in bundling the data together with its metadata. This approach is very different from other plotting libraries and seems to be very effective when you get used to it.

Again, from the official [introduction](http://holoviews.org/getting_started/Introduction.html):

> With HoloViews, instead of building a plot using direct calls to a plotting library, you first describe your data with a small amount of crucial semantic information required to make it visualizable, then you specify additional metadata as needed to determine more detailed aspects of your visualization. This approach provides immediate, automatic visualization that can be effortlessly requested at any time as your data evolves, rendered automatically by one of the supported plotting libraries (such as Bokeh or Matplotlib).

HoloViews is not itself a plotting library, it is more of a tool which allows other plotting libraries (like [Bokeh], [Matplotlib] or even [plotly]) to handle graphical representation of the data.

A killer feature here is __live streaming__ data and event system! It's not interesting for us at this particular moment, but it deffinitely is something worth mentioning.

Pros:

🗸 Community<br>
🗸 Customizability<br>
🗸 Ease of use<br>
🗸 Flexibility<br>
🗸 Performance<br>
🗸 Prettiness<br>

The community is great. As mensionted, [Holoviews] integrated with [Bokeh], there is plenty of tutorials in [pyviz] repo and it has very good documentation.

Its performance is *impressive* and it can handle *HUGE* amount of points due to the way it approaches data visualization. It is an amazing tool to use for heat maps and other intensity maps, for example.

It focuses on simple and quick usage, hence in a few lines of code you get quite a pretty output.

Cons:
✗ Persistence<br>

○ Extensibility<br>
○ Suitable for hierarchical structure<br>

It should be possible to reuse the great work there is done on [Holoviews] site and extend it to be suitable for hierarchical structures. In fact, since it uses Bokeh as one of the visualization extensions, it should be no more (or no less) difficult than writing a custom module for Bokeh itself (as mentioned before).

But the same comments here as mentioned in [plotly] and [Bokeh] sections.

So despite [Holoviews] being awesome (I mean it, really, I love it), it is not suitable for us either.

<br>

#### [mpld3]

This is the last library (check out the [summary table](#summary) for info about the rest) that I want to check more extensively, since this one is quite new to me and it looks very promissing. From the description:

> The mpld3 project brings together [Matplotlib](http://www.matplotlib.org/), the popular Python-based graphing library, and [d3], the popular JavaScript library for creating interactive data visualizations for the web. The result is a simple API for exporting your matplotlib graphics to HTML code which can be used within the browser, within standard web pages, blogs, or tools such as the IPython notebook.

Now, that is a neat idea! Get two things that people are closely familiar with -- matplotlib for Python team and d3 for team JavaScript and connect them together! Instant points to the ease of use column. [#ilike]

Pros:

🗸 Customizability<br>
🗸 Ease of use<br>
🗸 Extensibility<br>
🗸 Flexibility<br>
🗸 Performance<br>

Cons:

✗ Community<br>
✗ Performance<br>
✗ Persistence<br>

The [mpld3] introduces additional overhead in converting the matplotlib figure into d3. From what I understood, it also deploys it's own small HTTP server for comunication and copies over the whole JS templates. This, from my point of view, seemed like a bad performance indicator, although I might be wrong, I haven't actually tested it with bigger amount of data.

There seem to be quite a lot about [mpld3], has plenty of stars on [GitHub], but the last commit was a year ago, the examples are in old Jupyter notebook version and the open issues don't seem be answered very frequently.

Another problem here is that everything about the figure gets embedded into the notebook `*.ipynb` (yet it still does not preserve the output? Kinda weird.. ). And by everything I do mean *EVERYTHING*, if you wanted to take a closer look at the output of *one* cell in JS console, you'd see something like

```<string is too large to edit>```

That might also be a potential security issue.


Something in between:

○ Ease of extensibility <br>
○ Prettiness<br>
○ Suitable for hierarchical structure<br>

You may have noticed that I had to introduce a special category -- `Ease of extensibility`. That is because the [mpld3] makes it _really_ easy to write custom plugin. The catch is, however, that most of the code is in JavaScript, more specificaly, you guessed it, [d3]. So unless you're fimiliar with JavaScript AND [d3], this is gonna be hard for you.
Same for Prettiness and the other point, unless you know how to do it in [d3].

<br>

I leave it to you to explore the other libraries and check whether I missed something. I am sure I did, but I believe it was not anything so significant that it would change my opinion about the need of a novel, different and effective approach.

<br>

### [Summary and conclusion](#summary)

|                |            Community           |               Customizability              |                 Ease of use                |                Extensibility               |                 Flexibility                |                 Performance                |           Persistence          |                 Prettiness                 |                 Suitability                |
|----------------|:------------------------------:|:------------------------------------------:|:------------------------------------------:|:------------------------------------------:|:------------------------------------------:|:------------------------------------------:|:------------------------------:|:------------------------------------------:|:------------------------------------------:|
| bokeh          |  <div class="check">TRUE</div> | <div class="suboptimal">&lt;suboptimal&gt;</div> | <div class="suboptimal">&lt;suboptimal&gt;</div> | <div class="suboptimal">&lt;suboptimal&gt;</div> |       <div class="cross">FALSE</div>       | <div class="suboptimal">&lt;suboptimal&gt;</div> | <div class="cross">FALSE</div> | <div class="suboptimal">&lt;suboptimal&gt;</div> | <div class="suboptimal">&lt;suboptimal&gt;</div> |
| holoviews      |  <div class="check">TRUE</div> |        <div class="check">TRUE</div>       |        <div class="check">TRUE</div>       | <div class="suboptimal">&lt;suboptimal&gt;</div> |        <div class="check">TRUE</div>       |        <div class="check">TRUE</div>       | <div class="cross">FALSE</div> |        <div class="check">TRUE</div>       | <div class="suboptimal">&lt;suboptimal&gt;</div> |
| mpld3          | <div class="cross">FALSE</div> |        <div class="check">TRUE</div>       |        <div class="check">TRUE</div>       |        <div class="check">TRUE</div>       |        <div class="check">TRUE</div>       |       <div class="cross">FALSE</div>       | <div class="cross">FALSE</div> | <div class="suboptimal">&lt;suboptimal&gt;</div> | <div class="suboptimal">&lt;suboptimal&gt;</div> |
| plotly         |  <div class="check">TRUE</div> |        <div class="check">TRUE</div>       | <div class="suboptimal">&lt;suboptimal&gt;</div> |       <div class="cross">FALSE</div>       | <div class="suboptimal">&lt;suboptimal&gt;</div> |       <div class="cross">FALSE</div>       | <div class="cross">FALSE</div> |        <div class="check">TRUE</div>       | <div class="suboptimal">&lt;suboptimal&gt;</div> |
| ipywidgets + ? |  <div class="check">TRUE</div> |        <div class="check">TRUE</div>       |       <div class="cross">FALSE</div>       |        <div class="check">TRUE</div>       |        <div class="check">TRUE</div>       | <div class="suboptimal">&lt;suboptimal&gt;</div> | <div class="cross">FALSE</div> | <div class="suboptimal">&lt;suboptimal&gt;</div> | <div class="suboptimal">&lt;suboptimal&gt;</div> |

All of the libraries mentioned are great for visualizing data off the shelf and produce pretty results.

Plotly and is my personal favorite for quick high-quality plots with low-dimensional data. [Holoviews] kindof the same but for high dimensional or big amount of data and Bokeh or ipywidgets (more on that later) for more customized widget-like interaction, ideal for data tweaking.

However, when it comes to custom tweaked user experience, we hit the wall there. We'd need to dig deeper into the source code to see if we could fork it and build upon it in order to extend it specially for our use case.

Also, __none__ of those libraries solves the __persistency__ problem completely. That is, when we save the notebook and publish it for example to GitHub or nbviewer, the output will be lost. I think bokeh is quite close to persistent output, but it still not satisfactory.

All of the libraries above use JavaScript frontend to visualize the data. Typicaly this is [d3] or [BokehJS] or very customized JavaScript frontend. The common downside of these libraries is that, obviously, you have to learn how to use it. And when you do, you hope that it is the be-all and end-all. And trust me, it is not.

There is one more additional problem I have not mentioned and which definitely *should * be mentioned and that is __safety__. This would make another blog post so I am not gonna go into details here, just briefly.. Jupyter by default stores the JavaScript code which produced the plot and runs it on notebook reload. Whether user ran the cell or not!.

> ⚠️ Jupyter by default stores the JavaScript code which produced the plot and runs it on notebook reload. Whether user ran the cell or not!.

<br>

__What other options do we have?__

We could potentially combine a plotting library with [Jupyter Widgets](https://ipywidgets.readthedocs.io/en/stable/) and either reuse existing widgets and actions to interact with our plots produced by one of the libraries above or we could eventually write custom widgets.
The former option, spoken from experience, does not guarantee consistent and flake-free workflow and its performance is not very satisfying, because it usually has to update the whole cell output. This also produces some unpleasant freezes and lags.

The latter one is quite interesting. But you may have noticed that we're already getting very close to **writing our own library**, since this would include learning internals of Jupyter widget and interaction and binding the JavaScript frontend to it. Oh boy, that is a bit terrifying!

Don't worry, we're not gonna do that. At least not yet... instead, we will introduce a novel approach to integrate custom JavaScript code with Jupyter Notebooks and we will dig deeper into [d3] -- low level JavaScript library which I've already mentioned and which will enable us to do ... anything.

All of that in the next article. Stay tuned and thank you for reaching the end of this post!

<br>

### Afterword

:TODO:

---

[Bokeh]: https://bokeh.pydata.org/
[d3]: https://d3js.org/
[Holoviews]: http://holoviews.org/
[mpld3]: http://mpld3.github.io/
[plotly]: https://plot.ly/
[pyviz]: https://github.com/pyviz/pyviz
[threejs]: https://threejs.org/
