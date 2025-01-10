# Reproducibility Instructions (and instructions for the teacher)

There are two notebook files:
- `project.ipynb` which is the final project itself
- `data.ipynb` which includes the code for collecting the data.

## Setup
To run and view the `project.ipynb` notebook successfully, you need to install the following R packages:
- `tidyverse`
- `plotly`
- `RColorBrewer`
- `fmsb`

To run `data.ipynb` notebook successfully, you need to install the following R packages:
- `rvest`
- `worldfootballR`
- `tidyverse`
- `stringr

You can install the required packages by running the following commands in your R console or
by first removing # for the first code chunks found in the first cell.

## Interactive Plots Note:
The interactive plots in this notebook are not viewable directly on GitHub. To view the interactive visualizations:
1. Clone the repository to your local machine.
2. Open the notebook in RStudio or Jupyter Notebook.
3. Ensure the required packages (`plotly`, etc.) are installed.
4. Restart kernel to render the notebook as whole.

There could be some rendering issues regarding the drop-down menu of the interactive plot.
If this occurs, you might need to manually adjust its position. I will mark it in the code.

## Side note
I couldn't manage to hide the code cells when displaying on GitHub. My intentions were to collapse all code cells.