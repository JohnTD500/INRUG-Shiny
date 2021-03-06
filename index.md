---
title       : Shiny for Interactive Application Development using R
subtitle    : 
author      : Vivek Patil
job         : Associate Professor of Marketing, Gonzaga University
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : [mathjax]            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
---

## What is shiny

* Package from RStudio ([http://shiny.rstudio.com/](http://shiny.rstudio.com/))
* Interactive analysis of data
* Stand-alone applications and dashboards


```r
if (!require("devtools"))
  install.packages("devtools")
devtools::install_github("rstudio/shiny")
```

---
## Components of an application

Two files

1. User-Interface file (ui.R) - inputs and display of output in a customizable layout
2. Server (server.R) - the work-horse that takes inputs, processes them, and creates outputs to display in the user-interface

---
## Developing a sample application using iris

Sepal length and width and Petal length and width of 50 flowers from each of 3 species of iris - setosa, versicolor, and virginica

1. View data (or a part of it)
2. Summarize data 
3. 2 graphs of data - a scatter plot and a box plot

---
## View Data


```r
head(iris,5)
```

```
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
## 1          5.1         3.5          1.4         0.2  setosa
## 2          4.9         3.0          1.4         0.2  setosa
## 3          4.7         3.2          1.3         0.2  setosa
## 4          4.6         3.1          1.5         0.2  setosa
## 5          5.0         3.6          1.4         0.2  setosa
```

```r
# tail(iris,n) 
# iris
```

---
## Summarize Data


```r
summary(iris)
```

```
##   Sepal.Length    Sepal.Width     Petal.Length    Petal.Width   
##  Min.   :4.300   Min.   :2.000   Min.   :1.000   Min.   :0.100  
##  1st Qu.:5.100   1st Qu.:2.800   1st Qu.:1.600   1st Qu.:0.300  
##  Median :5.800   Median :3.000   Median :4.350   Median :1.300  
##  Mean   :5.843   Mean   :3.057   Mean   :3.758   Mean   :1.199  
##  3rd Qu.:6.400   3rd Qu.:3.300   3rd Qu.:5.100   3rd Qu.:1.800  
##  Max.   :7.900   Max.   :4.400   Max.   :6.900   Max.   :2.500  
##        Species  
##  setosa    :50  
##  versicolor:50  
##  virginica :50  
##                 
##                 
## 
```

---
## Plot 1 - Scatter plot


```r
library(ggplot2)
ggplot(iris,aes(x=Sepal.Length,y=Sepal.Width,color=Species))+geom_point()
```

![plot of chunk unnamed-chunk-4](assets/fig/unnamed-chunk-4-1.png) 

---
## Plot 2 - Box plot


```r
ggplot(iris,aes(x=Species,y=Sepal.Length))+geom_boxplot()
```

![plot of chunk unnamed-chunk-5](assets/fig/unnamed-chunk-5-1.png) 

---
## Recap

1. View data (or a section of it) (using `head`, `tail`, or just the entire data)
2. Summarize data (using `summary`)
3. 2 graphs of data - a scatter plot (x= Sepal.Length, y= Sepal.Width) and a box plot (how distribution of Sepal.Length varied across the three Species)

---
## Imagine the difficulty below

1. What if one wanted the ability to specify any variable for x and y axes of the scatter plot?
2. What if one wanted to alter the variable whose distribution one wanted to plot across the three species in the box plot?
3. What if one wanted to select a subset of the data (any range of any of the 4 numeric variables) and wanted to see how the data, summary, and the two plots changed?

---
## Let's work on the user interface

Off-the-shelf widgets for inputs: [http://shiny.rstudio.com/gallery/widget-gallery.html](http://shiny.rstudio.com/gallery/widget-gallery.html)

<iframe src="http://shiny.rstudio.com/gallery/widget-gallery.html"></iframe>

---
## Our Widgets

* One "Select box" each for the x-variable and y-variable (for scatter plot), and one "Select box" for the dvariable (for distribution to study in box plot) - The options for each of the 4 select boxes will be the same, the four numeric variables
* One "Slider range" for each of the four numeric variables - the minimum and maximum for each "Slider range" should be the minimum and maximum of the variable they are representing. Default state of each: The entire range (complete dataset)

---
## Input widgets for user interface


```r
# The 5th variable in the iris dataset is not numeric - Species - Not a choice option 
selectInput("xvar", "x-variable:", choices=names(iris[,-5]))
selectInput("yvar", "y-variable:", choices=names(iris[,-5]),selected = names(iris[2]))
selectInput("dvar", "Distribution of which variable for box plot?", choices=names(iris[,-5]), selected = names(iris[3]))

sliderInput("Sepal.Length", label = "Sepal.Length", min = min(iris$Sepal.Length), max = max(iris$Sepal.Length), 
            value=c(min(iris$Sepal.Length),max(iris$Sepal.Length)))

sliderInput("Sepal.Width", label = "Sepal.Width", min = min(iris$Sepal.Width), 
                       max = max(iris$Sepal.Width), value = c(min(iris$Sepal.Width),max(iris$Sepal.Width)))

sliderInput("Petal.Length", label = "Petal.Length", min = min(iris$Petal.Length), 
                       max = max(iris$Petal.Length), value = c(min(iris$Petal.Length),max(iris$Petal.Length)))
           
sliderInput("Petal.Width", label = "Petal.Width", min = min(iris$Petal.Width), 
                       max = max(iris$Petal.Width), value = c(min(iris$Petal.Width),max(iris$Petal.Width)))
```

---
## Output spots in ui.r

Source: [http://shiny.rstudio.com/tutorial/lesson4/](http://shiny.rstudio.com/tutorial/lesson4/)

| Output function | Output|
|------------- |-------------|
| htmlOutput      | raw HTML |
| imageOutput      | image      |
| plotOutput | plot      |
| tableOutput      | table |
| textOutput      | text      |
| uiOutput | raw HTML      |


---
## Output Spots and their unique names in ui.R

`dataTableOutput(outputId="subsetdata")`

To present an interactive table of the entire data using the jQuery library `DataTables` 

`verbatimTextOutput("summary")`

For a verbatim textOutput of the `summary` function

`plotOutput("scatterplot")`

`plotOutput("boxplot")`

For each of the two plots - the scatter plot and the box plot

---
## Laying out the ui.R

Source: [http://shiny.rstudio.com/articles/layout-guide.html](http://shiny.rstudio.com/articles/layout-guide.html)

<iframe src="http://shiny.rstudio.com/articles/layout-guide.html"></iframe>

---

## The ui with no output
Code of this intermediate app: [https://github.com/patilv/INRUG-ShinyUI](https://github.com/patilv/INRUG-ShinyUI)

<iframe src="https://patilv.shinyapps.io/INRUG-ShinyUI/"></iframe>

---
## server.R

* Take inputs from sliders and subset the data
  * Display resulting subset using datatable (name: "subsetdata" in ui.r)
* Compute summary of subsetted data and ship it off to "summary" in ui.r
* Take inputs for x and y variable 
  * Create scatter plot using the `ggplot2` package and ship this off to "scatterplot" in ui.r
* Take input for dvar (for distribution variable) and create box plot
  * Create box plot using `ggplot2` and ship this last piece off to "boxplot" in ui.r
  
**Don't forget, the input variable names in the server should be the same ones the ui.r is sending to it.**

---
## Subsetting data

* Creating `dataset` that is reactive to the inputs from the 4 sliders 
* Each of those sliders returns two values, the lower and upper bounds of the range


```r
dataset=reactive(iris[(iris$Sepal.Length>=input$Sepal.Length[1] & iris$Sepal.Length<=input$Sepal.Length[2]&
                            iris$Sepal.Width>=input$Sepal.Width[1] & iris$Sepal.Width<=input$Sepal.Width[2]&
                               iris$Petal.Length>=input$Petal.Length[1] & iris$Petal.Length<=input$Petal.Length[2]&
                                  iris$Petal.Width>=input$Petal.Width[1] & iris$Petal.Width<=input$Petal.Width[2]),])
```

More information on "reactivity" can be found here: [http://shiny.rstudio.com/articles/reactivity-overview.html](http://shiny.rstudio.com/articles/reactivity-overview.html)

---
## Rendering the outputs

Source: [http://shiny.rstudio.com/tutorial/lesson4/](http://shiny.rstudio.com/tutorial/lesson4/)

| Render function | Creates|
|------------- |-------------|
| renderImage      | images (saved as a link to a source file) |
| renderPlot        | plots   |
| renderPrint      | any printed output      |
| renderTable | data frame, matrix, other table like structures      |
| renderText      | character strings |
| renderUI      | a Shiny tag object or HTML      |

---

## Creating the outputs



```r
output$subsetdata <- renderDataTable(dataset(),options=list(pageLength=10)) # for Data table
  
output$summary <- renderPrint(summary(dataset()))
  
output$scatterplot=renderPlot(ggplot(dataset(),aes_string(x=input$xvar, y=input$yvar,color="Species"))+ geom_point()+ggtitle("Scatter Plot"))
  
output$boxplot=renderPlot(ggplot(dataset(),aes_string(x="Species",y=input$dvar))+ geom_boxplot()+ ggtitle("Box Plot"))
```

* Note how the reactive dataset is referred to as `dataset()`
* Since the inputs from "Select box" show up as strings, we use `aes_string` instead of `aes` in our ggplot creations

---
## Putting it all together with some instructions and messages

Code for final app: [https://github.com/patilv/INRUG-ShinyFinal](https://github.com/patilv/INRUG-ShinyFinal)

App in new window: [https://patilv.shinyapps.io/INRUG-ShinyFinal/](https://patilv.shinyapps.io/INRUG-ShinyFinal/)
<iframe src="https://patilv.shinyapps.io/INRUG-ShinyFinal/"></iframe>

---
## Deploying/Sharing an Application

Source: [http://shiny.rstudio.com/tutorial/lesson7/](http://shiny.rstudio.com/tutorial/lesson7/)

if {R and shiny available on computer} 
{

* Two R files
* runURL (from any web link to a zip file comprising the two R files)
* runGist (Try: `runGist("460f7ee20def9a506714")`)
* runGitHub (Try: `runGitHub("INRUG-ShinyFinal","patilv")`)

}
else
{

* [http://www.shinyapps.io/](http://www.shinyapps.io/)  - see [https://patilv.shinyapps.io/INRUG-ShinyFinal/](https://patilv.shinyapps.io/INRUG-ShinyFinal/)
* Shiny Server - Open Source or Professional [http://www.rstudio.com/products/shiny/shiny-server/](http://www.rstudio.com/products/shiny/shiny-server/)  

}

---

## Few Resources

* [http://shiny.rstudio.com/](http://shiny.rstudio.com/)
* Sample apps I have worked on: [http://patilv.com/tags/#Shiny](http://patilv.com/tags/#Shiny)
* A good collection of other shiny apps, many with the code: [http://www.showmeshiny.com/](http://www.showmeshiny.com/)
* Search on R-Bloggers: [http://www.r-bloggers.com/?s=shiny](http://www.r-bloggers.com/?s=shiny) 

This presentation was created using slidify and the code for it can be found at: [https://github.com/patilv/INRUG-Shiny](https://github.com/patilv/INRUG-Shiny)

