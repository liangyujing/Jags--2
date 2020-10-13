```
#setwd(<<insert your working directory>>)
library(runjags)
library(coda)

## 1.The model
##  dbeta
## this is a character string
modelString = "
model {
## data
k ~ dbin( theta , n) #likelihood;

## prior
theta ~ dbeta( A , B )
A<-1 
B<-1
}
"
## which is written to a text file to your working directory
writeLines( modelString , con="rate_simple_new.txt" )

## and can then be used when calling JAGS
out <- run.jags( model="rate_simple_new.txt" , monitor=parameters ,
                 data=data ,  inits=myinits , n.chains=nchains ,  adapt=nadapt ,
                 burnin=nburn ,  sample=niter )

## dunif
## restore data list to original (no A or B)
data = list( 
  k = k,
  n = n )

modelString = "

model{
k ~ dbin(theta,n)
theta ~ dunif(0,1)
}

"
writeLines( modelString , con="rate_simple_new.txt" )

## 2.Input coding (R)

data <-  c(
  k = k,
  n = n 
)

##Another way involves setting k=0 and n=0 in R. 
## changing data
data <- list(
  k=0,
  n=0
)

## Get additional information out of the modelv
## resetting initial values
myinits <- list(
  list(theta = 0.1), #chain 1 starting value
  list(theta = 0.9) #chain 2 starting value
)

out <- run.jags( model="rate_simple.txt" , monitor=parameters ,
                 data=data ,  inits=myinits , n.chains=nchains ,  adapt=nadapt ,
                 burnin=nburn ,  sample=niter )


##Both solutions discussed above allow you to sample from the prior, 
##but you can’t sample from the posterior anymore.


## Sample from both the posterior and the prior in one run:

modelString <- "
model{
# Observed Data
k ~ dbin(theta,n)
# Prior on Rate Theta
theta ~ dbeta(1,1)
# Prior on Rate Theta
thetaprior ~ dbeta(1,1)
}
"
writeLines(modelString,"rate_simple_new.txt")
```








