Genome extraction
=======================================

## Load data


```r
library("mmgenome")
knit("Load_data.Rmd", tangle = TRUE)
source(file = "Load_data.R")
```

```
## Error: cannot open the connection
```


## Initial extraction
The initial genome selection.

```r
x <- "S1"
y <- "S2"
data <- d
```

```
## Error: object 'd' not found
```

```r

p <- ggplot(data, aes_string(x = x, y = y, size = "length", color = "phylum")) + 
    scale_x_log10() + scale_y_log10() + geom_point(alpha = 0.1, color = "black") + 
    geom_point(data = subset(data, phylum != "NA"), shape = 1, alpha = 0.7) + 
    scale_size_area(name = "Scaffold length", max_size = 20) + guides(colour = guide_legend(override.aes = list(alpha = 1, 
    size = 5, shape = 19)))
```

```
## Error: ggplot2 doesn't know how to deal with data of class function
```

```r

# p sel <- ggplot_locator(p)

sel <- data.frame(S1 = c(14.6754, 35.0108, 80.6126, 51.7301, 16.6163, 12.5092), 
    S2 = c(16.6438, 35.8536, 31.4102, 13.4684, 6.089, 7.5246))

p + geom_polygon(data = sel, aes_string(x, y), size = 0.5, color = "darkred", 
    fill = NA, linetype = 2) + geom_point(data = sel, aes_string(x, y), size = 2, 
    color = "black")
```

```
## Error: object 'p' not found
```


## Extract scaffolds and essential genes

Extract all scaffolds and information on essential genes within the defined subspace using the `extract` function.


```r
dA <- extract(d, sel)
```

```
## Error: object 'd' not found
```

```r
eA <- extract(e, sel)
```

```
## Error: object 'e' not found
```

We store the extracted scaffolds in the variable `dA` and the extracted essential genes in `eA`. `dA` simply stands for `extraction A` of `dataframe d`.

## Calculate statistics on the extracted scaffolds
Then we use the `calc.genome.stats` function to see the basic statistics of the extracted scaffolds.


```r
sstats(dA, eA)
```

```
## Error: object 'dA' not found
```


Look for duplicated single copy genes.


```r
head(eduplicates(eA))
```

```
## Error: fejl under evaluering af argument 'x' under valg af metode for funktion 'head': Fejl i eduplicates(eA) : object 'eA' not found
```


## First cleanup

We use the pairs function to plot the first 3 components, GC and coverage. 

```r
data <- dA
```

```
## Error: object 'dA' not found
```

```r
palette(gc.trans)
```

```
## Error: object 'gc.trans' not found
```

```r

pairs(data[, c("PC1", "PC2", "PC3", "gc", "S1", "S2")], upper.panel = NULL, 
    col = data$gc - min(d$gc), cex = sqrt(data$length)/75, pch = 20)
```

```
## Error: object of type 'closure' is not subsettable
```


PC2 and PC3 seem to be nice for further subsetting.


```r
x <- "PC2"
y <- "PC3"
data <- dA
```

```
## Error: object 'dA' not found
```

```r

p <- ggplot(data, aes_string(x = x, y = y, size = "length", color = "phylum")) + 
    # scale_x_log10() + scale_y_log10() +
geom_point(alpha = 0.1, color = "black") + geom_point(data = subset(data, phylum != 
    "NA"), shape = 1, alpha = 0.7) + scale_size_area(name = "Scaffold length", 
    max_size = 20) + guides(colour = guide_legend(override.aes = list(alpha = 1, 
    size = 5, shape = 19)))
```

```
## Error: ggplot2 doesn't know how to deal with data of class function
```

```r

# p sel <- ggplot_locator(p)

sel <- data.frame(PC2 = c(0.0313, 0.142, 0.264, 0.2705, 0.1241, 0.0232), PC3 = c(0.022, 
    0.1095, 0.0708, -0.156, -0.245, -0.1172))

p + geom_polygon(data = sel, aes_string(x, y), size = 0.5, color = "darkred", 
    fill = NA, linetype = 2) + geom_point(data = sel, aes_string(x, y), size = 2, 
    color = "black")
```

```
## Error: object 'p' not found
```



```r
dB <- extract(dA, sel)
```

```
## Error: object 'dA' not found
```

```r
eB <- extract(eA, sel)
```

```
## Error: object 'eA' not found
```



```r
sstats(dB, eB)
```

```
## Error: object 'dB' not found
```



```r
eduplicates(eB)
```

```
## Error: object 'eB' not found
```


## Using PE connections

We can use the paried-end connections to include repeats and scaffolds missed in the initial extractions as well as obvious contamination. 

```r
subset <- extract_nodes(d, dB)
```

```
## Error: fejl under evaluering af argument 'object' under valg af metode for funktion 'na.omit': Fejl: object 'dB' not found
```

```r
sub.graph <- induced.subgraph(g, subset$nodes)
```

```
## Error: object 'g' not found
```


Next we can plot all scaffolds connected to 

```r
palette(gc.trans)
```

```
## Error: object 'gc.trans' not found
```

```r
plot(sub.graph, layout = layout.fruchterman.reingold)
```

```
## Error: fejl under evaluering af argument 'x' under valg af metode for funktion 'plot': Fejl: object 'sub.graph' not found
```


It dosn't look like much in this example. But we do get a few scaffolds pulled in, but also some additional contamination.

```r
dC <- d[subset$scaffolds, ]
```

```
## Error: object 'd' not found
```

```r
eC <- e[e$scaffold %in% dC$scaffold, ]
```

```
## Error: object 'e' not found
```


Looking at the stats.

```r
sstats(dC, eC)
```

```
## Error: object 'dC' not found
```


## Final clean up

We use the pairs function to plot the first 3 components, GC and coverage. 

```r
data <- dC
```

```
## Error: object 'dC' not found
```

```r
palette(gc.trans)
```

```
## Error: object 'gc.trans' not found
```

```r

pairs(data[, c("PC1", "PC2", "PC3", "gc", "S1", "S2")], upper.panel = NULL, 
    col = data$gc - min(d$gc), cex = sqrt(data$length)/75, pch = 20)
```

```
## Error: object of type 'closure' is not subsettable
```


PC1 and PC2 seem to be nice for further subsetting.


```r
x <- "PC1"
y <- "PC2"
data <- dC
```

```
## Error: object 'dC' not found
```

```r

p <- ggplot(data, aes_string(x = x, y = y, size = "length", color = "phylum")) + 
    # scale_x_log10() + scale_y_log10() +
geom_point(alpha = 0.1, color = "black") + geom_point(data = subset(data, phylum != 
    "NA"), shape = 1, alpha = 0.7) + scale_size_area(name = "Scaffold length", 
    max_size = 20) + guides(colour = guide_legend(override.aes = list(alpha = 1, 
    size = 5, shape = 19)))
```

```
## Error: ggplot2 doesn't know how to deal with data of class function
```

```r

# p sel <- ggplot_locator(p)

sel <- data.frame(PC1 = c(-0.1619, -0.0733, 0.0838, 0.1482, 0.1386, -0.0419, 
    -0.1644), PC2 = c(0.1572, 0.2698, 0.2747, 0.1808, 0.0779, 0.0015, 0.0098))

p + geom_polygon(data = sel, aes_string(x, y), size = 0.5, color = "darkred", 
    fill = NA, linetype = 2) + geom_point(data = sel, aes_string(x, y), size = 2, 
    color = "black")
```

```
## Error: object 'p' not found
```



```r
dD <- extract(dC, sel)
```

```
## Error: object 'dC' not found
```

```r
eD <- extract(eC, sel)
```

```
## Error: object 'eC' not found
```



```r
sstats(dD, eD)
```

```
## Error: object 'dD' not found
```



```r
eduplicates(eD)
```

```
## Error: object 'eD' not found
```


Write the scaffolds to a file.

```r
writeXStringSet(assembly[dD$scaffold], file = "genome1.fa")
```

```
## Error: object 'assembly' not found
```
