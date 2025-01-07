# NER-mountain-name-detection
## Dataset
For this task, i created a dataset using Gemini (dataset.ipynb)
I asked him to generate texts in different topics, formats and sizes, marking the mountain names with []. For example [Everest].
When I was testing base model, I noticed, that it poorly detects names of locations, if the name is lowercase. This suggests that the model primarily focuses on the capitalization of the first letter rather than the word's context. To avoid this hint, I lowercase half of the texts in dataset. Also I randomly changed half of the mountain names in dataset to a different mountain name from a predefined list. That reduces the quality of texts, but encourages the model to better understand context of target entity.
The Gemini documentation specifies the number of requests per minute as 10, but from my understanding, this number is dynamic. We will comply with this restriction, ignoring cases when Google returns an error about exceeding resources.
The hardest challenge was to make Gemini return texts, correctly marking mountain names. Failure to do so will have a bad effect on tuned model. I achieved acceptable quality (different common names in different contexts), but AI never does things perfectly, so still happens marking mistakes.
## Core model
For the core model i used bert-base-NER, which can be found https://huggingface.co/dslim/bert-base-NER. This model is finetuned to recognize four types of entities: location (LOC), organizations (ORG), person (PER) and Miscellaneous (MISC). Because it already recognizes common names, especially locations, and understands the distinctions between these types of entities, it makes the training process easier.
## Tuned model
Fine tuned model takes tokenized sequence, attention mask and token type ids (which are set to 0) and returns labels for each token, where 1 indicates the beginning of a mountain name, 2 indicates being inside a mountain name, 0 - any other token.
Evaluation (Green for correct predictions, Red for falsely predicted mountains, Yellow for missed true mountains):

![image](https://github.com/user-attachments/assets/56898169-82ed-4282-b018-a94fcaa6ca6d)

The model demonstrates almost perfect accuracy in labeling mountain names, even when faced with challenging scenarios involving many other common names and instances of lowercase text.

To use the model, ensure you are using legacy Keras. You can install the necessary package with: pip install tf-keras. Before importing TensorFlow in your Python script, add the following line: import os; os.environ['TF_USE_LEGACY_KERAS'] = '1'. Then, load the model using: tf.keras.models.load_model('mount_ner_model'). The tokenizer can be loaded from the transformers library using: transformers.AutoTokenizer.from_pretrained("dslim/bert-base-NER").


