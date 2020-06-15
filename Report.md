# Report

We have a very **domain-specific classification problem** at our hands i.e to classify the category of a given product/service given its title and its description (scraped most probably from an e-commerce site).

## Evaluation Metric Choice
I used **F1** as my evaluation metric as it can **balance both recall and precision** at once and as we also have a (**un-visible**) class-imbalance problem in the given test set.

## Best Test Score

**F1 Score : 0.9541**

**Model  : Ensemble of 4 Models (FlipCNN_Backwards + Naive Bayes + LogReg + LightGBM)**


## Validation Strategy

As it was seen in EDA that we have very **limited set of unique descriptions** (1295 in train) and each of them was **mapped to exclusive category**.

So if we intend to use description as a textual feature (trust me, we do!) it is very easy for a model to **overfit on these given set of descriptions**.

To tackle this I made a **Group-Stratified Split Strategy**. 

This basically means I would **first stratify-split the whole data into K folds** and then **for each fold I would use GroupShuffleSplit where putting groups=description** and create train and valid set.

This was in a **particular fold_id we would not have any intersections of descriptions** both in train and validation set and this would help us in **early-stopping** and **better generalization**.  

## Approach 

I tried to build a good pipeline which can easily integrate with many approaches.
 

The gist for the code/notebooks  : 

0. **Validation Strategy + Split Data** : 
	
	- Build the validation strategy
	- Divide the entire data of (1.2M) into 10 folds so we can use a subsample for testing our hypothesis

1. **EDA** : 

	- Target Distribution : Balanced
	- Text Length Distribution : Finding the max_seq_len using 95% technique
	-  Naive Hypothesis : Built a baseline using a naive and simple rule :
	
		- If the title/description has the word "product" then it is of "R" category
		- Else if title/description has the word "service" then it is of "S" category.  
		- Else random
	- WordClouds : This builds a clear picture of what does the categories look like
	- Word2Vec + T-SNE 
 
2. **Preprocessing** :
	
	- Text Cleaning
	- Text PP (For embeddings) 
3. **Imputing Descriptions** : 
	
	- Description is a very important textual feature. I try to impute descriptions using the **most similar title in train** and using Word2Vec vector to perform the same. 
4. **Modelling - 1** : 
	
	a. Frequency Based : Used BOW Models techniques (Count, TFIDF)
	b. Meta Feature : Not very viable for this task
5.  **Modelling - 2** : Used CNN to baseline as it is a kind-of-a-BOW model and is very lightening fast
	
	a. Baselined Pre-Trained Embeddings : The coverage for the pre-trained embeddings was very low. That might be due to the domain-specific task in our hands and usually the pre-trained embeddings are trained on more generic text like news, tweets, etc. 
	b.  Custom Word2Vec : This was as hypothesized better than the pre-trained embeddings and got a huge boost in F1 vs the pre-trained ones.
6.  **Temporal Modelling** : Baselined multiple temporal models. These tend to overfit to the train set very fast and are very slow to train.  
	
	a. FlipCNN
	b. Bi-LSTM
	c. Bi-LSTM + CNN
	d. Bi-LSTM + Attention

	In this notebook I also tried to visualize the attention to check which keywords are effecting the classification. 

7.  **Ensembling**  : In this I just ensembled/averaged the predictions of 4 models 
	a. FlipCNN_Backwards : CNN trained on reversed text input.
	b. NaiveBayes : CountVec
	c. LogReg : TFIDF
	d. LightGBM : CountVec
	
	This ensemble had a score of **F1 : 0.9541**.

## Conclusion 

I conclude this report by saying that this was a task in which the **semantic meaning didn't matter much** as usually the titles and **descriptions of a product/service are meant to attract customers** so hence are made with so many **eye-catchy keywords**. 

This is also the reason why the **Bag-of-Words models were having performance better than the temporal models**.

Thanks!
Stay Safe!
