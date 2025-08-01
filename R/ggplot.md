# Unix Command Cheatsheet

##### _I like my plots like I like my data, tidy and ggorgeous!_

--- 

## What **** is ggplot2

`ggplot2` (Grammar of Graphics plot version 2) is a R package part of tidyverse which allows you to create complex, customizable, publication-quality plots to **visualize your data**.


---


## ggplot2 general syntax structure

The ggplot2 framework is broken down into individual components with specific purposes layered ontop of each other:

- **Data**: What you are plotting
    - Long-format dataframe (each row is observation and each column is a variable)
- **Aesthetic**: How the variables are mapped to visual properties
    - e.g. what is the x axis, what is the y axis, color the groups
- **Geometries**: Type of plot you want to visualize your data
    - e.g. scatterplot, barplot, lineplot, violinplot, boxplot
- **Facets**: Generating multiple panel plots
    - Split by subgroups, conditions
- **Scales**: Customize how the visual aesthetics are displayed
    - log scale axis, map color
- **Themes**: Customize overall appearance 
    - Modify axis, legend, text sizes



In UNIX, commands follow a standard and specific structure:

