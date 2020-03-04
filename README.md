# U.S. Politcal Candidate Tweet Analysis

This project was a look at the tweets of the various potential U.S. 2020 democratic candidates as well as current U.S. president Donald Trump as of Feb 26, 2020. The project took into account the last 1000 tweets from each of the seven candidates and evaluated theirr comments to establish the hierarchy of the political issues they were advocating for, as well as some of the most common phrases used to describe these issues.

Prerequisites
For this project, most all of the data extraction, cleaning, manipulation, and analysis was done using python version 3.7.3. The data was sourced from the twitter api for which you can apply for developer permissions, and using the free Tableau Public visualization software for the final interactive visual product that you can find here: https://public.tableau.com/profile/mitchell.joseph.laferla#!/vizhome/TweeterinChief_15826376792510/Dashboard1

## Instructions

Part 1: Data Extraction
The data was extracted via the twitter api. You have to apply for developer permissions to recieve access to the API which you can do here: https://developer.twitter.com/en/apply-for-access. I used tweepy to navigate the API, which is a really easy to use python library. I returned the data and put it into a pandas dataframe, however, it automatically returns json per usual. 

```
# developer permissions
consumerKey = 'XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX'
consumerSecret = 'XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX'

auth = OAuthHandler(consumerKey, consumerSecret)

accessToken = 'XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX'
accessTokenSecret = 'XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXx'

auth.set_access_token(accessToken, accessTokenSecret)
api = tw.API(auth, wait_on_rate_limit=True, wait_on_rate_limit_notify=True)
```

Part 2: Data preprocessing, cleaning, formatting
Twitter returns the text data with a bunch of junk mixed in as well that makes it difficult to analyze. I was able to clean it up easily with nltk. This function took care of most of the irregulaties within the text:


```
def process_tweet_text(text):
    """cleans data for clarity and uniformity"""
    text = re.sub(r"http\S+", "", text)
    text = contractions.fix(text)
    text = text.replace('\n', ' ')
    text = text.replace('—', ' ') # two types of hyphens
    text = text.replace('-', ' ')
    text = re.sub('@[A-Za-z0-9_]\S+', '', text)
    text = re.sub('#[A-Za-z0-9_]\S+', '', text)
    text = ''.join([c for c in text if c not in string.punctuation])
    text = re.sub('[^a-zA-Z0-9 -]', '', text)
    return text
```

Part 3: Text Analysis
Here I again used nltk to remove stopwords and tokenize the text before I looked at ngrams on the text which found the most common combinations of two or three word combinations for each candidate. This was how I itentified the most common phrases for each candidate. 


```
def tokenize_ngrams(candidate):
    
    """This tokenizes the text as well as calculates ngrams to find common phrases"""
    
    # put all of the tweets together in an object called 'text_all'
    text_all = candidate['processed_tweets'].str.cat(sep=' ')

    # intialize tokenizer and tokenize all of the text
    tokenizer = RegexpTokenizer('\s+', gaps=True)
    tokens = tokenizer.tokenize(text_all.lower())

    # eliminate stop words from text
    cached_stop_words = stopwords.words("english")
    filtered_tokens = [w for w in tokens if not w in cached_stop_words] 
    
    # find most common phrases of 2 & 3 words and store them together in ngrams
    bi_gram = Counter(list(ngrams(filtered_tokens, 2)))
    tri_gram = Counter(list(ngrams(filtered_tokens, 3)))
    ngram = bi_gram.most_common(25) + tri_gram.most_common(25)

    # reate dataframe to store data
    results = pd.DataFrame(ngram, columns=['phrase', 'count'])
    
    return results
```
Step 4: Data visualization 
Using Tableau Public which is the free version of a great data viz software which I highly recommend, I was apply to create a lovely visual to represent my findings for others to explore. The viz can be found here: https://public.tableau.com/profile/mitchell.joseph.laferla#!/vizhome/TweeterinChief_15826376792510/Dashboard1


## Built With
Twitter API - API for data source
Tweepy - Easy to use python library for acessing the Twitter API
NLTK - Python library for text cleaning, and analysis
Tableau Public - Free data visualization software

## Authors
Mitchell Laferla - mitchell@laferla.us - https://public.tableau.com/profile/mitchell.joseph.laferla#!/
