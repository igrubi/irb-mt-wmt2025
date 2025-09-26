# IRB-MT-WMT2025
Code and data of machine translation workflow of IRB-MT team used for WMT25 general and terminology tasks. 

## Usage

### Preparation
Install all dependencies:
`python -m pip install -r requirements.txt`

Open `settings.py` and add your HuggingFace token.
Adjust directory and file paths to your needs.

### Running IRB-MT solution for WMT25 General task
```bash
python wmt_preprocess.py -t wmt25-general
python wmt_translate.py -lc --task wmt25-general
python wmt_submit.py --task wmt25-general
```

### Running IRB-MT solution for WMT25 Terminology Track 1 task
```bash
python wmt_preprocess.py -t wmt25-terminology-track1
python wmt_translate.py -lc --task wmt25-terminology-track1
python wmt_combine_translations.py --task wmt25-terminology-track1 --action translation
python wmt_revise.py -lc --task wmt25-terminology-track1
python wmt_combine_translations.py --task wmt25-terminology-track1 --action revision
python wmt_submit.py --task wmt25-terminology-track1
```

### Running IRB-MT solution for WMT25 Terminology Track 2 task
```bash
python wmt_preprocess.py -t wmt25-terminology-track2
python wmt_translate.py -lc --task wmt25-terminology-track2
python wmt_combine_translations.py --task wmt25-terminology-track2 --action translation
python wmt_revise.py -lc --task wmt25-terminology-track2
python wmt_combine_translations.py --task wmt25-terminology-track2 --action revision
python wmt_submit.py --task wmt25-terminology-track2
```

## Entrypoint Scripts

### wmt_preprocess.py - Data Preparation
The script for data preparation of original test datasets.

```bash
# Preprocess data for all tasks
python wmt_preprocess.py
# Preprocess data for one task at a time
python wmt_preprocess.py -t wmt25-general 
python wmt_preprocess.py -t wmt25-terminology-track1
python wmt_preprocess.py -t wmt25-terminology-track2
```

### wmt_translate.py - Translation
```bash
# Show help
python wmt_translate.py -h
# Example runs for each task 
python wmt_translate.py -lc --task wmt25-general
python wmt_translate.py -lc --task wmt25-terminology-track1
python wmt_translate.py -lc --task wmt25-terminology-track2
```

### wmt_revise.py - Revision
```bash
# Show help
python wmt_revise.py -h
# Example runs for each task (used only for terminology tasks)
python wmt_revise.py -lc --task wmt25-terminology-track1
python wmt_revise.py -lc --task wmt25-terminology-track2
```

### wmt_evaluate.py - Evaluation
The script for evaluating translations.
```bash
# Show help
python wmt_evaluate.py -h
# Example runs for each task (used only for terminology tasks)
python wmt_evaluate.py --task wmt25-terminology-track1
python wmt_evaluate.py --task wmt25-terminology-track2
```

### wmt_combine_translations.py - Combine Translations
This script combines output translations of multiple translation agents into single HF dataset.
```bash
# Show help
python wmt_combine_translations.py -h
# Example runs for each task (used only for terminology tasks)
python wmt_combine_translations.py --task wmt25-terminology-track1 --action translation
python wmt_combine_translations.py --task wmt25-terminology-track2 --action translation
python wmt_combine_translations.py --task wmt25-terminology-track1 --action revision
python wmt_combine_translations.py --task wmt25-terminology-track2 --action revision
```

### wmt_submit.py - Generate Submission
The script for creating submission files.
```bash
# Show help
python wmt_submit.py -h 
# Example runs for each task
python wmt_submit.py --task wmt25-general 
python wmt_submit.py --task wmt25-terminology-track1
python wmt_submit.py --task wmt25-terminology-track2
```

### run.sh - Python Script Runner

Universal entrypoint for running Python scripts with proper environment setup.

```bash
./run.sh [python_script]
./run.sh -h                 # Show help and usage
```

### test.sh - Pytest Runner

Entrypoint for running pytest with flexible test selection and environment setup.

```bash
./test.sh [test_pattern] [pytest_options...]
./test.sh -h                # Show help and usage
```


## Data
All data is in the `data` directory. It contains the original test datasets and the translation outputs of our systems for two WMT25 tasks:
- General MT (https://www2.statmt.org/wmt25/translation-task.html)
- Terminology Translation (https://www2.statmt.org/wmt25/terminology.html)

Original test datasets can be found at:
- General MT: `data/wmt25-general/inputs/jsonl`
- Terminology Translation:
    - Track 1: `data/wmt25-terminology-track1/inputs/jsonl`
    - Track 2: `data/wmt25-terminology-track2/inputs/jsonl`

Translation outputs of our systems can be found at:
- General MT: `data/wmt25-general/translations`
- Terminology Translation:
    - Track 1: `data/wmt25-terminology-track1/translations`
    - Track 2: `data/wmt25-terminology-track2/translations`


### Data Preparation BUG
In our submissions there was a data preparation bug for translations from Traditional Chinese to English in Terminology Translations Track 2 task. The error occurred when projecting document-level terminology map to the paragraph level. This operation, performed because our system operates on the paragraph level, used whitespace-delimiters for term matching. This approach, suitable for European languages, is not correct for Chinese texts.

To reproduce this bug only one line of code needs to be commented out.
It is a line in the script `wmt_preprocess.py`, inside of `get_search_pattern` method, a line with this code:
`if lang=='zh': pattern = escaped_substring`

All translations of both, submitted and debugged version of our system can be found at:
- Submitted: `data/wmt25-terminology-track2/translations/MeGuMa[submitted]`
- Submitted: `data/wmt25-terminology-track2/translations/MeGuMa[debugged]`

## Licence
The script located at `nlp_tools/SentAligner.py` is based on the original SentAlign implementation by Steinþór Steingrímsson et al. SentAlign is released under the Apache License, Version 2.0. Original code is available on the link: https://github.com/steinst/SentAlign

The rest of the repository is available under the MIT license.