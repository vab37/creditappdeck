---
title       : Predicting Credit Worthiness Of Customers
subtitle    : A Slidify App
author      : Vaibhav Agarwal
job         : 
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : []            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
knit        : slidify::knit2slides
---

## About the app

The [Credit Worthiness Prediction App](https://vaibhavagarwal.shinyapps.io/creditapp/) takes different customer attributes and predicts the credit worthiness

Customer Attributes it uses are:

- Customer Age in Years
- Whether Foreign Worker (Yes/No)
- Whether owns Real Estate (Yes/No)
- Whether howsing is owned (Yes/No)
- Whether critical account/ other credits existing (not at this bank)(Yes/No)

Prediction Output on credit worthiness
- Good
- Bad


---

## Methodology

The app uses logistic regression model trained over GermanCredit data set available in the caret package. 


```r
mod_fit <- train(Class ~ Age + ForeignWorker + Property.RealEstate + Housing.Own + 
    CreditHistory.Critical, data = training, method = "glm", family = "binomial")
```
The predicted values are returned to the app using a prediction function

```r
predCredit <- function(inpAge, inpForeignWorker, inpProperty.RealEstate, inpHousing.Own, 
    inpCreditHistory.Critical) {
    pred = predict(mod_fit, newdata = data.frame(Age = inpAge, ForeignWorker = inpForeignWorker, 
        Property.RealEstate = inpProperty.RealEstate, Housing.Own = inpHousing.Own, 
        CreditHistory.Critical = inpCreditHistory.Critical))
    as.character(pred)
}
```

---

## UI Features (ui.R)

The UI uses a mix of slider and radio buttons to accept different inputs 


```r
ui <- fluidPage(
  ...
    
    sliderInput(inputId = "iAge",label = "Customer Age in Years", 
                value= 40, min = 18, max = 80),
    radioButtons("iForeignWorker", "Whether Foreign Worker (Yes/No)",
               c("No" = "0",
                 "Yes" = "1"
                 ),selected="1"),
    radioButtons("iProperty.RealEstate", "Whether owns Real Estate (Yes/No)",
               c("No" = "0",
                 "Yes" = "1"
               )),
 ...
```

---

## Server Features (server.R)

The UI uses a mix of slider and radio buttons to accept different inputs 


```r
source("predfunc.R")
library(shiny)
server <- function(input, output) {
    output$prediction <- renderPrint({
        predCredit(input$iAge, as.numeric(as.character(input$iForeignWorker)), 
            as.numeric(as.character(input$iProperty.RealEstate)), as.numeric(as.character(input$iHousing.Own)), 
            as.numeric(as.character(input$iCreditHistory.Critical)))
    })
}
```

---

## App Benefit

- The app can provide a simple interface for credit assessment. 
- The various predictors can be tweeked to understand the impact on overall credit worthiness
- The underlying model can learn from the manual credit worthiness assessment history and help standardize decisions across organization with the help of the app
