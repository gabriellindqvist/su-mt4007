---
title: "test"
output: html_document
---

## This is a Markdown Heading

Some text explaining the analysis.

```{r}
# This is an R code chunk
print("Hello, R Markdown!")

```{r, echo = FALSE}
# ------------------------------------ Install required packages ------------------------------------------
# install.packages("tidyverse")
# install.packages("RColorBrewer")
# install.packages("plotly")
# install.packages("IRkernel")
# devtools::install_github("JaseZiv/worldfootballR")

# ------------------ Loading necessary libraries and data preparations ------------------------------------
suppressPackageStartupMessages({
    library(tidyverse)
    library(ggplot2)
    library(stringr)
    library(fmsb)
    library(plotly)
    library(RColorBrewer)
    IRKernel::installspec()
})
```
# Who Is The Better Striker? Alexander Isak vs. Viktor Gyökeres

## Introduction
Sweden as a footballing nation (men's football) has not yet made a significant impact internationally, compared to the so-called **Big Five** (England, Germany, Spain, Italy and France). Historically, Sweden has not been known for mass producing world-class players. However, the tide may be turning. Today, Sweden might be in its best position recorded with standout players like Isak, Gyökeres, Kulusevski, all whom are chasing the legacy of Zlatan Ibrahimović. 

Now, the question arises: who could possibly be Swedens next star striker? Amomg the candidates are two exciting forwards—Alexander Isak and Viktor Gyökeres—who both have made significant strides in their careers in recent years. This project aims explore this question purely based on recorded player metrics. Specifically, player metrics from Domestic Leagues and European Cups during the seasons 2023-2025. 

By comparing the performance of Isak and Gyökeres across various metrics to determine their strengths and weaknesses. By focusing on key areas such as goal-scoring efficiency, creativity, and possession metrics, we aim to provide an objective evaluation of their contributions on the pitch.

The data used in this analysis was extracted from the website `https://fbref.com/en/`, using the package `worldfootballR`. For more coding details regarding the collection of data, see `data.ipynb` in the repo. 

## Selection of Players
The selection of players are based on:

- Plays in one of the **Big Five** domestic leagues
- Performances during 2023-2024 and/or high market value, based on transfermarkt.com

## Visualization: Interactive Bar Plots
The interactive barplots displayed have been created using the package `plotly`. It includes a drop-down button menu, where each button yields a different plot. Furthermore, you can disable a displayed metric by clicking on the specific metric found in the legend.

### Visualization: Attacking Metrics

```{r, echo = FALSE}
# Read and assign the data to a variable
df <- read.csv("playerstatdf.csv", fileEncoding = "UTF-8")

# Remove column X
df <- df %>%
    select(-X)

# Add Progressive Carries per 90 and Progressive Passes Received per 90 stat
df <- df %>%
    mutate(
        PrgCPer90 = PrgC / MinutesPer90,
        PrgRPer90 = PrgR / MinutesPer90,
        PrgPPer90 = PrgP / MinutesPer90
    )

# Added the total number of observations for each striker
## which is used in some visualizations of metrics
df <- df %>%
    group_by(Player) %>%
    add_tally() %>%
    ungroup()

## NOTE: Removed the player Samu Omorodion due to low playing time
df <- df %>%
    filter(Player != "Samu Omorodion")
```
# Who Is The Better Striker? Alexander Isak vs. Viktor Gyökeres

## Introduction (Work In Progress)
Sweden as a footballing nation have not yet made a significant impact internationally, compared to the so-called **Big Five** (England, Germany, Spain, Italy and France). Historically, Sweden has not been known for mass producing world-class players. However, the tide may be turning. 

According to a report by Riksidrottsförbundent, football is by far the largest sport in Sweden, with over 1.5 million Swedish Football Association (Fotbollsförbundet) members. ... 

Today, Sweden might be in its best position recorded with standout players like Isak, Gyökeres, Kulusevski, all whom are chasing the legacy of Zlatan Ibrahimović. 

Now, the question arises: who could possibly fill Zlatan's shoes? Amomg the candidates are two exciting forwards—Alexander Isak and Viktor Gyökeres—who both have made significant strides in their careers in recent years. This project aims to answer this question purely based on recorded player metrics. Specifically, player metrics from Domestic Leagues and European Cups during the seasons 2023-2025.  we compare the performance of Isak and Gyökeres across various metrics to determine their strengths and weaknesses. By focusing on key areas such as goal-scoring efficiency, creativity, and possession metrics, we aim to provide an objective evaluation of their contributions on the pitch.

The data used in this analysis was extracted from the website `https://fbref.com/en/`, using the package `worldfootballR`. For more coding details, see `data.ipynb` in the repo. 
## Selection of Players
The 

## Visualization: Interactive Bar Plots
The interactive barplots displayed have been created using the package `plotly`. It includes a drop-down button menu, where each button yields a different plot. Furthermore, you can disable a displayed metric by clicking on the specific metric found in the legend.

### Visualization: Attacking Metrics

```{r, echo = FALSE}
# Prepare data to be displayed
gsa <- df %>%
    group_by(Player) %>%
    summarise(
        Shots90 = round(mean(ShotsPer90), digits = 3),
        SoT90 = round(mean(SoTPer90), digits = 3),
        GoalConversionRate = round(sum(Goals) / sum(Shots), digits = 3) * 100,
        ShootingEfficiency = round(sum(SoTPer90) / sum(ShotsPer90), digits = 3) * 100,
        GCA90 = round(mean(GCAPer90), digits = 3),
        SCA90 = round(mean(SCAPer90), digits = 3),
        xG90 = round(mean(xGPer90), digits = 3),
        Goals90 = round(mean(GoalsPer90), digit = 3),
        xAG90 = round(mean(xAGPer90), digits = 3),
        Assists90 = round(mean(AssistsPer90), digits = 3)
    )

# Define a colorblind-friendly palette
colors <- brewer.pal(n = 11, name = "RdBu")

# Define the order of the bars
player_order <- c("Viktor Gyökeres", "Alexander Isak", "Dušan Vlahovic", "Erling Haaland", "Harry Kane", "Jonathan David", 
                  "Kylian Mbappé", "Lautaro Martínez",  "Loïs Openda", "Marcus Thuram", "Robert Lewandowski")

# Initiate Interactive Plot
gsa_plot <- plot_ly()

# Add a trace for each attribute
gsa_plot <- gsa_plot %>%
    add_trace(
        data = gsa,
        x = ~GoalConversionRate, y = ~Player, type = "bar", name = "Goal Conversion Rate (%)", marker = list(color = colors[1]), visible = TRUE
    ) %>%
    add_trace(
        data = gsa,
        x = ~ShootingEfficiency, y = ~Player, type = "bar", name = "Shooting Efficiency (%)", marker = list(color = colors[10]), visible = TRUE
    ) %>%
    add_trace(
        data = gsa,
        x = ~Shots90, y = ~Player, type = "bar", name = "Shots per 90", marker = list(color = colors[1]), visible = FALSE
    ) %>%
    add_trace(
        data = gsa,
        x = ~SoT90, y = ~Player, type = "bar", name = "Shots on Target per 90", marker = list(color = colors[10]), visible = FALSE
    ) %>%
    add_trace(
        data = gsa,
        x = ~GCA90, y = ~Player, type = "bar", name = "Goal Creating Actions per 90", marker = list(color = colors[1]), visible = FALSE
    ) %>%
    add_trace(
        data = gsa,
        x = ~SCA90, y = ~Player, type = "bar", name = "Shot Creating Actions per 90", marker = list(color = colors[10]), visible = FALSE
    ) %>%
    add_trace(
        data = gsa,
        x = ~xG90, y = ~Player, type = "bar", name = "Expected Goals per 90", marker = list(color = colors[1]), visible = FALSE
    ) %>%
    add_trace(
        data = gsa,
        x = ~Goals90, y = ~Player, type = "bar", name = "Goals per 90", marker = list(color = colors[10]), visible = FALSE
    ) %>%
    add_trace(
        data = gsa,
        x = ~xAG90, y = ~Player, type = "bar", name = "Expected Assists per 90", marker = list(color = colors[1]), visible = FALSE
    ) %>%
    add_trace(
        data = gsa,
        x = ~Assists90, y = ~Player, type = "bar", name = "Assists per 90", marker = list(color = colors[10]), visible = FALSE
    )

# Add a drop down menu
gsa_plot <- gsa_plot %>%
    layout(
        title = "Average Attacking Metrics: Domestic League and European Cups 2023-2025",
        xaxis = list(title = "Proportion (%)"),
        yaxis = list(title = "Player",
                    categoryorder = "array",
                    categoryarray = player_order),
        bargap = 0.5,
        updatemenus = list(
            list(
                type = "buttons",
                x = 1.17,
                y = 0.2,
                xanchor = "right",
                yanchor = "bottom",
                buttons = list(
                    list(
                        method = "update",
                        args = list(
                            list(visible = c(TRUE, TRUE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE)),
                            list(xaxis = list(title = "Proportion (%)"))
                        ),
                        label = "Goal Conversion Rate and SoT (%)"
                    ),
                    list(
                        method = "update",
                        args = list(
                            list(visible = c(FALSE, FALSE, TRUE, TRUE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE)),
                            list(xaxis = list(title = "Per 90"))
                        ),
                        label = "Shots90 vs SoT90"
                    ),
                    list(
                        method = "update",
                        args = list(
                            list(visible = c(FALSE, FALSE, FALSE, FALSE, TRUE, TRUE, FALSE, FALSE, FALSE, FALSE)),
                            list(xaxis = list(title = "Per 90"))
                        ),
                        label = "GCA/90 and SCA/90"
                    ),
                    list(
                        method = "update",
                        args = list(
                            list(visible = c(FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, TRUE, FALSE, FALSE)),
                            list(xaxis = list(title = "Per 90"))           
                        ),
                        label = "xG/90 vs Actual"
                    ),
                    list(
                        method = "update",
                        args = list(
                            list(visible = c(FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, TRUE)),
                            list(xaxis = list(title = "Per 90"))
                        ),
                        label = "xAG/90 vs Actual"
                    )
                ),
                direction = "down",
                showactive = TRUE
            )
        )
    )

gsa_plot
```
Viktor Gyökeres and Alexander Isak demonstrate exceptional attacking efficiency among the analyzed strikers. Ranking 2nd and 4th (0.2 percent behind 3rd in Kane) in Goal Conversion Rate (Goals /Shots), Gyökeres $(28.4\%)$ and Isak $(26.3\%)$ showcase their clinical finishing abilites. They also maintain an impressive Shooting Efficiency, with almost half of the shots recorded being on target. The recorded metrics are similar to the likes of Robert Lewandowski and Harry Kane, who both are proven star players. 

When comparing Expected Goals per 90 minutes (xG/90) to Actual Goals per 90 minutes (Goals/90), Gyökeres significantly exceeds expectations, highlighting his ability to convert opportunities into goals at an exceptional rate. Conversely, Isak performs consistently with his expected values, neither underperforming nor overperforming. Interestingly, elite strikers like Kylian Mbappé and Erling Haaland underperform their average xG/90, reflecting potential inefficiencies or tactical dependencies.

Analyzing Goal Creating Actions per 90 minutes (GCA/90) and Shot Creating Actions per 90 minutes (SCA/90), Gyökeres consistently outperforms Isak. Furthermore, Gyökeres excels in Assists per 90 minutes, surpassing Isak in creating scoring opportunities for teammates. Notably, both players shows slight underperformance in the Assists per 90 metric, with only three strikers outperforming their respective expected metric. Despite this, Gyökeres' higher involvement in both GCA/90 and SCA/90 highlights his integral role in driving his team's offensive output.

Gyökeres' overall attacking metrics are exceptionally strong, compared to the other players in the dataset. His ability to consistently create and assist opportunities, and score goals, places him among the most dynamic forwards in the dataset, with similar recorded metrics to Harry Kane. On the other hand, Isak demonstrates a slightly more balanced profile, with solid Creating Action metrics but clinically exceptional.

```{r, echo = FALSE}
# Prepare data to be displayed
poss <- df %>%
    group_by(Player) %>%
    summarise(
        PrgC90 = round(mean(PrgCPer90), digits = 3),
        PrgR90 = round(mean(PrgRPer90), digits = 3),
        PrgP90 = round(mean(PrgPPer90), digits = 3),
        FinalThirdC90 = round(sum(FinalThirdCarries / MinutesPer90) / unique(n), digits = 3),
        CarriesPenaltyArea90 = round(sum(CarriesPenaltyArea / MinutesPer90) / unique(n), digits = 3),
        SuccessfulTakeOns = round(mean(SuccessfulTakeOnsPercent), digits = 3),
        Dispossessed90 = round(sum(Dispossessed / MinutesPer90) / unique(n), digits = 3)
    )

# Initiate Interactive Plot
poss_plot <- plot_ly() 

# Add a trace for each attribute
poss_plot <- poss_plot %>%
    add_trace(
        data = poss,
        x = ~PrgC90, y = ~Player, type = "bar", name = "Progressive Carries per 90", marker = list(color = colors[1]), visible = TRUE
    ) %>%
    add_trace(
        data = poss,
        x = ~PrgR90, y = ~Player, type = "bar", name = "Progressive Passes Received per 90", marker = list(color = colors[10]), visible = TRUE
    ) %>%
    add_trace(
        data = poss,
        x = ~PrgP90, y = ~Player, type = "bar", name = "Progressive Passes per 90", marker = list(color = colors[3]), visible = TRUE
    ) %>%
    add_trace(
        data = poss,
        x = ~FinalThirdC90, y = ~Player, type = "bar", name = "Final Third Carries per 90", marker = list(color = colors[1]), visible = FALSE
    ) %>%
    add_trace(
        data = poss,
        x = ~SuccessfulTakeOns, y = ~Player, type = "bar", name = "Successful Take-Ons (%)", marker = list(color = colors[1]), visible = FALSE
    ) %>%
    add_trace(
        data = poss,
        x = ~Dispossessed90, y = ~Player, type = "bar", name = "Dispossessed per 90", marker = list(color = colors[1]), visible = FALSE
    )

# Add a drop down menu
poss_plot <- poss_plot %>%
    layout(
        title = "Average Possession Metrics: Domestic League and European Cups 2023-2025",
        xaxis = list(title = "Prg/90"),
        yaxis = list(title = "Player",
                    categoryorder = "array",
                    categoryarray = player_order),
        bargap = 0.5,
        updatemenus = list(
            list(
                type = "buttons",
                x = 1.10,
                y = 0.2,
                xanchor = "right",
                yanchor = "bottom",
                buttons = list(
                    list(
                        method = "update",
                        args = list(
                            list(visible = c(TRUE, TRUE, TRUE, FALSE, FALSE, FALSE)),
                            list(xaxis = list(title = "Prg/90"))
                        ),
                        label = "Progressive Metrics"
                    ),
                    list(
                        method = "update",
                        args = list(
                            list(visible = c(FALSE, FALSE, FALSE, TRUE, FALSE, FALSE)),
                            list(xaxis = list(title = "1/3 per 90"))
                        ),
                        label = "Final Third Carries per 90"
                    ),
                    list(
                        method = "update",
                        args = list(
                            list(visible = c(FALSE, FALSE, FALSE, FALSE, TRUE, FALSE)),
                            list(xaxis = list(title = "Succ%"))       
                                   ),
                        label = "Successful Take-Ons (%)"
                    ),
                    list(
                        method = "update",
                        args = list(
                            list(visible = c(FALSE, FALSE, FALSE, FALSE, FALSE, TRUE)),
                            list(xaxis = list(title = "Dis/90"))
                        ),
                        label = "Dispossessed per 90"
                    )
                ),
                direction = "down",
                showactive = TRUE
            )
        )
    )

poss_plot
```
Viktor Gyökeres and Alexander Isak demonstrate strong possession metrics relative to other strikers. In particular, Gyökeres excels in Progressive Carries per 90, Final Third Carries per 90 as well as Progressive Passes Received per 90, indicating his ability to advance the ball effectively both on- and off the ball. This highligts his importance in transitions and build-up play. Conversley, Isak scores lower in these three metrics but performs better in Progressive Passes per, reflecting a more balanced role in in linking play and distrbuting the ball. Isak also shows above-average performance in Successful Take-Ons (%), while Gyökeres ranks second to last in this metric. This suggests that Isak has an edge in dribbling proficiency and retaining possession under pressure. However, Gyökeres and Isak ranks 1st and 2nd in Dispossessed per 90 (Dis/90), which may reflect their willingness to take more risks in possession compared to the other strikers.

Gyökeres' possession metrics emphasize his versatility and pivotal role in his team's attacking structure. On the other hand, Isak's metrics highlights a different skill set, with potentially a more technical and creative approach to ball progression. Together, these stats displays a nuanced perspective of their respective playing style but should be part of a broader analysis incorporating team and tactical context. part of a broader analysis incorporating team and tactical context.

## Radar Chart: Isak vs Gyökeres vs Strikers
The final visualization is a side by side comparison using a radar chart, created using the package `fmsb`.

```{r, echo = FALSE}
radar_stats <- df %>%
    group_by(Player) %>%
    summarise(
        Goals90 = mean(GoalsPer90),
        Assists90 = mean(AssistsPer90),
        SoTPercentage = round(sum(SoTPer90) / sum(ShotsPer90), digits = 3) * 100,
        GoalConversionRate = sum(Goals) / sum(Shots),
        SuccessfulTakeOns = mean(SuccessfulTakeOnsPercent),
        SCA90 = mean(SCAPer90),
        GCA90 = mean(GCAPer90),
        Progression90 = sum(PrgCPer90 + PrgPPer90 + PrgRPer90) / unique(n) # Average of total Progression per 90
    )

## OPTIONAL: display radar_stats and its summary
#summary(radar_stats)
#radar_stats %>% filter(Player %in% c("Alexander Isak", "Viktor Gyökeres"))

# Manually construct the data frame for plotting the radar chart, based on the values in radar_stats
comp_stats <- data.frame(
    # variable = c(max, min, Isak recorded stat, Gyökeres recorded stat)
    # max is the maximum value in each column found in radar_stats
    Goals90 = c(1.005, 0, 0.820, 0.927),
    Assists90 = c(0.3275, 0, 0.1100, 0.2225),
    SoTPercentage = c(49.90, 0, 48.2, 45.5),
    GoalConversionRate = c(0.2906, 0, 0.2632, 0.2844),
    GCA90 = c(0.615, 0, 0.37, 0.61),
    SCA90 = c(4.185, 0, 2.667, 4.185),
    SuccessfulTakeOns = c(49.62, 0, 41.07, 37.08),
    Progression90 = c(21.01, 0, 10.89, 15.84),
    row.names = c("max", "min", "Alexander Isak", "Viktor Gyökeres")
)

colnames(comp_stats) <- c("Goals/90", "Assists/90", "SoT (%)", "Goal Conversion (%)", "GCA/90", "SCA/90", "Succ%", "Prg/90")

# Define fill colors
colors_fill <- c(
    scales::alpha("gray", 0.4),
    scales::alpha("green", 0.1)
)

# Define line colors
colors_line <- c(
    scales::alpha("black", 0.7),
    scales::alpha("darkgreen", 0.7)
)

# Generate plot
radarchart(
    comp_stats,
    seg = 9,
    title = "Radar Chart: Isak vs. Gyökeres vs. Top Strikers 2023-2025",
    pcol = colors_line,
    pfcol = colors_fill,
    plty = "solid",
    plwd = 2,
    cglty = "solid",
    cglcol = "gray"
 
)

# Legend
legend(
    x = 0.5,
    y = 1.33,
    legend = rownames(comp_stats[-c(1, 2), ]),
    bty = "n", pch = 20, col = colors_line, cex = 1.2, pt.cex = 3
)
```
The radar chart consists of some importants metrics considered earlier; Goals per 90, Assists per 90, SoT% (Proportions of shots being on target), Goal Conversion% (Proportions of shots ending up as Goal), GCA/90, SCA/90, Succ% (Successful Take-Ons) and Prg/90 (Average of total progression metrics per 90). Each metrics upper bound is based on the datasets maximum value (e.g Gyökeres has the maximum recorded SCA90).

Overall, the radar chart gives a quick summary of the two players strenghts and weaknesses relative to the other players in the data.

## Conclusion
Based on the data at hand, Gyökeres is the better all around striker outperforming Isak in almost all metrics. The results shows that Gyökeres is a proficient striker, competing with the worlds best strikers to this date.

Nontheless, it is important to highlight that these results should be interpreted cautiously. There are several non-measurable factors that can impact the overall data, for example: differences in league strenght, team quality, and tactical systems might influence these metrics.