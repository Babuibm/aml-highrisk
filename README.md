# Banking-AntiMoneyLaundering
Deep Learning Model (Variational Auto-Encoder - VAE) for monitoring high risk transactions under Anti Money Laundering initiatives
# Highlights 
- This is a deep learning model for Anti Money Launder (AML) compliance specifically for the scenario of “High Risk transactions - Focus on Couter Party (HR-CP)”. This scenario is widely used by banks worldwide in their AML compliance processes but the current process in most banks is based on rule based logic 
-	Variation Auto Encoder(VAE) a deep learning, unsupervised learning model is built as a solution to the AML monitoring as this approach is more effective (compared to rule based or Supervised Learning based AI Models) in identifying high risk transactions that do not follow usual patterns.  
- Synthetic transaction data is prepared to mimic the production data. Training data  of 10000 transactions is used to train the model and the model is applied on new/unseen data (11000 transactions) to observe model performance. The model is also evaluated against the results of rule-based logic
- The bank is assumed to be blended bank (good mix of retail and corporate customers) based in the US
# Model - Variational Auto Encoder(VAE)
- Model Architecture - Encoder with 7 hidden layers and Decoder with a symmetric structure, ‘Relu’ as activation layer, ‘Adam’ as optimiser 
- Train the model with synthetic transaction data (same data used for rule based logic) and mark transactions a anomalous transactions based on the model output  
- Finalize the model after training and apply the model on new/unseen data
# Files / Notebooks
- Model_Build_Training.ipynb
  - Upload ‘MasterData.xlsx’ and ‘Conditions.xlsx’
  - Generate customer, account and transaction data for training 
  - Apply rule based logic and identify the alerts for limited comparison with model results 
  - VAE Model build / training / save the model 
- ModelApplicationOn_New-UnseenData.ipynb
  - Upload ‘MasterData.xlsx’ and ‘Conditions.xlsx’
  - Generate new/unseen customer, account and transaction data
  - Apply the rule-based logic on new data
  - Import the VAE model built/trained already and apply the same one new data
  - Compare the results

# Data
- Training Data
  - Data for training the model is generated synthetically and effort is taken to prepare the data very similar to the production data
  - Master data used – Business of the customer (Business Code), Geography, Customer Type, Product, Transaction Channel, Transaction Type and Currency. These master data is prepared manually and each of the values for these master data is mapped to indicate the risk level. For HR-CP scenario all these master data is used except Transaction Type and Customer Type which are useful if the scope is expanded to cover other AML scenarios 
  - Transaction data – Customer, Account and Transaction data are prepared synthetically using python scripts and Faker library. Effort is taken to ensure the transaction data reflects the production data to the extent possible with (a) volume thresholds based on currency, risk level (b) Right ratio of customers to accounts to transactions (c) Dependency between product-transaction type, channel-transaction types, currency-transaction types 
  - A total of 400 customers, 1000 accounts and 10000 transactions are generated synthetically with a transaction date spanning around 15 days.
 
- New Data (unseen data)
  - After the model is built and trained on the training data, the model is applied on a new data (unseen data). This data is also generated synthetically with the same methodology as mentioned above for the ‘Training Data’ but with slight tweaks on the composition of risk levels in the overall volume of transactions 
  - For the ‘new/unseen data’, 500 customers, 1100 accounts and 11,000 transactions are generated  
# Evaluation of the model results 
- Rule based logic is applied on the synthetic data, alerts generated and transactions linked to the alerts identified. Rule based logic is as follows:
  - ‘Overall Transaction Risk’ is calculated as follows:
    - Overall Transaction is classified as ‘High Risk’ - If any of the CounterParty(CP) Business Risk, CP Geography Risk, Channel Risk, Product Risk is 10 (high risk) or if Politically Exposed Person(PEP) flag is Y or if CP is in watchlist
    - Overall Transaction is classified as ‘Medium Risk’ - If PEP flag is N or if CP is not in watchlist and if any of the CounterParty(CP) Business Risk, CP Geography Risk, Channel Risk, Product Risk is 5(medium risk) 
    - Low Risk - All other transactions are marked as Low Risk
  - ‘Overall Initiating Customer (IC) Risk’ is calculated as follows:
    - Overall Initiating Customer (IC) Risk  is classified as ‘High Risk’ - If any of the IC Business Risk, IC Geography Risk, Channel Risk, Product Risk is 10(high risk) or if PEP flag is Y or if CP is in watchlist
    - Overall Initiating Customer (IC) Risk  is classified as ‘Medium Risk’ - if PEP flag is N and if IC is in watchlist and if any of the IC Business Risk, IC Geography Risk, Channel Risk, Product Risk is 5 (high risk) 
    - Low Risk – all other initiating customers are marked as Low Risk
  - Alerts are generated as follows:
    - Transaction amounts are aggregated by ‘Initiating Customer’ and by ‘Overall Transaction Risk’
    - If ‘overall Transaction risk’ is high risk and ‘h.risky transactions’ are >= 80% and ‘transaction amount’ > 1500 mark the alert ‘A-P1’ against such transactions
       where ‘h. risk transactions’ = (Transactions initiated by IC where ‘Overall Transactions Risk’ is High Risk / (total transactions by IC))
    - If ‘overall Transaction risk’ is medium risk and ‘m.risky transactions’ are >= 60% and ‘transaction amount’ > 1000 mark the alert ‘A-P2’ against such transactions
      where ‘m. risk transactions’ = (Transactions initiated by IC where ‘Overall Transactions Risk’ is Medium Risk / (total transactions by IC))
- VAE Model results
  - Apply the model on new/unseen data and extract high risk transactions with the similarity score threshold of 97%
  - Compare the transactions identified with the alerts generated by the rule based logic with the transactions identified as anomalous by VAE model, using the transaction ID
# Results & Conclusion
- Results of the model application on new/unseen data: Alerts generated by rule-based logic are compared with Variational Auto-Encoder(VAE) model results. For the purpose of classifying High Risk transactions, reconstruction error was considered as the base and 97% threshold was applied (similarity score of 97% between the original and reconstructed input)
- There is a match of 4% between the high risk transactions indicated by rule-based logic and the VAE model. I consider this as a good starting point and with the following actions it could improve:
    - Objective of the % match is not to match the alerts generated by rule-based logic as it is, as this represents the total number of alerts before processing the alerts through the alert life cycle. To match the % alerts, we should take the actual alerts acted upon and really meaningful for the bank
    - Data used is synthetic data and is not a match for production data. If the model building / training is done on the production data, there would be significant improvement, not only by tuning but also revisiting the transaction features
    - Further tuning Model hyper-parameters – The model is tuned with limited data and limited hyper-parameters due to limitation on resources (GPU). With further tuning model performance and % match would improve
# Future enhancements 
- Watchlist – Currently a simple name matching is considered for watchlist, NLP could be applied to match the watchlist names which will be more realistic with actual watchlists
- Include other scenarios in scope of AML monitoring in the bank like ‘Rapid movement of Funds’, ‘Activity in Inactive/Dormant accounts’ etc in the same deep learning model scope which will result in more business benefits
