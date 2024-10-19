+++
author = "FERMATRICA"
title = "Documentation"
date = "2024-10-16"
description = "Quick intro, Guides, API Reference"
tags = [
    "Docs"
]
+++

![Documentation](/docs/intro.jpg)

## How to start

FERMATRICA contains three publicly available reposotories, install all of them for most scenarios.

1. Clone repos

  ```shell
  cd [my_fermatrica_folder]
  git clone https://github.com/FERMATRICA/fermatrica_utils.git 
  git clone https://github.com/FERMATRICA/fermatrica.git
  git clone https://github.com/FERMATRICA/fermatrica_rep.git 
  ```

2. Install each via `pip install [path]`. We recommend to create new virtual environment for your FERMATRICA projects.

  ```shell
  cd [my_fermatrica_folder]
  pip install [my_fermatrica_folder]/fermatrica_utils
  pip install [my_fermatrica_folder]/fermatrica
  pip install [my_fermatrica_folder]/fermatrica_rep
  ```

3. Read [the guide](/fermatrica/guides/FERMATRICA_and_MMM_instruction.html) and try the [sample](https://github.com/FERMATRICA/fermatrica/tree/master/samples).

4. Troubleshooting. Most frequent issue is version incompatibility between Python packages used by FERMATRICA. Check the error message and try to upgrade / downgrade the package in question. If it doesn't help, feel free to raise an issue.

5. If broader consulting is required, contact us via contact info listed in the footer.


## Guides

Guides help you to dive both into marketing mix modeling pipeline and FERMATRICA itself.

- [FERMATRICA Marketing Mix Modeling Intro](/fermatrica/guides/FERMATRICA_and_MMM_instruction.html)


## API References

API references provide info about class and function interfaces in FERMATRICA packages / references. What does the specific function does, what arguments expects and what value returns.

- [FERMATRICA](/fermatrica/api/index.html): modeling, predicting, optimization

- [FERMATRICA_REP](/fermatrica_rep/api/index.html): analysis, reporting, visuals

- [FERMATRICA_UTILS](/fermatrica_utils/api/index.html): general / low-level functionality for FERMATRICA




