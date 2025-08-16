# Taller MLOps
Este documento contiene gran parte del código a utilizar. 
Para la elaboración de este taller se están utilizado recursos que provienen de las documentaciones de DVC y MLFlow.
* [DVC Intro](https://dvc.org/doc/start)
* [DVC Pipelines](https://dvc.org/doc/start/data-pipelines/data-pipelines)
* [MLflow](https://mlflow.org/docs/latest/ml/)


# Entorno Virtual y dependencias
``` bash
virtualenv venv && echo "venv" > .gitignore
pip install -r src/requirements.txt
```

# MLflow
``` bash
mlflow server --host 127.0.0.1 --port 8080
```
```python
import mlflow

mlflow.set_tracking_uri("http://127.0.0.1:8080")
```

```python
with mlflow.start_run() as run:

    mlflow.log_params(params)

    mlflow.log_metrics(metrics)

    mlflow.sklearn.log_model(sk_model=rf, input_example=X_val, name=artifact_path)
```

```python
mlflow.sklearn.autolog(max_tuning_runs=10)
```

```bash
set MLFLOW_TRACKING_URI=http://localhost:8080
```

```bash
mlflow models serve -m models:/model-taller/1 --port 5002
```

# DVC
```bash
dvc init
```

```bash
git status
```

```bash
git commit -m "DVC inicializado"
```

```bash
dvc add data/data.xml
git add .
git commit -m "Datos agregados"
```

Mac/Linux
```bash
mkdir /tmp/dvcstore
dvc remote add -d myremote /tmp/dvcstore
```

Windows
```bash
mkdir %TEMP%/dvcstore
dvc remote add -d myremote %TEMP%\dvcstore
```
```bash
dvc push
```

Nuevos datos
```bash
dvc add data/data.xml
```

```bash
dvc push
git commit data/data.xml.dvc -m "Dataset updates"
```

```bash
git checkout HEAD~1 data/data.xml.dvc
dvc checkout
```

## DVC Pipelines
```bash
dvc stage add -n prepare \
                -p prepare.seed,prepare.split \
                -d src/prepare.py -d data/data.xml \
                -o data/prepared \
                python src/prepare.py data/data.xml
```


```bash
dvc stage add -n featurize \
                -p featurize.max_features,featurize.ngrams \
                -d src/featurization.py -d data/prepared \
                -o data/features \
                python src/featurization.py data/prepared data/features
```


```bash
dvc stage add -n train \
                -p train.seed,train.n_est,train.min_split \
                -d src/train.py -d data/features \
                -o model.pkl \
                python src/train.py data/features model.pkl
```

```bash
dvc repro
```

```bash
git add dvc.lock && git commit -m "first pipeline repro"
```
```bash
dvc dag
