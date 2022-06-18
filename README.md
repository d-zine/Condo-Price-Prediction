<h2 align="center"> Condo Price Prediction</h2>

<h3 align="center"> Identifying Undervalued Condos in Northeastern US Cities</h3>

<h3 align="center"> Executive Summary</h3>
With so many condos to choose from, how can buyers quickly identify fair and bargain value condos? 
The purpose of this capstone project is to answer the following question: despite being a seller’s market
(as of June 2022), can we use multiple linear regression to find undervalued condos in the Northeastern 
cities?

<h3 align="center"> Key Findings</h3>

The answer is: Sort of.

I originally wanted to tackle this question solely by creating a model to predict condo prices as 
accurately as possible. To do this, I first built a multi-city multiple linear regression model. I predicted 
home prices to be within $363K or 59% of the actual price on average. The accuracy was not great, so I 
trained a Gradient Boosting model to predict home price, which reduced the error to within $243K and 
24% of the actual price on average.

These models found the following features to be predictive of condo price: city, square footage, 
bathrooms, bedrooms, HOA fee, attached garage, walk score, transit score, commute time, median age, 
marriage status, median household income greater than 150K, home ownership, and year-built post 
1980.

Recognizing that city plays a bigger role in accurately predicting condo prices and my multi-city model is 
trained on 75% NYC data, I built another multiple linear regression using Philadelphia condos. This 
model allowed me to predict condo price within $246K and 44% of actual price on average. I also trained 
a Gradient Boosting model to predict home price, which reduced the error to $135K and 21% of actual 
price on average. Gradient Boosting outperformed regression in the multi-city and Philadelphia model
because it accounts for non-linear relationships. The Philadelphia specific model included a smaller set 
of predictors to the multi-city model with the addition of neighborhood.

After training these models, I learned there is more to the story. From this process, I realized it’s tough
to price condos because some factors are hard to label. For example, neighborhood safety affects condo 
price, but in the United States, crime is an ever-changing situation with recent gun violence activity in 
previously safe locations. It’s also much easier to assess [property condition](https://www.researchgate.net/publication/328393238_Estimating_property_condition_effect_on_residential_property_value_Evidence_from_U_S_home_sales_data)
and desirable [architectural styles](https://thearchitectsdiary.com/how-architecture-influences-your-homes-selling-price/)
by looking at pictures than to label them. 

Other issues that make it challenging to accurately model condo prices include data quality (missing or 
messy data), how to encode eccentric property characteristics (desirable water views, penthouse/condo 
hotels, owned by famous figures), data availability (there’s no simple way to pull crime/neighborhood 
quality data), and determining the best algorithm to use. Sentiment reviews are important but are 
complicated to integrate into a model since reviews can come from so many places.
Even Zillow could not profitably automate home buying based on their own model. Zillow’s overreliance 
on their model to automate this process failed: Zillow cut 25% of their workforce and wrote off $500 
million this year.

For these reasons, I ultimately created a decision support tool that combines model predictions, a 
[PowerBI dashboard](https://github.com/d-zine/Condo-Price-Prediction/tree/main/PowerBI%20Dashboard/),
and in-depth research on promising condos that appear undervalued, which 
accomplishes the original intent of this project.


<h3 align="center"> Introduction </h3>

I acquired property listing data for 13,920 condos in 16 Northeastern cities.

<p align="center">
<img src="Images/Choropleth Map.png" width=850>
</p>


### Why the Northeast?

Elasticity is the change in demand given change in price. The Northeast region exhibits [price 
inelasticity](https://voxeu.org/article/declining-elasticity-us-housing-supply/) (denoted in red below), 
meaning that supply cannot keep up with demand and thereare a lack of substitutes. Northeastern states 
are also more densely populated and have bodies of water that limit the land available for construction, 
so are more comparable than regions with 
more open space.

<p align="center">
<img src="Images/Condos in NE Cities exhibit Price Inelasticity.png" >
</p>

<h3 align="center"> Analytical method </h3>

### Step 1: Data Collection and Preprocessing

The property dataset contains 13,920 condo records from Rapid API’s Zillow.com API, where I pulled 
from Extended Search, Property Details, and Walk and Transit Score. 
Other important features, such as population density, median household income, and commute time,
were obtained from data aggregator simplemaps.com.
I cleaned and merged the data sets, imputed missing values, and removed condos with extremely high 
prices (>$5.995M).


### Step 2: Merge Data to Postgres to PowerBI Dashboard

I appended all condo records into one dataset and overlaid local Philly shootings to visualize crimes near 
condos. This merged dataset was pushed to PostgreSQL, PostgreSQL was connected to PowerBI. Both 
datasets have lat-long coordinates that allow PowerBI to plot them on a map. This visualization helps
gauge the safety of a neighborhood where condo prices are listed on sale, displays geographic patterns 
in condo pricing, and provides further context to prospective homebuyers.

<p align="center">
<img src="Images/PowerBI Screenshot.png">
</p>

### Step 3: Exploratory Data Analysis (EDA)

As part of my quest to identify undervalued condos, I wanted to understand which factors impact condo 
price, based on a detailed literature review and verifying those trends in my data set.

1. Literature review suggests price is higher for condos with a sought-after water view. After 
inspecting the data, I observed that the median price of a condo with a water view is 50% 
higher! Not all water views are created equal, however. The locations where my model 
underestimated the value of a water view – i.e., the most desirable water views – were in 
Boston Waterfront, Hudson Yards, and Baltimore Harbor.

<p align="left">
<img src="Images/Water View Barchart.png">
</p>

2. Literature review suggests the more bedrooms and bathrooms, the higher the condo price. After 
inspecting the data, this is true, and in fact bathrooms have a stronger influence on price than 
bedrooms. I found that the most common (modal) condo has two bedrooms and one bathroom.

<p align="left">
<img src="Images/Bathrooms and Bedrooms Barchart.png">
</p>

3. Literature review suggests that an attached garage increases condo’s price. The data supports 
this. The median price of a condo with an attached garage is $775k, compared to $360k among 
condos without an attached garage. In other words, more than double the price!

<p align="left">
<img src="Images/Attached Garage Barchart.png">
</p>

4. Literature review suggests the higher the elementary school score, the higher the condo price.
Better elementary schools help students qualify for magnet highs schools that prep student for a 
better college experience. This environment will help students secure higher paying jobs in the 
future.

  My data set does not support this theory. Condos in areas with an elementary school score of 10 
  (the highest rating) have the highest median condo price. However, there is not a strong 
  relationship between elementary school quality and condo price for elementary schools with 
  scores of 1-9. Condos with an elementary school score of 1 have a higher median price, but 
  represent only 1% of records, so may not be representative. In this case, there may be some 
  expensive condos next to poorly rated public schools where students are sent to private schools 
  instead.

<p align="left">
<img src="Images/Elementary School Score Barchart.png" width=400>
</p>

  I am initially surprised that the correlation coefficient is low (r=.16). One potential explanation is 
  that condo buyer motivations are different. Condo buyers with or plan to have children place 
  value in elementary school score, while those without may not, so that relationship gets
  obscured.

5. Literature review suggests that both historic homes and new builds have higher prices. After 
inspecting the data, year built has a U-shaped relationship relative to price, which is consistent 
with the prior research. Newer builds tend to be larger, have modern aesthetics, and do not 
require as much maintenance or renovation, while historic homes pre-1900s are treasured
because of its rich history, architecture, and charm. They are likely in dense, central locations 
that are attractive for tourism and can offer tax benefits.

<p align="left">
<img src="Images/Year Built Barchart.png" >
</p>

6. Sun score is a composite score that measures your property’s solar power potential to install 
solar panels. While research suggests installing solar panels increases property value, after 
inspecting the data, sun score appears to have no relationship with price. This could be because
many condos – especially in NYC – are in apartment buildings, where individual condo owners 
cannot choose to install solar panels regardless of the building’s solar potential.

7. Literature review suggests the better the walk score, the higher the condo price. Manhattan is 
an anomaly since it is a very walkable city independent of condo pricing and very expensive. For 
that reason, I separated Manhattan from the rest of the data to unearth other relationships 
between walk score and price. Even after separating Manhattan results, walk score still follows 
an exponential relationship. Excluding Manhattan, 75% of condo units in my data set are in very 
walkable areas. Walkability likely increases condo prices because amenities and transit are 
easily accessible, which is desirable.

<p align="left">
<img src="Images/Walk Score Barchart.png" >
</p>

8. Literature review suggests the better the transit score, the higher the property price. After 
running a correlation matrix, it confirms transit score is highly correlated with walk score
(correlation coefficient = 0.75) and have higher prices. Locations with a higher walk score 
typically have good public transit due to thoughtful urban planning.

<p align="left">
<img src="Images/Transit Score Barchart.png" width=710>
</p>

9. Based on literature review, the higher the HOA fee, the lower the condo price. After inspecting 
the data and dropping null HOA fees, it appears monthly HOA fees is weakly positively 
correlated with price (correlation coefficient = 0.07) and not negatively correlated. Monthly HOA 
fees could be calculated as a percentage of condo fees and not an offset, which would explain 
the positive correlation.

10. Literature review suggests the lower commute times, the higher the property value. I binned 
commute time into 3 quantiles and used the mean price instead. After inspecting the data, this 
is consistent with literature review, that commute times are negatively correlated with condo 
price (correlation coefficient=-.37). In general, people are willing to pay for a unit that shortens 
their commute time, so they have more free time for other activities.

<p align="left">
<img src="Images/Commute Time Barchart.png" width=350>
</p>

### Step 4: Modeling Building

Predictive modeling is the process of applying a statistical model or an algorithm to data to predict 
future observations.

It is important to use in this analysis because traditional house price prediction is based on cost and sale 
price comparison and lacks a standard process of evaluation and certification. A predictive model solves 
for a lack of objective criteria, an information gap, and helps uncover the pricing inefficiencies in real 
estate.

Multiple linear regression is a machine learning technique used to predict the outcome of a continuous 
variable based on the value of two or more variables. With multiple linear regression, it is easier to 
interpret and quantify how much each numerical and categorical feature affects the condo price or 
predict a future condo price with new data.

Gradient-boosted decision tree is a machine learning technique that accounts for non-linear 
relationships between factors but is less interpretable. It trains a sequence of decision trees, with each 
successive tree attempting to minimize the error of the previous tree, which can improve predictive 
accuracy.

I explored these two algorithms because multiple linear regression resulted in a high error, so I tried a 
gradient-boosted decision tree to improve the model fit.

Specific error metrics, such as MAE, MAPE, and RMSE, are considered to evaluate the performance of 
the two algorithms.

• The Mean Absolute Error (MAE) is the average of the absolute error in dollars and is most 
interpretable. It measures predictive accuracy. From these results, it means, on average, our 
model predictions are off by ± the MAE value.

• The Mean Absolute Percentage Error (MAPE) measures how far the model’s predictions are off 
on average expressed as a percentage. In other words, the average percentage difference 
between the predicted value and the actual value is the MAPE.

• The Root mean squared error (RMSE) measures the average magnitude of the error in dollars,
and compared to the MAE, penalizes large errors. A high RMSE is not necessarily a bad thing,
because large errors can help pinpoint undervalued condos where predicted price is greater 
than actual price, so RMSE will not be deeply assessed to measure model performance.


### Feature Importance

Literature review suggests bathrooms, bedrooms, square footage, expensive cities, attached garages, 
sought-after water views, good walk/transit scores, year built post-1980s, and positive HOA fees are
positively correlated with condo price. A lower commute time also positively impacts condo price. I 
leveraged Azure Machine ML Studio to quickly understand feature importance of these predictors in the 
model.

After running feature importance from the gradient boosting model, I noticed all the features above and
demographic information like marriage status, income > $150k, and median age, are important in the 
aggregate model. I’m not at all surprised. 75% of this data reflects characteristics of NYC and their 
residents/preferences. Excluding demographic attributes reduces R2 from 0.74 to 0.73 and increases
error. Overall, I agree with the features identified as important, but I expected more features to be
significant that were not.

Factors I initially thought were important, but the model did not, are elementary school score, 
population density, sun score, and days on Zillow. These features may be influential depending on the 
city. Architectural style and property condition are important, but only if their labels are more precise. 
It’s easier to assess these two through pictures or viewing the property in person than in the dataset.
My final linear regression model predicted condo price within $363K, or 59%, on average (MAE and 
MAPE, respectively). This is a high error given that the average condo price is $1.018 Mil. As a result, I 
trained a Gradient Boosted Decision Tree model to improve accuracy. Even though the model is not as 
interpretable, gradient boosting can yield better performance and accuracy because it accounts for non 
linear relationships between the features and price.


### Step 5a: Validating Errors
An MAPE of 59% means the average prediction’s predicted price and actuals price are far off based on 
the features I fed into the regression model. While the error is large, it is still useful to look at properties 
where the prediction is higher than the actuals. That signals that the model predicts the condo is 
undervalued, which may make it more appealing to prospective buyers.
To validate the model, I looked at nine undervalued condos in Philadelphia, which were all the errors 
where actual price was less than $325K. I ranked the errors from highest to lowest where predicted 
price is greater than actual price and reviewed each record. Analyzing errors help me determine 
whether there’s a pricing inefficiency I can exploit or there’s some qualities of the condo the model just 
cannot capture.

#### Multi-city Model - Undervalued Condos

Condos that are undervalued in this price range have the essentials for a great condo! Four of the nine 
records are Philadelphia condos which have now been sold in the two months since the data was 
extracted. What the model could not capture is that the remaining five condos that have not sold have 
some flaw: they are either in a bad neighborhood, have poor management, have poor conduct by 
workers, are noisy, or a majority of residents of a condo building are senior citizens (that means people 
might change their behavior to accommodate demographic need). This can only be discovered by 
reading reviews about the condo building and is not easily encoded in the dataset in a way that can be 
captured by a predictive model.

1. <b>Plain but great price.</b> 118 S 21 St 618. Predicted Price: $170,354, Actual Price: $98,115. This has 
a pending offer. It is a bargain in Rittenhouse Square, an affluent neighborhood. It offers 24/7 
security, a rooftop deck, air conditioning, and is close to food/shops. It could have taken better 
pictures, but it’s a bargain.

<p align="left">
<img src="Images/1st condo.png" width=400>
</p>

2. <b>Progressive style in a hip neighborhood. </b> 810 N Hancock St 2. Predicted Price: $338,152, Actual 
Price: $320,000. This is sold. It’s in North Liberties, a young professional neighborhood close to
food and entertainment. There’s a garage, natural lighting throughout the property, and a 
rooftop deck. It has a very modern design, which I enjoy.

<p align="left">
<img src="Images/2nd condo.png" width=400>
</p>

3. <b>Average and livable.</b> 2101 Chestnut 403. Predicted Price: $180,120. Actual Price: $174,500 (Sold 
Price:$160k). This is sold. Riverwest Condos is in Rittenhouse, close to Trader Joes, restaurants, 
and walking distance to universities. The downside is it looks very typical.

<p align="left">
<img src="Images/3rd condo.png" width=400>
</p>

### Step 5b: Error Findings
NYC represents 75% of the condos in the data set, so the model reflects more of what NYC condo buyers 
value. This contributes to larger percentage errors for condos in the remaining non-NYC cities in the 
dataset. The MAE for NYC is $349K, MAPE is 43%, and the MAE for Non-NYC is $281k, MAPE is 61%.

The highest overvalued errors in the original multi-city linear regression model seem to be condos with 
unique and distinctive qualities such as specific water views, homes where famous figures once resided 
or utilized, condos located in hotels, and penthouses.

As with the undervalued condos, there are many ways a condo could be unique that dramatically 
increase its value but are difficult to encode in the data set in a meaningful way, which increases error.

### Step 5c: Improving model fit by binning numerical features to capture non-linear relationships

I leveraged Azure ML Studio to efficiently test which algorithm generated the best fit and minimized 
error. Boosted Decision Tree Regression has much better performance than a multiple linear regression. 
This could mean that some features have a non-linear relationship with price or have interactions with 
other features. The multiple linear regression could be improved if it accounted for non-linear 
relationships such as by binning numeric predictors into groups.

While errors could indicate pricing inefficiency, it is also likely that Multiple Linear Regression is just 
unable to capture non-linear relationships. It’s important to account for this so we can isolate pricing 
inefficiencies as much as possible. Gradient Boosted Decision Tree produced an R2 of 0.83. On average, 
its predictions were off by ± $243k (vs. $346k for Multiple Linear Regression, a $103k improvement).
Ultimately, Gradient Boosted Decision Tree yielded a better fit and reduced error.


### Step 5d: Multicollinearity

Multicollinearity is detected when independent variables in the regression model are highly correlated 
with each other. Multicollinearity does not impact model fit, but it does make the coefficients more 
difficult to interpret. I used Variable Inflation Factor (VIF) to identify variables with a high degree of 
collinearity, but ended up leaving collinear predictors in the regression to maximize predictive accuracy.


### Step 6: Philadelphia Multiple Linear Regression Model

Suspecting that a multi-city model would not be as precise as a local model, I used Azure ML Studio 
again to build a Boosted Decision Tree Regression on only the Philadelphia condo records. First, feature 
importance is used to determine which features provided the most information. Then the same features 
were fed into the multiple linear regression model, but any features with p>.05 were dropped. By
focusing on Philadelphia, I was able to add dummy variables for highly desirable Philadelphia 
neighborhoods, to better tailor the predictions by neighborhood.

In the Philadelphia dataset, the year-built feature is more significant. Bathroom, bedroom, walk score, 
square footage, and HOA fee are still important. All other features considered important in the multi-city
model are no longer important.

None of this is surprising. Philadelphia prides itself on historical preservation (our founding fathers 
signed the Declaration of Independence in Philadelphia) which is why year built is a key differentiator in 
this market. A well-maintained home vs one that needs work (beyond aesthetics) can be the 
determining factor between buying and passing on comparable homes with all the essential features.

#### Philadelphia Model - Undervalued Condos

I used the Philadelphia Multiple Linear Regression model to identify additional undervalued condos.
Capping price at a $325K threshold, I identified 14 total undervalued condos, 3 of which I like:

1. <b>Clean layout, high ceilings, and natural sunlight.</b> 444 N 4th St Unit 313. Predicted Price: 
$368,817, Actual Price: $305K (Sold Price: $299,900).

<p align="left">
<img src="Images/4th condo.png" width=800>
</p>
  In Northern Liberties, a young professional neighborhood, the visuals are impressive. The condo
  uses updated design concepts.

2. <b>Cozy layout and design. </b> 1431 N 5th St #1. Predicted Price: $800k, Actual Price: $320K. The 
predicted price is high because it has 3 bedrooms, 2 bathrooms, and is 1769 sq ft.

<p align="left">
<img src="Images/5th condo.png" width=400>
</p>

  While Kensington overall is not known to be safe, Old Kensington is the most gentrified because 
  it’s by Northern Liberties, so safety is improving as more young professionals move into this 
  neighborhood.

3. <b>The living room, kitchen, and bathroom design are appealing.</b> 3900 Ford Rd APT 3O
Predicted Price: $453,690. Actual Price: $315K (Sold Price: $80K).

<p align="left">
<img src="Images/6th condo.png" width=800>
</p>

  Park Plaza is downtown. It looks like a great place to host friends.

  While I found a few hidden gems listed above, most undervalued condos have an average 
  appearance in terms of condition and style. As a prospective buyer, I want to buy something
  impressive. While my model did not include any features on quality or condition, a more 
  advanced machine learning model may be able to extract a listing’s visual attractiveness by 
  analyzing ad scoring the listing’s photos.

#### Philadelphia Model - Overvalued Condos

Next, capping price at a $325K threshold, I looked at overvalued condos to assess the validity of those 
results. I agree with 12 of the 14 errors. Either they are in buildings that are relatively average or in 
average neighborhoods. There were 2 overvalued errors that caught my eye.

1. <b>Of Sentimental Value.</b> 604 S Washington Sq APT 1806. Predicted Price: $-238K. Actual Price: 
$217K (Sold Price: $202K). It has a fantastic view but is overvalued by $455k. It has 0 bedrooms 
and is small at only 600 square feet but is right by a beautiful square. It is one of my favorite 
spots to talk to friends! Because I included neighborhoods in my model (this neighborhood is 
cheaper) and no HOA fee was listed, the predicted price came out negative.

<p align="left">
<img src="Images/7th condo.png" width=800>
</p>

2. <b>Modern and chic layout.</b> 1931 Spruce St 1C. Predicted Price: $215k. Actual Price: $275K (Sold 
Price: $256K). It is overvalued by $60k, but the renovations and décor are impressive. The model 
cannot capture how well maintained or stylistically unique a condo is.

<p align="left">
<img src="Images/8th condo.png" width=400>
</p>

In summary, these two overvalued condos require compromises between less attractive and more 
attractive features.

By focusing only on Philadelphia, the error metrics improved. MAE for the Philly Gradient Boosted 
Decision Tree is $135k, vs. $246k MAE for the Philly Multiple Linear Regression. The MAPE for the 
Philadelphia GBM is 21% vs. 44% for the MLR.

At both the Northeast regional and local Philadelphia level, Gradient Boosted Decision Trees outperform 
Multiple Linear Regression in accurately predicting condo price.

The Philly Multiple Linear Regression outperforms the Multi-City Multiple Linear Regression. The MAE 
decreased from $363k to $246k and MAPE went down from 59% to 44%.

Bottom line: it is more useful to model and evaluate undervalued condos at the city-level, because both
algorithms yielded more interesting errors to think about compared to the aggregate (multi-city) 
models. However, even the city-level models cannot distinguish hidden gems (great style, upkeep, great 
neighborhood) vs condos that have potential but there’s no effort to renovate, restore, or have poor 
reviews. A purchase strategy is if you find a condo you like that has already sold but fits your criteria is to 
wait until another comparable unit pops up in the same building.


### Step 7: Conclusion/Findings

I initially wanted to identify undervalued condos by creating a model to predict condo prices as 
accurately as possible. At first, I approached this by building a multi-city multiple linear regression model 
with relevant features. The accuracy was not great, so I trained a gradient boosting model which 
improved this metric. Sensing that different cities value different condo features, I built a multiple linear 
regression model using Philadelphia condos, which reduced my errors compared to the original multi-city multiple 
linear regression model. The Philadelphia specific model included a smaller set of 
predictors to the multi-city model with the addition of neighborhood. Gradient Boosting outperformed 
regression in the multi-city and Philadelphia model because it accounts for non-linear relationships. 

However, there are still challenges in modeling condo pricing due data quality (missing or messy data), 
how to encode eccentric property characteristics (desirable water views, penthouse/condo hotels, 
owned by famous figures), and data availability (there’s no simple way to pull crime/neighborhood 
quality data). Sentiment reviews are important but are complicated to integrate into a model since 
reviews can come from so many places.

As a result, my approach evolved from strictly using the model to identify undervalued properties, to 
using it – along with the PowerBI dashboard – as decision support tools alongside in-depth (and manual) 
research. Oversimplifying the home buying process is too costly and can lead to buyer’s remorse.
Especially if I am only planning to buy one home in the foreseeable future, I want it to be worth my 
while.

This process has given me inspiration to explore condo buildings that I have not considered before
because I’ve been downtown-centric in my search. I plan to visit at least one property to make sure 
what I see is what I expect and read through reviews.


### When the zestimate fails:

The zestimate is Zillow’s house valuation tool that “allows users to see how much homes are worth. The 
zestimate is based on information from sources like comparable sales and public data. Zestimates are 
only as accurate as the data behind them, meaning they may be outdated or incorrect”.

I did not use zestimates in this project because Zillow’s overreliance of the zestimate led to the company 
laying off 25% of their workforce and writing off $500M this year.

Zillow leadership’s strategy was to use the zestimate to buy undervalued properties and flip them. The 
strategy failed since Zillow purchased properties at too high a price. The intent of the zestimate is to be 
used starting point of conversation about value, not the final decision. Zillow did not get input from 
agents, brokers, or appraisers (professionals who have been inside the home) to accurately assess the 
value of the property. Doing so would have reduced the number of overvalued properties they bought. 

Another weakness is that Zillow data is not always correct, or there are edge cases like view 
obstruction/noise that penalize price that humans can easily see that machine learning cannot detect.
Finally, Zillow’s zestimate is not designed to adjust to the rapid changes in the volatile real estate 
market. That is why I use predictive models as a starting point to identify potentially undervalued 
properties, but ultimately much more information is gained by viewing the property and talking to 
appraisers.

If there’s anything we’ve learned from Zillow’s failure and the 2008 subprime crisis, it is to verify that 
your original assumptions are correct before making large transactions.


### Final Thoughts

On the topic of the tradeoff between interpretability vs. accuracy with predictive models, most roles
require translating technical concepts for non-technical people. That is why it is important to use a 
model that can be easily explained. However, there are still benefits of using machine learning without 
entirely understanding how it runs under the hood. If an algorithm continues to work, more insights will 
follow, so the interpretability lags the technology being deployed. If the algorithm fails, people abandon 
it, and there’s no longer feedback. It's good to know which algorithm performs the best, just in case this 
plays a bigger role in the future. For this project, using multiple linear regression was the best choice to 
keep it simple.

### Step 8: Next steps
If there was more time, here are other ways to enhance the project:

• Look at undervalued Philadelphia condo errors above $325k for more inspiration and to 
understand what else the model can’t account for. Streamline city-level error outputs.

• Analyze condos by city. Each city has a differentiator and faces different risks. A differentiator in 
Boston is hiking trails and the waterfront view. A risk in Baltimore is environmental flooding. 
Each housing market is very different, and one may get better results focusing on one city. Note 
the condo inventory on sale is lower than 1,000 units in every northeastern city except New 
York City. That means that while one may get better results focusing on one city, there is a 
tradeoff in terms of data availability to train the model.

• Given that in New York City, Jersey City, and Boston, residents primarily rent because they 
moved there for work, are more transient, and it’s more expensive to own property, does it 
make more sense to rent instead of buy in these locations? This project can be replicated and 
repurposed to identify affordable apartments for rent, using monthly rent as the target variable.

### Python Libraries Used
- Pandas
- Matplotlib
- Seaborn
- Plotly
- StatsModels
- Scipy
- Sklearn
- Sqlalchemy
- Json
- Requests
- Time

### Methods
- Multiple Linear Regression
- Gradient-Boosted Decision Tree
- Data Visualization
