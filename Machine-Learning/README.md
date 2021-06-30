# Machine Learning Exercise

## Readings
**Introduction to Statistical Learning**. Available free: 
https://statlearning.com
- 2.1-2.2 (Background)
- 3.1-3.2 (Linear regression)
- 4.1-4.3 (Classification/logistic regression)
- 5.1 (Cross-Validation)

## Exercise
Work through Sherri's example of training three different kinds models/machine
learning methods

First generate and summarize some data with a specific functional relationship

```r
################
#Simulated Data#
################

# Set random seed so that data reproduces the same each time it is run
set.seed(27)

# Sample Size
n <- 1000

# Generate data
df2 <- data.frame(W1=runif(n, min=0.5, max=1),
		  W2=runif(n, min=0, max=1))
df2 <- transform(df2, #add Y
		 Y=rbinom(n, 1, 1/(1 + exp(-(-3*W1 - 2*W2 + 2)))))
 
# Summary of the variables in dataset ‘df2'
summary(df2)
head(df2)
```

Next train a good old-fashioned logistic regression

```r
# Run a multivariable logistic regression
logistic1 <- glm(Y~W1+W2, data=df2, family="binomial")

# Predicted probabilities from the regression
pylogistic1 <- predict(logistic1, type="response")
```

Train and visualize a classification tree

```r
# Run a classification tree
library(rpart)	# if you do not have this library install it with 
		# the line `install.packages(“rpart”)` and then run
		# library command

rpart1 <- rpart(Y ~ W1 + W2, data=df2)
 
# Plot the tree
plot(rpart1)
text(rpart1, use.n=TRUE, cex=0.5)

# Predicted probabilities from the tree
pyrpart1 <- predict(rpart1)
```

Next train an 
<img src="https://render.githubusercontent.com/render/math?math=L_1"> (Lasso) 
penalized linear regression

```r
# Run a lasso penalized regression

library(glmnet)	# install with line install.packages(“glmnet”) if needed
		# then run library command
Y <- df2$Y
W <- matrix(c(df2$W1,df2$W2), nrow=n, ncol=2)

lasso1 <- glmnet(W,Y, family="binomial") # default is alpha=1, lasso
```

Use cross-validation to select 
<img src="https://render.githubusercontent.com/render/math?math=\lambda"> value

```r
# Cross-validation is used to select the lambda value
cv.lasso1     <- cv.glmnet(W,Y)
select_lambda <- cv.lasso1$lambda.min

# Predicted probabilities from the lasso with optimal lambda
pylasso1 <- predict(cv.lasso1, newx=W)
```

Next, it's your turn! Get creative and compare the 3 models; feel to use plots,
tables, or anything you think would be helpful. You can add them to the 
[Google Doc](https://docs.google.com/document/d/1xtwR8NZBcPXmNaaoIr1Tc_w7BJcXhnAx_XFTBEipOdw/edit?usp=sharing)
or you can try your hand at using [git](https://git-scm.com/), GitHub, and
[Markdown](https://guides.github.com/features/mastering-markdown/) to edit this
file.

## Results
