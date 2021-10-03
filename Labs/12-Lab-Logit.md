> - Dataset: [Ecommerce Customers](https://www.kaggle.com/srolka/ecommerce-customers)
> - Colab Notebook of this lab: [Ecommerce-Regression.ipynb](https://colab.research.google.com/drive/1DvdewmdId2BJHIGL4QkSj0oak9Mfpc6r#scrollTo=7XODuZ1TERhT)


## Binary Dependent Variable
- Not all problem has the continuous variable as the dependent variable as in [[11-Lab-Regression]]. Sometimes, we dependent variables is categorical
- In this lab, we will build the logit model for a binary dependent variable: `is_highval`
	- This is constructed from the `Yearly Amount Spent` - the target variable in [[11-Lab-Regression]]
	- We define customer with `is_highval = True` as customer with `Yearly Amount Spent` > p80
```
ecommerce_df['is_highval'] = ecommerce_df['Yearly Amount Spent'] > ecommerce_df['Yearly Amount Spent'].quantile(0.8)

ecommerce_df['is_highval'].value_counts()
```

## Logit Model
```
regressors = ['Avg. Session Length','Time on App', 'Time on Website','Length of Membership']

logit_model = sm.Logit(
	ecommerce_df['is_highval'],
	ecommerce_df[regressors]
).fit()
```

### Interpret the Results
![[attachments/lab12/lab12.1.png]]

> Discussion: We have the results as below. How you will interpret the `coef`?

Remember in [[../Tutorials/11-12-Regression-Analysis]], we construct the formula of Logit as below:

![[tutorial11-12.6.png]]

- Mathematically, this is how it looks like:

$$Y = f(b0 + b1 \times X1 + b2 \times X2 + ...) + \epsilon$$

- Function `f` have the form: $f(z) = \frac{1}{1 + exp(z)}$
	- Replace: $z = b0 + b1 \times X1 + b2 \times X2 + ...$
- By transforming $f(z)$, we will also have:

$$log(\frac{P(y=1)}{1 - P(y=1)}) = b0 + b1 \times X1 + b2 \times X2 + ...$$

So that, the meaning of `coef`:
- Positive sign: the increase in that variable will increase the log-odd (then increase the likelihood of being `is_highval`) -> Negative is the opposite.
- Different from the interpretation of `coef` in [[11-Lab-Regression]], the `coef` in logit is not the direct impact on the probability of `y`, but one unit increase/decrease will change the log-likelihood by `coef` units
	- ==Exponentiated (reserve the log) coefficient== -> the effect on odds (odds ratio)

### Predict: Probability
- Predict on the regressors, we can see that the output of prediction is the probability, with value between 0 and 1, rather than binary Yes/No or True/False

![[attachments/lab12/lab12.2.png]]

- It means we need a threshold for probability to convert the probability into binary. For example, if the predicted probability is `>0.5`, we will predict the label as `Yes`

![[attachments/lab12/lab12.3.png]]

### Confusion Matrix
The simple idea to check the accuracy of logit model (and other classifiers) is: ==How correct they predict the actual class of the observations?==
- We do it by cross-table the actual class and the predicted class (after applying the threshold)

![[attachments/lab12/lab12.4.png]]

> As you might guess, the accuracy and Fall Positive & False Negatve is determined by the threshold we choose. We will further discuss about this [[#Threshold - FP FN Trad-off]]

## Evaluation
Evaluation Metrics is built on top of the [[#Confusion Matrix]] in the previous part:

![[attachments/lab12/lab12.5.png]]

### Evaluation Metrics
```
def print_eval_class(true, predicted):
	acc = metrics.accuracy_score(true, predicted)
	precision = metrics.precision_score(true, predicted)
	recall = metrics.recall_score(true, predicted)
	f1 = metrics.f1_score(true, predicted)
	auc = metrics.roc_auc_score(true, predicted)

	print('Accuracy: {:,.2f}'.format(acc))
	print('Precision: {:,.2f}'.format(precision))
	print('Recall: {:,.2f}'.format(recall))
	print('F1: {:,.2f}'.format(f1))
	print('AUC: {:,.2f}'.format(auc))

	return acc, precision, recall, f1, auc
```
-   **Accuracy**: How accurate the model classify to the right class (cautious with imbalance)
-   **Precision**: More False Positive, the lower precision (False Positive is tolerant for high-risk problems, i.e. COVID diagnostic)
-   **Recall (Sensitivity)**: More False Negative, the lower recall (False Negative is tolerant, Spam)
-   **F1**: single score balances both the concerns of precision and recall. Good F1-score = low Precision, low Recall. Perfect = 1, Failure = 0
-   **ROC Curve**: Plot of tpr (true positive rate) vs fpr (false positive rate) => Higher area under the curve better the models

### Thresholds - FP/FN Trad-off 
> Before we discuss that the FP/FN is varied by the threshold we set

![[attachments/lab12/lab12.6.png]]

For example for the COVID case:
- If we set the threshold at `0.3` rather than `0.5`, any patients with the prediction of `0.3` will be classified as `COVID positive`, with the lower predicted probabitlity:
	- Now, we are more frequent to predict a `COVID-positive` patients -> We will have higher `True Positive` (as we predict True more often)
	- We will have higher `False Negative`, the patients not positive to COVID but predicted to `COVID positive` for further actions (quarantine, further tests, etc.)
	- Yet, we will also have lower `False Positive` - the chance that we missclassify a patient with `COVID` (which is far more dangerous to the community)

- Depends on the situation, and the cost of FN/FP that we decide a low/high threshold. 

### ROC - AUC
- To illustrate the ideas in [[#Thresholds - FP FN Trad-off]], we varied the thresholds, and observe the corresponding value of `fpr`, and `tpr`
- Plot the `fpr` and `tpr`, we have the ROC graph:
![[attachments/lab12/lab12.7.png]]
- `AUC`: ==Area under the curve== is the measure of the area under the `blue` ROC curve
	- The higher AUC, the more accurate the classifier

![[attachments/lab12/lab12.9.png]]

![[attachments/lab12/lab12.11.png]]

## Recap
- We learn the logit model in Python with the binary dependent variable
	- The `coef` is the marginal effect on log-odd
	- The prediction is probability
- To evaluate the performance of logit model:
	- Confusion Matrix: FP, FN
	- Other metrics: `Accuracy`, `Precision`, `Recall` (Sensitivity), `F1`
- For different thresholds of predicted probability (to convert it to binary), we have different value pairs of `FPR` and `TPR`, by plotting them, we have `ROC` and `AUC`