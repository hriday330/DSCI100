# Statistical Inference

To study a **population**, we are interested in computing a **population parameter** which is a numerical characteristic of the population.
In practice, we do this by taking measurements from a (representative) **sample** and computing a **sample estimate** which approximates the population parameter. The process of using a sample to draw conclusions about the broader population is called **statistical inference**

To take (reps) number of random samples from a population data frame, use:


```R
sample_1 <- rep_sample_n(tbl = population, size = 40, reps = 20)
```

Estimates may vary between samples due to **sampling variability.**. To visualise how much the sample variability is for a particular population, we take many sample proportions to get many sample estimates and plot these on a histogram, creating a **sampling distribution**. 


```R
sample_estimates <- samples |>
  group_by(replicate) |>
  summarize(sample_proportion = sum(room_type == "Entire home/apt") / 40)
##new column is made to calculate proportion/whatever sample estimate we want
```

The shape of the sampling distribution is usually a bell-curve with a peak centered at the population mean/proportion. As the sample size increases, the spread of the sampling distribution decreases (approaches the population mean) 

### Bootstrapping:
In practice, we cannot construct an exact sampling distribution because we don't usually have full access to the population data. 

**Creating a bootstrap distribution:** To create a bootstrap distribution from a single sample of size n using R: 
1) Randomly select observation from original sample
2) Record the value of the observation 
3) Replace that observation
4) Repeat 1)-3) to get n observations which form a **bootstrap sample**
5) Calculate the **bootstrap point estimate** of the n observations in the bootstrap sample
6) Repeat 1)-5) to create a distribution of point estimates, known as the **bootstrap distribution**.
7) Calculate the confidence interval for the observed point estimate

We can use the bootstrap distribution to calculate an approximate 95% percentile bootstrap **confidence interval**, which is a range of plausible values of the population parameter. This means that about 95% of the confidence intervals of 100 random samples would contain the population parameter's true value

The bootstrap distribution is expected to somewhat resemble the actual sampling distribution, and the shape and spread of the bootstrap distribution is a good approximation to that of the sampling distribution

## Chunks of Code

**Creating a population distribution:**


```R
options(repr.plot.width = 8, repr.plot.height = 7)
pop_dist <- ggplot(can_seniors, aes(age)) + 
    geom_histogram(binwidth = 1) +
    xlab("Age (years)") +
    ggtitle("Population distribution") +
    theme(text = element_text(size = 20), plot.margin = margin(10, 100)) # last x value was getting cut off
pop_dist
```

**Drawing a sample from the population:**


```R
one_sample <- can_seniors %>% 
    rep_sample_n(40) %>% 
    ungroup() %>% # ungroup the data frame 
    select(age) # drop the replicate column 
```

**Drawing n bootstrap samples:**


```R
boot1000<- one_sample %>%
    rep_sample_n(size = 40, replace = TRUE, reps = 1000)
```

**Calculating the mean of the n bootstrap sample:**


```R
boot1000_means <- boot1000 %>%
    group_by(replicate) %>%
    summarize(mean = mean(age))
```

**Visualising the bootstrap distribution:**


```R
boot_est_dist <- boot1000_means %>%
    ggplot(aes(x = mean)) + 
        geom_histogram(binwidth = 1) + 
        xlab("Average/mean age (years)") + 
        ggtitle("Bootstrap sample point estimate distribution")
```

**Calculating a 95% confidence interval for the point estimates:**

Note that the the actual population parameter may not always fall within the lower and upper bounds of the confidence interval. 


```R

```
