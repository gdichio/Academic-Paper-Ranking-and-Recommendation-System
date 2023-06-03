### **Abstract**

Academic research is a key component in the advancing of innovative technologies in a variety of industries and a signifier of the state of human knowledge. This report aims to help researchers efficiently find high impact research papers to read and use for their research. ArXiv, a free and open-access archive for 2,180,040 scholarly articles in various STEM fields, provides a dataset of high energy physics meta data including but not limited to abstracts, titles, authors and citations. Visualization and Application techniques were used with abstract and citation data to give insight into the connection between papers and their impact quality. With a dataset containing over 1.7 million STEM-related papers, sparsity in data arises, justifying tools such as TF-IDF, WordVectorizer, and PageRank to find the connections between papers and their impact.

### **Dataset**

There are two datasets used, the first contains up-to-date arXiv metadata  including but not limited to title, abstract, category, and authors information. An example datapoint within the set is,

"root":{\
"id":"0704.0001",\
"submitter":"Pavel Nadolsky",\
"authors":"C. Bal\'azs, E. L. Berger, P. M. Nadolsky, C.-P. Yuan",\
"title":"Calculation of prompt diphoton production cross sections at Tevatron and LHC energies",\
"comments":"37 pages, 15 figures; published version",\
"journal-ref":"Phys.Rev.D76:013009,2007",\
"doi":"10.1103/PhysRevD.76.013009",\
"abstract":" A fully differential calculation in perturbative quantum chromodynamics is presented for the production of massive photon pairs at hadron colliders. All next-to-leading order perturbative contributions from quark-antiquark, gluon-(anti)quark, and gluon-gluon subprocesses are included, as well as all-orders resummation of initial-state gluon radiation valid at next-to-next-to-leading logarithmic accuracy. The region of phase space is specified in which the calculation is most reliable. Good agreement is demonstrated with data from the Fermilab Tevatron, and predictions are made for more detailed tests with CDF and DO data. Predictions are shown for distributions of diphoton pairs produced at the energy of the Large Hadron Collider (LHC). Distributions of the diphoton pairs from the decay of a Higgs boson are contrasted with those produced from QCD processes at the LHC, showing that enhanced sensitivity to the signal can be obtained with judicious selection of events. ",\
"report-no":"ANL-HEP-PR-07-12",\
"categories":[
0:"hep-ph"
],\
"versions":[
0:"v1"
1:"v2"
],\
}


For this study, we use the "hep-ph" (High Energy Physics) category, with a sample size of $n=78,014$. Preprocessing includes cleaning abstracts to retain useful keyworks related to the category, such as "model", "neutrino", "higgs", then reducing the unique word count down from $33161$ to $3718$, through analysis of word frequency.

<center> <img src="https://drive.google.com/uc?id=1tZ9of6ukueSozO2wFyPyAqmur2zrcOks" alt="word freq."> </center>
<center> Sharp curve displays that words with less than ~100 occurances make up 90% of the words. </center>


The second dataset is an older copy of the metadata that also includes citation data. This dataset was used with WordVectorizer and PageRank implementations to find similarities in meaning between papers and the most cited papers within the dataset. The sample size in the second dataset is $n=1,644,669$. The citation data is a dictionary pointing from a paper's ArXiv ID to the cited paper's ID within the paper. Many papers in the dictionary only cited themselves or had no citations. This greatly reduced the dimensions of the dataset bringing it down to $n=864,117$. Upon further inspection, the papers with no outside citations did have citations within their paper, however, they were not within the ArXiv Database. This means that those papers did not have a paper ID and could not be added to the dataset. An example datapoint would be,


"hep-lat/0403001":[\
  "hep-lat/0305022",\
  "cond-mat/9702070",\
  "hep-lat/0312035",\
  "hep-lat/0403001",\
  "hep-lat/0311039",\
  "hep-lat/9710001",\
  "cond-mat/9705295"]


The hep-lat, high energy physics, papers did not only cite other high energy physics papers, making the data set more difficult to operate with due to the fact that list of papers and the list of citations are not equal.

 Similar preprocessing is used for abstract information, applied in WordVectorizer.


### **Methodology**

The approach used for this study includes TF-IDF, NMF factorization, WordVectorizer and PageRank. Using TF-IDF and NMF Factorization, we are able to create features out of NMF: $V \approx WH$, $W$ matrix, which contains "dictionary" information, in terms of TF-IDF matrix $V$, allowing us to create topic clustering between the top $k=25$ words related to High Energy Physics category. Another practical use of the $W$ matrix is for creating a Recommender System, such that given a paper title, it finds the most similar papers ranked by cosine distance. Furthermore, cosine distance also allows us to find similar titles to a given user query, leading to the final result of a paper "search engine" that finds similar paper names, and their related papers.

(APPROACH FOR USING WORDVECTORIZER)

The approach used for this study includes TF-IDF, NMF factorization, WordVectorizer and PageRank.

In finding the impact of individual papers, a Page Rank method was used. INitial attempt were to build a Coordinate form representation of the data, that is, every paper is given a column index, row index, and value. The representation would then be converted to a link matrix from papers to citations. The final step would be to go through Page Rank provess,and find eigenvalues. This process turned out to be too heavy on the system and another method was proposed. 

A CSR representation of data, and the power method for page rank were the right methods for the given task. A CSR representation of a data point is given by a row pointer array(the amount of non-zero values per row), an array of column indeces, and an array of values.

<img src="https://op2.github.io/PyOP2/_images/csr.svg" width="90%" />

[Image Source](https://op2.github.io/PyOP2/_images/csr.svg)

The power method uses a CSR matrix and a random initialization for the dominant eigenvector. Through an iterative process with a maximum iteration, the pwoer method finds an optimized eigenvector.

### **Results and Observations**


Topic Clustering:

<div>
<img src="https://drive.google.com/uc?id=1JEF7wKYxB7vyNXWjj3-GPYLzhwCc-4P1" alt="topic clustering" width=600 height=600>
</div>

Above is the result of topic clustering for $k=25$ most relevant words for the High Energy Physics catergory. As seen there are many words that are related to more than one topic, while words that are less general tend to be near their own topic.

Recommeder System:

<div>
<img src="https://drive.google.com/uc?id=1-iagqzTbBA6VgkWKFIoQndyAREFUAIZq" alt="recommender" width=350 height=350>
</div>

Above the cosine difference of paper abstracts shows very promising results with most of the results having high correlation. Observing the titles, keywords such as "neutrinos" and "interaction" are picked up between the similar papers.

Paper Query:
<div>
<img src="https://drive.google.com/uc?id=1dxSDICvB-7-KRTrMCQWBRHh9XgW5PjSS" alt="query" width=350 height=350>
</div>

Above displays the results of page querying, it is succesful at finding similar titles given user input. Although the results of cosine difference show the lack of phrase correlation between the query and possible titles. As seen, it finds topics surrounding "neutrinos" and "supernovas" rather than "supernova neutrinos".



We used pretrained word2vec model by google for sentiment analysis.
[Source](https://code.google.com/archive/p/word2vec/)

Now, just to see how word embedding from word2vec works we have to refer the following two images:  
<img src="<img src="https://drive.google.com/file/d/1R2zMGm_EhgW_SHpsq4MCy-uGTkNyZkzf/view?usp=sharing" alt="Abstract to word tuples">" alt="Abstract to word tuples">

[Image Source](https://israelg99.github.io/2017-03-23-Word2Vec-Explained/)
<img src="<img src="https://drive.google.com/file/d/1D8F9WHacF7hOB-PbYbZyCwHscVpfoXsQ/view?usp=sharing" alt="Vector representation of the word tuples">" alt="Example of the vectors of word tuples">

[Image Source](https://www.analyticsvidhya.com/blog/2020/08/top-4-sentence-embedding-techniques-using-python/)

After the word tuples are obtained, they are then replaced by their word vectors, and then at the end we calculated the cosine similarity between the list of tuples of those abstract and got our results.

<center> <img src="https://drive.google.com/uc?id=1R2zMGm_EhgW_SHpsq4MCy-uGTkNyZkzf" alt="Target Word cloud"> </center>
<center> Word cloud of the target abstract</center>

<center> <img src="https://drive.google.com/uc?id=1KJNFWl8H16tgHFW0UtTLdrjvTfoAvPBD" alt="Target"> </center>
<center> Target abstract</center>


<center> <img src="https://drive.google.com/uc?id=1uaTqaD32cs80LxN8_TV0c_dgQGZTtBSp" alt="Most similar Word cloud"> </center>
<center> Word cloud of the most similar abstract</center>

<center> <img src="https://drive.google.com/uc?id=154RKxC2y7yY6Fw5Zc2-7w1GtcnaT0FAk" alt="Most similar"> </center>
<center> Most similar abstract</center>


<center> <img src="https://drive.google.com/uc?id=18rDJicNOoO-MQCHYsREBagSzUYOwXKL6" alt="2nd most similar Word cloud"> </center>
<center> Word cloud of the 2nd most similar abstract</center>

<center> <img src="https://drive.google.com/uc?id=154RKxC2y7yY6Fw5Zc2-7w1GtcnaT0FAk" alt="2nd most similar"> </center>
<center> 2nd most similar abstract</center>


<center> <img src="https://drive.google.com/uc?id=1ECOHi2ojan9JmKJa-fR2d8rg3eOdNN0U" alt="3rd most similar Word cloud"> </center>
<center> Word cloud of the 3rd most similar abstract</center>

<center> <img src="https://drive.google.com/uc?id=154RKxC2y7yY6Fw5Zc2-7w1GtcnaT0FAk" alt="3rd most similar"> </center>
<center> 3rd most similar abstract</center>

The results of Page Rank via power method and CSR representation successfully returned the top ranked papers within the dataset. The eigenvector nonsurprisingly returned very small equivalent components at 8 Significant Figures. Upon further inspection, the components showed differences at the 14th Significant Digit. This is because of the size and sparsity of the matrix. 

<div>
<img src='https://drive.google.com/uc?id=1z-UQ53Akv0YPacndWhkhNAUsvF-tBTSO' alt="ranks" width=600 height=75>
</div>

This result shows that, within this dataset, there are not many papers that have a much higher impact than the rest. Furthermore, due to the papers being in the isolated ArXiv database, the highest ranked paper is not as accurate as could be. The results are not very accurate due to the following;

  1. ### Not all the cited papers within papers were in the dataset. 
    There are unnaccounted for papers outside of the dataset.

  2. ### There are papers outside of the ArXiv database that cited a paper in the dataset.
    The papers within the dataset could be ranked higher.

  3. ### The papers within the ArXiv dataset are not peer reviewed or from credible journals.
    The papers themselves may not be credible.

These observations could be fixed by using a more credible research paper database with more papers such as Elsevier as they offer a paper citaion data API.

Another analysis that could be done on the data would be to build a dictionary from cited paper to papers so that the amount of papers that cite a given paper can be quantized and filtered.

  


### **Conclusion**


The application of NMF factorization as a precendant step for TFIDF is a major improvement to using TF-IDF alone, reducing memory restrictions, while also giving us key information, from a largly sparse dataset. Furthermore, NMF allows us to find similarties in words, in order to make representations of TD-IDF results, in the form of topic querying. Another benefit of NMF is it's use in making a Recommender System, similar to PCA, it finds relationships between the decomposition of the TF-IDF matrix, and uses cosine similarity as a metric for relevance between abstracts.

Considering the large dataset for just a single field hep-ph (High Energy Physics) among the ArXiv, it is clear that most of the data must be loaded beforehand, including NMF results for each field, to make it practical for real implementation. Another issue about TFiDF, it isn't robust to arrangement in documents, as a result, the example query from above "Supernova neutrinos", results it recommendations, that focus more on the "neutrino" aspect, rather than the whole phrase. Although one approach to fixing this would be using bigrams (TFIDF produces 2 word phrases, rather than just single words), neither is the abstract information reliable enough to handle two-word phrases, it doesn't fix the problem of a query with 3+ words.

Possible improvements to the model include using more modern NLP approachs, uniquely Neural Networks which have become the key application when dealing with textual information.

Another possible improvement for word2vec could be using word embedding from Bert model, since it's embedded representation could had better context then word2vec or TF-IDF, the information loss during abstract embedding could be further minimized. Also, we could try euclidea distance rather than cosine similarity for taking the most similar abstract.

In the goal of helping researchers find high impact papers related to their field, TD-IDF, WordVectorizer, and PageRank are effective methods, given a large enough dataset. Datasets with enough papers exist, but for accurate reccomendations and rankings, a dataset of all research papers from all researchers and academic journals would pose as the best source, trivially. 
