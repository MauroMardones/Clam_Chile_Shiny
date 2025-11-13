
```markdown
# Clam Fishery Monitoring App (Chile, 2018)

This Shiny application was developed in 2018 to visualize and analyze monitoring data from the **Chilean clam (*Almeja*) fishery**.  
The app provides interactive tools for exploring spatial and temporal patterns in catch, effort, and biological sampling data collected under the IFOP monitoring program.

---

## 🐚 Overview

The application allows users to:
- Visualize fishing activity by region and season.
- Explore size distributions and biological indicators of clam populations.
- Analyze trends in fishing effort and catch per unit effort (CPUE).
- Export summary tables and plots for reporting and management purposes.

The app was designed to support artisanal fishery assessments and data-driven management of benthic resources along the Chilean coast.

---

## ⚙️ Project Structure

```

Almeja_XI/
│
├── ui.R                # User Interface (UI) definition
├── server.R            # Server-side logic
├── www/                # Folder for CSS, images, and JavaScript files
│   ├── style.css
├── data/               # Input datasets (CSV, RDS, etc.) (upon request)
└── README.md

````

---

## 🖥️ UI and Server Description

### **ui.R**
Defines the layout and user interface of the application, including:
- Data filters (region, year, species)
- Tabs for visualizations and tables
- Interactive maps and plots using `leaflet`, `ggplot2`, and `plotly`
- Download buttons for exporting data and figures

Example:
```r
library(shiny)
shinyUI(fluidPage(
  titlePanel("Clam Fishery Monitoring - Chile 2018"),
  sidebarLayout(
    sidebarPanel(
      selectInput("region", "Select Region:", choices = c("X", "XI", "XII")),
      sliderInput("year", "Select Year:", min = 2000, max = 2018, value = 2018),
      actionButton("update", "Update Data")
    ),
    mainPanel(
      tabsetPanel(
        tabPanel("Spatial View", leafletOutput("map")),
        tabPanel("Size Structure", plotOutput("sizePlot")),
        tabPanel("CPUE Trends", plotlyOutput("cpuePlot"))
      )
    )
  )
))
````

---

### **server.R**

Implements the logic and data processing behind the UI, including:

* Reading and filtering input data
* Generating summaries and visualizations
* Rendering interactive elements (maps, plots, tables)
* Handling download requests

Example:

```r
library(shiny)
library(dplyr)
library(ggplot2)
library(leaflet)
library(plotly)

shinyServer(function(input, output, session) {
  
  # Reactive dataset
  filteredData <- reactive({
    data <- read.csv("data/catch_data.csv")
    data %>% filter(Year == input$year, Region == input$region)
  })
  
  # Map output
  output$map <- renderLeaflet({
    leaflet(filteredData()) %>%
      addTiles() %>%
      addCircleMarkers(~Longitude, ~Latitude, popup = ~paste("Catch:", Catch))
  })
  
  # Size distribution
  output$sizePlot <- renderPlot({
    bio <- read.csv("data/biological_data.csv")
    bio %>%
      filter(Region == input$region) %>%
      ggplot(aes(x = Length)) +
      geom_histogram(binwidth = 1, fill = "skyblue", color = "white") +
      labs(x = "Shell length (mm)", y = "Frequency")
  })
  
  # CPUE plot
  output$cpuePlot <- renderPlotly({
    data <- filteredData()
    p <- ggplot(data, aes(x = Month, y = CPUE)) +
      geom_line() +
      geom_point() +
      labs(x = "Month", y = "CPUE (kg/day)")
    ggplotly(p)
  })
})
```

---

## 🚀 How to Run the App

1. Clone or download this repository:

   ```bash
   git clone https://github.com/yourusername/Almeja_XI.git
   ```

2. Open R or RStudio and set your working directory to the app folder:

   ```r
   setwd("~/IFOP/CBA/Base_Datos_Erizo_Almeja_2016/Almeja_XI")
   ```

3. Run the app:

   ```r
   library(shiny)
   runApp()
   ```

4. The app will launch locally in your web browser at
   **[http://127.0.0.1:xxxx](http://127.0.0.1:xxxx)**

---

## 🧩 Dependencies

Make sure the following R packages are installed:

```r
install.packages(c("shiny", "leaflet", "ggplot2", "plotly", "dplyr"))
```

---

## 👨‍💻 Author

**Mauricio Mardones**
Fisheries Researcher, Chile
Focus: Artisanal and small-scale fisheries, spatial analysis, and R/Shiny applications.
GitHub: [https://github.com/MauroMardones](https://github.com/MauroMardones)

---

## 📅 Version

**Developed:** 2018
**Updated:** November 2025
**Institution:** Instituto de Fomento Pesquero (IFOP), Chile

---

