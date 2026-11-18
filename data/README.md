# Data

No data are in this repository. The sources below are those of the paper (Section 3.7 of the
arXiv v2 preprint). The paper states no licence for any of them.

| Source | Content | Access terms |
|---|---|---|
| European DataWarehouse (EUDW), residential mortgage data for the Netherlands (<https://editor.eurodw.eu/>, accessed 2023-05-01 according to the paper) | Loan-level records of three Dutch residential mortgage portfolios, 2013 to 2022. They give the high-risk and low-risk labels. | The paper calls them restricted loan-level data. Access is through the EUDW. |
| Seeking Alpha market news (<https://seekingalpha.com/market-news>, accessed 2024 according to the paper) | News articles selected by keyword in six categories: Netherlands, interest rate, mortgage, housing market, unemployment and European policy. | Not stated in the paper or the repository. |
| Sentiment scores | VADER scores computed from the news text and averaged per category. | Derived from the news above. |
| AHN2 0.5 m digital elevation model (Actueel Hoogtebestand Nederland) in the Google Earth Engine Data Catalog (<https://developers.google.com/earth-engine/datasets/catalog/AHN_AHN2_05M_RUW>) | LiDAR elevation images, matched to each loan through its postal code. | The paper describes the catalogue as openly accessible. |

## Where the code expects the files

The scripts in `code/` read the folder named by the environment variable `DATA_DIR` (default:
`data`, relative to the working directory). They expect these files there:

| File | Used as |
|---|---|
| `data_clean (1).csv` | Loan records. The scripts use the columns `AR18`, `AR19`, `AR129` (the number of the LiDAR image of the loan), `AR166` (the label: 0 stays 0, any other value becomes 1) and `text_clean` (news text). |
| `clean_housing5.csv` | Second text channel: columns `lbl` and `text_clean`. |
| `sentiment.csv` | Sentiment scores. Every column is used as a numeric feature. |
| `all_data_reg.csv` | Column `lbl`, used to print the class counts. |
| `int.zip` | LiDAR images as PNG files. The number in each file name matches `AR129`. |
| `int/` | Optional folder of LiDAR PNG files. |

The scripts combine the CSV files by row position, so their rows must be in the same order.

The scripts extract `int.zip` into `data_image/first_class/` in the working directory. They then
move (not copy) every `.png` file from `$DATA_DIR/int/` and from the working directory into that
folder. Keep a copy of the images elsewhere if you need them in place.
