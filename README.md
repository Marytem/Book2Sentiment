# Book2Sentiment

__This project is on analysing literature texts. We worked with raw texts of 147 books (listed in 'data' folder) of different authors and time periods. The main idea of the project is to extract plotlines from books and then cluster them. The project consists of three major parts:__
* Extraction of plotlines from books with sentiment analysis
* Clustering of extracted plotlines
* Extraction of main characters from raw books

## Sentiment analysis for extracting plotlines (by Stanislav Vdovych @vdovych)

__Data:__ 14k tweets about airlines labeled as positive, neutral or negative.

Tweets are usually short sentences with simple emotions thus they are best fitted for classifying sentences.

__Processing:__ 
Using pretrained embeddings transform all words into 300d vectors, removing super rare words in process. 
Zero-pad for training the model.

__Model:__
For sentiment analysis I use LSTM model to analyze sentences and predict sentiment on scale from 1 to -1 (1 - pos,0 - neutral,-1 - neg)

It's a powerful tool to capture relations between the words in sentence as it can learn higher-level concepts such as negations on it's own. Using non-DL approach would've required handling it manually during preprocessing and allowing room for error.

__Other sentiment analyzers:__

 As multiple libs have their own sentiment analyzers, I compared them with mine. From 3 I chose to compare TextBlob was easiest to use but relatively the worst in accuracy, NLTK has great non-DL solution, that gives only sligtly lower accuracy, and spaCy offers very similar analyzer to the one I'm using, just heavier. If given more resourses to train, I would have probably just used spaCy.

__Visualisation:__ 

Sentence by sentence sentiments are usually chaotic but general trends can be seen such as very positive or dark moments in the story. In some books there is a shift in tone, for example "The Picture of Dorian Gray" from pretty positive-neutral tone into a noticeably dark one.

* TREASSURE ISLAND
![alt text](https://github.com/Marytem/Book2Sentiment/blob/master/images/treasure_island.png) 
* Game of thrones (is dark)
![alt text](https://github.com/Marytem/Book2Sentiment/blob/master/images/got.png) 
* the Picture of Dorian Grey
![alt text](https://github.com/Marytem/Book2Sentiment/blob/master/images/dorian_grey.png) 

Here's how the last books plotline looks like if we smooth the sequence with rolling average. Can you see the pattern in the above heatmap? The second part of The Picture of Dorian Grey is more pink, which means more dark and pesimistic.

![alt text](https://github.com/Marytem/Book2Sentiment/blob/master/images/dorian_grey_func.png) 

## Clustering of extracted plotlines (by Maryana Temnyk @Marytem)

__Data:__ 147 sentiment sequences of our books-dataset. These are sequences of numbers from -1 to 1 indicating how positive or negative each sentence in the book is.

__processing:__ 
* stretch all sentiment sequences to the length of the longest one.
* smooth sentiment sequences with rolling average to get a plot-line 
* take 1000 points uniformly selected from the plotline of all sequences to form a dataset

![alt text](https://github.com/Marytem/Book2Sentiment/blob/master/images/raw_sents.png) --> ![alt text](https://github.com/Marytem/Book2Sentiment/blob/master/images/messy_sents.png) --> ![alt text](https://github.com/Marytem/Book2Sentiment/blob/master/images/smooth_sents.png) --> ![alt text](https://github.com/Marytem/Book2Sentiment/blob/master/images/reduced_sents.png)

__visualization:__
* To understand how our sequences look in space we shrink them to 2 components(dimentions) with t-sne:

![alt text](https://github.com/Marytem/Book2Sentiment/blob/master/images/simple_tsne.png)

__Clustering:__
* ELBOW METHOD with kmeans

![alt text](https://github.com/Marytem/Book2Sentiment/blob/master/images/elbow.png)

Elbow method showed that the optimal number of clusters is from 5 to 10. We have very different books in our dataset, so we assume there are like 7-10 clusters, so we chose K=9

* ALGORITHMS applied
Clustering went successfully! In the notebooks we can see, that books with similar plots are usually in one cluster, like Harry Potter books, most of which are in one cluster, or other cluster that gathers quite dark books together (Metamorposis, notes from underground, Jekyl&Hide, ...).

K-means

![alt text](https://github.com/Marytem/Book2Sentiment/blob/master/images/kmeans.png)

DBSCAN

![alt text](https://github.com/Marytem/Book2Sentiment/blob/master/images/dbscan.png)

Affinity propagation

![alt text](https://github.com/Marytem/Book2Sentiment/blob/master/images/AP.png)

Spectral clustering

![alt text](https://github.com/Marytem/Book2Sentiment/blob/master/images/SC.png)

Birch

![alt text](https://github.com/Marytem/Book2Sentiment/blob/master/images/birch.png)

* METRICS & COMPARISON of algorithms. We used DBi to measure how good our clusters are and Silhouette score to measure how good an assignment of each point is.

![alt text](https://github.com/Marytem/Book2Sentiment/blob/master/images/dbi.png)
![alt text](https://github.com/Marytem/Book2Sentiment/blob/master/images/silhouette.png)

From the plots above: 

K-means: has relatively average performance comparing with others.

DBSCAN: has the worst performance as density based algorithms do not fit to our data.

Affinity propagation: has relatively average performance comparing with others.

Spectral clustering: has a performance that is slightly lower than average with a bit higher DBi and a bit lower Silhouette score.

Birch: has the lowest DBi and the highest Silhouette score, which makes him the best according to chosen metrics.

In reality we should choose an algoritm according to what we need. For example Affinity propagation might be useful because it can choose a "representative" of the cluster, DBSCAN could be useful for data with different densities and Birch is suitable for big datasets.


## Extraction of main characters from raw books (by Maryana Temnyk @Marytem)

__Data&liraries:__ raw books, NLTK&Spacy

__extraction pipeline:__ 
##### prep data
* remove unnecessary punctuation
* tokenize
* remove stopwords
* apply POS-tagging
##### extract characters
* perform NER 
* count number of unique entities labeled "PERSON"
##### results
###### Game of thrones 
![alt text](https://github.com/Marytem/Book2Sentiment/blob/master/images/got_nltk.png) 

![alt text](https://github.com/Marytem/Book2Sentiment/blob/master/images/got_spacy.png)   
###### Harry Potter and deathly hallows
![alt text](https://github.com/Marytem/Book2Sentiment/blob/master/images/hp_nltk.png)   

![alt text](https://github.com/Marytem/Book2Sentiment/blob/master/images/hp_spacy.png)   
###### The Witcher: Blood of Elves
![alt text](https://github.com/Marytem/Book2Sentiment/blob/master/images/witcher_nltk.png)

![alt text](https://github.com/Marytem/Book2Sentiment/blob/master/images/witcher_spacy.png)

From what we can see NLTK extracts only three types of entities, and makes lots of mistakes labeling 'Night' or 'Watch' as people. It's entities consist of one word, which creates lots of duplicates or half-truths.

Spacy does better job, it extracts more kinds of entities than nltk. It still has some false positives like "'m" or "'s" that are parts of mentioning a character in the text(eg. "Tywin 's"), but other than that all characters seem to be properly extracted with their full names like 'Ser Jorah' or 'Eddard Stark'.
