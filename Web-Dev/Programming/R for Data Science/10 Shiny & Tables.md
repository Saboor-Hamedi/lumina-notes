---
tags: [r, shiny, tables, communication]
---

# 10 — Shiny & Tables

## Shiny Web Apps

```r
library(shiny)

ui <- fluidPage(
    titlePanel("Sales Dashboard"),
    fluidRow(
        column(3,
            selectInput("region", "Region", choices = unique(df$region)),
            dateRangeInput("dates", "Date Range",
                start = min(df$date), end = max(df$date)),
            actionButton("update", "Update", class = "btn-primary")
        ),
        column(9,
            tabsetPanel(
                tabPanel("Overview",
                    valueBoxOutput("total_revenue"),
                    valueBoxOutput("avg_order"),
                    valueBoxOutput("order_count"),
                    plotOutput("trend")
                ),
                tabPanel("Details", DT::DTOutput("details"))
            )
        )
    )
)

server <- function(input, output, session) {
    filtered <- eventReactive(input$update, {
        req(input$dates)
        df %>%
            filter(region %in% input$region,
                   between(date, input$dates[1], input$dates[2]))
    }, ignoreNULL = FALSE)

    revenue <- reactive({
        req(filtered())
        sum(filtered()$amount, na.rm = TRUE)
    })

    output$total_revenue <- renderValueBox({
        valueBox(value = scales::dollar(revenue()),
            subtitle = "Total Revenue", icon = icon("dollar-sign"), color = "green")
    })

    output$avg_order <- renderValueBox({
        valueBox(value = scales::dollar(mean(filtered()$amount, na.rm = TRUE)),
            subtitle = "Average Order", icon = icon("receipt"), color = "blue")
    })

    output$order_count <- renderValueBox({
        valueBox(value = nrow(filtered()),
            subtitle = "Orders", icon = icon("shopping-cart"), color = "yellow")
    })

    output$trend <- renderPlot({
        ggplot(filtered(), aes(x = date, y = amount, color = category)) +
            geom_smooth(se = FALSE) + theme_minimal() +
            labs(title = "Sales Trend")
    })

    output$details <- DT::renderDT({
        filtered() %>%
            datatable(filter = "top", options = list(pageLength = 10, scrollX = TRUE))
    })
}

shinyApp(ui, server)
```

## kableExtra

```r
library(kableExtra)

df %>%
    head(15) %>%
    knitr::kable(caption = "Top 15 Records") %>%
    kable_styling(
        bootstrap_options = c("striped", "hover", "condensed"),
        full_width = FALSE, position = "left"
    ) %>%
    add_header_above(c(" " = 1, "Demographics" = 3, "Financial" = 2)) %>%
    column_spec(1, bold = TRUE, width = "3cm") %>%
    column_spec(2:4, width = "4cm") %>%
    row_spec(0, background = "#3B9CDA", color = "white") %>%
    footnote(general = "Preliminary data", number = "Confidential") %>%
    scroll_box(width = "100%", height = "400px")
```

## gt

```r
library(gt)

summary_tbl <- df %>%
    group_by(region, category) %>%
    summarise(
        orders = n(),
        revenue = sum(amount, na.rm = TRUE),
        avg_order = mean(amount, na.rm = TRUE),
        .groups = "drop"
    )

summary_tbl %>%
    gt(groupname_col = "region") %>%
    tab_header(title = md("**Sales Summary**"), subtitle = "Q1 2024") %>%
    fmt_currency(columns = c(revenue, avg_order), currency = "USD") %>%
    fmt_number(columns = orders, decimals = 0) %>%
    data_color(columns = revenue, method = "numeric",
               palette = "viridis", domain = range(summary_tbl$revenue)) %>%
    cols_align(align = "center") %>%
    cols_label(orders = "Orders", revenue = "Revenue", avg_order = "Avg Order") %>%
    tab_source_note("Source: Internal Database")
```

## DT (DataTables)

```r
library(DT)

datatable(df,
    filter = "top",
    extensions = c("Buttons", "Scroller"),
    options = list(
        pageLength = 25,
        dom = "Bfrtip",
        buttons = list(
            list(extend = "copy"), list(extend = "csv", filename = "export"),
            list(extend = "excel", filename = "export")
        ),
        scrollX = TRUE, scrollY = 500, scroller = TRUE
    ),
    class = "display compact cell-border",
    rownames = FALSE
) %>%
    formatCurrency(columns = c("amount", "revenue"), currency = "$") %>%
    formatDate(columns = "date", method = "toLocaleDateString") %>%
    formatStyle("status",
        backgroundColor = styleEqual(
            c("active", "inactive", "pending"),
            c("#d4edda", "#f8d7da", "#fff3cd")
        ), fontWeight = "bold")
```

**Links**: [[Web-Dev/Programming/R for Data Science/08 Reports (R Markdown & Quarto)]] | [[Web-Dev/Programming/R for Data Science/05 Visualization (ggplot2)]] | [[Web-Dev/Programming/R for Data Science/01 Getting Started]]
**See also**: [[Dashboard Design]], [[Data Storytelling]]
