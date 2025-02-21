```{r, echo = FALSE}
posts <- list.files("website/content/blog", 
           "^index.md",
           recursive = TRUE, 
           full.names = TRUE)
posts <- lapply(posts, readLines)

find_key <- function(x, key){
  j <- lapply(x, function(x){
    k <- grep(sprintf("^%s:", key), 
            x, value = TRUE)
    k <- gsub(sprintf("^%s: |'", key), "", k)
    k[1] 
  })
  unlist(j)
}

postdf <- data.frame(
  n = seq_along(posts),
  draft = find_key(posts, "draft") |> 
    grepl(pattern = "true", x = _),
  date = as.Date(find_key(posts, "date")),
  slug = find_key(posts, "slug") |> 
    gsub('\\"', "", x = _),
  title = find_key(posts, "title")
) |> 
  subset(subset = !draft)
postdf$link <-  sprintf("[%s](https://drmowinckels.io/blog/%s)", 
                  postdf$title,
                  postdf$slug)

today    <- Sys.Date()
min_date <- min(postdf$date)
last_post <- as.numeric(max(postdf$date) - today)

postavg <- nrow(postdf)/as.numeric(today - min_date) * 30
postavg <- sprintf("%0.2f", postavg)

postbtw <- as.numeric(today - min_date) / nrow(postdf)
postbtw <- sprintf("%s", round(postbtw, digits = 0))

```


🎉 [DrMowinckels.io](https://drmowinckels.io/) has **`r nrow(postdf)`** posts since **`r min_date`**!

📅 That's a post roughly every **`r postbtw`** days, or about **`r postavg`** posts per month, since `r min_date`.

✍️ The last post was published **`r last_post`** days ago (`r tail(postdf, 1)$link`).

```{r 'plot', echo = FALSE,  fig.width=10, fig.height=2.5}
library(lattice)

postdf$ones <- 1

# Assuming postdf is loaded and has a 'date' column
xyplot(ones ~ date, data = postdf,
       type = 'p',
       pch = "|",  
       cex = 5,   
       col = "cyan3",
       xlab = "",
       ylab = "",
       main = "Published posts",
       scales = list(x = list(cex = 1.4), y = list(draw = FALSE)),
       strip = FALSE,  # Removes strip labels
       axis.line = list(col = "transparent"),
       layout = c(1, 1),  # Single panel
       par.settings = list(
         strip.border = list(col = "transparent"), #making the border transparent
         axis.line = list(col = "transparent") #making the axes transparent
       )
      )

```

<details><summary>📂 Click to expand a full list of posts</summary>

```{r posts-table, results='asis', echo = FALSE}
data.frame(
  Date = rev(postdf$date),
  Title = rev(postdf$link)
) |> 
  knitr::kable()
```
</details>
