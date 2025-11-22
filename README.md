## COMPARE-ALL-THE-NAMES
An efficient Python package for all-to-all comparisons of large datasets of names


### The Why
Sometimes you have a lot of different records and you want to identify which names could correlate to the same individual across multiple records.


### The Problem
Names are really tricky. Sometimes the words are in a different order. Sometimes words are shortened to initials. Other times, they are replaced by nicknames that are non intuitive (e.g. William and Bill). Sometimes a word is just spelled really weird. These are normal, daily occurances in historic data that make life frustrating.


### The Second Problem
Even if you could take into account all this, there is still the issue that you have potentially hundreds of thousands of records, and comparing every single name to every other name is stupid. You could easily compute til the end of time.


### The Solution: `compare-all-the-names`
This package solves both those problems. Name comparison is now easy and really really fast for large datasets. By bucketing the names, we are able to efficiently identify which ones could potentially be a match, and validate from there. 

All of this is done for you. You just need to `pip install compare-all-the-names` and use a script similar to this.

```python
from compare_all_the_names import compare_all_names

my_list = [
    'john c weymouth',
    'christian weymouth',
    'weymouth jean',
    'weymouth jeanette',
    'bobby weymouth',
    'robert w',
    'charles weymouth',
    'charlie w',
    'charlie c w',
    'charlie james',
    'charlie james c weymouth',
]

compare_all_names(my_list)
```

The function `compare_all_names` takes an input of `list[str]`. It runs and creates an output file in your temp dir, printing its location out for you. This is required, given you are likely going to run this on massive lists where many names will correlate to one another. Due to current system memory constraints, this is the best solution.


### Expected Performance
A lot went into making this fast and somewhat memory efficient (Golang bin under the hood, interesting inverted bucketing algorithm, etc). A modern computer should be able to take a dataset of a million names and find all the matches in under a day. I was able to do so on a laptop in 4 hours, but your mileage will heavily vary on how generic your names are, how common certain words are, and how many words exist per name on average.

Your output will be names that at least share two words that match, or three if both names contain at least three words.


### Refining output
I have also included a couple of functions to refine the output. Use `add_scrutiny` and a scoring function like `simple_scoring_func` to enable taking your results and limiting them to what you find is ideal.

```python
raw_matches_filepath = compare_all_names(all_names)
print(f"Raw matches saved to: {raw_matches_filepath}")

print("\nApplying scoring and filtering...")
filtered_filepath = add_scrutiny(
    original_filepath=raw_matches_filepath,
    scoring_func=simple_scoring_func,
    threshold=70.0  # Only keep matches with score >= 70
)
print(f"Filtered matches saved to: {filtered_filepath}")
```

### Caveats
Just because two names match does not mean they are the same person represented in two different records. Use other record data and understand that some names are generic. Refining output in some way should probably be part of your data pipeline.

If all the names are really really similar, the bucketing that is done under the hood won't help much. It will be very very slow.

The `simple_scoring_func` I created is not yet optimized. It isn't the worst, but it is still pretty bad. I am planning on making it better.

Another thing to note- running `compare_all_names` and other functions currently prints a lot of output, especially loading bars and such for good time estimates. I should probably add an arg that removes output, but I don't want to touch the Go/Python integration again, so I don't know when I'll get to it.

The output is also currently just tuples in a `.txt` file, which isn't ideal. I'll consider doing something smarter, like `.jsonl`, as each match output could represent a lot of not a lot of data using a future version of `add_scrutiny`, but we'll get there when we get there.

ENJOY!