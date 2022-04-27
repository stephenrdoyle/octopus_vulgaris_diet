
```R
# load libraries
library(tidyverse)
library(reshape2)
library(viridis)

# lead data and transform it
data <- read.table("prey_species_counts.txt", header=T, sep="\t")
data_2 <- melt(data, id.vars = c("Species_group_1", "Species_group_2", "Species_group_3", "Species", "Count", "Reads", "Reads_percent"))



# plot
ggplot(data_2) +
     geom_tile(aes(variable, Species, fill=log10(value+1), group=paste0(Species_group_3," | ",Species_group_1))) + scale_fill_viridis(option="plasma") +
     scale_y_discrete(limits = rev) +
     facet_grid(paste0(Species_group_3," | ",Species_group_1)~., scales="free", space = "free") +
     theme_bw() + theme(panel.spacing.y = unit(0, "lines"), strip.text.y = element_text(angle = 0)) +
     labs(y="Prey species", x="Sampling location and conditions", fill="log10(read_count)")

# save it
ggsave("prey_species_counts_heatmap.pdf", height=12, width=8)
ggsave("prey_species_counts_heatmap.png")
```
![](../04_analysis/prey_species_counts_heatmap.png)

```R
library(tidyverse)
library(reshape2)
library(viridis)
library(patchwork)

data <- read.table("prey_species_counts_summary.txt", header=T, sep="\t")
data_2 <- melt(data, id.vars = c("Group"))


# plot
plot_1 <- ggplot(data_2, aes(x=variable, y=value, fill=Group)) +
     geom_bar(position="fill", stat="identity") +
     scale_fill_viridis_d(option="plasma")

# log10+1 transformed
plot_2 <- ggplot(data_2, aes(x=variable, y=log10(value+1), fill=Group)) +
     geom_bar(position="fill", stat="identity") +
     scale_fill_viridis_d(option="plasma")

plot_1 + plot_2 + plot_layout(ncol=1, guides = 'collect')


```


```R
library(tidyverse)
library(reshape2)
library(viridis)
library(patchwork)
library(ggrepel)

data <- read.table("prey_per_sucker.txt", header=T, sep="\t")


ggplot(data, aes(Suckers, Abundance, col=Species)) +
     geom_line(size=1) +
     geom_point() +
     scale_colour_viridis(discrete=TRUE, option="plasma") +
     theme_bw() + theme(legend.position="none") +
     geom_text_repel(aes(label = Species),
               data = data %>% group_by(Species) %>% filter(Suckers == max(Suckers)),
               nudge_x = 0.5,
               box.padding = 0.5, max.overlaps = Inf,
               size = 4) +
     scale_x_continuous(expand = expansion(mult = 0.3)) +
     scale_y_continuous(expand = expansion(mult = 0.3)) +
     labs() +
     facet_grid(Location ~ .)


```
