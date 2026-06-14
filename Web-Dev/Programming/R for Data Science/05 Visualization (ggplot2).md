---
tags: [r, ggplot2, visualization]
---

# 05 — Visualization (ggplot2)

## Themes System

```r
library(ggplot2)

# Built-in themes
p + theme_minimal(base_size = 14, base_family = "Arial")
p + theme_bw()
p + theme_classic()
p + theme_light()
p + theme_dark()
p + theme_void()

# Complete custom theme
p + theme(
    plot.title = element_text(size = 18, face = "bold", hjust = 0.5),
    plot.subtitle = element_text(size = 12, color = "grey40", hjust = 0.5),
    plot.caption = element_text(size = 9, color = "grey60", face = "italic"),
    axis.title = element_text(size = 12, face = "bold"),
    axis.text = element_text(size = 10),
    axis.text.x = element_text(angle = 45, hjust = 1),
    axis.ticks = element_line(color = "grey70"),
    panel.grid.major = element_line(color = "grey90", linewidth = 0.5),
    panel.grid.minor = element_blank(),
    panel.background = element_rect(fill = "white", color = NA),
    plot.background = element_rect(fill = "#f9f9f9", color = NA),
    legend.position = "bottom",
    legend.title = element_text(face = "bold"),
    legend.key = element_rect(fill = "white"),
    strip.background = element_rect(fill = "#3B9CDA", color = NA),
    strip.text = element_text(color = "white", face = "bold"),
    plot.margin = margin(15, 15, 15, 15)
)

# Extension themes
# library(ggthemes)
p + theme_economist() + scale_fill_economist()
p + theme_fivethirtyeight()
p + theme_tufte()
p + theme_wsj()
```

## Scales

```r
# Position scales
p + scale_x_continuous(breaks = seq(0, 100, 10), limits = c(0, NA))
p + scale_y_continuous(labels = scales::comma)
p + scale_y_log10()
p + scale_x_reverse()

# Discrete scales
p + scale_fill_brewer(palette = "Set2")
p + scale_color_viridis_d(option = "D", begin = 0.2, end = 0.8)
p + scale_fill_manual(values = c("A" = "#E41A1C", "B" = "#377EB8", "C" = "#4DAF4A"))

# Gradient scales
p + scale_fill_gradient(low = "white", high = "red")
p + scale_fill_gradient2(low = "#4575B4", mid = "#FFFFBF", high = "#D73027", midpoint = 0)
p + scale_fill_gradientn(colors = c("navy", "blue", "green", "yellow", "red"))

# Date scales
p + scale_x_date(date_breaks = "1 month", date_labels = "%b %Y")
p + scale_x_datetime(date_breaks = "6 hours", date_labels = "%H:%M")
```

## Faceting

```r
p + facet_wrap(~ region, ncol = 3)
p + facet_wrap(~ region, scales = "free_y")
p + facet_grid(year ~ month)
p + facet_grid(region ~ .)
p + facet_grid(region ~ category, scales = "free", space = "free_x")

# Custom labeller
region_labels <- c(North = "Northern Region", South = "Southern Region")
p + facet_wrap(~ region, labeller = labeller(region = region_labels))

# Multiple variables
p + facet_grid(region + category ~ year)
```

## Annotations and Labels

```r
# Text annotations
p + geom_text(aes(label = label), data = highlight_df, vjust = -0.5)
p + annotate("text", x = 50, y = 100, label = "Important Value", size = 5, fontface = "bold")

# Label repulsion with ggrepel
library(ggrepel)
p + geom_label_repel(
    aes(label = customer),
    data = filter(df, amount > quantile(amount, 0.95)),
    box.padding = 0.5, point.padding = 0.3, segment.color = "grey50"
)

# Rectangles and shading
p + annotate("rect",
    xmin = as.Date("2024-01-01"), xmax = as.Date("2024-03-31"),
    ymin = -Inf, ymax = Inf, alpha = 0.1, fill = "yellow")

# Lines
p + geom_vline(xintercept = 50, linetype = "dashed", color = "red", linewidth = 1)
p + geom_hline(yintercept = 0, linetype = "dotted")
p + geom_abline(intercept = 0, slope = 1, color = "blue")
```

## Saving Plots

```r
# Basic
ggsave("plot.png")
ggsave("plot.pdf", width = 8, height = 5, units = "in")

# High resolution
ggsave("publication.png", dpi = 600, width = 10, height = 6.5)

# Vector formats
ggsave("figure.svg", device = "svg", width = 8, height = 5)
ggsave("figure.eps", device = "eps")

# Customize
ggsave(
    filename = "report/figure1.pdf",
    plot = last_plot(),
    device = cairo_pdf,
    width = 7, height = 5, dpi = 300, bg = "white"
)

# Save multiple
plots <- list(p1, p2, p3, p4)
walk2(plots, names(plots), ~ ggsave(paste0("plots/", .y, ".png"), .x, width = 8, height = 5))
```

**Links**: [[Web-Dev/Programming/R for Data Science/03 Data Wrangling (dplyr)]] | [[Web-Dev/Programming/R for Data Science/04 Data Tidying (tidyr)]] | [[Web-Dev/Programming/R for Data Science/08 Reports (R Markdown & Quarto)]]
**See also**: [[Python Data Visualization]], [[Data Storytelling]]
