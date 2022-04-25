# Clustering 

**Clustering** is an unsupervised learning task (no response variable labels or values) where we are trying to find groups in the data 

**WSSD: Within-cluster Sum of Squared Distances**. Lower WSSD means better clustering (clusters are tightly packed together)

**K Means Algorithm:** Groups the unlabelled data into k clusters via the following step:
1) Random initialisation of clusters
2) **Center update**: Compute the center of each cluster.
3) **Label update**: Reassign each data point to the cluster with the nearest center.
4) Repeat 2) and 3) until no clusters change /no label assignments change

Generally, we use a set number of random restarts (nstart argument). Sometime, on a particular initialisation, the k-means algorithm gets stuck in a bad solution. By using a few restarts, we can just simply repeat the algorithm with a different initialisation and pick the best clustering (if one exists). 

**Choosing K:** Choose K at the elbow on an elbow plot (total WSSD vs K). K smaller than this would result in too few clusters, so groups are not visible in the data. Conversely, if K is too large, then there are too many subgroups which means important patterns or clusters are not visible. 


**Standardising the data for K-Means** Variables with a large scale will have a much larger effect on deciding cluster assignment than variables with a small scale. This is because K-Means uses euclidean distance to compute the distance between a point and the center of a cluster. 


```R
standardized_data <- not_standardized_data %>%
  mutate(across(everything(), scale))

standardized_data
```




One disadvantage of not being able to visualise the clusters with multidimensional data is it is harder, or even not possible, to assign human-readable labels to the clusters based on their relative positions on the respectives variable axes. We would require further data processing to get the approximate range of values in each cluster and assign such labels.

## Chunks of code

**Creating the elbow stats data frame:**


```R
k_vals <- tibble(k = seq(1,10))
scaled_km_clustered <- k_vals %>%
    rowwise() %>%
    mutate(poke_clusts = list(kmeans(scaled_km_data, nstart = 10, k)))

scaled_km_glanced <- scaled_km_clustered %>%
    mutate(glanced = list(glance(poke_clusts)))

elbow_stats<- scaled_km_glanced %>%
    unnest(glanced) %>%
    select(-poke_clusts)

print(elbow_stats)

```


**Creating the Elbow plot:** Once the plot is done, we choose the k value at the elbow.


```R
elbow_plot <- elbow_stats %>%
    ggplot(aes(x = k, y = tot.withinss)) +
        geom_point() + 
        geom_line() +
        xlab("Number of clusters") + 
        ylab("Within-cluster sum of squares") + 
        ggtitle("Elbow Plot")
elbow_plot
```



**Performing K-means on the scaled data with the optimal k:**


```R
pokemon_final_kmeans <- kmeans(scaled_km_data, centers = 3)
df <- augment(pokemon_final_kmeans, scaled_km_data)
pokemon_final_clusters_plot <- ggplot(df, aes(x = Speed, y = Defense, colour = .cluster))+
        geom_point() + 
        labs(x = "Speed points", y = "Defensive Strength", colour = "Cluster")
                                
pokemon_final_clusters_plot
```



```R

```
