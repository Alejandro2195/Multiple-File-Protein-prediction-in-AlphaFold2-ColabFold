# Multiple-File-Protein-prediction-in-AlphaFold2-ColabFold
### View as HTML: https://alejandro2195.github.io/Multiple-File-Protein-prediction-in-AlphaFold2-ColabFold/
### The provided code snippet forms a critical component of an automated script aimed at facilitating the prediction of protein structures using the AlphaFold model within a Google Colab environment. The script is tailored to handle the simultaneous upload of multiple input files, each containing multiple protein sequences for prediction. The code initiates by configuring preferences, such as determining whether images should be displayed during execution, and importing essential modules. It performs checks to identify specific GPU types and adapts environment variables accordingly.

### An array of callback functions is defined, enabling the visualization of multiple sequence alignments and the predicted protein structures. As the script progresses, it processes the uploaded protein sequences from multiple files, generating unique job names and establishing directories to store the forthcoming results. In a systematic loop, the script iterates through these designated job names. For each job, it configures prediction parameters and invokes the AlphaFold model to predict protein structures for the corresponding sequences.

### This integrated system is meticulously designed to offer a seamless user experience. It not only streamlines the prediction process but also ensures the entire pipeline is automated – from the initial upload of files containing protein sequences to the final step of packaging and saving the prediction results. This comprehensive automation, coupled with interactive widgets and the utilization of the ColabFold package, underscores the script's capability to empower users with efficient and hands-free protein structure prediction and result management.

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
warnings.simplefilter(action='ignore', category=FutureWarning)
from Bio import BiopythonDeprecationWarning
warnings.simplefilter(action='ignore', category=BiopythonDeprecationWarning)
from pathlib import Path
from colabfold.download import download_alphafold_params, default_data_dir
from colabfold.utils import setup_logging
from colabfold.batch import get_queries, run, set_model_type
from colabfold.plot import plot_msa_v2
import os
import numpy as np
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

## 5. Setting Up PDBFixer Import Path (if condition):
This part of the code seems to be specific to handling the Amber molecular dynamics package (use_amber) and adjusting the Python path to include the necessary packages.

```{python}
if use_amber and f"/usr/local/lib/python{python_version}/site-packages/" not in sys.path:
    sys.path.insert(0, f"/usr/local/lib/python{python_version}/site-packages/")
```

## 6. Input Features Callback Function:
This function input_features_callback is responsible for displaying an MSA plot if display_images is set to True.

```{python}
def input_features_callback(input_features):  
    if display_images:    
        plot_msa_v2(input_features)
        plt.show()
        plt.close()
```

## 7. Prediction Callback Function:
This function prediction_callback handles the visualization of predicted protein structures. It uses the plot_protein function to create a plot if display_images is True and the prediction is not relaxed.

```{python}
def prediction_callback(protein_obj, length,
                        prediction_result, input_features, mode):
    model_name, relaxed = mode
    if not relaxed:
        if display_images:
            fig = plot_protein(protein_obj, Ls=length, dpi=150)
            plt.show()
            plt.close()
```

## 8. Processing Uploaded Files:
This section deals with processing uploaded protein sequences. It reads the uploaded files, processes the sequences, generates a unique job name based on the input file, and prepares a directory to save the results.

```{python}
uploaded_files = files.upload()

for input_filename, uploaded_content in uploaded_files.items():
    # ... (code to process uploaded content and create jobname)
```

## 9. Running Protein Structure Predictions:
This part iterates through the list of job names and runs protein structure predictions using the AlphaFold model. It sets up various parameters for the prediction process.

```{python}
for jobname in jobname_list:
    # ... (code to set up prediction parameters and run predictions)
```

## 10. Packaging and Downloading Results:
Finally, this section iterates through the job names, packages the prediction results into a ZIP file, and provides options for downloading the ZIP file or uploading it to Google Drive.

```{python}
for jobname in jobname_list:
    # ... (code to package results, download results, and optionally upload to Google Drive)
```

Please note that the code assumes the availability of certain modules, functions, and variables that are defined elsewhere. To fully understand how this code fits into the larger context and its intended use, you would need to examine the entire program or script it's a part of.
