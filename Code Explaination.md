#Data  
    library(ISLR)
we do not need to load the College data set as it is already present in your R software.

    head(College, 2)
![](https://github.com/anagar20/College-Predictions/blob/master/images/img1.png)

    str(College)
![](https://github.com/anagar20/College-Predictions/blob/master/images/img2.png)

#Scaling the Data
###create vector of column max and min values
    str(College[,2:18])
    maxs = apply(College[,2:18],2,max)
    mins = apply(College[,2:18],2,min)
    scaled_data = as.data.frame(scale(College[,2:18],center = mins, scale = maxs-mins))

    head(scaled_data,2)
![](https://github.com/anagar20/College-Predictions/blob/master/images/img3.png)

    head(College$Private,20)
![](https://github.com/anagar20/College-Predictions/blob/master/images/img4.png)
###convert private column from yes/no to 1/0
    Private = as.numeric(College$Private)-1

    data = cbind(Private,scaled_data)

    head(data$Private,20)
![](https://github.com/anagar20/College-Predictions/blob/master/images/img5.png)

#Splitting the data into Training set and Test set
    library(caTools)
    set.seed(101)

###create split
    split = sample.split(data$Private, SplitRatio = 0.70)
    train = subset(data,split==TRUE)
    test = subset(data,split ==FALSE)


    feats = names(scaled_data)
###concatenate strings
    f = paste(feats,collapse= ' + ')
    f = paste('Private ~', f)
    f = as.formula(f)
    f
![](https://github.com/anagar20/College-Predictions/blob/master/images/img6.png)

#Making Predictions
    library(neuralnet)
    nn = neuralnet(f, data, hidden = c(8,10),learningrate=0.01,linear.output = FALSE)

    plot(nn)
![](https://github.com/anagar20/College-Predictions/blob/master/images/img7.png)  

    pred = compute(nn,test[2:18])
    print(head(pred$net.result))

    pred$net.result = sapply(pred$net.result, round, digits=0)

    conf = table(test$Private,pred$net.result)

    conf
![](https://github.com/anagar20/College-Predictions/blob/master/images/img8.png)

    acc=sum(diag(conf))/sum(conf)
    acc
0.9871244635
