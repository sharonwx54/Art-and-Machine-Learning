# 11-667 Homework 3

other_scripts.zip includes 10 files.

1. EDA.ipynb
- The jupyter notebook contains codes for data exploration used to answer Q1 and Q2, including plotting the required charts and creating the suffixs.

2. config_clean.yaml and config.yaml
- The two yaml files are used to train the model based on original and cleand datasets. Note that I use DecoderLM model, which is the model I wrote in HW2. 

3. model.py, utils.py and train.py
- The three python scripts are copied from HW2. There is no change to the model.py and utils.py, and I add one argument "model_on_original" to run train.py. The argument takes in a string, and if the string is "True", the codes would train a DecoderLM model on original datasets using config.yaml. Otherwise, the codes would train a DecoderLM model on the cleaned datasets using config_clean.yaml.
- The original datasets are saved in the "data" folder under train_orig_clean.npy and val_orig_clean.npy. The cleaned datasets are saved in the "data" folder under train_clean.npy and val_clean.npy. Note that these npy files only store tokens of the text field from the fetched datasets. 
- To run the training code, "python train.py config.yaml True" or "python train.py config_clean.yaml False"
- Note you should have a folder named data in the directory where train.py and yaml files are saved.
- Note that the train.py requires some external packages other than those in requirement.txt. 

4. get_data.py
- This is the provided file to fetch datasets. It is called in EDA.ipynb and cleaning.py to fetch the datasets and apply cleaning and tokenization process.

5. profanity_wordlist.txt
- This is the list of profane words provided in the python package better_profanity. The original file could be found: https://github.com/snguyenthanh/better_profanity/blob/master/better_profanity/ profanity_wordlist.txt. 
- In cleaning.py, the clean_other function refers to this list and removes words that appear in the list. Note that instead of using better_profanity.censor function to remove profane words, which is very slow, I use Regex to compile the list of profane words and replace the profane words by empty string. This way is much faster and also avoids installing additional packages other than those in requirement.txt.

6. perplexity.py
- This file runs perplexity calculation for the five texts asked in question 5.1.1 Evaluating specific examples. To run the file, "python perplexity.py config_clean.yaml model_clean.pt" or "python perplexity.py config.yaml model.pt"

7. toxicity_score.py
- This file runs call to PerspectiveAPI and calculate the mean toxicity on the generated tokens based on RealToxicityPrompts. To run the file, "python toxicity_score.py" will do everything. However, you should input your API key in the file first, and must ensure that "generation_orig.jsonl" and "generation_clean.jsonl" exist in the data folder. 

cleaning.py

This file runs codes for cleaning the dataset. save_concat_clean_data() function runs everything at once.
1. fetching original train and val datasets
2. tokenize the text field in the original datasets and save the tokens in two npy files. Note the tokenization add EOS at the end of each text. 
3. apply cleaning to the text field of datasets, including removing non-English words, removing PII information, and removing words in profane list. Note that when removing non-English words, the function used is filter_noneng_partial() instead of filter_noneng(). The former function only removes words that are non-English, rather than dropping the entire text. However, if more than 50% of words in the texts are considered non-English, the function would drop the entire text and return an empty string.
4. tokenize the cleaned texts and save the tokens into npy files. In addition, the cleaned validation dataset (including all fields, not just the text field) are saved to dataset object in "cleaned_data.arrow". 

generate.py

This file runs code to generate tokens based on provided prefixes, mainly copied from codes in HW2. Changes including adding new argument specifying the model to use, and using yaml to read config rather than using OmegaConf, as OmegaConf is an external package not included in the requirement.txt.

