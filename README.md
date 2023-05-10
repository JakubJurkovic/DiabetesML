# Check out the [HTML VERSION](https://jakubjurkovic.github.io/DiabetesML/)

# Biological Databases & Datamining : Final
## Creating a prediction model for diabetes patients.

# Predicting Diabetes in patients
The following document is a final project fro Biological Databases & Data mining, in which we will attempt to build and evaluate multiple models to predict diabetes in patients. The caveat is that we expect our training data to contain a lot of missing data.



# Data Loading 
Let's Load the data and have a quick glance at it!

<div class="layout-chunk" data-layout="l-page">
<details>
<summary>Show code</summary>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='va'>data</span> <span class='op'>&lt;-</span> <span class='fu'><a href='https://rdrr.io/r/utils/read.table.html'>read.csv</a></span><span class='op'>(</span><span class='st'>"PimaIndiansDiabetes.csv"</span><span class='op'>)</span></span>
<span><span class='fu'>rmarkdown</span><span class='fu'>::</span><span class='fu'><a href='https://pkgs.rstudio.com/rmarkdown/reference/paged_table.html'>paged_table</a></span><span class='op'>(</span><span class='va'>data</span><span class='op'>)</span></span></code></pre></div>
</div>

# Q1: Numerical & Categorical data
*Which variables are numeric and which are categorical?*

The attributes are:

* Pregnancies: Number of times pregnant 
* Glucose: Plasma glucose concentration a 2 hours in an oral glucose tolerance test 
* BloodPressure: Diastolic blood pressure (mm Hg) 
* SkinThickness: Triceps skin fold thickness (mm) 
* Insulin: 2-Hour serum insulin (mu U/ml) 
* BMI: Body mass index (weight in kg/(height in m)^2) 
* DiabetesPedigreeFunction: Diabetes pedigree function 
* Age: Age (years) 
* Outcome: Class variable (0 or 1) where 1 means they have diabetes.

From this we see that only **Outcome** is **categorical**. A debate can be held about pregnancies being categorical, however it is in the end just a discrete quantitative variable.

<div class="layout-chunk" data-layout="l-body">
<details>
<summary>Show code</summary>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='co'># We see Outcome is categorical, hence will be recast as factor</span></span>
<span></span>
<span><span class='co'>#The code is commented out since corr plot complains with factors, will be factorized later</span></span>
<span><span class='va'>data</span><span class='op'>$</span><span class='va'>Outcome</span> <span class='op'>&lt;-</span> <span class='fu'><a href='https://rdrr.io/r/base/factor.html'>factor</a></span><span class='op'>(</span><span class='va'>data</span><span class='op'>$</span><span class='va'>Outcome</span>, labels <span class='op'>=</span> <span class='fu'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='op'>(</span><span class='st'>"Healthy"</span>, <span class='st'>"Diabetes"</span><span class='op'>)</span><span class='op'>)</span></span></code></pre></div>

</details>

</div>


# Q2: Overviewing the dataset
*Use appropriate graphs to predict which of the variables (on their own) are most helpful in predicting the outcome?*
Let's use Violin plots to overview our population, jitter dot plots to see clusterings and heat map to see associations.

<div class="layout-chunk" data-layout="l-page">
<details>
<summary>Show code</summary>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='va'>data_long</span> <span class='op'>&lt;-</span>  <span class='fu'><a href='https://rdrr.io/r/utils/read.table.html'>read.csv</a></span><span class='op'>(</span><span class='st'>"PimaIndiansDiabetes.csv"</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> </span>
<span>  <span class='fu'><a href='https://tidyr.tidyverse.org/reference/pivot_longer.html'>pivot_longer</a></span><span class='op'>(</span>cols <span class='op'>=</span> <span class='fl'>1</span><span class='op'>:</span><span class='fl'>8</span>, names_to <span class='op'>=</span> <span class='st'>"Variable"</span>, values_to <span class='op'>=</span> <span class='st'>"Value"</span><span class='op'>)</span></span>
<span></span>
<span><span class='va'>data_long</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/ggplot.html'>ggplot</a></span><span class='op'>(</span>mapping <span class='op'>=</span> <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/aes.html'>aes</a></span><span class='op'>(</span>x<span class='op'>=</span><span class='va'>Variable</span>, y<span class='op'>=</span><span class='va'>Value</span><span class='op'>)</span><span class='op'>)</span> <span class='op'>+</span> </span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/geom_violin.html'>geom_violin</a></span><span class='op'>(</span><span class='op'>)</span> <span class='op'>+</span> </span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/facet_wrap.html'>facet_wrap</a></span><span class='op'>(</span><span class='fu'><a href='https://ggplot2.tidyverse.org/reference/vars.html'>vars</a></span><span class='op'>(</span><span class='va'>Variable</span><span class='op'>)</span>, scales <span class='op'>=</span> <span class='st'>"free"</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/labs.html'>labs</a></span><span class='op'>(</span></span>
<span>    title <span class='op'>=</span> <span class='st'>"Violin Distributions of quantitative variables"</span>,</span>
<span>    subtite <span class='op'>=</span> <span class='st'>"Before Imputing"</span></span>
<span>  <span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/ggtheme.html'>theme_minimal</a></span><span class='op'>(</span><span class='op'>)</span></span></code></pre></div>

</details><img src="jj3003_files/figure-html5/VariablesInspection-1.png" style="display: block; margin: auto auto auto 0;" /><details>
<summary>Show code</summary>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='va'>data_long</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/ggplot.html'>ggplot</a></span><span class='op'>(</span>mapping <span class='op'>=</span> <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/aes.html'>aes</a></span><span class='op'>(</span>x<span class='op'>=</span><span class='va'>Variable</span>, y<span class='op'>=</span><span class='va'>Value</span>, col<span class='op'>=</span><span class='fu'><a href='https://rdrr.io/r/base/factor.html'>as.factor</a></span><span class='op'>(</span><span class='va'>Outcome</span><span class='op'>)</span><span class='op'>)</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/geom_jitter.html'>geom_jitter</a></span><span class='op'>(</span>width <span class='op'>=</span> <span class='fl'>5</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/facet_wrap.html'>facet_wrap</a></span><span class='op'>(</span><span class='fu'><a href='https://ggplot2.tidyverse.org/reference/vars.html'>vars</a></span><span class='op'>(</span><span class='va'>Variable</span><span class='op'>)</span>, scales <span class='op'>=</span> <span class='st'>"free"</span><span class='op'>)</span> <span class='op'>+</span> </span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/labs.html'>labs</a></span><span class='op'>(</span></span>
<span>    title <span class='op'>=</span> <span class='st'>"Jittered point-plot, to look for clustering"</span>,</span>
<span>    col <span class='op'>=</span> <span class='st'>"Outcome"</span></span>
<span>  <span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/ggtheme.html'>theme_minimal</a></span><span class='op'>(</span><span class='op'>)</span></span></code></pre></div>

  
</details><img src="jj3003_files/figure-html5/VariablesInspection-2.png" style="display: block; margin: auto auto auto 0;" /><details>
<summary>Show code</summary>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='va'>data</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/ggplot.html'>ggplot</a></span><span class='op'>(</span>mapping <span class='op'>=</span> <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/aes.html'>aes</a></span><span class='op'>(</span>x<span class='op'>=</span><span class='fu'><a href='https://rdrr.io/r/base/factor.html'>as.factor</a></span><span class='op'>(</span><span class='va'>Outcome</span><span class='op'>)</span><span class='op'>)</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/geom_bar.html'>geom_bar</a></span><span class='op'>(</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/ggtheme.html'>theme_minimal</a></span><span class='op'>(</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/labs.html'>labs</a></span><span class='op'>(</span></span>
<span>    x <span class='op'>=</span> <span class='st'>"Outcome"</span>,</span>
<span>    y <span class='op'>=</span> <span class='st'>"Count"</span>,</span>
<span>    title <span class='op'>=</span> <span class='st'>"Diagnosis counts"</span></span>
<span>  <span class='op'>)</span></span></code></pre></div>

</details><img src="jj3003_files/figure-html5/VariablesInspection-3.png" style="display: block; margin: auto auto auto 0;" /><details>
<summary>Show code</summary>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='va'>cor_data</span> <span class='op'>&lt;-</span> <span class='fu'><a href='https://rdrr.io/r/stats/cor.html'>cor</a></span><span class='op'>(</span><span class='fu'><a href='https://rdrr.io/r/utils/read.table.html'>read.csv</a></span><span class='op'>(</span><span class='st'>"PimaIndiansDiabetes.csv"</span><span class='op'>)</span><span class='op'>)</span></span>
<span><span class='va'>melt_cor_data</span> <span class='op'>&lt;-</span> <span class='fu'><a href='https://rdrr.io/pkg/reshape2/man/melt.html'>melt</a></span><span class='op'>(</span><span class='va'>cor_data</span><span class='op'>)</span></span>
<span><span class='fu'><a href='https://ggplot2.tidyverse.org/reference/ggplot.html'>ggplot</a></span><span class='op'>(</span>data <span class='op'>=</span> <span class='va'>melt_cor_data</span>, <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/aes.html'>aes</a></span><span class='op'>(</span>x<span class='op'>=</span><span class='va'>Var1</span>, y<span class='op'>=</span><span class='va'>Var2</span>, fill<span class='op'>=</span><span class='va'>value</span><span class='op'>)</span><span class='op'>)</span> <span class='op'>+</span> </span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/geom_tile.html'>geom_tile</a></span><span class='op'>(</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/scale_gradient.html'>scale_fill_gradient2</a></span><span class='op'>(</span>low <span class='op'>=</span> <span class='st'>"blue"</span>, high <span class='op'>=</span> <span class='st'>"red"</span>, mid <span class='op'>=</span> <span class='st'>"white"</span>, midpoint <span class='op'>=</span> <span class='fl'>0</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/ggtheme.html'>theme_minimal</a></span><span class='op'>(</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/theme.html'>theme</a></span><span class='op'>(</span>axis.text.x <span class='op'>=</span> <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/element.html'>element_text</a></span><span class='op'>(</span>angle <span class='op'>=</span> <span class='fl'>45</span>, vjust <span class='op'>=</span> <span class='fl'>1</span>, hjust <span class='op'>=</span> <span class='fl'>1</span><span class='op'>)</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/labs.html'>labs</a></span><span class='op'>(</span></span>
<span>    x<span class='op'>=</span> <span class='st'>"Variable 1"</span>,</span>
<span>    y<span class='op'>=</span> <span class='st'>"Variable 2"</span>,</span>
<span>    title <span class='op'>=</span> <span class='st'>"Correlation Heatmap"</span></span>
<span>  <span class='op'>)</span></span></code></pre></div>

 </details><img src="jj3003_files/figure-html5/VariablesInspection-4.png" style="display: block; margin: auto auto auto 0;" />

</div>

The violin plots shows us the distribution of our populations for each value, and gives a hint at present outliers. We see that some of them also contain missing data (to be handled later).
The jittered point plot reveals some clear clusters being observable between the diagnosis. The most notable one is in the **Glucose** & **BMI** plot, which is also confirmed by the correlation heat map - these should be the most contributing variables to the predictions.

# Q3 + Q4: Handling missing data & Normalization

## Identifying missing values
Let's have a glance which IVs contain 0s indicating missing variables and whether they are actual NAs or they make biological sense

<div class="layout-chunk" data-layout="l-body">
<details>
<summary>Show code</summary>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='co'># We know that there are some missing values coded as 0, let's check which columns contain such missing data</span></span>
<span><span class='kw'>for</span> <span class='op'>(</span><span class='va'>i</span> <span class='kw'>in</span> <span class='fl'>1</span><span class='op'>:</span><span class='fl'>8</span><span class='op'>)</span><span class='op'>{</span></span>
<span>  <span class='fu'><a href='https://rdrr.io/r/base/paste.html'>paste0</a></span><span class='op'>(</span><span class='fu'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='op'>(</span><span class='fu'><a href='https://rdrr.io/r/base/colnames.html'>colnames</a></span><span class='op'>(</span><span class='va'>data</span><span class='op'>)</span><span class='op'>[</span><span class='va'>i</span><span class='op'>]</span>, <span class='st'>": "</span>, <span class='op'>(</span><span class='va'>data</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> <span class='fu'><a href='https://dplyr.tidyverse.org/reference/filter.html'>filter</a></span><span class='op'>(</span><span class='va'>data</span><span class='op'>[</span>,<span class='va'>i</span><span class='op'>]</span> <span class='op'>==</span> <span class='fl'>0</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> <span class='fu'><a href='https://rdrr.io/r/base/nrow.html'>nrow</a></span><span class='op'>(</span><span class='op'>)</span><span class='op'>)</span><span class='op'>)</span>, collapse <span class='op'>=</span> <span class='st'>""</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> <span class='fu'><a href='https://rdrr.io/r/base/print.html'>print</a></span><span class='op'>(</span><span class='op'>)</span></span>
<span><span class='op'>}</span></span></code></pre></div>

</details>

```
[1] "Pregnancies: 102"
[1] "Glucose: 5"
[1] "BloodPressure: 32"
[1] "SkinThickness: 196"
[1] "Insulin: 322"
[1] "BMI: 9"
[1] "DiabetesPedigreeFunction: 0"
[1] "Age: 0"
```

<details>
<summary>Show code</summary>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='co'># It is expected to find 0s for pregnancies, however, it doesn't make biological sense to have 0s in the remaining categories.</span></span>
<span><span class='co'># Let's convert them to NAs so we can use KNN Imputer</span></span>
<span><span class='va'>data_na</span> <span class='op'>&lt;-</span> <span class='fu'><a href='https://rdrr.io/r/utils/read.table.html'>read.csv</a></span><span class='op'>(</span><span class='st'>"PimaIndiansDiabetes.csv"</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span>  <span class='fu'><a href='https://dplyr.tidyverse.org/reference/mutate_all.html'>mutate_at</a></span><span class='op'>(</span><span class='fl'>2</span><span class='op'>:</span><span class='fl'>6</span> ,<span class='op'>~</span><span class='fu'><a href='https://dplyr.tidyverse.org/reference/na_if.html'>na_if</a></span><span class='op'>(</span><span class='va'>.</span>, <span class='fl'>0</span><span class='op'>)</span><span class='op'>)</span></span></code></pre></div>

</details>

</div>


We see that **Age** and **DiabetesPedigreeFunction** have no entries where the value is 0. We have quite a lot of 0s for pregnancies, but that makes buological sense. For the remaining variables: **Glucose**, **BloodPressure**, **SkinThickness**, **Insulin**, **BMI**, we will assume that the 0s represent NAs and hence should be imputed. We can't realy drop the NAs as **Insulin** has 322 missing values and we would loose a lot of our data.

## Normalization 
In order to handle the missing data, I want to deploy an KNN imputer. This however requires the dataset to be normalized a slightly earlier than the homework asks for. However, any imputed data should be within the normalized range.


<div class="layout-chunk" data-layout="l-body">
<details>
<summary>Show code</summary>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='co'># Q4! Since I plan to use KNN Imputer I want to normalize the dataset first as KNN performs better with normalized data.</span></span>
<span><span class='kw'>for</span> <span class='op'>(</span><span class='va'>i</span> <span class='kw'>in</span> <span class='fl'>1</span><span class='op'>:</span><span class='fl'>8</span><span class='op'>)</span><span class='op'>{</span></span>
<span>  <span class='va'>tmp_mean</span> <span class='op'>&lt;-</span> <span class='fu'><a href='https://rdrr.io/r/base/mean.html'>mean</a></span><span class='op'>(</span><span class='va'>data_na</span><span class='op'>[</span>,<span class='va'>i</span><span class='op'>]</span>, na.rm <span class='op'>=</span> <span class='cn'>T</span><span class='op'>)</span></span>
<span>  <span class='va'>tmp_sd</span> <span class='op'>&lt;-</span> <span class='fu'><a href='https://rdrr.io/r/stats/sd.html'>sd</a></span><span class='op'>(</span><span class='va'>data_na</span><span class='op'>[</span>,<span class='va'>i</span><span class='op'>]</span>, na.rm <span class='op'>=</span> <span class='cn'>T</span><span class='op'>)</span></span>
<span>  <span class='va'>data_na</span><span class='op'>[</span>,<span class='va'>i</span><span class='op'>]</span> <span class='op'>&lt;-</span> <span class='op'>(</span><span class='va'>data_na</span><span class='op'>[</span>,<span class='va'>i</span><span class='op'>]</span> <span class='op'>-</span> <span class='va'>tmp_mean</span><span class='op'>)</span><span class='op'>/</span><span class='va'>tmp_sd</span></span>
<span><span class='op'>}</span></span></code></pre></div>

</details>

</div>


## Imputing
Originally I planned to remove the DV from the data available for the KNN Imputer, but based on https://doi.org/10.1186/s12874-016-0281-5 I learned that it is better to keep the DV for the imputer. 

<div class="layout-chunk" data-layout="l-page">
<details>
<summary>Show code</summary>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='co'># Q3 Ctd</span></span>
<span></span>
<span><span class='co'># Run the KNN Imputer and bring back the outcome column</span></span>
<span><span class='va'>data_na</span> <span class='op'>&lt;-</span> <span class='fu'><a href='https://rdrr.io/pkg/impute/man/impute.knn.html'>impute.knn</a></span><span class='op'>(</span><span class='fu'><a href='https://rdrr.io/r/base/matrix.html'>as.matrix</a></span><span class='op'>(</span><span class='va'>data_na</span><span class='op'>)</span>, k <span class='op'>=</span> <span class='fl'>15</span>, rowmax <span class='op'>=</span> <span class='fl'>0.5</span>, colmax <span class='op'>=</span> <span class='fl'>0.8</span>, maxp <span class='op'>=</span> <span class='fl'>1500</span>, rng.seed<span class='op'>=</span><span class='fl'>362436069</span><span class='op'>)</span></span>
<span><span class='va'>imputed_data</span> <span class='op'>&lt;-</span> <span class='va'>data_na</span><span class='op'>[[</span><span class='st'>"data"</span><span class='op'>]</span><span class='op'>]</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> <span class='fu'><a href='https://rdrr.io/r/base/as.data.frame.html'>as.data.frame</a></span><span class='op'>(</span><span class='op'>)</span></span>
<span><span class='va'>imputed_data</span><span class='op'>$</span><span class='va'>Outcome</span> <span class='op'>&lt;-</span> <span class='fu'><a href='https://rdrr.io/r/utils/read.table.html'>read.csv</a></span><span class='op'>(</span><span class='st'>"PimaIndiansDiabetes.csv"</span><span class='op'>)</span><span class='op'>$</span><span class='va'>Outcome</span></span>
<span></span>
<span><span class='co'># Now let's re-inspect the data</span></span>
<span></span>
<span><span class='va'>data_long_imp</span> <span class='op'>&lt;-</span> <span class='va'>imputed_data</span>  <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> <span class='fu'><a href='https://tidyr.tidyverse.org/reference/pivot_longer.html'>pivot_longer</a></span><span class='op'>(</span>cols <span class='op'>=</span> <span class='fl'>1</span><span class='op'>:</span><span class='fl'>8</span>, names_to <span class='op'>=</span> <span class='st'>"Variable"</span>, values_to <span class='op'>=</span> <span class='st'>"Value"</span><span class='op'>)</span></span>
<span></span>
<span><span class='va'>data_long_imp</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/ggplot.html'>ggplot</a></span><span class='op'>(</span>mapping <span class='op'>=</span> <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/aes.html'>aes</a></span><span class='op'>(</span>x<span class='op'>=</span><span class='va'>Variable</span>, y<span class='op'>=</span><span class='va'>Value</span><span class='op'>)</span><span class='op'>)</span> <span class='op'>+</span> </span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/geom_violin.html'>geom_violin</a></span><span class='op'>(</span><span class='op'>)</span> <span class='op'>+</span> </span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/facet_wrap.html'>facet_wrap</a></span><span class='op'>(</span><span class='fu'><a href='https://ggplot2.tidyverse.org/reference/vars.html'>vars</a></span><span class='op'>(</span><span class='va'>Variable</span><span class='op'>)</span>, scales <span class='op'>=</span> <span class='st'>"free"</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/ggtheme.html'>theme_minimal</a></span><span class='op'>(</span><span class='op'>)</span></span></code></pre></div>

</details><img src="jj3003_files/figure-html5/Imputing-1.png" style="display: block; margin: auto auto auto 0;" /><details>
<summary>Show code</summary>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='va'>cor_data_imp</span> <span class='op'>&lt;-</span> <span class='fu'><a href='https://rdrr.io/r/stats/cor.html'>cor</a></span><span class='op'>(</span><span class='va'>imputed_data</span><span class='op'>)</span></span>
<span><span class='va'>melt_cor_data_imp</span> <span class='op'>&lt;-</span> <span class='fu'><a href='https://rdrr.io/pkg/reshape2/man/melt.html'>melt</a></span><span class='op'>(</span><span class='va'>cor_data_imp</span><span class='op'>)</span></span>
<span><span class='fu'><a href='https://ggplot2.tidyverse.org/reference/ggplot.html'>ggplot</a></span><span class='op'>(</span>data <span class='op'>=</span> <span class='va'>melt_cor_data_imp</span>, <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/aes.html'>aes</a></span><span class='op'>(</span>x<span class='op'>=</span><span class='va'>Var1</span>, y<span class='op'>=</span><span class='va'>Var2</span>, fill<span class='op'>=</span><span class='va'>value</span><span class='op'>)</span><span class='op'>)</span> <span class='op'>+</span> </span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/geom_tile.html'>geom_tile</a></span><span class='op'>(</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/scale_gradient.html'>scale_fill_gradient2</a></span><span class='op'>(</span>low <span class='op'>=</span> <span class='st'>"blue"</span>, high <span class='op'>=</span> <span class='st'>"red"</span>, mid <span class='op'>=</span> <span class='st'>"white"</span>, midpoint <span class='op'>=</span> <span class='fl'>0</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/ggtheme.html'>theme_minimal</a></span><span class='op'>(</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/theme.html'>theme</a></span><span class='op'>(</span>axis.text.x <span class='op'>=</span> <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/element.html'>element_text</a></span><span class='op'>(</span>angle <span class='op'>=</span> <span class='fl'>45</span>, vjust <span class='op'>=</span> <span class='fl'>1</span>, hjust <span class='op'>=</span> <span class='fl'>1</span><span class='op'>)</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/labs.html'>labs</a></span><span class='op'>(</span></span>
<span>    x<span class='op'>=</span> <span class='st'>"Variable 1"</span>,</span>
<span>    y<span class='op'>=</span> <span class='st'>"Variable 2"</span>,</span>
<span>    title <span class='op'>=</span> <span class='st'>"Correlation Heatmap"</span></span>
<span>  <span class='op'>)</span></span></code></pre></div>

</details><img src="jj3003_files/figure-html5/Imputing-2.png" style="display: block; margin: auto auto auto 0;" /><details>
<summary>Show code</summary>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='co'># Refactorize the outcome</span></span>
<span><span class='va'>imputed_data</span><span class='op'>$</span><span class='va'>Outcome</span> <span class='op'>&lt;-</span> <span class='va'>imputed_data</span><span class='op'>$</span><span class='va'>Outcome</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> <span class='fu'><a href='https://rdrr.io/r/base/factor.html'>factor</a></span><span class='op'>(</span><span class='op'>)</span></span></code></pre></div>

</details>

</div>


Looking at our violin plots their general "shapes" have remained almost the same, with the "tails" caused by excess NAs in the form have been de-facto trimmed. This means that our imputer maintained the relative distribution of our values. We can also observe change in the correlation heat map, boosting the importance of some factors that were otherwise masked by the high number of 0 present.

# Q5 : Model Training
For this dataset I plan to try to train 3 types of models:

1. KNN
2. Random Forest
3. Neural network

The best performer will be then chosen based on the the mixture of highest and least variable AUC.  

## K-Fold Validation & AUC

In order to utilize 100% of our dataset let's perform the k-fold validation. We can use **vfold_cv** command from base R.
The performance of the model will be assessed using AUC.

<div class="layout-chunk" data-layout="l-body">
<details>
<summary>Show code</summary>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='co'># Split our dataset into 10 folds for validation</span></span>
<span><span class='va'>fold_data</span> <span class='op'>&lt;-</span> <span class='fu'>vfold_cv</span><span class='op'>(</span><span class='va'>imputed_data</span><span class='op'>)</span></span>
<span><span class='co'># Set the evaluation approach: Area Under Curve (AUC)</span></span>
<span><span class='va'>roc_res</span> <span class='op'>&lt;-</span> <span class='fu'>metric_set</span><span class='op'>(</span><span class='va'>roc_auc</span><span class='op'>)</span></span></code></pre></div>

</details>

</div>


## Random Forest
Let's use grid search to hyper tune the random forest. Engine used for the forest will be cforest from party package.
The parameters used for hyper tuning will be 

1. number of random variables (mtry)
2. number of trees

<div class="layout-chunk" data-layout="l-page">
<details>
<summary>Show code</summary>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='co'># Let's create a Tidy Random Forest Model</span></span>
<span><span class='co'># The tidy approach allows us to modify the engine in the </span></span>
<span><span class='co'># later stage of the project</span></span>
<span><span class='va'>rf_model</span> <span class='op'>=</span> <span class='fu'><a href='https://parsnip.tidymodels.org/reference/rand_forest.html'>rand_forest</a></span><span class='op'>(</span>mtry<span class='op'>=</span><span class='fu'><a href='https://hardhat.tidymodels.org/reference/tune.html'>tune</a></span><span class='op'>(</span><span class='op'>)</span>, trees <span class='op'>=</span> <span class='fu'><a href='https://hardhat.tidymodels.org/reference/tune.html'>tune</a></span><span class='op'>(</span><span class='op'>)</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> </span>
<span>  <span class='fu'><a href='https://parsnip.tidymodels.org/reference/set_engine.html'>set_engine</a></span><span class='op'>(</span><span class='st'>"partykit"</span>, trace <span class='op'>=</span> <span class='fl'>0</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> </span>
<span>  <span class='fu'><a href='https://parsnip.tidymodels.org/reference/set_args.html'>set_mode</a></span><span class='op'>(</span><span class='st'>"classification"</span><span class='op'>)</span></span>
<span></span>
<span><span class='co'># Extract parameters to tune</span></span>
<span><span class='va'>rf_param</span> <span class='op'>&lt;-</span> <span class='fu'><a href='https://hardhat.tidymodels.org/reference/hardhat-extract.html'>extract_parameter_set_dials</a></span><span class='op'>(</span><span class='va'>rf_model</span><span class='op'>)</span></span>
<span></span>
<span><span class='co'># Create parameter grid</span></span>
<span><span class='va'>rf_grid</span> <span class='op'>&lt;-</span> <span class='fu'><a href='https://tidyr.tidyverse.org/reference/expand.html'>crossing</a></span><span class='op'>(</span></span>
<span>  mtry <span class='op'>=</span> <span class='fl'>1</span><span class='op'>:</span><span class='fl'>4</span>,</span>
<span>  trees <span class='op'>=</span> <span class='fu'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='op'>(</span><span class='fl'>100</span>, <span class='fl'>200</span>, <span class='fl'>300</span>, <span class='fl'>400</span>, <span class='fl'>500</span><span class='op'>)</span><span class='op'>)</span></span>
<span></span>
<span><span class='co'># Set the model's recipe</span></span>
<span><span class='va'>rf_rec</span> <span class='op'>&lt;-</span> <span class='fu'>recipe</span><span class='op'>(</span><span class='va'>Outcome</span> <span class='op'>~</span> <span class='va'>.</span>, data <span class='op'>=</span> <span class='va'>imputed_data</span><span class='op'>)</span></span>
<span></span>
<span><span class='co'># Combine the model and recipe</span></span>
<span><span class='va'>rf_wflow</span> <span class='op'>&lt;-</span> </span>
<span>  <span class='fu'>workflow</span><span class='op'>(</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> </span>
<span>  <span class='fu'>add_model</span><span class='op'>(</span><span class='va'>rf_model</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> </span>
<span>  <span class='fu'>add_recipe</span><span class='op'>(</span><span class='va'>rf_rec</span><span class='op'>)</span></span>
<span></span>
<span><span class='co'># Edit the parameters to it knows what to expect</span></span>
<span><span class='va'>rf_param</span> <span class='op'>&lt;-</span> </span>
<span>  <span class='va'>rf_wflow</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> </span>
<span>  <span class='fu'><a href='https://hardhat.tidymodels.org/reference/hardhat-extract.html'>extract_parameter_set_dials</a></span><span class='op'>(</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> </span>
<span>  <span class='fu'><a href='https://rdrr.io/r/stats/update.html'>update</a></span><span class='op'>(</span></span>
<span>    mtry <span class='op'>=</span> <span class='fu'>mtry</span><span class='op'>(</span><span class='fu'><a href='https://rdrr.io/r/base/range.html'>range</a></span><span class='op'>(</span><span class='fl'>1</span>,<span class='fl'>4</span><span class='op'>)</span><span class='op'>)</span>,</span>
<span>    trees <span class='op'>=</span> <span class='fu'>trees</span><span class='op'>(</span><span class='fu'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='op'>(</span><span class='fl'>100</span>,<span class='fl'>500</span><span class='op'>)</span><span class='op'>)</span></span>
<span>  <span class='op'>)</span></span>
<span></span>
<span><span class='co'># Perform Grid Search</span></span>
<span><span class='va'>rf_reg_tune</span> <span class='op'>&lt;-</span></span>
<span>  <span class='va'>rf_wflow</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span></span>
<span>  <span class='fu'>tune_grid</span><span class='op'>(</span></span>
<span>    <span class='va'>fold_data</span>,</span>
<span>    grid <span class='op'>=</span> <span class='va'>rf_grid</span>,</span>
<span>    metrics <span class='op'>=</span> <span class='va'>roc_res</span></span>
<span>  <span class='op'>)</span></span>
<span></span>
<span><span class='co'># Results</span></span>
<span><span class='fu'><a href='https://ggplot2.tidyverse.org/reference/autoplot.html'>autoplot</a></span><span class='op'>(</span><span class='va'>rf_reg_tune</span><span class='op'>)</span> <span class='op'>+</span> </span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/ggtheme.html'>theme_minimal</a></span><span class='op'>(</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/scale_viridis.html'>scale_color_viridis_d</a></span><span class='op'>(</span>direction <span class='op'>=</span> <span class='op'>-</span><span class='fl'>1</span><span class='op'>)</span> <span class='op'>+</span> </span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/theme.html'>theme</a></span><span class='op'>(</span>legend.position <span class='op'>=</span> <span class='st'>"top"</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/labs.html'>labs</a></span><span class='op'>(</span>y<span class='op'>=</span> <span class='st'>"AUC"</span><span class='op'>)</span></span></code></pre></div>

</details><img src="jj3003_files/figure-html5/RandomForest-1.png" style="display: block; margin: auto auto auto 0;" />

</div>


The hypertuning shows that the best parameters given this dataset for the cforest appear to be 500 trees and 2 randomly selected predictors.

## KNN

<div class="layout-chunk" data-layout="l-page">
<details>
<summary>Show code</summary>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='co'># Tidy (Parsnip's) KNN Model</span></span>
<span><span class='va'>knn_model</span> <span class='op'>=</span> <span class='fu'><a href='https://parsnip.tidymodels.org/reference/nearest_neighbor.html'>nearest_neighbor</a></span><span class='op'>(</span>neighbors<span class='op'>=</span><span class='fu'><a href='https://hardhat.tidymodels.org/reference/tune.html'>tune</a></span><span class='op'>(</span><span class='op'>)</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> </span>
<span>  <span class='fu'><a href='https://parsnip.tidymodels.org/reference/set_engine.html'>set_engine</a></span><span class='op'>(</span><span class='st'>"kknn"</span>, trace <span class='op'>=</span> <span class='fl'>0</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> </span>
<span>  <span class='fu'><a href='https://parsnip.tidymodels.org/reference/set_args.html'>set_mode</a></span><span class='op'>(</span><span class='st'>"classification"</span><span class='op'>)</span></span>
<span></span>
<span><span class='co'># Extract parameters to tune</span></span>
<span><span class='va'>knn_param</span> <span class='op'>&lt;-</span> <span class='fu'><a href='https://hardhat.tidymodels.org/reference/hardhat-extract.html'>extract_parameter_set_dials</a></span><span class='op'>(</span><span class='va'>knn_model</span><span class='op'>)</span></span>
<span></span>
<span><span class='co'># Create parameter grid</span></span>
<span><span class='va'>knn_grid</span> <span class='op'>&lt;-</span> <span class='fu'><a href='https://tidyr.tidyverse.org/reference/expand.html'>crossing</a></span><span class='op'>(</span></span>
<span>  neighbors <span class='op'>=</span> <span class='fl'>3</span><span class='op'>:</span><span class='fl'>75</span><span class='op'>)</span></span>
<span></span>
<span><span class='co'># Set the model's recipe</span></span>
<span><span class='va'>knn_rec</span> <span class='op'>&lt;-</span> <span class='fu'>recipe</span><span class='op'>(</span><span class='va'>Outcome</span> <span class='op'>~</span> <span class='va'>.</span>, data <span class='op'>=</span> <span class='va'>imputed_data</span><span class='op'>)</span></span>
<span></span>
<span><span class='co'># Combine the model and recipe</span></span>
<span><span class='va'>knn_wflow</span> <span class='op'>&lt;-</span> </span>
<span>  <span class='fu'>workflow</span><span class='op'>(</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> </span>
<span>  <span class='fu'>add_model</span><span class='op'>(</span><span class='va'>knn_model</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> </span>
<span>  <span class='fu'>add_recipe</span><span class='op'>(</span><span class='va'>knn_rec</span><span class='op'>)</span></span>
<span></span>
<span><span class='co'># Edit the parameters to it knows what to expect</span></span>
<span><span class='va'>knn_param</span> <span class='op'>&lt;-</span> </span>
<span>  <span class='va'>knn_wflow</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> </span>
<span>  <span class='fu'><a href='https://hardhat.tidymodels.org/reference/hardhat-extract.html'>extract_parameter_set_dials</a></span><span class='op'>(</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> </span>
<span>  <span class='fu'><a href='https://rdrr.io/r/stats/update.html'>update</a></span><span class='op'>(</span></span>
<span>    neighbors <span class='op'>=</span> <span class='fu'>neighbors</span><span class='op'>(</span><span class='fu'><a href='https://rdrr.io/r/base/range.html'>range</a></span><span class='op'>(</span><span class='fl'>3</span>,<span class='fl'>75</span><span class='op'>)</span><span class='op'>)</span></span>
<span>  <span class='op'>)</span></span>
<span></span>
<span><span class='co'># Perform Grid Search</span></span>
<span><span class='va'>knn_reg_tune</span> <span class='op'>&lt;-</span></span>
<span>  <span class='va'>knn_wflow</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span></span>
<span>  <span class='fu'>tune_grid</span><span class='op'>(</span></span>
<span>    <span class='va'>fold_data</span>,</span>
<span>    grid <span class='op'>=</span> <span class='va'>knn_grid</span>,</span>
<span>    metrics <span class='op'>=</span> <span class='va'>roc_res</span></span>
<span>  <span class='op'>)</span></span>
<span></span>
<span><span class='co'># Results</span></span>
<span><span class='fu'><a href='https://ggplot2.tidyverse.org/reference/autoplot.html'>autoplot</a></span><span class='op'>(</span><span class='va'>knn_reg_tune</span><span class='op'>)</span> <span class='op'>+</span> </span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/scale_viridis.html'>scale_color_viridis_d</a></span><span class='op'>(</span>direction <span class='op'>=</span> <span class='op'>-</span><span class='fl'>1</span><span class='op'>)</span> <span class='op'>+</span> </span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/theme.html'>theme</a></span><span class='op'>(</span>legend.position <span class='op'>=</span> <span class='st'>"top"</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/ggtheme.html'>theme_minimal</a></span><span class='op'>(</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/labs.html'>labs</a></span><span class='op'>(</span>y<span class='op'>=</span> <span class='st'>"AUC"</span><span class='op'>)</span></span></code></pre></div>

</details><img src="jj3003_files/figure-html5/KNN-1.png" style="display: block; margin: auto auto auto 0;" />


</div>


The KNN's hypertuning through grid search revealed that the performance platens at k=50, however most of the importance is achieved at k=29

## Neural Networks

<div class="layout-chunk" data-layout="l-page">
<details>
<summary>Show code</summary>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='co'># Tidy (Parsnip's) Neural Network Model</span></span>
<span><span class='va'>nn_model</span> <span class='op'>=</span> <span class='fu'><a href='https://parsnip.tidymodels.org/reference/mlp.html'>mlp</a></span><span class='op'>(</span>hidden_units <span class='op'>=</span> <span class='fu'><a href='https://hardhat.tidymodels.org/reference/tune.html'>tune</a></span><span class='op'>(</span><span class='op'>)</span>, penalty <span class='op'>=</span> <span class='fu'><a href='https://hardhat.tidymodels.org/reference/tune.html'>tune</a></span><span class='op'>(</span><span class='op'>)</span>, epochs <span class='op'>=</span> <span class='fu'><a href='https://hardhat.tidymodels.org/reference/tune.html'>tune</a></span><span class='op'>(</span><span class='op'>)</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> </span>
<span>  <span class='fu'><a href='https://parsnip.tidymodels.org/reference/set_engine.html'>set_engine</a></span><span class='op'>(</span><span class='st'>"nnet"</span>, trace <span class='op'>=</span> <span class='fl'>0</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> </span>
<span>  <span class='fu'><a href='https://parsnip.tidymodels.org/reference/set_args.html'>set_mode</a></span><span class='op'>(</span><span class='st'>"classification"</span><span class='op'>)</span></span>
<span></span>
<span><span class='co'># Create parameter grid</span></span>
<span><span class='va'>nn_grid</span> <span class='op'>&lt;-</span> <span class='fu'><a href='https://tidyr.tidyverse.org/reference/expand.html'>crossing</a></span><span class='op'>(</span></span>
<span>  hidden_units <span class='op'>=</span> <span class='fl'>1</span><span class='op'>:</span><span class='fl'>6</span>,</span>
<span>  penalty <span class='op'>=</span> <span class='fu'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='op'>(</span><span class='fl'>0.0</span>, <span class='fl'>0.05</span>, <span class='fl'>0.1</span><span class='op'>)</span>,</span>
<span>  epochs <span class='op'>=</span> <span class='fu'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='op'>(</span><span class='fl'>5</span>,<span class='fl'>10</span>,<span class='fl'>15</span>,<span class='fl'>20</span>,<span class='fl'>25</span>,<span class='fl'>50</span>,<span class='fl'>75</span>,<span class='fl'>100</span>,<span class='fl'>125</span>,<span class='fl'>150</span>,<span class='fl'>175</span>,<span class='fl'>200</span><span class='op'>)</span><span class='op'>)</span></span>
<span></span>
<span><span class='co'># Set the model's recipe</span></span>
<span><span class='va'>nn_rec</span> <span class='op'>&lt;-</span> <span class='fu'>recipe</span><span class='op'>(</span><span class='va'>Outcome</span> <span class='op'>~</span> <span class='va'>.</span>, data <span class='op'>=</span> <span class='va'>imputed_data</span><span class='op'>)</span></span>
<span></span>
<span><span class='co'># Combine the model and recipe</span></span>
<span><span class='va'>nn_wflow</span> <span class='op'>&lt;-</span> </span>
<span>  <span class='fu'>workflow</span><span class='op'>(</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> </span>
<span>  <span class='fu'>add_model</span><span class='op'>(</span><span class='va'>nn_model</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> </span>
<span>  <span class='fu'>add_recipe</span><span class='op'>(</span><span class='va'>nn_rec</span><span class='op'>)</span></span>
<span></span>
<span><span class='co'># Edit the parameters so it knows what to expect</span></span>
<span><span class='va'>nn_param</span> <span class='op'>&lt;-</span> </span>
<span>  <span class='va'>nn_wflow</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> </span>
<span>  <span class='fu'><a href='https://hardhat.tidymodels.org/reference/hardhat-extract.html'>extract_parameter_set_dials</a></span><span class='op'>(</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> </span>
<span>  <span class='fu'><a href='https://rdrr.io/r/stats/update.html'>update</a></span><span class='op'>(</span></span>
<span>    hidden_units <span class='op'>=</span> <span class='fu'>hidden_units</span><span class='op'>(</span><span class='fu'><a href='https://rdrr.io/r/base/range.html'>range</a></span><span class='op'>(</span><span class='fl'>1</span><span class='op'>:</span><span class='fl'>6</span><span class='op'>)</span><span class='op'>)</span>,</span>
<span>    penalty <span class='op'>=</span> <span class='fu'>penalty</span><span class='op'>(</span><span class='fu'><a href='https://rdrr.io/r/base/range.html'>range</a></span><span class='op'>(</span><span class='fl'>0.0</span><span class='op'>:</span><span class='fl'>0.1</span><span class='op'>)</span><span class='op'>)</span>,</span>
<span>    epochs <span class='op'>=</span> <span class='fu'>epochs</span><span class='op'>(</span><span class='fu'><a href='https://rdrr.io/r/base/range.html'>range</a></span><span class='op'>(</span><span class='fl'>10</span><span class='op'>:</span><span class='fl'>200</span><span class='op'>)</span><span class='op'>)</span><span class='op'>)</span></span>
<span></span>
<span><span class='co'># Perform Grid Search</span></span>
<span><span class='va'>nn_reg_tune</span> <span class='op'>&lt;-</span></span>
<span>  <span class='va'>nn_wflow</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span></span>
<span>  <span class='fu'>tune_grid</span><span class='op'>(</span></span>
<span>    <span class='va'>fold_data</span>,</span>
<span>    grid <span class='op'>=</span> <span class='va'>nn_grid</span>,</span>
<span>    metrics <span class='op'>=</span> <span class='va'>roc_res</span></span>
<span>  <span class='op'>)</span></span>
<span></span>
<span><span class='co'># Results</span></span>
<span><span class='fu'><a href='https://ggplot2.tidyverse.org/reference/autoplot.html'>autoplot</a></span><span class='op'>(</span><span class='va'>nn_reg_tune</span><span class='op'>)</span> <span class='op'>+</span> </span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/scale_viridis.html'>scale_color_viridis_d</a></span><span class='op'>(</span>direction <span class='op'>=</span> <span class='op'>-</span><span class='fl'>1</span><span class='op'>)</span> <span class='op'>+</span> </span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/theme.html'>theme</a></span><span class='op'>(</span>legend.position <span class='op'>=</span> <span class='st'>"top"</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/ggtheme.html'>theme_minimal</a></span><span class='op'>(</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/labs.html'>labs</a></span><span class='op'>(</span>y<span class='op'>=</span> <span class='st'>"AUC"</span><span class='op'>)</span></span></code></pre></div>

</details><img src="jj3003_files/figure-html5/NeuralNetworks-1.png" style="display: block; margin: auto auto auto 0;" />

</div>


Hypertuning of the neural network shows that the model with 1 hidden unit had the most consistent performance. Although some other models peaked above that one, it has been less consistent overall.

# Q6: Wrapping up

Both the neural network and random forest managed to produce at least one model that produced consistent AUC. However both models are somewhat prone to noise in randomness, and as such the random forest seems to more consistent (smaller variation in the AUC), and thus I am going to go forward with the random forest and its best parameters from the hyper tuning.

## Final Model

Let's build the final model and save it as an R object

<div class="layout-chunk" data-layout="l-body">
<details>
<summary>Show code</summary>
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='va'>final_model</span> <span class='op'>&lt;-</span> <span class='fu'><a href='https://rdrr.io/pkg/partykit/man/cforest.html'>cforest</a></span><span class='op'>(</span><span class='va'>Outcome</span> <span class='op'>~</span> <span class='va'>.</span>, <span class='va'>imputed_data</span>, ntree <span class='op'>=</span> <span class='fl'>500</span>, mtry <span class='op'>=</span> <span class='fl'>2</span><span class='op'>)</span></span>
<span><span class='fu'><a href='https://rdrr.io/r/base/readRDS.html'>saveRDS</a></span><span class='op'>(</span><span class='va'>final_model</span>, <span class='st'>"jj3003_cforest.rds"</span><span class='op'>)</span></span></code></pre></div>

</details>

</div>


```{.r .distill-force-highlighting-css}
```
