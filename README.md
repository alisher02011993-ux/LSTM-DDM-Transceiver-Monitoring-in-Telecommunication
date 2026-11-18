# Code accompanying the paper

> N. Juraeva, D. Davronbekov, A. Khayrullaev, M. Muradov, M. Khadjaev, J. Kurbanov, R. Nasimov, *LSTM-Based Recognition of Degradation States in Fiber-Optic Transceivers Using Digital Diagnostic Monitoring Data*, submitted for publication.

The study evaluates an LSTM-based approach for recognizing degradation states of fiber-optic SFP transceivers from synthetic Digital Diagnostic Monitoring (DDM) data. The main experiment uses 20-sample temporal windows and nine input features:

- **Five original DDM parameters**: temperature, supply voltage, laser bias current, transmitted optical power, and received optical power.
- **Four first-difference features**: temperature, laser bias current, transmitted optical power, and received optical power.

Three technical states are considered: **normal**, **degradation**, and **pre-failure**. The continuous auxiliary output is treated as a **synthetic normalized risk indicator** reflecting proximity to alarm conditions, not as a calibrated probability of physical failure.

The supplied CSV contains **60,000 observations from 300 simulated transceivers**, with 200 consecutive time steps per device. The class distribution is 47,729 normal, 5,932 degradation, and 6,339 pre-failure observations.

## Training protocol

The primary experiment uses a fixed device-level test split of 20% (60 devices), with the remaining 240 devices divided into 204 training and 36 validation devices for each repetition. The outer test split uses seed 42. Five repeated runs are performed with seeds **42, 137, 2024, 31415, and 65537**.

The reference model is a two-layer LSTM with 64 and 32 recurrent units, dropout 0.2, a shared dense layer of 32 neurons, and two output heads: a three-class Softmax classifier and a Sigmoid regression output for the normalized risk indicator.

Training uses Adam (learning rate 1e-3), sparse categorical cross-entropy for classification, MSE for the continuous output, loss weights 1.0 and 0.5, batch size 64, a maximum of 30 epochs, early-stopping patience of 5 epochs, and ReduceLROnPlateau patience of 3 epochs.

For comparison, the notebook also evaluates GRU, 1D-CNN, MLP, Random Forest, and XGBoost under the same device-level protocol.

## Repository layout

```text
ddm_dataset.csv
    Synthetic DDM dataset with 60,000 observations from 300 simulated SFP transceivers.

DDM_paper_aligned_corrected_v2 (1).ipynb
    Dataset validation, preprocessing, device-level splitting, repeated training,
    evaluation, model comparison, plots, artifact generation, and TensorFlow Lite export.

README.md
    Repository documentation.
```

The notebook writes a timestamped results directory containing experiment metadata, device-split manifests, per-run metrics, mean ± standard deviation summaries, model-comparison tables, plots, trained model files, preprocessing metadata, predictions, and TensorFlow Lite export validation.

Notebook comments are partly in Uzbek; code and variable names are in English.

## Reproducing

1. **Data.** Place `ddm_dataset.csv` in the working directory. The current notebook searches by default for `ddm_synthetic_dataset.csv`, so either rename the file accordingly or update the `DATA_FILENAME` variable in the notebook.

2. **Environment.** The notebook requires Python with TensorFlow/Keras, NumPy, pandas, scikit-learn, matplotlib, joblib, and XGBoost.

3. **Experiments.** Open and run `DDM_paper_aligned_corrected_v2 (1).ipynb` sequentially. The notebook validates the dataset, creates the fixed device-level test cohort, performs repeated training and evaluation, and stores all outputs in a timestamped results folder.

4. **TensorFlow Lite.** With `RUN_TFLITE_EXPORT = True`, the first prespecified LSTM repetition (`seed = 42`) is exported to TensorFlow Lite and compared numerically with the Keras reference model on the fixed test set.

5. **Important reproducibility note.** The repository currently contains the generated synthetic dataset and the training/evaluation notebook. The uploaded notebook does **not** contain the original script that generated the raw synthetic DDM values in the CSV. For complete regeneration of the dataset, the original dataset-generation script and its generation parameters should be added separately.

## Citation

If you use this code or dataset, please cite the paper above and this repository:

> Juraeva, N., Davronbekov, D., Khayrullaev, A., Muradov, M., Khadjaev, M., Kurbanov, J., & Nasimov, R. (2026). *Code and data for "LSTM-Based Recognition of Degradation States in Fiber-Optic Transceivers Using Digital Diagnostic Monitoring Data"*. Zenodo. [DOI to be added]

```bibtex
@software{juraeva2026ddm,
  author    = {Juraeva, Nafisa and
               Davronbekov, Dilmurod and
               Khayrullaev, Alisher and
               Muradov, Muhammad and
               Khadjaev, Mukhammadzoir and
               Kurbanov, Janibek and
               Nasimov, Rashid},
  title     = {Code and data for "LSTM-Based Recognition of Degradation States in
               Fiber-Optic Transceivers Using Digital Diagnostic Monitoring Data"},
  year      = {2026},
  publisher = {Zenodo},
  doi       = {[DOI]},
  url       = {https://doi.org/[DOI]}
}
```


