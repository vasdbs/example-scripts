```
███╗   ██╗██╗   ██╗███╗   ███╗███████╗██████╗  █████╗ ██╗
████╗  ██║██║   ██║████╗ ████║██╔════╝██╔══██╗██╔══██╗██║
██╔██╗ ██║██║   ██║██╔████╔██║█████╗  ██████╔╝███████║██║
██║╚██╗██║██║   ██║██║╚██╔╝██║██╔══╝  ██╔══██╗██╔══██║██║
██║ ╚████║╚██████╔╝██║ ╚═╝ ██║███████╗██║  ██║██║  ██║██║
╚═╝  ╚═══╝ ╚═════╝ ╚═╝     ╚═╝╚══════╝╚═╝  ╚═╝╚═╝  ╚═╝╚═╝
```

The official example scripts for the [Numerai](https://numer.ai/) Data Science Tournament.

# Contents

- [Quick Start](#quick-start)
- [Datasets](#datasets)
- [Next Steps](#next-steps)
  - [Research](#research)
  - [Staking](#staking)
  - [Automation](#automation)
- [FAQ](#faq)
- [Help and Discussions](#help-and-discussions)

# Quick Start

```
pip install -U pip && pip install -r requirements.txt
python example_model.py
```

The example script model will produce a `validation_predictions.csv` file which you can upload at
https://numer.ai/tournament to get model diagnostics.

> TIP: The example_model.py script takes ~45-60 minutes to run. If you don't want to wait, you can upload
> `example_diagnostic_predictions.csv` to get diagnostics immediately.

![upload-diagnostics](https://numerai-public-images.s3.us-west-2.amazonaws.com/example-scripts/upload_diagnostics.gif)

If the current round is open (Saturday 18:00 UTC through Monday 14:30 UTC), you can submit your predictions and start
getting results on live tournament data. You can create your submission by uploading the `tournament_predictions.csv`
file at https://numer.ai/tournament

![upload-tournament](https://numerai-public-images.s3.us-west-2.amazonaws.com/example-scripts/upload_tournament.gif)

# Datasets

### numerai_training_data

- Description: Labeled training data

- Dimensions: ~2M rows x ~1K columns

- Size: ~10GB CSV, ~1GB Parquet

- Columns:

  - "id": string labels of obfuscated stock IDs

  - "era": string labels of points in time for a block of IDs

  - "data_type": string label "train"

  - "feature\_...": floating-point numbers, obfuscated features for each stock ID

  - "target": floating-point numbers, the relative performance of that stock during that era

- Notes: Check out the analysis_and_tips notebook for a detailed walkthrough of this dataset

### numerai_validation_data

- Description: Labeled holdout set used to generate validation predictions and for computing validation metrics

- Dimensions: ~540K rows x ~1K columns

- Size: ~2.5GB CSV, ~210MB Parquet

- Columns:

  - "id": string labels of obfuscated stock IDs

  - "era": string labels of points in time for a block of IDs

  - "data_type": string label "validation"

  - "feature\_...": floating-point numbers, obfuscated features for each stock ID

  - "target": floating-point numbers, the relative performance of that stock during that era

- Notes: It is highly recommended that you do not train on the validation set.

### numerai_tournament_data

- Description: Unlabeled feature data used to generate predictions and for computing live tournament scores

- Dimensions: ~1.4M rows x ~1K columns

- Size: ~6GB CSV, ~550MB Parquet

- Columns:

  - "id": string labels of obfuscated stock IDs

  - "era": string labels of points in time for a block of IDs

  - "data_type": string labels "test" and "live"

  - "feature\_...": floating-point numbers, obfuscated features for each stock ID

  - "target": NaN (not-a-number), intentionally left blank

- Notes: This file changes every week, so make sure to download the most recent version of this file each round.

### example_validation_predictions

- Description: The predictions generated by the example_model on the numerai_validation_data

- Dimensions: ~540K rows x 1 column

- Size: ~14MB CSV

- Columns:

  - "id": string labels of obfuscated stock IDs

  - "prediction": floating-point numbers between 0 and 1 (exclusive)

- Notes: Useful for ensuring you can get diagnostics and debugging your prediction
  file if you receive an error from the diagnostics API. This is what your uploads
  should look like when using the diagnostics section under a model's "More" dropdown
  in the [models page](numer.ai/models).

### example_predictions

- Description: The predictions generated by the example_model on the numerai_tournament_data

- Dimensions: ~1.4M rows x 1 column

- Size: ~37MB CSV

- Columns:

  - "id": string labels of obfuscated stock IDs

  - "prediction": floating-point numbers between 0 and 1 (exclusive)

- Notes: Useful for ensuring you can make a submission and debugging your prediction
  file if you receive an error from the submissions API. This is what your submissions
  should look like (same ids and data types).

# Next Steps

## Research

The example model is a good baseline model, but we can do much better. Check out example_model_advanced for the best model made by Numerai's internal research team (takes 2~3 hours to run!).

To learn more about the underlying concepts used to construct the advanced example model, check out the analysis_and_tips notebook.

## Staking

Once you have a model you are happy with, you can stake [NMR](https://www.coinbase.com/price/numeraire) on it to start earning rewards.

Head over to the [website](https://numer.ai/tournament) to get started or read more about staking in our [official rules and getting started guide](https://docs.numer.ai/tournament/learn#staking).

## Automation

You can upload your predictions directly to our [GraphQL API](https://api-tournament.numer.ai/) or through the [Python client](https://github.com/uuazed/numerapi/).

To access the API, you must first create your API keys in your [account page](https://numer.ai/account) and provide them to the client:

```python
example_public_id = "somepublicid"
example_secret_key = "somesecretkey"
napi = numerapi.NumerAPI(example_public_id, example_secret_key)
```

After instantiating the NumerAPI client with API keys, you can then upload your submissions programmatically:

```python
# upload predictions
model_id = napi.get_models()['your_model_name']
napi.upload_predictions("tournament_predictions.csv", model_id=model_id)
```

The recommended setup for a fully automated submission process is to use Numerai Compute. Please see the
[Numerai CLI documentation](https://github.com/numerai/numerai-cli) for instructions on how to deploy your
models to AWS.

# FAQ

### What are the system requirements?

- Minimum: 16GB RAM and 4 core (Intel i5) / 6 cores (AMD Ryzen 5)
- Recommended: 32GB RAM and 8 core (Intel i7/i9) / 12 cores (AMD Ryzen 7/9)

### What exactly is in the Numerai dataset?

The Numerai Dataset contains decades of historical data on the global stock market. Each era represents a time period and each id represents a stock. The features are made from market and fundamental measures of the companies, and the targets are a measure of return.

The stock ids, features, and targets are intentionally obfuscated.

### How often is the dataset updated?

The historical portions of the dataset (training_data, validation_data) are relatively static and is updated about every 3-6 months, usually with just more rows.

The live portion of the dataset (tournament_data) is updated every week and represents the latest state of the global stock market.

# Help and Discussions

For help and discussions, connect with us on our [community chat](http://community.numer.ai/) or [forums](https://forum.numer.ai/)

If something in this repo doesn't work, please [file an issue](https://github.com/numerai/example-scripts/issues)
