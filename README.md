# Multiple-File-Protein-prediction-in-AlphaFold2-ColabFold
### View explanation as HTML: https://alejandro2195.github.io/Multiple-File-Protein-prediction-in-AlphaFold2-ColabFold/
### Test the code in a modified ColabFold notebook: https://colab.research.google.com/github/Alejandro2195/Multiple-File-Protein-prediction-in-AlphaFold2-ColabFold/blob/main/AlphaFold2%20modified%20code%20for%20multiple%20protein%20prediction.ipynb

### The provided code snippet forms a critical component of an automated script aimed at facilitating the prediction of protein structures using the AlphaFold model within a Google Colab environment. The script is tailored to handle the simultaneous upload of multiple input files, each containing multiple protein sequences for prediction. The code initiates by configuring preferences, such as determining whether images should be displayed during execution, and importing essential modules. It performs checks to identify specific GPU types and adapts environment variables accordingly.

### An array of callback functions is defined, enabling the visualization of multiple sequence alignments and the predicted protein structures. As the script progresses, it processes the uploaded protein sequences from multiple files, generating unique job names and establishing directories to store the forthcoming results. In a systematic loop, the script iterates through these designated job names. For each job, it configures prediction parameters and invokes the AlphaFold model to predict protein structures for the corresponding sequences.

### This integrated system is meticulously designed to offer a seamless user experience. It not only streamlines the prediction process but also ensures the entire pipeline is automated – from the initial upload of files containing protein sequences to the final step of packaging and saving the prediction results. This comprehensive automation, coupled with interactive widgets and the utilization of the ColabFold package, underscores the script's capability to empower users with efficient and hands-free protein structure prediction and result management.

### If you want to test the code you can do it with either AlphaFold or ColabFold, you just have to make sure to install the dependencies of the latter before executing said code.


The code is quite complex and is used to manage the process of running AlphaFold predictions on protein sequences, handling various configurations, and saving the results. It involves managing input data, template options, MSA modes, and other advanced settings to customize the predictions.
Let's break down the code step by step:

## 1. Display Images Setting:
This line creates a boolean variable display_images that can be toggled using Colab's interactive widgets. It will be used to determine whether to display images during the execution of the code.

```{python}
display_images = True #@param {type:"boolean"}
```

## 2. Importing Modules:
This section imports various Python modules and classes required for the rest of the code. It includes modules for handling warnings, working with file paths, downloading AlphaFold parameters, setting up logging, managing batches of protein sequences, and plotting Multiple Sequence Alignments (MSAs).

```{python}
import sys
import warnings
...
import matplotlib.pyplot as plt

```

## 3. Checking for GPU Type and Adjusting Environment:
This section checks if a specific GPU type (Tesla K80) is available. If it's found, it prints a warning and adjusts certain environment variables related to memory management.

```{python}
try:
    K80_chk = os.popen('nvidia-smi | grep "Tesla K80" | wc -l').read()
except:
    K80_chk = "0"
    pass
if "1" in K80_chk:
    # ... (code to modify environment variables)

```

## 4. Additional Imports and Configuration:
Here, additional functions and classes are imported for protein visualization, working with file paths, and plotting using Matplotlib.

```{python}
from colabfold.colabfold import plot_protein
from pathlib import Path
import matplotlib.pyplot as plt

```

##5. User Input Section:
Here, the user is prompted to provide input. They need to input a protein sequence, a job name, a number of models to use for relaxation, and a template mode. The parameters are adjusted using annotations (@param). Remember that the query_sequence is not important as a parameter, since the user will then be prompted to upload their sequence files.

```{python}
query_sequence = 'PIAQIHILEGRSDEQKETLIREVSEAISRSLDAPLTSVRVIITEMAKGHFGIGGELASK' #@param {type:"string"}
jobname = 'test' #@param {type:"string"}
num_relax = 0 #@param [0, 1, 5] {type:"raw"}
template_mode = "none" #@param ["none", "pdb70","custom"]
...

```


## 5. Setting Up PDBFixer Import Path (if condition):
This part of the code seems to be specific to handling the Amber molecular dynamics package (use_amber) and adjusting the Python path to include the necessary packages.

```{python}
if use_amber and f"/usr/local/lib/python{python_version}/site-packages/" not in sys.path:
    sys.path.insert(0, f"/usr/local/lib/python{python_version}/site-packages/")
```

## 6. Preprocessing Input:
The input protein sequence is cleaned of whitespace characters. The job name is cleaned up and hashed with the sequence to create a unique identifier for the job.

```{python}
query_sequence = "".join(query_sequence.split())
basejobname = "".join(jobname.split())
basejobname = re.sub(r'\W+', '', basejobname)
jobname = add_hash(basejobname, query_sequence)

```

## 7. Creating and Managing Directories:
A directory is created with the job name to save results. If a directory with the same name already exists, a new name with an appended number is generated to avoid overwriting existing data.

```{python}
if not check(jobname):
    n = 0
    while not check(f"{jobname}_{n}"): n += 1
    jobname = f"{jobname}_{n}"
os.makedirs(jobname, exist_ok=True)

```

## 8. Saving Query Sequences:
The protein sequence is saved to a CSV file within the job directory.

```{python}
queries_path = os.path.join(jobname, f"{jobname}.csv")
with open(queries_path, "w") as text_file:
    text_file.write(f"id,sequence\n{jobname},{query_sequence}")

```

## 9. Template Handling:
Depending on the chosen template mode, template paths are managed accordingly. Templates provide structural information for modeling.

```{python}
if template_mode == "pdb70":
    use_templates = True
    ...
elif template_mode == "custom":
    custom_template_path = os.path.join(jobname,f"template")
    ...
else:
    custom_template_path = None
    use_templates = False

```

## 10. MSA (Multiple Sequence Alignment) Handling:
This section determines the path to the multiple sequence alignment (a3m) file based on the chosen MSA mode.

```{python}
...
if "mmseqs2" in msa_mode:
    a3m_file = os.path.join(jobname,f"{jobname}.a3m")
elif msa_mode == "custom":
    a3m_file = os.path.join(jobname,f"{jobname}.custom.a3m")
    ...
else:
    a3m_file = os.path.join(jobname,f"{jobname}.single_sequence.a3m")

```

## 11. Advanced Settings:
Advanced settings related to the AlphaFold model are provided, including the model type, number of recycles, dropout usage, and more.

```{python}
model_type = "auto" #@param ["auto", "alphafold2_ptm", ...]
...
save_recycles = False #@param {type:"boolean"}
dpi = 200 #@param {type:"integer"}

```

## 12. Loop for Running Predictions:
This loop iterates through the job names and runs the AlphaFold prediction process for each job. The results are then compressed into a ZIP file.

```{python}
for jobname in jobname_list:
    ...
    results = run(
        queries=queries,
        ...
    )
    results_zip = f"{jobname}.result.zip"
    os.system(f"zip -r {results_zip} {jobname}")

```

## 13. Packaging and Downloading Results:
After the predictions are completed, the results are packaged into a ZIP file and then downloaded. If enabled, the results are also uploaded to Google Drive.

```{python}
...
for jobname in jobname_list:
    ...
    result_zip = f"{jobname}.result.zip"
    os.system(f"zip -r {result_zip} {jobname}")
    files.download(result_zip)

```

Please note that the code assumes the availability of certain modules. To fully understand how this code fits into the larger context and its intended use, you would need to examine the entire program or script it's a part of. If you want to test the code with your data, you can do so via the modified ColabFold link provided at the beginning of the document.
