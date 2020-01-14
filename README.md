SkLearn2PMML
============

Python library for converting [Scikit-Learn](https://scikit-learn.org/) pipelines to PMML.

# Features #

This library is a thin wrapper around the JPMML-SkLearn command-line application.

For a list of supported Estimator and Transformer types, please refer to [JPMML-SkLearn features](https://github.com/jpmml/jpmml-sklearn#features).

# Prerequisites #

* Python 2.7, 3.4 or newer.
* Java 1.8 or newer. The Java executable must be available on system path.

# Installation #

Installing the latest release version from PyPI:

```
pip install sklearn2pmml
```

Alternatively, installing a snapshot version from GitHub:

```
pip install git+https://github.com/jpmml/sklearn2pmml.git
```

# Usage #

A typical workflow can be summarized as follows:

1. Create a `PMMLPipeline` object, and populate it with pipeline steps as usual. Class `sklearn2pmml.pipeline.PMMLPipeline` extends class `sklearn.pipeline.Pipeline` with the following functionality:
  * If the `PMMLPipeline.fit(X, y)` method is invoked with `pandas.DataFrame` or `pandas.Series` object as an `X` argument, then its column names are used as feature names. Otherwise, feature names default to "x1", "x2", .., "x{number_of_features}".
  * If the `PMMLPipeline.fit(X, y)` method is invoked with `pandas.Series` object as an `y` argument, then its name is used as the target name (for supervised models). Otherwise, the target name defaults to "y".
2. Fit and validate the pipeline as usual.
3. Optionally, compute and embed verification data into the `PMMLPipeline` object by invoking `PMMLPipeline.verify(X)` method with a small but representative subset of training data.
4. Convert the `PMMLPipeline` object to a PMML file in local filesystem by invoking utility method `sklearn2pmml.sklearn2pmml(pipeline, pmml_destination_path)`.

Developing a simple decision tree model for the classification of iris species:

```python
import pandas

iris_df = pandas.read_csv("Iris.csv")

from sklearn.tree import DecisionTreeClassifier
from sklearn2pmml.pipeline import PMMLPipeline

pipeline = PMMLPipeline([
	("classifier", DecisionTreeClassifier())
])
pipeline.fit(iris_df[iris_df.columns.difference(["Species"])], iris_df["Species"])

from sklearn2pmml import sklearn2pmml

sklearn2pmml(pipeline, "DecisionTreeIris.pmml", with_repr = True)
```

Developing a more elaborate logistic regression model for the same:

```python
import pandas

iris_df = pandas.read_csv("Iris.csv")

from sklearn_pandas import DataFrameMapper
from sklearn.decomposition import PCA
from sklearn.feature_selection import SelectKBest
from sklearn.preprocessing import Imputer
from sklearn.linear_model import LogisticRegression
from sklearn2pmml.decoration import ContinuousDomain
from sklearn2pmml.pipeline import PMMLPipeline

pipeline = PMMLPipeline([
	("mapper", DataFrameMapper([
		(["Sepal.Length", "Sepal.Width", "Petal.Length", "Petal.Width"], [ContinuousDomain(), Imputer()])
	])),
	("pca", PCA(n_components = 3)),
	("selector", SelectKBest(k = 2)),
	("classifier", LogisticRegression())
])
pipeline.fit(iris_df, iris_df["Species"])

from sklearn2pmml import sklearn2pmml

sklearn2pmml(pipeline, "LogisticRegressionIris.pmml", with_repr = True)
```

# Documentation #

Up-to-date:

* [Stacking Scikit-Learn, LightGBM and XGBoost models](https://openscoring.io/blog/2020/01/02/stacking_sklearn_lightgbm_xgboost/)
* [Converting Scikit-Learn hyperparameter-tuned pipelines to PMML documents](https://openscoring.io/blog/2019/12/25/converting_sklearn_gridsearchcv_pipeline_pmml/)
* [Extending Scikit-Learn with GBDT plus LR ensemble (GBDT+LR) model type](https://openscoring.io/blog/2019/06/19/sklearn_gbdt_lr_ensemble/)
* [Converting Scikit-Learn based TPOT automated machine learning (AutoML) pipelines to PMML documents](https://openscoring.io/blog/2019/06/10/converting_sklearn_tpot_pipeline_pmml/)
* [Converting Scikit-Learn based LightGBM pipelines to PMML documents](https://openscoring.io/blog/2019/04/07/converting_sklearn_lightgbm_pipeline_pmml/)
* [Extending Scikit-Learn with business rules (BR) model type](https://openscoring.io/blog/2018/09/17/sklearn_business_rules/)

Slightly outdated:

* [Converting Scikit-Learn to PMML](https://www.slideshare.net/VilluRuusmann/converting-scikitlearn-to-pmml)

# De-installation #

Uninstalling:

```
pip uninstall sklearn2pmml
```

# License #

SkLearn2PMML is dual-licensed under the [GNU Affero General Public License (AGPL) version 3.0](https://www.gnu.org/licenses/agpl-3.0.html), and a commercial license.

# Additional information #

SkLearn2PMML is developed and maintained by Openscoring Ltd, Estonia.

Interested in using JPMML software in your application? Please contact [info@openscoring.io](mailto:info@openscoring.io)

