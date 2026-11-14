# Pay Later: Who Will Miss the Next Payment?

Data Engineering & Science Club, Applied Projects Committee, KFUPM. Term 262. Prediction case study.

Buy-now-pay-later and credit card services let customers spread a purchase over several months. For the company, the risk is the customer who stops paying. Spotting that risk early lets it lower a limit, send a reminder or offer a payment plan before the debt grows.

In this project you build a model that predicts whether a credit card customer will miss next month's payment, from their credit limit and six months of bills and repayments.

## Scope

This is a case study. You will not make lending decisions about real people. You work with one public table of 30,000 anonymised credit card customers in Taiwan, recorded from April to September 2005, and you show how the data science workflow (understanding, cleaning, exploring, modelling and communicating) answers the question on that table.

## The client and the question

| Item | Description |
| --- | --- |
| Client | The risk team of a consumer credit company that wants an early-warning list each month |
| Question | Given a customer's limit, bills and repayment history, will they miss next month's payment? |
| Prediction | `missed_payment`: 1 if the customer missed the next payment, 0 if they paid |
| Score | Balanced accuracy on the hidden test set (see Grading) |

## The data

`data/train.csv` contains about 80% of the customers. The committee holds the remaining 20% as the hidden test set. Only about one customer in five missed the payment, so a model that always predicts 0 looks accurate but finds no one; this is why the score is balanced accuracy.

Gender and marital status were removed: a credit model should not judge a customer on them. Some coded columns contain values that the original documentation does not explain; deciding how to treat them is part of the work.

| Column | Meaning | Unit |
| --- | --- | --- |
| `ID` | Customer identifier, not used as a feature | number |
| `LIMIT_BAL` | Credit limit, including supplementary cards | NT dollars |
| `EDUCATION` | 1 graduate school, 2 university, 3 high school, 4 other | code |
| `AGE` | Age of the customer | years |
| PAY_0, PAY_2 to PAY_6 | Repayment status from September (PAY_0) back to April (PAY_6): -1 paid on time, 1 one month late, 2 two months late, and so on | code |
| BILL_AMT1 to BILL_AMT6 | Bill amount from September (1) back to April (6) | NT dollars |
| PAY_AMT1 to PAY_AMT6 | Amount paid from September (1) back to April (6) | NT dollars |
| `missed_payment` | Target: 1 missed the next payment, 0 paid | label |

Test rows contain every column except `missed_payment`.

Source: Yeh, I. (2009). Default of Credit Card Clients [Dataset]. UCI Machine Learning Repository. DOI 10.24432/C55S3H. Licence: CC BY 4.0.

## Prerequisites

Learn these before you start. Each row gives one source to start with and one to go further.

| Skill | Start with | Go further |
| --- | --- | --- |
| Git and GitHub | [Introduction to GitHub](https://github.com/skills/introduction-to-github), GitHub Skills | [Pro Git](https://git-scm.com/book/en/v2), chapters 1 to 3 |
| Python | [Python](https://www.kaggle.com/learn/python), Kaggle Learn | [The Python Tutorial](https://docs.python.org/3/tutorial/) |
| pandas | [Pandas](https://www.kaggle.com/learn/pandas), Kaggle Learn | [10 minutes to pandas](https://pandas.pydata.org/docs/user_guide/10min.html) |
| Data cleaning | [Data Cleaning](https://www.kaggle.com/learn/data-cleaning), Kaggle Learn | [Working with missing data](https://pandas.pydata.org/docs/user_guide/missing_data.html) |
| Charts | [Data Visualization](https://www.kaggle.com/learn/data-visualization), Kaggle Learn | [Ten Simple Rules for Better Figures](https://doi.org/10.1371/journal.pcbi.1003833) |
| Prediction | [Intro to Machine Learning](https://www.kaggle.com/learn/intro-to-machine-learning), Kaggle Learn | [scikit-learn: Getting started](https://scikit-learn.org/stable/getting_started.html) |
| Animation | [Manim Community quickstart](https://docs.manim.community/en/stable/tutorials/quickstart.html) | [Animations using Matplotlib](https://matplotlib.org/stable/users/explain/animations/animations.html) |
| Slides | [Ten Simple Rules for Making Good Oral Presentations](https://doi.org/10.1371/journal.pcbi.0030077) |  |

Background on the data: the [dataset page](https://archive.ics.uci.edu/dataset/350/default+of+credit+card+clients) in the UCI Machine Learning Repository.

## Getting started

```bash
git clone https://github.com/dse-club-kfupm/262-pay-later.git
cd 262-pay-later
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter lab
```

Open `notebooks/01_data_understanding.ipynb`. Google Colab is also acceptable; upload `data/train.csv` and keep your notebooks in the repository.

## Repository structure

```text
262-pay-later/
├── README.md
├── requirements.txt
├── data/
│   ├── train.csv
│   └── README.md               source and licence
├── notebooks/
│   ├── 01_data_understanding.ipynb
│   ├── 02_cleaning_and_exploration.ipynb
│   └── 03_modelling.ipynb
├── src/
│   ├── features.py             cleaning and feature functions
│   └── predict.py              submission entry point
├── models/                     saved model, if predict.py loads one
├── tests/
│   └── check_submission.py     run before submitting
└── visuals/
    ├── slides/                 presentation as PDF
    └── animation/              animation source code and rendered video
```

## What you submit

Part A, prediction (50 points)

- `src/predict.py` containing a function `predict(df)`. It receives a table with the test columns described above and returns 1 (will miss the payment) or 0 (will pay) per row, in the same order.
- The three notebooks showing how you reached the model.
- Run `python tests/check_submission.py` before your final pull request. It confirms that `predict.py` loads and returns predictions in the right format.

Part B, visual communication (50 points)

- Clear, labelled charts in the notebooks.
- Slides: 5 to 7 slides that take the client from the question to the answer, saved as PDF in `visuals/slides/`.
- An animation or a short video of up to 2 minutes made with Python (Manim or matplotlib), with its source code and the rendered file in `visuals/animation/`. A good subject: how a customer's bills and repayments move over the six months, and how the model's estimate of risk changes as each month is added.

## Grading

Your `predict.py` is run once on the hidden test set after the submission deadline.

| Level | Requirement | Points |
| --- | --- | --- |
| Runs | Returns a valid prediction for every test row | 10 |
| Bronze | Scores above the baseline, which always predicts that the customer pays: 50.0% | 30 |
| Silver | Balanced accuracy of at least 60.5% | 40 |
| Gold | Balanced accuracy of at least 70.9%, the committee's reference model | 50 |

Balanced accuracy is the average of two percentages: the share of customers who missed a payment that your model catches, and the share of customers who paid that it correctly leaves alone. A model that always gives the same answer scores 50%.

| Part B | Missing | Basic | Good | Excellent |
| --- | --- | --- | --- | --- |
| Charts in the notebooks | 0 | 5 | 10 | 15 |
| Slides, 5 to 7, as PDF | 0 | 5 | 10 | 15 |
| Animation or video, made with Python, up to 2 minutes | 0 | 7 | 14 | 20 |

Any team member may be asked to explain any part of the work. If no one can, the Part A points do not count.

## Milestones

Progress is tracked with four milestones, each with its own issues.

| Milestone | Complete when |
| --- | --- |
| 1. Data understanding | Notebook 01 and the cleaning functions are merged |
| 2. Exploration | Notebook 02, with charts and written findings, is merged |
| 3. Prediction submission | Notebook 03 and `src/predict.py` are merged and pass the format check |
| 4. Visual communication | The slides and the animation or video are merged |

## Rules

- Train only on `data/train.csv`.
- Do not add gender or marital status back from other copies of the data.
- Every change reaches `main` through a pull request reviewed by a teammate.
- AI tools are allowed if declared in the Credits section, and every member must be able to explain the work.
- `predict.py` may use only pandas, NumPy and scikit-learn, so that it runs in the grading environment. Notebooks and visuals may also use matplotlib, seaborn, plotly and Manim.

## Credits

- Project Leads: Name (@github), Name (@github)
- Contributors: Name (@github)
- Organized by: Applied Projects Committee, Data Engineering & Science Club, KFUPM
- Data: Yeh, I. (2009). Default of Credit Card Clients [Dataset]. UCI Machine Learning Repository. DOI 10.24432/C55S3H. Licence: CC BY 4.0.
- AI tools used: none, or name the tool and what it was used for
