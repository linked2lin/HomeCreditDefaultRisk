# HomeCreditDefaultRisk (Python) by Lin He
### **How likely are Home Credit's loan owners going to default?**

## Purpose
Home Credit, a consumer finance provider that lends primarily to people with little or no credit history, hosted a competition on Kaggle to help it predict probability of default based on client's data. This github project was aimed at uncovering full data insights (without domain knowledge for feature engineering) and building machine learning models based on the full set of data.

## Dataset
Data is available at https://www.kaggle.com/c/home-credit-default-risk/data  
There are 7 datasets available:
1. application train and test data.
   This is a primary dataset with target (default or not) in the training dataset and has the current credit history of its clients. The primary key in this table is SK_ID_CURR.
2. previous application:
   This dataset has client's previous loan info. It has both the SK_ID_CURR and SK_ID_PREV as unique identifiers.
3. point of sale cash balance:
   It has the monthly balance of client's previous loans. It also has both the SK_ID_CURR and SK_ID_PREV as unique identifiers.
4. credit card balance:
   It has the monthly balance of client's previous credit card loans. The unique identifiers are SK_ID_CURR and SK_ID_PREV.
5. installments payments:
   Past payment data for each installment of previous credits related to loans. The unique identifiers are SK_ID_CURR and SK_ID_PREV.
6. bureau:
   Application data from previous clent's loans from other institutions that were report to Credit Bureau. SK_ID_CURR is the unique identifier.
7. bureau balance:
   Monthly balance of credits in Credit Bureau. SK_ID_CURR is the unique identifier.

## Feature list
Home Credit provided a generous dataset, with over 100 features alone in the primary dataset - application train. That being said, data will be under utilized without joining the primary dataset with other datasets. For example, there are strong reasons to believe a client's previous application and point of sale, however limited history he/she has, will provide some indication about his/her ability to pay on time. This is the key exercise of this project - joining all datasets into one. Thanks to Guillaume Martin, one of the winners of the Kaggle competition for sharing his tips of reducing memory size.

## Steps
All the steps are included in the notebook - define, discover, develop.

**1. Define:**
The problem is defined by Home Credit - predict the probability of default of its clients.

**2. Discover:**
The first step is to load data, which is easy thanks to python's ability to read data in specified chunk size without flooding the memory of a local machine. The second step is the hard one - joining all data frames by SK_ID_CURR and SK_ID_PREV. The first time I did that, I got a memory error. I then went to the route of using dask - a library that enables parallel computing in Python. It didn't work either until I implemented Guillaume Martin's memory compression method. The method, in a nutshell, is to change the default size of each data point. In Python, a numeric column's default size is either int64 or float64, depending on if it is an integer or float. This is a sword of double edges - it greatly conveniences the programmer to code without defining a data type, however, it generates a lot of unnecessary buffers. By understanding a column's max size, we can make data type to be lean, just enough for storage. The reduction ratio was impressive - about 70% reduction of each of the dataset. Once this step is performed, we will have the full dataset to work with and can perform data wrangling and cleaning as usual.

**3. Develop:**
Given there are lots of missing data and many available features (over 300 once all the data frames are joined), we can try random forest as a baseline model given its flexibility of subselecting features and bagging. The baseline model performed fairly well, with area under the curve (auc) of over 72%. FYI, area under the curve for a random guess is 50%. Next we can move on to XGBoost - a sequential learning from the mistake approach to see if this score can be improved. The result is over 4% increase to an auc of nearly 77%. It might not mean much in some scenarios, but in this case, it meant a lot - the ranking could be 1000 higher (out of 7000 teams in the competition) and each percentage point gave Home Credit more confidence of its clients. We are unsure if Home Credit will do something about it, but it could definitely improve budgeting process and business planning.

## Summary
Credit risk in my opion is an under study area for machine learning. That's why it is such a hot topic these days as a model can have substantial margin return on investments. With respect to this project, there are a couple areas for improvement. First, I didn't apply any domain knowledge for feature engineering. It is a well known fact that rate (the ratio of two or more features) can be a more important feature than absolute number in the area of financial risk. Second, there is no hyperparameter tuning such as grid search or random search.
