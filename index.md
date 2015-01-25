---
title       : AFORE Savings Calculator
subtitle    : A Shiny app for estimating savings yields in Mexico
author      : Carlos Martinez-Torteya
job         : 
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : [mathjax]            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
---

## AFOREs

* AFOREs (Retirement Funds Administrators), are the centerpiece of Mexico's Retirement Savings System
* They are private companies (typically part of larger financial or insurance institutions) that offer heavily regulated accounts where workers invest their retirement fund.
* Each one can choose the AFORE they want to join, but it is mandatory to have one.
* The retirement funds can only be accesed upon retirement. AFOREs invest the money both in the Mexican Stock Exchange exclusively
* Given that the money will only be accessed upon retirment, which may be dramatically closer/further away depending on worker age, AFOREs are broken up into 4 age brackets (18-36, 36-45, 46-55, 56-65). The closest one is to retirement age (65 years), the less risk the Siefore is allowed to take on.
* Each AFORE company chooses their investment strategy (within the regulations)
* Very different yields depending on company and worker age!

---

## The Data

* The data used in this app was taken directly from CONSAR (National Comission for Retirement Savings System), who publishes the net yields for all the Siefores periodically
* Since this is a proof-of-concept app more than a ready-for-consumption one, we only used the latest data (Date Pulled: Jan/25/2015), not the full historical data


```r
AforeData <- read.csv("AforeData.csv")
head(AforeData)
```

```
##     AFORE Rend.Neto.SB4 Rend.Neto.SB3 Rend.Neto.SB2 Rend.Neto.SB1
## 1  Azteca        0.0851        0.0815        0.0718        0.0594
## 2 Banamex        0.1122        0.0990        0.0894        0.0703
## 3 Banorte        0.1011        0.0929        0.0846        0.0664
## 4  Coppel        0.0785        0.0759        0.0698        0.0543
## 5     GNP        0.1106        0.0969        0.0880        0.0727
## 6 Inbursa        0.0620        0.0572        0.0504        0.0487
```

---

## The UI

* The only information asked for is: Age and AFORE (company)
* The UI displays the values entered as well as the yield-upon-retirement (per MX Peso)

Some sample code:
```
sidebarPanel(
               numericInput('age', 'Age (years)', 40, min = 18, max = 64, step = 1),
               selectInput('afore', "Choose your AFORE:", choices = c('Azteca'=1 [...]
               submitButton('Submit')
          ), [...]
mainPanel(
[...]
h4('For each MXN (MX Peso) in your AFORE account today, you will have: '),
               verbatimTextOutput("savings"),
               [...]
)
```

---

## The Server

* Calculation of net yield (per Peso invested today) depends on worker age and AFORE

Sample Code:
```
AforeData <- read.csv("AforeData.csv")
AgeBrackets <- c(36, 45, 59, 65)

Savings <- function(age,afore) {
     if (age<AgeBrackets[1]) {
          (1+AforeData[afore,2])^(AgeBrackets[1]-age)
          +(1+AforeData[afore,3])^(AgeBrackets[2]-AgeBrackets[1])[...]
     }
     else if(age<AgeBrackets[2]){
          (1+AforeData[afore,3])^(AgeBrackets[2]-age)
          +(1+AforeData[afore,4])^(AgeBrackets[3]-AgeBrackets[2])+[...]
```
