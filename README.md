# pipeline-turbo

pipeline-turbo is a package that will accelerate your processing pipeline. It works with the multi-threading concept in the background. It has been successful in both CPU and GPU tasks.

The only pre-requisite is to load the function running for a single process and adjust the threads according to your resource availability.
![turbo](images/graph.jpg | width=100)

Read more about threading here: https://www.activestate.com/blog/how-to-manage-threads-in-python/

## Installation

Use the package manager [pip](https://pip.pypa.io/en/stable/) to install pipeline-turbo

```bash
pip install pipeline-turbo
```

## Example Usage

```python
# let's get some data for processing
sentences = ["Nevertheless, Trump and other Republicans have tarred the protests as havens for terrorists intent on destroying property.", "Billie Eilish issues apology for mouthing an anti-Asian derogatory term in a resurfaced video.", "Christians should make clear that the perpetuation of objectionable vaccines and the lack of alternatives is a kind of coercion.", "There have been a protest by a group of people", "While emphasizing he’s not singling out either party, Cohen warned about the danger of normalizing white supremacist ideology."]

sentences = sentences * 100

# Create your process - Here is an example of running a bias detection model across few sentences
from transformers import AutoTokenizer, TFAutoModelForSequenceClassification
from transformers import pipeline
tokenizer = AutoTokenizer.from_pretrained("d4data/bias-detection-model")
model = TFAutoModelForSequenceClassification.from_pretrained("d4data/bias-detection-model")
classifier = pipeline('text-classification', model=model, tokenizer=tokenizer) 

def bias_classification(text):
    out = classifier(text)
    #label_ = out[0]['label']
    #probability_ = out[0]['score']
    
    return out 

# without turbo, looping across all the sentences - Normal Method
out_list = []
for sent in sentences:
    out = bias_classification(sent)
    out_list.append(out)

# with turbo, call the turbo_threading function
"""
1. Each of the item in 'sentences' list has to be iterated and that has to be defined as the first argument
2. It should be followed by the function and its other arguments (if there are additional arguments for the function)
3. Define the thread based on your resource availability (5, 10 would be ideal based on your resources)
"""
from pipeline_turbo.turbo import turbo_threading # import the turbo threading function which does the magic
turbo_out = turbo_threading(sentences,bias_classification, num_threads=5)

"""
Note: You can pass any number of arguments inside the function, but the iterable list has to be defined first
The performance varies based on the processing speed of your machine/compute
"""
```

## About
This package is created by Deepak John Reji, Afreen Aman. It was first used to speed up some deep learning pipeline projects and later made it open source. This can be used for normal CPU process as well.

## License
[MIT](https://choosealicense.com/licenses/mit/) License
