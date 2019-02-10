# Guide to better pandas DataFrame representation

## Interaction between Javascript and Python

<br>

### Foreword

You've found this article so I assume that you are already familiar with Jupyter notebooks and pandas DataFrames. These will not be introduced in this post and although I'll be very glad if you continue to read this article anyway, I would strongly suggest you to continue reading only if you already have at least a brief experience with pandas library and Jupyter Notebooks.

Also, I might sometimes interchange words Jupyter and IPython. This is due to the evolution of the project Jupyter itself and even though I'll do my best not to confuse the two, there are certain components which usually suriprise me to which one they actually belong. You can read more on that [in the dedicated article from DataCamp](https://www.datacamp.com/community/blog/ipython-jupyter).

<br>

### What are [DataTables]?

Simply put, [DataTables] are HTML tables augmented by advanced interaction controls like `search`, `sort` or responsivness etc., which are run on javascript. They allow for custom modifications and styling as well.

Pros:

- clarity
- interactivity
- modularity
- performance
- responsivness

Cons:

- requires setup (at least for now)

And this is what we're trying to achieve:

![DataTable representation of pandas DataFrame](/datatables/static/img/datatables.png)

<br>

### Refresher on DataFrame representation

Quick refresher on DataFrames representation in Jupyter notebooks:

```python
import numpy as np
import pandas as pd

# example from http://pandas.pydata.org/pandas-docs/stable/getting_started/10min.html
df = pd.DataFrame({ 'A' : 1.,
                    'B' : pd.Timestamp('20130102'),
                    'C' : pd.Series(1,index=list(range(4)),dtype='float32'),
                    'D' : np.array([3] * 4,dtype='int32'),
                    'E' : pd.Categorical(["test","train","test","train"]),
                    'F' : 'foo' })
df
```

![Original pandas DataFrame](/datatables/static/img/dataframe.png)

<br>

### What happens under the hood?

When a code is executed in the cell, the last lines output is rendered. Let's take a look how that rendering actually happens.

Behind the scene, IPython determines whether an onject has declared one of the so called `"_repr_*_"` methods. The `*` there is for example `html`, `javascript`, `latex`, `svg` etc. You can read more on this [here](https://ipython.readthedocs.io/en/stable/config/integrating.html). If none of those method is implemented, there is a fallback to `__repr__` magic method.

In case of the pandas DataFrame, it is `_repr_html_` which is being called. This method, appart from doing some additional wrangling, returns simple HTML table as a string.

Example of what that string might look like:

```python
df.to_html()
```

```html
<table border="1" class="dataframe">\n  <thead>\n    <tr style="text-align: right;">\n      <th></th>\n      <th>A</th>\n      <th>B</th>\n      <th>C</th>\n      <th>D</th>\n      <th>E</th>\n      <th>F</th>\n    </tr>\n  </thead>\n  <tbody>\n    <tr>\n      <th>0</th>\n      <td>1.0</td>\n      <td>2013-01-02</td>\n      <td>1.0</td>\n      <td>3</td>\n      <td>test</td>\n      <td>foo</td>\n    </tr>\n    <tr>\n      <th>1</th>\n      <td>1.0</td>\n      <td>2013-01-02</td>\n      <td>1.0</td>\n      <td>3</td>\n      <td>train</td>\n      <td>foo</td>\n    </tr>\n    <tr>\n      <th>2</th>\n      <td>1.0</td>\n      <td>2013-01-02</td>\n      <td>1.0</td>\n      <td>3</td>\n      <td>test</td>\n      <td>foo</td>\n    </tr>\n    <tr>\n      <th>3</th>\n      <td>1.0</td>\n      <td>2013-01-02</td>\n      <td>1.0</td>\n      <td>3</td>\n      <td>train</td>\n      <td>foo</td>\n    </tr>\n  </tbody>\n</table>
```

To see the whole code of `pd.DataFrame._repr_html_` method, run the following code in the Jupyter cell:

```python
??df._repr_html_
```

The returned string of this method is then fed to `HTML` function from `IPython.core.display` module which embeds it into the output element of the current cell (more on that part later).

<br>

### Integrating Javascript into Jupyter notebook

Now that we know what is happening, we can start to think about the steps we'll have to take.

##### Step 1)  Load [DataTables] javascript library

Jupyter notebooks use requireJS. We can take advantage of that and add the [DataTables] library using [CDN] to the path.

At this point, for convenience we will use the build-in magic `%%javascript` to execute the code in the cell via `Javascript` function, again from the `IPython.core.display` module.

```javascript
%%javascript

require.config({
    paths: {
        DT: '//cdn.datatables.net/1.10.19/js/jquery.dataTables.min',
    }
});
```

> __NOTE__ that there is __NOT__ `.js` at the end of the link!

This code now allows us to call the `.DataTable()` function on the table DOM object.


##### Step 2) Create addressable table DOM object

We already know that IPython creates the table element by calling the `HTML` method. The problem is that we don't know how to address that table at this moment. There are multiple approach to solve this problem, but we'll stick to the one I believe is the easiest for now.

Let's add an `id` attribute to our rendered table. We can do that by calling the `to_html` method with parameter `table_id`

```python
from IPython.core.display import HTML

HTML(df.to_html(table_id='myTable'))  # choose whichever you like, if you want to
```

If you kindly access the developers console in your browser (this is usually done by either `F12` or `CTRL+SHIFT+C` shortcuts), you should see our table rendered with the given `id`.

What this allows us to do, is to use `jQuery` to find the table element on which we'd call `.DataTable()` method.

##### Step 3) Turn the HTML table element into DataTable

Let's do that. Once more we will make use of the `%%javascript` magic. Run the following code in another cell. Do not delete the cell with rendered HTML table, though!

```javascript
%%javascript

// the 'DT' here is the key in the `require.config` call
// make sure it matches the one from above
require(["DT"], function(DT) {
    $(document).ready( () => {
        // Turn existing table addressed by its id `myTable` into datatable
        $("#myTable").DataTable();
    })
});
```

Your table in the previous cell's output should now change into DataTable representation. Simple, isn't it?

*Wait, something's not right. It doesn't look like the one you showed me.*

Correct, we have to add one additional piece. The CSS styling.

##### Step 4) Add the styling

```javascript
%%javascript

$('head').append('<link rel="stylesheet" type="text/css" href="//cdn.datatables.net/1.10.19/css/jquery.dataTables.min.css">');
```

By running this code, you'll append the link to the `head` part of our notebook. That way we don't have to include it anywhere else. The [CDN] was taken from the official [DataTables] pages.

Your DataFrame representation should have changed and it should be the same (or very similar) to mine from the beggining of this article.

There is still a couple of issues we can address but this is a decent starting point, isn't it?

<br>

### Improvements

A couple of issues we have right now:

- we probably don't want to make up a new `id` every time
- we certainly don't want to create that HTML table by hand at all
- we also refuse to add auxiliary cell to call the javascript for every `df` we want to look at
- finally, changing the element after it has been created is probably not the best way to go either


In other words, we just want to do nothing besides calling the `df` at the end of the jupyter cell and get the nice DataTable representation, right? Oh yea, we can do that. Let's do it than!

First thing we need to know is what method will be called for the DataFrame to be rendered correctly by IPython. In our case, we want to execute it as javascript. That means, we'll have to override `pd.DataFrame._repr_javascript_` method.

Lets create a function called `init_datatable_mode`.

```python
def init_datatable_mode():
    """Initialize DataTable mode for pandas DataFrame represenation."""
```

The first thing this function is going to do is load the [DataTables] library and add the styling.

```python
    display(Javascript("""
        require.config({
            paths: {
                DT: '//cdn.datatables.net/1.10.19/js/jquery.dataTables.min',
            }
        });

        $('head').append('<link rel="stylesheet" type="text/css" href="//cdn.datatables.net/1.10.19/css/jquery.dataTables.min.css">');
    """))
```

One thing to note here. We want the javascript code to be executed directly, we're not returning it though, which means that we'll have to use the `display` function itself. This is a built-in if you're working in Jupyter notebooks, otherwise it's a function from `IPython.core.display` module as well.

Then we're going to define a method which actually creates the html table for us and in place turns it into DataTable.

We'll make use of `jQuery` again and take adventage of IPython core, which makes it very easy for us to address the output of the current cell by the `element` object.
Hence we can just set the html content of the output by issueing `$(element).html(...)`.

```python
    def _repr_datatable_(self):
      """Return DataTable representation of pandas DataFrame."""
      # create table element
      script = (
          f'$(element).html(`{self.to_html(index=False)}`);\n'  # omit the index (optional)
      )

      # execute jQuery to turn table into DataTable
      # search for table of class `dataframe` among children of the current cell's output (repreented by the `element` object)
      script += """
          require(["DT"], function(DT) {
              $(document).ready( () => {
                  // Turn existing table into datatable
                  $(element).find("table.dataframe").DataTable();
              })
          });
      """

      return script
```

> __NOTE__: I omitted the index here for nicer table view, you don't (and probably shouldn't even by default) have to do that.


The very last piece is to bind the bethod to the `pd.DataFrame` class.

```python
    pd.DataFrame._repr_javascript_ = _repr_datatable_
```

<br>

### Putting it all together
```python
"""
Copyright 2019, Marek Cermak

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
"""

def init_datatable_mode():
    """Initialize DataTable mode for pandas DataFrame represenation."""
    import pandas as pd
    from IPython.core.display import display, Javascript

    # configure path to the datatables library using requireJS
    # that way the library will become globally available
    display(Javascript("""
        require.config({
            paths: {
                DT: '//cdn.datatables.net/1.10.19/js/jquery.dataTables.min',
            }
        });

        $('head').append('<link rel="stylesheet" type="text/css" href="//cdn.datatables.net/1.10.19/css/jquery.dataTables.min.css">');
    """))

    def _repr_datatable_(self):
        """Return DataTable representation of pandas DataFrame."""
        # classes for dataframe table (optional)
        classes = ['table', 'table-striped', 'table-bordered']

        # create table DOM
        script = (
            f'$(element).html(`{self.to_html(index=False, classes=classes)}`);\n'
        )

        # execute jQuery to turn table into DataTable
        script += """
            require(["DT"], function(DT) {
                $(document).ready( () => {
                    // Turn existing table into datatable
                    $(element).find("table.dataframe").DataTable();
                })
            });
        """

        return script

    pd.DataFrame._repr_javascript_ = _repr_datatable_
```

<br>

### Usage:

Execute the following in the notebook cell. You should see the table being rendered with javascript backed [DataTables].

```python
import numpy as np
import pandas as pd

init_datatable_mode()  # initialize [DataTables]
```

And let jupyter render the DataFrame new representation

```python
# example from http://pandas.pydata.org/pandas-docs/stable/getting_started/10min.html
df = pd.DataFrame({ 'A' : 1.,
                    'B' : pd.Timestamp('20130102'),
                    'C' : pd.Series(1,index=list(range(4)),dtype='float32'),
                    'D' : np.array([3] * 4,dtype='int32'),
                    'E' : pd.Categorical(["test","train","test","train"]),
                    'F' : 'foo' })
df
```

![DataTable representation of pandas DataFrame](/datatables/static/img/datatables.png)

<br>

### Conclusion

I love the DataTable representation. It often makes me more productive, speeds up the notebook, saves resources (the rendering part is expansive usually) and that `search` field is incredible.

Important to state, this approach does not apply only to [DataTables]. You can apply it to [d3](https://d3js.org/) as well (see for example [this article](https://www.stefaanlippens.net/jupyter-custom-d3-visualization.html)).

I hope that you found this useful. In the future, I might turn all this into either Jupyter extension or a python package on PyPI but for now, you are free to use it under the terms of [MIT](https://opensource.org/licenses/MIT) license as specified in the code block above.

[CDN]: https://datatables.net/
[DataTables]: https://datatables.net/
