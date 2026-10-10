# Matplotlib Handbook

A practical reference for data visualization in Python using Matplotlib.

## Table of Contents

- [Introduction](#introduction)
- [Importing Matplotlib](#importing-matplotlib)
- [Basic Plotting](#basic-plotting)
- [Figure and Axes](#figure-and-axes)
- [Customizing Plots](#customizing-plots)
- [Multiple Plots](#multiple-plots)
- [Plot Types](#plot-types)
- [Saving Figures](#saving-figures)
- [Common Mistakes](#common-mistakes)
- [Official Documentation](#official-documentation)

## Introduction

Matplotlib is a Python library for creating visualizations from numerical and categorical data.

It supports line plots, scatter plots, bar charts, histograms, and many other forms of visualization.

## Importing Matplotlib

The commonly used plotting interface is `matplotlib.pyplot`.

```python
import matplotlib.pyplot as plt
import numpy as np
```

## Basic Plotting

A simple line plot:

```python
x = np.array([1, 2, 3, 4, 5])
y = np.array([2, 4, 3, 7, 6])

plt.plot(x, y)
plt.xlabel("X")
plt.ylabel("Y")
plt.title("A Simple Line Plot")
plt.show()
```

## Figure and Axes

A Figure represents the overall canvas. An Axes represents an individual plotting area inside that canvas.

The object-oriented interface provides explicit control over these objects.

```python
fig, ax = plt.subplots()

ax.plot([1, 2, 3], [2, 4, 3])
ax.set_xlabel("X")
ax.set_ylabel("Y")
ax.set_title("Using Figure and Axes")

plt.show()
```

## Customizing Plots

Plots can be customized with labels, colors, markers, line styles, legends, grid lines, and axis limits.

```python
fig, ax = plt.subplots()

ax.plot(
    [1, 2, 3, 4],
    [2, 5, 3, 7],
    marker="o",
    linestyle="-",
    label="Measurements",
)

ax.set_xlabel("Sample")
ax.set_ylabel("Value")
ax.set_title("Customized Plot")
ax.grid(True)
ax.legend()

plt.show()
```

## Multiple Plots

`plt.subplots()` can create several Axes within a single Figure.

```python
fig, axes = plt.subplots(1, 2, figsize=(10, 4))

axes[0].plot([1, 2, 3], [1, 4, 9])
axes[0].set_title("Line Plot")

axes[1].scatter([1, 2, 3], [1, 4, 9])
axes[1].set_title("Scatter Plot")

fig.tight_layout()
plt.show()
```

## Plot Types

Common plot types include:

- `plot()` — line plots
- `scatter()` — scatter plots
- `bar()` — bar charts
- `hist()` — histograms
- `boxplot()` — box plots
- `imshow()` — displaying two-dimensional data as an image

## Saving Figures

Save a figure using `savefig()`.

```python
fig, ax = plt.subplots()
ax.plot([1, 2, 3], [2, 4, 3])

fig.savefig("plot.png", dpi=300, bbox_inches="tight")
```

Save the figure before calling `plt.show()` when working in environments where showing a figure might close or reset it.

## Common Mistakes

- Forgetting to call `plt.show()` in a script when an interactive display is expected.
- Confusing the Figure object with an Axes object.
- Forgetting to label axes or provide a legend when needed.
- Creating misleading visualizations through inappropriate axis limits.
- Using the stateful `pyplot` interface when explicit Figure and Axes objects would make a complex plot easier to manage.

## Official Documentation

- [Matplotlib Documentation](https://matplotlib.org/stable/)
- [Pyplot Tutorial](https://matplotlib.org/stable/tutorials/pyplot.html)
- [Quick Start Guide](https://matplotlib.org/stable/users/explain/quick_start.html)
