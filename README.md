## BD4H-Project
### Leveraging Unstructured Clinical Data for Predictive Modeling: Reproducing and Extending a Readmission Prediction Work
Our goal for this project is to replicate and validate the methodology from "Predicting Heart Failure Readmission from Clinical Notes Using Deep Learning" (Liu et al., 2019). We aim to achieve three objectives: (1) gaining practical experience in integrating unstructured data into predictive models, (2) systematically evaluating the reproducibility and effectiveness of the study’s approach, and (3) exploring lange models, especially large language models (LLMs), for this task, which will expand our team’s expertise in deriving predictive insights from text data. The overall pipeline is shown below. 
<img src='https://github.com/Shayne66/BD4H-Project/blob/main/Graphs/Pipeline.png'>

### Dependencies
All required packages are saved in requirement.txt

### Data
Data is from PhysioNet MIMIC-III dataset (https://physionet.org/content/mimiciii/1.4/). To access the MIMIC-III dataset from PhysioNet, first create an account on the PhysioNet website. After logging in, navigate to the MIMIC-III dataset page and click on Request Access. You will be required to complete the Human Subjects Research Training through the CITI Program. Once you complete the training, submit your certificate of completion. Next, accept the Data Use Agreement. Once your access is granted, you can download the data in formats such as CSV or JSON.

### Instruction
Data Preprocessing: The data preprocessing followed Liu et al. (2019) methodology. First, admissions related to heart failure were filtered using ICD-9 codes, resulting in 14,040 unique admissions. We then excluded the most recent admission for each patient, leaving 3,604 admissions, and further reduced the dataset to 969 cases by limiting the interval between discharge and readmission to less than 30 days. Cases with a discharge summary were retained, and stop words were removed. Finally, undersampling was applied to balance positive and negative cases.

Model Development: Two models, CNN and RF, were developed. Clinical notes were preprocessed and embedded using word vectors from 'PubMed-and-PMC-w2v.bin'. The CNN model consisted of three convolutional layers with kernel sizes of 1, 2, and 3, followed by max pooling and a softmax layer for classification. The RF model used TF-IDF weights, and different feature counts were tested for optimization.

LLM-based Prediction: For leveraging LLMs in predictions, DSPy was intially chosen as the orchestration layer to adjust prompts and weights. We integrated open-source models (e.g., Meditron, BioMistal) and proprietary LLM APIs (e.g., GPT, Gemini). We opted for GPT4.0, which performed well in terms of speed and accuracy. DSPy was used to optimize the model with few-shot learning, but biases toward positive predictions were observed. The workflow was then switched to LangChain for more control, and achieved a reasonable performance. Finally, we fine-tuned a pre-trained BERT model (DistilBERT) and ClinicalBERT for improved prediction performance, given the domain-specific focus on clinical text.

### Noteboooks
  1. Preprocessing (https://github.com/Shayne66/BD4H-Project/blob/main/Code/0_Preprocessing.ipynb)
  2. CNN & Random Forest (https://github.com/Shayne66/BD4H-Project/blob/main/Code/1.1_CNN_RanForest_final.ipynb)
  3. LangChain + GPT4 (https://github.com/Shayne66/BD4H-Project/blob/main/Code/2.1_LLM_LangChain.ipynb)
  4. BERT (https://github.com/Shayne66/BD4H-Project/blob/main/Code/2.2_LLM_BERT.ipynb)

### Results
General-Readmission
<div style="text-align:center;">

| Model                                 | Precision | Recall | F1-score | Accuracy
|---------------------------------------|----------|---------|----------|---------|
| CNN                                   | 0.641    | 0.686   | 0.663    | 65.16%  |
| Random Forest                         | 0.683    | 0.707   | 0.694    | 67.98%  |
| Few-shot Learning                     | 0.625    | 1.00    | 0.769    | 70.0%   |
| Zero-shot Learning & Chain-of-thought | 0.625    | 1.00    | 0.769    | 70.0%   |
| DistilBERT                            | 0.635    | 0.667   | 0.645    | 63.2%   |
| ClinicalBERT                          | 0.697    | 0.787   | 0.666    | 71.1%   |

</div>

30-day-Readmission
<div style="text-align:center;">

| Model                                 | Precision | Recall | F1-score | Accuracy
|---------------------------------------|----------|---------|----------|---------|
| CNN                                   | 0.676    | 0.781   | 0.725    | 70.47%  |
| Random Forest                         | 0.630    | 0.723   | 0.673    | 65.80%  |
| Few-shot Learning                     | 1.00     | 0.20    | 0.333    | 60.0%   |
| Zero-shot Learning & Chain-of-thought | 0.555    | 1.00    | 0.714    | 60.0%   |
| DistilBERT                            | 0.622    | 0.642   | 0.637    | 63.2%   |
| ClinicalBERT                          | 0.680    | 0.839   | 0.739    | 70.8%   |

</div>
