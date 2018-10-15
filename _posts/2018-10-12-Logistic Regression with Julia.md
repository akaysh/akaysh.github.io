

This post is the next tutorial in the series of ML with Julia. Today we are going to see how to use logistic regression for linear and non-linear classification, how to do feature mapping, and how and where to use regularization.

```julia
using DataFrames, CSV
using Plots, StatPlots
pyplot();
```

## Most important thing first! Where is my data?

#### This is a sample data stored in a CSV file ex1data2.txt

You can get the data [here](https://github.com/akaysh/MLWithJulia/blob/master/Example_two/ex2data1.txt).


```julia
## Here your task is to build a classification model that estimates an applicant’s
## probability of admission based the scores from those two exams.
data = CSV.read("ex2data1.txt");

## Renaming columns of the dataset
## This is how we can rename columns of a DataFrame in Julia v1.0
newnames = ["Score_1", "Score_2","Result"];
names!(data, Symbol.(newnames));
```

Want to see how the data is like? Sure.


```julia
head(data)
```




<table class="data-frame"><thead><tr><th></th><th>Score_1</th><th>Score_2</th><th>Result</th></tr></thead><tbody><tr><th>1</th><td>30.2867</td><td>43.895</td><td>0</td></tr><tr><th>2</th><td>35.8474</td><td>72.9022</td><td>0</td></tr><tr><th>3</th><td>60.1826</td><td>86.3086</td><td>1</td></tr><tr><th>4</th><td>79.0327</td><td>75.3444</td><td>1</td></tr><tr><th>5</th><td>45.0833</td><td>56.3164</td><td>0</td></tr><tr><th>6</th><td>61.1067</td><td>96.5114</td><td>1</td></tr></tbody></table>



Visualize, always if you can.


```julia
@df data scatter(:Score_1,:Score_2, zcolor= :Result, xaxis = "Score in Test 1", yaxis="Score in Test 2", lab="Admit")
```



![LR_Scatter2](/assets/images/Logistic%20Regression%20with%20Julia_7_0.png "Data Visualization")




Here 1 (yellow color) means admitted and 0 (blue color means rejected)

**We are going to use an awesome, simple to use and extensively customizable package Flux.jl which is entirely built in Julia**


```julia
using Flux
using Statistics
using Flux.Tracker, Statistics
using Flux.NNlib
using Flux: throttle, normalise
using Flux: binarycrossentropy
using Base.Iterators: repeated
using Flux: @epochs
```

We need to build a **simple perceptron** to classify our data in admits and rejects. So follow the steps for building a perceptron.


```julia
## Splitting the features i.e. scores in tests here (X) and the categories i.e. admit or reject here (Y)
X = convert(Array,data[:,[:Score_1,:Score_2]]); #features
y = convert(Array,data[:Result]);  #Y-values
## To see number of training samples
num_tr_ex = length(y); #99
```


```julia
## Normalizing the features
X = (X .- mean(X, dims = [99,1])) ./ std(X, dims = [99,1]);
```


```julia
size(X), size(y)
```




    ((99, 2), (99,))




```julia
## Building a simple model for Logistic regression in Flux
## param() lets the Flux know about the MLP parameters to train
W = param(zeros(2))
b = param([0.])

## Here W is a matrix of weights for the MLP and b is the bias added with the weights.
## So our MLP is having two input nodes ( as we have 2 features which are Score 1 and Score 2) followed by 1 hidden
## layer with 4 nodes and then 1 output node ( as we have only 2 categories).


## This is our prediction function (basically the MLP)
## Here the output is it sigmoid of the perceptron output. The sigmoid activation function lets us classify the output
## into categories for binary logistic regression
## σ(x) = 1 / (1 + exp(-x))

predict(x) = NNlib.σ.(x*W .+ b)
```



This is the loss function for binary classification ( logistic regression ). We are using Flux's built in binary crossentropy function to calculate the loss. Loss is basically the cost we need to minimize to fit our model on the data.



```julia
loss(x, y) = sum(binarycrossentropy.(predict(x), y))/num_tr_ex
```





```julia
par = Params([W, b])
## Stocastic Gradient Descent with learning rate 0.01
opt = SGD(par, 0.1; decay = 0)
evalcb() = @show(loss(X, y))
```




You can read more about logistic regression [here](https://ml-cheatsheet.readthedocs.io/en/latest/logistic_regression.html). It will give you an idea about the why's regarding the activation function, cost function etc.


```julia
data = repeated((X, y), 200)
Flux.train!(loss, data, opt, cb = evalcb)
```

    loss(X, y) = 0.678119310455579 (tracked)
    loss(X, y) = 0.6638170741749312 (tracked)
    loss(X, y) = 0.6502040573326028 (tracked)
    loss(X, y) = 0.6372447731091508 (tracked)
    loss(X, y) = 0.6249048297043459 (tracked)
    loss(X, y) = 0.6131510590538459 (tracked)
    loss(X, y) = 0.47748629204559834 (tracked)
    loss(X, y) = 0.47213993531238796 (tracked)
    loss(X, y) = 0.4669936354514276 (tracked)
    loss(X, y) = 0.4620370159119419 (tracked)
    loss(X, y) = 0.45726033920258796 (tracked)
    loss(X, y) = 0.45265446503779516 (tracked)
    loss(X, y) = 0.3912379072073174 (tracked)
    loss(X, y) = 0.38870341298459943 (tracked)
    loss(X, y) = 0.3862368052312892 (tracked)
    loss(X, y) = 0.3838353888949361 (tracked)
    loss(X, y) = 0.38149660614239184 (tracked)
    loss(X, y) = 0.37921802807241384 (tracked)
    loss(X, y) = 0.3541626352017241 (tracked)
    loss(X, y) = 0.35252851719016054 (tracked)
    loss(X, y) = 0.35092942841606445 (tracked)
    loss(X, y) = 0.34936423215472107 (tracked)
    loss(X, y) = 0.3478318400781661 (tracked)
    loss(X, y) = 0.3281649903203591 (tracked)
    loss(X, y) = 0.3270407438103603 (tracked)
    loss(X, y) = 0.32593626026315475 (tracked)
    loss(X, y) = 0.32485100792481525 (tracked)
    loss(X, y) = 0.323784474065412 (tracked)
    loss(X, y) = 0.3227361641409067 (tracked)
    loss(X, y) = 0.321705600998443 (tracked)
    loss(X, y) = 0.30973355535926445 (tracked)
    loss(X, y) = 0.30890950378184695 (tracked)
    loss(X, y) = 0.30809769528140496 (tracked)
    loss(X, y) = 0.30729785004359783 (tracked)
    loss(X, y) = 0.3065096968278599 (tracked)
    loss(X, y) = 0.2985417821305888 (tracked)
    loss(X, y) = 0.2978754927423481 (tracked)
    loss(X, y) = 0.29721801906808043 (tracked)
    loss(X, y) = 0.296569181427643 (tracked)
    loss(X, y) = 0.2959288050728036 (tracked)
    loss(X, y) = 0.2893975944673467 (tracked)
    loss(X, y) = 0.28884670714309096 (tracked)
    loss(X, y) = 0.2827160344571201 (tracked)
    loss(X, y) = 0.2765226767811106 (tracked)
    loss(X, y) = 0.2761141887543308 (tracked)
    loss(X, y) = 0.27570984193328196 (tracked)
    loss(X, y) = 0.27530957183165594 (tracked)
    loss(X, y) = 0.2690723112722448 (tracked)
    loss(X, y) = 0.2687357889029776 (tracked)
    loss(X, y) = 0.26462586975221564 (tracked)
    loss(X, y) = 0.2643287512151905 (tracked)
    loss(X, y) = 0.26403417267632406 (tracked)
    loss(X, y) = 0.26374210099355466 (tracked)
    loss(X, y) = 0.26345250361279404 (tracked)
    loss(X, y) = 0.26316534855477197 (tracked)
    loss(X, y) = 0.2628806044022352 (tracked)
    loss(X, y) = 0.26259824028748896 (tracked)

  Skipped printing some losses in between to not make it too long.


Lets Visualize the prediction with the decision boundary using a contour plot.


```julia
function contour_func_1(x,y)
    predict(hcat(x,y))
end
```




```julia
xs = collect(range(-2,stop=2,length=25))
ys = collect(range(-2,stop=2,length=25))
Z = [Tracker.data(contour_func_1(x,y)[1]) for x=xs, y=ys];
contour(xs,ys,Z, levels=1)
scatter!(X[:,1],X[:,2],zcolor = y, xaxis="Test1", yaxis="Test2")
```




![LR_Scatter_predict](/assets/images/Logistic%20Regression%20with%20Julia_23_0.png "Prediction Visualization")



To calculate the accuracy of our model we check what percentage of labels are correctly predicted by our model.


```julia
p = predict(X) .> 0.5
sum(p .== y) / length(y)
```




    0.898989898989899



This shows that our model predicted 89.89 % of labels correctly. And below is an example to show the admission probability of a student with marks 45 and 85 in the tests.


```julia
predict([45 85])
```




    Tracked 1-element Array{Float64,1}:
     1.0



# Non-Linear Logistic regression with feature mapping

**What to do if we need to classify data which is not linearly seperable?**

We can either use non linear classifiers like K-Nearest Neighbors, Support Vector Machines or Random Forests. We can also use a simple perceptron to classify this kind of data but we need to do an additional step called feature mapping. We will take up other classifying methods in coming tutorials.

**What is feature mapping and when should we use it?**


When we have data which cannot be separated into classes with a straight line we use feature mapping and create more features from each data point. A logistic regression classifier trained on this higher-dimension feature vector will have a more complex decision boundary and will appear nonlinear when drawn in our 2-dimensional plot.

**What is regularized logistic regression and when should we use it?**

While the feature mapping allows us to build a more expressive classifier, it also more susceptible to overfitting. In the next parts of the exercise, we will implement regularized logistic regression to fit the data and also see how regularization can help combat the overfitting problem.

Lets get our data!

You can get the data [here](https://github.com/akaysh/MLWithJulia/blob/master/Example_two/ex2data2.txt).



```julia
# Suppose you are the product manager of the factory and you
# have the test results for some microchips on two different tests.
# From these two tests, you would like to determine whether the microchips
# should be accepted or rejected. To help you make the decision,
# you have a dataset of test results on past microchips, from which you can build a logistic regression model.
data = CSV.read("ex2data2.txt");

## Renaming columns of the dataset
## This is how we can rename columns of a DataFrame in Julia v1.0
newnames = ["Test_1", "Test_2","Result"];
names!(data, Symbol.(newnames));
```


```julia
head(data)
```




<table class="data-frame"><thead><tr><th></th><th>Test_1</th><th>Test_2</th><th>Result</th></tr></thead><tbody><tr><th>1</th><td>-0.092742</td><td>0.68494</td><td>1</td></tr><tr><th>2</th><td>-0.21371</td><td>0.69225</td><td>1</td></tr><tr><th>3</th><td>-0.375</td><td>0.50219</td><td>1</td></tr><tr><th>4</th><td>-0.51325</td><td>0.46564</td><td>1</td></tr><tr><th>5</th><td>-0.52477</td><td>0.2098</td><td>1</td></tr><tr><th>6</th><td>-0.39804</td><td>0.034357</td><td>1</td></tr></tbody></table>



Visualizing the data


```julia
@df data scatter(:Test_1,:Test_2, zcolor= :Result, xaxis = "Score in Test 1", yaxis="Score in Test 2", lab="Admit")
```




![LR_Scatter_2](/assets/images/Logistic%20Regression%20with%20Julia_34_0.png "Data 2 Visualization")



Here 1 (yellow color) means admitted and 0 (blue color means rejected)


```julia
## Splitting the features i.e. scores in tests here (X) and the categories i.e. admit or reject here (Y)
X = convert(Array,data[:,[:Test_1,:Test_2]]); #features
y = convert(Array,data[:Result]);  #Y-values
## To see number of training samples
num_tr_ex = length(y); #117
```

We need to create a function to map existing features to new ones. We have two features say x1 and x2, so we will map the features into all polynomial terms of x1 and x2 up to the sixth power.
i.e. [x1,x2,x1^2,x1x2,x2^2......x2^6]


```julia
function map_features(X1,X2)
    degree = 6
    out = ones(size(X1[:,1]))
    for i=1:6
        for j=0:i
            out = hcat(out,(X1.^(i-j)).*(X2.^j))
        end
    end
    return out
end
```




```julia
X1 = X[:,1]
X2 = X[:,2]
X = map_features(X1,X2);
```

So we have created 28 features for every data point in our dataset.


```julia
size(X)
```




    (117, 28)




```julia
## Building a simple model for Logistic regression in Flux
## param() lets the Flux know about the MLP parameters to train
W = param(zeros(28))
b = param([0.])
## Here W is a matrix of weights for the MLP and b is the bias added with the weights.
## So our MLP is having two input nodes ( as we have 2 features which are Score 1 and Score 2) followed by 1 hidden
## layer with 4 nodes and then 1 output node ( as we have only 2 categories).
predict(x) = NNlib.σ.(x*W .+ b)
```



Here the loss needs a regularization term to counter overfitting. We can regularise this by taking the (L2) norm of the parameters W and b.


```julia
using LinearAlgebra: norm
par = Params([W, b])
## Adding the regularization parameter to the binary cross entropy sum to prevent overfitting
loss(x, y) = sum(binarycrossentropy.(predict(x), y))/num_tr_ex + sum(norm, par)/num_tr_ex
```




```julia
## Adam optimiser
# opt = ADAM(params(m); β1 = 0.9, β2 = 0.999, ϵ = 1e-08, decay = 0)
## Stocastic Gradient Descent with learning rate 0.01
opt = SGD(par, 0.1; decay = 0)
evalcb() = @show(loss(X, y))
```




```julia
data = repeated((X, y), 200)
@epochs 10 Flux.train!(loss, data, opt, cb = throttle(evalcb, 50));
```

    Info: Epoch 1


    loss(X, y) = 0.6919000634010879 (tracked)
    loss(X, y) = 0.6016223487922494 (tracked)


    Info: Epoch 2


    loss(X, y) = 0.5612456293649328 (tracked)


    Info: Epoch 3


    loss(X, y) = 0.5353716703630391 (tracked)


    Info: Epoch 4


    loss(X, y) = 0.5173476711539267 (tracked)
    loss(X, y) = 0.5041364039747485 (tracked)


    Info: Epoch 6


    loss(X, y) = 0.4940871992819601 (tracked)


    Info: Epoch 7


    loss(X, y) = 0.48622209343844913 (tracked)


    Info: Epoch 8


    loss(X, y) = 0.47992452156180726 (tracked)
    loss(X, y) = 0.47478648982109684 (tracked)


    Info: Epoch 9
    Info: Epoch 10



```julia
#Validating the prediction of the samples on training set
p = predict(X) .>= 0.5;
sum(p .== y) / length(y)
```




    0.811965811965812



It shows 81% accuracy in classifying our training samples. (slightly less as we used regularization to prevent overfitting)

**To plot the decision boundary**


```julia
function map_feature_(X1,X2)
    degree = 6
    out = [1]
    for i=1:6
        for j=0:i
            out = hcat(out,(X1^(i-j)).*(X2^j))
        end
    end
    return out
end
```



```julia
function contour_func(x,y)
    predict(map_feature_(x,y))
end
```





```julia
xs = collect(range(-2,stop=2,length=25))
ys = collect(range(-2,stop=2,length=25))
Z = [Tracker.data(contour_func(x,y)[1]) for x=xs, y=ys];
```

Z will be a 25X25 array holding the contour values in xs x ys mesh.


```julia
contour(xs,ys,Z, levels=1)
scatter!(X1,X2,zcolor = y, xaxis="Score1", yaxis="Score2")
```




![LR_Scatter_predict](/assets/images/Logistic%20Regression%20with%20Julia_54_0.png "Prediction Visualization")



This contour plot shows the decision boundary of our prediction with threshold 0.5
