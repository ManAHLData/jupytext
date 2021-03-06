# Jupyter notebooks as Markdown documents, Julia, Python or R scripts

[![Pypi](https://img.shields.io/pypi/v/jupytext.svg)](https://pypi.python.org/pypi/jupytext)
[![Pypi](https://img.shields.io/pypi/l/jupytext.svg)](https://pypi.python.org/pypi/jupytext)
[![Build Status](https://travis-ci.com/mwouts/jupytext.svg?branch=master)](https://travis-ci.com/mwouts/jupytext)
[![codecov.io](https://codecov.io/github/mwouts/jupytext/coverage.svg?branch=master)](https://codecov.io/github/mwouts/jupytext?branch=master)
[![Language grade: Python](https://img.shields.io/lgtm/grade/python/g/mwouts/jupytext.svg?logo=lgtm&logoWidth=18)](https://lgtm.com/projects/g/mwouts/jupytext/context:python)
[![pyversions](https://img.shields.io/pypi/pyversions/jupytext.svg)](https://pypi.python.org/pypi/jupytext)
[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/mwouts/jupytext/master?filepath=demo)

You've always wanted to
* edit Jupyter notebooks as e.g. plain Python scripts in your favorite editor?
* do version control of Jupyter notebooks with clear and meaningful diffs?
* *collaborate* on Jupyter notebooks using standard (text oriented) merge tools?

Jupytext can convert notebooks to and from
- Julia, Python and R scripts (extensions `.jl`, `.py` and `.R`),
- Markdown documents (extension `.md`),
- R Markdown documents (extension `.Rmd`).

Jupytext is available from within Jupyter. You can work as usual on your notebook in Jupyter, and save and read it in the formats you choose. The text representations can be edited outside of Jupyter (see our [demo](#code-refactoring) below). When the notebook is refreshed in Jupyter, input cells are loaded from the script or Markdown document. Kernel variables are preserved. Outputs are not stored in such text documents, and are therefore lost when the notebook is refreshed. To avoid this, we recommend to [pair](#paired-notebooks) the text notebook with a traditional `.ipynb` notebook (both files are saved and loaded together).

| Format       | Extension          | Text editor friendly | Git friendly | Preserve outputs |
| ------------ | ------------------ | ----------- | ------------ | ---------------- |
| Jupyter notebook | `.ipynb`       |             |              | ✔                |
| Script or Markdown  | `.jl`/`.py`/`.R`/`.md`/`.Rmd` | ✔  | ✔            |                  |
| [Paired notebook](#paired-notebooks)  | (`.jl`/`.py`/`.R`/`.md`/`.Rmd`) + `.ipynb` | ✔ | ✔ | ✔ |


## Example usage

### Writing notebooks as plain text

You like to work with scripts? The good news is that plain scripts, which you can draft and test in your favorite IDE, open transparently as notebooks in Jupyter when using Jupytext. Run the notebook in Jupyter to generate the outputs, [associate](#paired-notebooks) an `.ipynb` representation, save and share your research as either a plain script or as a traditional Jupyter notebook with outputs.

### Collaborating on Jupyter Notebooks

With Jupytext, collaborating on Jupyter notebooks with Git becomes as easy as collaborating on text files.

The setup is straightforward:
- Open your favorite notebook in Jupyter notebook
- [Associate](#paired-notebooks) a `.py` representation (for instance) to that notebook
- Save the notebook, and put the Python script under Git control. Sharing the `.ipynb` file is possible, but not required.

Collaborating then works as follows:
- Your collaborator pulls your script. The script opens as a notebook in Jupyter, with no outputs.
- They run the notebook and save it. Outputs are regenerated, and a local `.ipynb` file is created.
- They change the notebook, and push their updated script. The diff is nothing else than a standard diff on a Python script.
- You pull the changed script, and refresh your browser. Input cells are updated. The outputs from cells that were changed are removed. Your variables are untouched, so you have the option to run only the modified cells to get the new outputs.

### Code refactoring

In the animation below we propose a quick demo of Jupytext. While the example remains simple, it shows how your favorite text editor or IDE can be used to edit your Jupyter notebooks. IDEs are more convenient than Jupyter for navigating through code, editing and executing cells or fractions of cells, and debugging.

- We start with a Jupyter notebook.
- The notebook includes a plot of the world population. The plot legend is not in order of decreasing population, we'll fix this.
- We want the notebook to be saved as both a `.ipynb` and a `.py` file: we add a `"jupytext_formats": "ipynb,py",` entry to the notebook metadata.
- The Python script can be opened with PyCharm:
  - Navigating in the code and documentation is easier than in Jupyter.
  - The console is convenient for quick tests. We don't need to create cells for this.
  - We find out that the columns of the data frame were not in the correct order. We update the corresponding cell, and get the correct plot.
- The Jupyter notebook is refreshed in the browser. Modified inputs are loaded from the Python script. Outputs and variables are preserved. We finally rerun the code and get the correct plot.

![](https://gist.githubusercontent.com/mwouts/13de42d8bb514e4acf6481c580feffd0/raw/b8dd28f44678f8c91f262da2381276fc4d03b00a/JupyterPyCharm.gif)

## Installation

Install Jupytext with
```bash
pip install jupytext --upgrade
```

Then, configure Jupyter to use Jupytext:
- generate a Jupyter config, if you don't have one yet, with `jupyter notebook --generate-config`
- edit `.jupyter/jupyter_notebook_config.py` and append the following:
```python
c.NotebookApp.contents_manager_class = "jupytext.TextFileContentsManager"
```
- and restart Jupyter, i.e. run
```bash
jupyter notebook
```

## Paired notebooks

The idea of paired notebooks is to store an `.ipynb` file alongside other formats. This lets us get the best of both worlds: an easily sharable notebook that stores the outputs, and one or more text-only files that can for instance be put under version control.

You can edit text-only files outside of Jupyter (first deactivate Jupyter's autosave by running `%autosave 0` in a cell), and then get the updated version in Jupyter by refreshing your browser.

When loading or refreshing an `.ipynb` file, the input cells of the notebook are read from the first non-`.ipynb` file among the associated formats.

When loading or refreshing a non-`.ipynb` file, the outputs are read from the `.ipynb` file (if `ipynb` is listed in the formats).

To enable paired notebooks, one option is to set the output formats by adding a `jupytext_formats` entry to the notebook metadata with *Edit/Edit Notebook Metadata* in Jupyter's menu:
```
{
  "jupytext_formats": "ipynb,py",
  "kernelspec": {
    (...)
  },
  "language_info": {
    (...)
  }
}
```
Accepted formats are: `ipynb`, `md`, `Rmd`, `jl`, `jl:percent`, `py`, `py:percent`, `py:sphinx`, `R` and `R:percent`.

Alternatively, it is also possible to set a default format pairing. Say you want to always associate `.ipynb` notebooks with an `.md` file  (and reciprocally). This is simply done by adding the following to your Jupyter configuration file:
```python
c.NotebookApp.contents_manager_class = "jupytext.TextFileContentsManager"
c.ContentsManager.default_jupytext_formats = "ipynb,md"
```
(and similarly for the other formats).

In case the [`percent` format](#julia-python-and-r-scripts-in-the-double-percent-format) is your favorite, add the following to your `.jupyter/jupyter_notebook_config.py` file:
```python
c.ContentsManager.preferred_jupytext_formats_save = "py:percent"
```
and then, Jupytext will understand `"jupytext_formats": "ipynb,py",` as an instruction to create the paired Python script in the `percent` format.

## Command line conversion

The package provides a `jupytext` script for command line conversion between the various notebook extensions:

```bash
jupytext --to python notebook.ipynb             # create a notebook.py file
jupytext --to py:percent notebook.ipynb         # create a notebook.py file in the double percent format
jupytext --to markdown notebook.ipynb           # create a notebook.md file
jupytext --output script.py notebook.ipynb      # create a script.py file

jupytext --to notebook notebook.py              # overwrite notebook.ipynb (remove outputs)
jupytext --to notebook --update notebook.py     # update notebook.ipynb (preserve outputs)
jupytext --to ipynb notebook1.md notebook2.py   # overwrite notebook1.ipynb and notebook2.ipynb

jupytext --to md --test notebook.ipynb          # Test round trip conversion

jupytext --to md --output - notebook.ipynb      # display the markdown version on screen
jupytext --from ipynb --to py:percent           # read ipynb from stdin and write double percent script on stdout
```

## Conversion in a Python script

`jupytext`'s main Python functions for reading and writing notebooks are:

```python
# Read notebook from file in given format (guess format when `format_name` is None)
readf(nb_file, format_name=None)

# Read notebook from text, given extension and format name
reads(text, ext, format_name=None, [...])

# Return the text representation for the notebook, given extension and format name
writes(notebook, ext, format_name=None, [...])

# Write notebook to file in desired format
writef(notebook, nb_file, format_name=None)
```

## Round-trip conversion

Round-trip conversion is safe! A few hundred tests help guarantee this. And you can test the round trip conversion on your favorite notebook with `jupytext --test`.

Please note that
- When you associate a Jupyter kernel with your text notebook, that information goes to a YAML header at the top of your script or Markdown document.
- Cell metadata are available in `light` and `percent` formats for all cell types. R Markdown and R scripts support cell metadata for code cells. Markdown documents do not currently support cell metadata.
- Representing Jupyter notebooks as Markdown document has the effect of splitting markdown cells with two consecutive blank lines into multiple cells (as the two blank line pattern is used to separate cells).

## Format specifications

### Markdown and R markdown

Our implementation for Jupyter notebooks as Markdown or [R Markdown ](https://rmarkdown.rstudio.com/authoring_quick_tour.html) documents is straightforward:
- A YAML header contains the notebook metadata (Jupyter kernel, etc)
- Markdown cells are inserted verbatim, and separated with two blank lines
- Code and raw cells start with triple backticks collated with cell language, and end with triple backticks. Cell metadata are not available in the markdown format. The [code cell options](https://yihui.name/knitr/options/) in the R Markdown format are mapped to the corresponding Jupyter cell metadata options, when available.

### R knit::spin scripts

Implement these [specifications](https://rmarkdown.rstudio.com/articles_report_from_r_script.html):
- Jupyter metadata in YAML format, in a `#' `-commented header
- Markdown cells are commented with `#' `
- Code cells are exported verbatim. Cell metadata are signalled with `#+`. Cells end with a blank line, an explicit start of cell marker, or a Markdown comment.

### Python and Julia scripts

The default format for Python and Julia scripts is the `light` format. That format can read any Python or Julia script as a Jupyter notebook, even scripts which were never prepared to become a notebook. When a notebook is written as a script using this format, as few cells markers as possible are introduced.

The `light` format has:
- A YAML header, commented with `# `, that contains the notebook metadata
- Markdown cells are commented with `# `, and separated with a blank line
- Code cells are exported verbatim (except for Jupyter magics, which are commented), and separated with blank lines. Code cells are reconstructed from consistent Python paragraphs (no function, class or multiline comment will be broken). A start-of-cell delimiter `# +` is used for cells that contain more than one Python paragraphs. `# + {}` is used for cells that have explicit metadata (inside the curly bracket, in JSON format). The end of cell delimiter is `# -`, and is omitted when followed by another explicit start of cell marker.

### Julia, Python and R scripts in the double percent format

Many Python editors recognize cells delimited with a commented double percent sign `# %%`, including
- [Spyder IDE](https://pythonhosted.org/spyder/editor.html),
- [Hydrogen](https://atom.io/packages/hydrogen), a package for Atom,
- [vscodeJupyter](https://marketplace.visualstudio.com/items?itemName=donjayamanne.jupyter), an extension for Visual Studio Code,
- and PyCharm Professional.

Our implementation of the `percent` format is compatible with the above editors. Cell headers have the following structure:
```python
# %% Optional text [cell type] {optional JSON metadata}
```
where cell type is either omitted (code cells), or `[markdown]` or  `[raw]`. The content of markdown and raw cells is commented in the resulting script.

Note that the double percent scripts you have written outside of Jupytext will be opened as such by Jupytext, provided that they contain at least two cells.

### Sphinx-gallery scripts

Another popular notebook-like format for Python script is the Sphinx-gallery [format](https://sphinx-gallery.readthedocs.io/en/latest/tutorials/plot_notebook.html). Scripts that contain at least two lines with more than twenty hash signs are classified as Sphinx-gallery notebooks by Jupytext.

If you want that the reStructuredText be converted to markdown for a nicer display, add a `c.ContentsManager.sphinx_convert_rst2md = True` line to your Jupyter configuration file. Please notice however that this is a non-reversible transformation - use this only with Binder. You should not use that if you want to edit the Sphinx Gallery files with Jupytext.

By the way, if you want to use Jupytext and Binder to visualize your Sphinx Gallery scripts, you just need to create two files in your GitHub repo:
- `binder/requirements.txt` with the required packages (including `jupytext`)
- `.jupyter/jupyter_notebook_config.py` with the following contents:
```python
c.NotebookApp.contents_manager_class = "jupytext.TextFileContentsManager"
c.ContentsManager.preferred_jupytext_formats_read = "py:sphinx"
c.ContentsManager.sphinx_convert_rst2md = True
```

## Jupyter Notebook or Jupyter Lab?

Jupytext works very well with the Jupyter Notebook editor, and we recommend that you get used to Jupytext within `jupyter notebook` first.

That being said, using Jupytext from Jupyter Lab is also an option. Please note that:
- Jupytext's installation is identical in both Jupyter Notebook and Jupyter Lab
- Jupyter Lab can open any [paired notebook](#paired-notebooks) with `.ipynb` extension. Paired notebooks work exactly as in Jupyter Notebook: input cells are taken from the text notebook, and outputs from the  `.ipynb` file. Both files are updated when the notebook is saved.
- Pairing notebooks is less convenient in Jupyter Lab than in Jupyter Notebook. Indeed, Jupyter Lab has no notebook metadata editor [yet](https://github.com/jupyterlab/jupyterlab/issues/1308), so you will have to open the JSON representation of the notebook, find the notebook metadata, and add the `"jupytext_formats": "ipynb,py",` entry manually.
- Jupyter Lab cannot currently open scripts or Markdown documents as notebooks. A workaround is to create the corresponding `.ipynb` file manually, e.g. `jupytext --to notebook.ipynb notebook.py`, and pair it with the script. Opening scripts or Markdown documents as notebooks will become possible in the next Jupyter Lab release (v. 0.35), for which we contributed a _Open With -> Notebook_ menu entry in the contextual menu (right-click on a text notebook).

## Will my notebook really run in an IDE?

Well, that's what we expect. There's however a big difference in the python environments between Python IDEs and Jupyter: in the IDE code is executed with  `python` and not in a Jupyter kernel. For this reason, `jupytext` comments Jupyter magics found in your notebook when exporting to the `light` (default) format. Comment a magic with `#noescape` on the same line to avoid escaping. User defined magics can be escaped with `#escape`. Magics are not commented in the plain Markdown representation, nor in the double percent format, as most editors use that format in combination with IPython or Jupyter kernels.

Also, you may want some cells to be active only in the Python, or R Markdown representation. For this, use the `active` cell metadata. Set `"active": "ipynb"` if you want that cell to be active only in the Jupyter notebook. And `"active": "py"` if you want it to be active only in the Python script. And `"active": "ipynb,py"` if you want it to be active in both, but not in the R Markdown representation...

## I like this, how can I contribute?

Your feedback is precious to us: please let us know how we can improve `jupytext`. With enough feedback we will be able to transition from the current beta phase to a stable phase. Thanks for staring the project on GitHub. Sharing it is also very helpful! By the way: stay tuned for announcements and demos on [medium](https://medium.com/@marc.wouts) and [twitter](https://twitter.com/marcwouts)!
