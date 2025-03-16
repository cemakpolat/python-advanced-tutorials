---
title: Machine Learning Pipelines with Scikit-learn
parent: Applying Advanced Concepts 
nav_order: 2
---

## Chapter 11: Machine Learning Pipelines with Scikit-learn


*   **11.1 Introduction to Machine Learning Pipelines:**

    Machine learning pipelines are a powerful tool for streamlining the development and deployment of machine learning models. They provide a way to chain together multiple steps in a machine learning workflow, such as data loading, preprocessing, feature engineering, model training, and evaluation, into a single, cohesive unit.

    Why use pipelines?

    *   **Code Organization:** Pipelines make your code more organized and easier to read by encapsulating the entire machine learning workflow in a single object.
    *   **Reproducibility:** Pipelines ensure that the same preprocessing and feature engineering steps are applied consistently across different datasets and training runs, making your results more reproducible.
    *   **Preventing Data Leakage:** Pipelines help prevent data leakage, which occurs when information from the test set is inadvertently used to train the model. By encapsulating all preprocessing steps within the pipeline, you can ensure that the test set is only used for evaluation.
    *   **Simplified Model Deployment:** Pipelines can be easily deployed to production environments, allowing you to automate the entire machine learning workflow.

    Common steps in a machine learning pipeline:

    1.  **Data Loading:** Loading the data from a file or database.
    2.  **Data Preprocessing:** Cleaning and transforming the data, such as handling missing values, encoding categorical features, and scaling numerical features.
    3.  **Feature Engineering:** Creating new features from existing features.
    4.  **Model Training:** Training a machine learning model on the preprocessed data.
    5.  **Model Evaluation:** Evaluating the performance of the trained model using appropriate metrics.

    Before diving into pipelines, let's briefly review some Scikit-learn basics:

    *   **Estimators:** Objects that can learn from data. Examples include classifiers (e.g., Logistic Regression, Random Forest) and regressors (e.g., Linear Regression, Support Vector Regression). Estimators have a `fit()` method that is used to train the model and a `predict()` method that is used to make predictions.

    *   **Transformers:** Objects that transform data. Examples include scalers (e.g., StandardScaler, MinMaxScaler) and encoders (e.g., OneHotEncoder, LabelEncoder). Transformers have a `fit()` method that is used to learn the transformation parameters and a `transform()` method that is used to apply the transformation.

    *   **Model Evaluation:** Evaluating the performance of a trained model using appropriate metrics, such as accuracy, precision, recall, F1-score, and AUC.

*   **11.2 Building Simple Pipelines with `Pipeline`:**

    You can create a pipeline using the `Pipeline` class from the `sklearn.pipeline` module. The `Pipeline` class takes a list of steps as input, where each step is a tuple containing a name and an estimator or transformer.

    Here's an example of a pipeline for preprocessing numerical data and training a linear regression model:

    ```python
    from sklearn.pipeline import Pipeline
    from sklearn.preprocessing import StandardScaler
    from sklearn.linear_model import LinearRegression
    from sklearn.model_selection import train_test_split
    import numpy as np

    # Sample data
    X = np.array([[1, 2], [3, 4], [5, 6], [7, 8]]) # Features
    y = np.array([3, 7, 11, 15]) # Target

    # Create a pipeline
    pipeline = Pipeline([
        ('scaler', StandardScaler()), # Step 1: Scale the data
        ('regressor', LinearRegression())  # Step 2: Train a linear regression model
    ])

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42) #Split test and train

    # Train the pipeline
    pipeline.fit(X_train, y_train)

    # Make predictions
    predictions = pipeline.predict(X_test) # Predict using scaled test

    print(f"Predictions: {predictions}")
    ```

    **Explanation:**

    1.  `from sklearn.pipeline import Pipeline`: Imports the `Pipeline` class.
    2.  `pipeline = Pipeline([...])`: Creates a `Pipeline` object. The steps in the pipeline are defined as a list of tuples. Each tuple contains a name for the step (e.g., `'scaler'`, `'regressor'`) and an estimator or transformer object.
    3.  `('scaler', StandardScaler())`: The first step in the pipeline is a `StandardScaler` object, which scales the data to have zero mean and unit variance.
    4.  `('regressor', LinearRegression())`: The second step in the pipeline is a `LinearRegression` object, which trains a linear regression model.
    5.  `pipeline.fit(X_train, y_train)`: Trains the pipeline on the training data. The `fit()` method calls the `fit()` method of each step in the pipeline in order.
    6.  `predictions = pipeline.predict(X_test)`: Makes predictions on the test data. The `predict()` method calls the `transform()` method of each transformer in the pipeline in order, and then calls the `predict()` method of the final estimator.

    Here's another example of a pipeline for preprocessing text data and training a Naive Bayes classifier:

    ```python
    from sklearn.pipeline import Pipeline
    from sklearn.feature_extraction.text import TfidfVectorizer
    from sklearn.naive_bayes import MultinomialNB
    from sklearn.model_selection import train_test_split
    from sklearn.metrics import accuracy_score

    # Sample data
    documents = [
        "This is the first document.",
        "This is the second second document.",
        "And the third one.",
        "Is this the first document?",
    ]
    labels = ['spam', 'spam', 'ham', 'ham']

    # Create a pipeline
    text_pipeline = Pipeline([
        ('tfidf', TfidfVectorizer()),  # Step 1: Convert text to TF-IDF vectors
        ('classifier', MultinomialNB())  # Step 2: Train a Naive Bayes classifier
    ])

    X_train, X_test, y_train, y_test = train_test_split(documents, labels, test_size=0.2, random_state=42)
    # Train the pipeline
    text_pipeline.fit(X_train, y_train)

    # Make predictions
    predictions = text_pipeline.predict(X_test)

    print(f"Accuracy: {accuracy_score(y_test, predictions)}")
    ```

*   **11.3 Column Transformer: Handling Heterogeneous Data:**

    In many real-world machine learning problems, you'll encounter heterogeneous data, which means data with different data types (e.g., numerical, categorical, text). Scikit-learn's `ColumnTransformer` allows you to apply different transformations to different columns of your data.

    To use `ColumnTransformer`, you need to specify a list of transformers, where each transformer is a tuple containing a name, a transformer object, and a list of columns to apply the transformer to.

    The `ColumnTransformer` class provides several options for handling remaining columns:

    *   `passthrough`: The remaining columns are passed through without any transformation.
    *   `drop`: The remaining columns are dropped.
    *   `remainder='transformer'`: All remaining columns are transformed using the specific transformer.

    Here's an example of a pipeline that uses `ColumnTransformer` to preprocess heterogeneous data and train a machine learning model:

    ```python
    import pandas as pd
    from sklearn.compose import ColumnTransformer
    from sklearn.preprocessing import StandardScaler, OneHotEncoder
    from sklearn.linear_model import LogisticRegression
    from sklearn.model_selection import train_test_split
    from sklearn.metrics import accuracy_score
    from sklearn.pipeline import Pipeline

    # Sample data
    data = {
        'numerical_feature': [1, 2, 3, 4, 5],
        'categorical_feature': ['A', 'B', 'A', 'C', 'B'],
        'text_feature': ["good", "bad", "good", "average", "bad"],
        'target': [0, 1, 0, 0, 1] # 0 or 1 target
    }
    df = pd.DataFrame(data)

    # Define transformers
    numerical_transformer = StandardScaler()
    categorical_transformer = OneHotEncoder(handle_unknown='ignore') # handle_unknown: how to handle unknown
    text_transformer = TfidfVectorizer()

    # Define column transformer
    preprocessor = ColumnTransformer(
        transformers=[
            ('num', numerical_transformer, ['numerical_feature']),
            ('cat', categorical_transformer, ['categorical_feature']),
            ('text', text_transformer, ['text_feature'])
        ],
        remainder='passthrough' #or 'drop'
    )

    # Create a pipeline
    pipeline = Pipeline([
        ('preprocessor', preprocessor),
        ('classifier', LogisticRegression())
    ])

    # Split data into training and testing sets
    X = df.drop('target', axis=1)
    y = df['target']
    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

    # Train the pipeline
    pipeline.fit(X_train, y_train)

    # Make predictions
    predictions = pipeline.predict(X_test)

    # Evaluate the model
    print(f"Accuracy: {accuracy_score(y_test, predictions)}")
    ```

    **Explanation:**

    1.  `ColumnTransformer([...])`: Creates a `ColumnTransformer` object.
    2.  `transformers=[...]`: Defines the list of transformers. Each transformer is a tuple containing a name, a transformer object, and a list of columns to apply the transformer to.
    3.  `('num', numerical_transformer, ['numerical_feature'])`: Applies the `numerical_transformer` (StandardScaler) to the `numerical_feature` column.
    4.  `('cat', categorical_transformer, ['categorical_feature'])`: Applies the `categorical_transformer` (OneHotEncoder) to the `categorical_feature` column.
    5. `('text', text_transformer, ['text_feature'])`: Applies the `text_transformer` (TFIDFVectorizer) to the `text_feature` column.
    6.  `remainder='passthrough'`: Specifies that any remaining columns should be passed through without any transformation (if you used drop, they won't be available).

*   **11.4 Custom Transformers:**

    While Scikit-learn provides a wide range of built-in transformers, you may sometimes need to create custom transformers to implement domain-specific transformations or handle complex data preprocessing logic.

    To create a custom transformer, you need to create a class that inherits from `BaseEstimator` and `TransformerMixin` from the `sklearn.base` module. You also need to implement the `fit()` and `transform()` methods.

    *   `fit(self, X, y=None)`: This method is used to learn the transformation parameters. It takes the input data `X` and the target variable `y` (if applicable) as input. The `fit()` method should return `self`.
    *   `transform(self, X)`: This method is used to apply the transformation to the input data `X`. It should return the transformed data.

    Here's an example of a custom transformer for handling missing values:

    ```python
    import numpy as np
    from sklearn.base import BaseEstimator, TransformerMixin

    class MissingValueImputer(BaseEstimator, TransformerMixin):
        def __init__(self, strategy='mean'):
            self.strategy = strategy
            self.fill_value = None

        def fit(self, X, y=None):
            if self.strategy == 'mean':
                self.fill_value = np.nanmean(X)
            elif self.strategy == 'median':
                self.fill_value = np.nanmedian(X)
            else:
                raise ValueError("Invalid strategy.  Must be 'mean' or 'median'.")
            return self

        def transform(self, X):
            return np.nan_to_num(X, nan=self.fill_value)

    #Example
    missing_imputer = MissingValueImputer(strategy='mean')
    data = np.array([[1, 2, np.nan], [4, np.nan, 6], [7, 8, 9]])
    imputed_data = missing_imputer.fit_transform(data)
    print(imputed_data)
    ```

    Here's another example of a custom transformer for performing feature scaling:

    ```python
    from sklearn.base import BaseEstimator, TransformerMixin
    import numpy as np

    class FeatureScaler(BaseEstimator, TransformerMixin):
        def __init__(self, feature_range=(0, 1)):
            self.feature_range = feature_range
            self.min_vals = None
            self.max_vals = None

        def fit(self, X, y=None):
            self.min_vals = np.min(X, axis=0)
            self.max_vals = np.max(X, axis=0)
            return self

        def transform(self, X):
            X_scaled = (X - self.min_vals) / (self.max_vals - self.min_vals)
            X_scaled = X_scaled * (self.feature_range[1] - self.feature_range[0]) + self.feature_range[0]
            return X_scaled
    ```

*   **11.5 Feature Union: Combining Multiple Feature Engineering Steps:**

    `FeatureUnion` from `sklearn.pipeline` allows you to combine multiple feature engineering steps into a single transformer. This is useful when you want to create different sets of features from the same data and then combine them into a single feature set.

    Here's an example of `FeatureUnion` to build different text transformer

    ```python
    from sklearn.pipeline import FeatureUnion
    from sklearn.feature_extraction.text import TfidfVectorizer, CountVectorizer

    union = FeatureUnion([("tfidf", TfidfVectorizer(ngram_range=(1,3))),
                          ("countvec", CountVectorizer())])
    ```

*   **11.6 Advanced Pipeline Techniques:**

    Scikit Learn pipeline also support many different techniques that are listed below

    *   **Pipeline with Feature Selection:**
        *   Integrating feature selection techniques (e.g., `SelectKBest`, `RFE`) into a pipeline.
        *   Preventing data leakage during feature selection.
    *   **Pipeline with Model Selection (Grid Search):**
        *   Using `GridSearchCV` to tune the hyperparameters of a pipeline.
        *   Searching over different models and preprocessing techniques.
        *   Nested cross-validation for unbiased model evaluation.
    *   **Pipelines with Custom Model:**
        *   Create a custom model and integrate it into the pipeline

*   **11.7 Best Practices for Machine Learning Pipelines:**

    *   Start with a simple pipeline and gradually add complexity.
    *   Document your pipelines clearly.
    *   Test your pipelines thoroughly.
    *   Use version control to track changes to your pipelines.
    *   Monitor the performance of your pipelines in production.

*   **Project 11.1: Building a Credit Risk Prediction Pipeline**

    Build a machine learning pipeline to predict credit risk based on a dataset of loan applications. The pipeline should include steps for:

    *   Loading and cleaning the data.
    *   Handling missing values.
    *   Encoding categorical features.
    *   Scaling numerical features.
    *   Selecting relevant features.
    *   Training a classification model (e.g., Logistic Regression, Random Forest).
    *   Evaluating the model's performance using appropriate metrics (e.g., accuracy, precision, recall, F1-score, AUC).
    *   Optimising the pipeline using grid search

    ```python
    import pandas as pd
    from sklearn.model_selection import train_test_split
    from sklearn.compose import ColumnTransformer
    from sklearn.preprocessing import StandardScaler, OneHotEncoder
    from sklearn.impute import SimpleImputer
    from sklearn.feature_selection import SelectKBest, f_classif
    from sklearn.linear_model import LogisticRegression
    from sklearn.pipeline import Pipeline
    from sklearn.model_selection import GridSearchCV
    from sklearn.metrics import classification_report, roc_auc_score

    # Load the dataset
    try:
        df = pd.read_csv("credit_risk_dataset.csv")
    except FileNotFoundError:
        print("Error: Credit risk dataset 'credit_risk_dataset.csv' not found.  Please download it and place it in the same directory as the script.")
        exit()
    # Preprocessing: Remove missing values, there are different strategies to solve it
    # Preprocessing: Balance the dataset if needed
    # See point 3: https://www.analyticsvidhya.com/blog/2023/07/5-effective-techniques-to-handle-imbalanced-data-for-classification/
    # For simplicity the scope will be reduced

    # Identify numerical and categorical features
    numerical_features = df.select_dtypes(include=['int64', 'float64']).columns.tolist()
    categorical_features = df.select_dtypes(include=['object']).columns.tolist()

    # Create preprocessing pipelines for numerical and categorical features
    numerical_pipeline = Pipeline([
        ('imputer', SimpleImputer(strategy='mean')), # Handle missing values
        ('scaler', StandardScaler()) # Scale numerical features
    ])

    categorical_pipeline = Pipeline([
        ('onehot', OneHotEncoder(handle_unknown='ignore')) # Encode categorical features
    ])

    # Combine the pipelines using ColumnTransformer
    preprocessor = ColumnTransformer([
        ('numerical', numerical_pipeline, numerical_features),
        ('categorical', categorical_pipeline, categorical_features)
    ])

    # Create the feature selection and model pipeline
    pipeline = Pipeline([
        ('preprocessor', preprocessor),
        ('feature_selection', SelectKBest(score_func=f_classif)), # Select the best features
        ('classifier', LogisticRegression(solver='liblinear', random_state=42)) # Use a classifier
    ])

    # Split the data into training and testing sets
    X = df.drop('loan_status', axis=1) # Drop the target variable
    y = df['loan_status']
    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

    # Define the hyperparameter grid
    param_grid = {
        'feature_selection__k': [5, 10, 15],  # Number of features to select
        'classifier__C': [0.1, 1.0, 10.0] # Regularization parameter for Logistic Regression
    }

    # Perform grid search with cross-validation
    grid_search = GridSearchCV(pipeline, param_grid, cv=3, scoring='roc_auc', verbose=1)
    grid_search.fit(X_train, y_train)

    # Print the best hyperparameters
    print("Best Hyperparameters:", grid_search.best_params_)

    # Evaluate the best model on the test set
    best_model = grid_search.best_estimator_
    predictions = best_model.predict(X_test)
    print("\nClassification Report:\n", classification_report(y_test, predictions))
    print("AUC:", roc_auc_score(y_test, best_model.predict_proba(X_test)[:, 1]))
    ```

    *   **Dataset:** Use a publicly available credit risk dataset (e.g., from Kaggle or UCI Machine Learning Repository).
    *   **Focus:** Reinforces Scikit-learn pipelines, data preprocessing, feature engineering, model selection, and evaluation.


*   **Project 11.2: Customer Churn Prediction Pipeline**

```python
import pandas as pd
from sklearn.model_selection import train_test_split, GridSearchCV
from sklearn.compose import ColumnTransformer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.pipeline import Pipeline
from sklearn.ensemble import GradientBoostingClassifier
from sklearn.metrics import classification_report

# Requires the churn_data.csv file
def create_churn_dataframe(num_samples=1000):
    """
    Creates a synthetic dataframe of churn data.
    """
    import numpy as np
    data = {
        'CustomerID': range(1, num_samples + 1),
        'Age': np.random.randint(18, 70, num_samples),
        'Gender': np.random.choice(['Male', 'Female'], num_samples),
        'Tenure': np.random.randint(1, 10, num_samples),
        'MonthlyBill': np.random.uniform(30, 150, num_samples),
        'TotalCharges': np.random.uniform(100, 1000, num_samples),
        'SubscriptionType': np.random.choice(['Basic', 'Premium', 'Standard'], num_samples),
        'UsageFrequency': np.random.randint(5, 30, num_samples),
        'SupportInteractions': np.random.randint(0, 5, num_samples),
        'Churned': np.random.choice([0, 1], num_samples, p=[0.8, 0.2]) # Imbalanced class for churn
    }
    df = pd.DataFrame(data)
    return df

# Create a sample churn dataset and save to CSV
df = create_churn_dataframe()
df.to_csv("churn_data.csv", index=False)

def generate_rfm_features(df):
    """Generates Recency, Frequency, Monetary Value (RFM) features."""
    df['Recency'] = np.random.randint(1, 30, len(df)) # Days since last interaction (replace with real calculation)
    df['Frequency'] = np.random.randint(1, 10, len(df)) # Total interactions (replace with real calculation)
    df['MonetaryValue'] = df['TotalCharges'] / df['Tenure'] # Avg charge per month
    return df

# Load the dataset
try:
    churn_df = pd.read_csv("churn_data.csv")
except FileNotFoundError:
    print("Error: Churn dataset 'churn_data.csv' not found. Please generate or download it.")
    exit()

# Generate RFM features
churn_df = generate_rfm_features(churn_df) # Add to dataframe

# Identify numerical and categorical features
numerical_features = ['Age', 'Tenure', 'MonthlyBill', 'TotalCharges', 'UsageFrequency', 'SupportInteractions', 'Recency', 'Frequency', 'MonetaryValue']
categorical_features = ['Gender', 'SubscriptionType']

# Create preprocessing pipelines
numerical_pipeline = Pipeline([
    ('scaler', StandardScaler())
])

categorical_pipeline = Pipeline([
    ('onehot', OneHotEncoder(handle_unknown='ignore'))
])

# Combine the pipelines using ColumnTransformer
preprocessor = ColumnTransformer([
    ('numerical', numerical_pipeline, numerical_features),
    ('categorical', categorical_pipeline, categorical_features)
])

# Create the model pipeline
pipeline = Pipeline([
    ('preprocessor', preprocessor),
    ('classifier', GradientBoostingClassifier(random_state=42)) # Use gradient boosting
])

# Split data into training and testing sets
X = churn_df.drop('Churned', axis=1)
y = churn_df['Churned']
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# Define the hyperparameter grid
param_grid = {
    'classifier__n_estimators': [100, 200],
    'classifier__learning_rate': [0.01, 0.1],
    'classifier__max_depth': [3, 5]
}

# Perform grid search with cross-validation
grid_search = GridSearchCV(pipeline, param_grid, cv=3, scoring='f1', verbose=1) #Use F1
grid_search.fit(X_train, y_train)

# Print the best hyperparameters
print("Best Hyperparameters:", grid_search.best_params_)

# Evaluate the best model on the test set
best_model = grid_search.best_estimator_
predictions = best_model.predict(X_test)
print("\nClassification Report:\n", classification_report(y_test, predictions))

```

**Explanation and Key Points:**

*   **Synthetic Data Generation:** The code generates synthetic churn data if a file isn't found.
*   **RFM Feature Engineering:** Added `generate_rfm_features` for a common technique in Customer Relationship Management (CRM). **Replace the random number generation with actual calculation logic**
*   **Gradient Boosting:** The pipeline now uses a Gradient Boosting Classifier, often more effective for churn prediction.
*   **F1 Score:** The `GridSearchCV` now uses the `f1` scoring metric because the churn dataset it unbalanced, and you should select the best features based on it.
*   **Dataset Location:** Make sure the Python script and the `churn_data.csv` are on the same local.
*   The key is now to run *pip install scikit-learn pandas*, there are no additional imports necessary

*   **Project 11.3: Image Classification Pipeline with Transfer Learning**

```python
import tensorflow as tf
from tensorflow.keras.preprocessing.image import ImageDataGenerator
from tensorflow.keras.applications import MobileNetV2
from tensorflow.keras.layers import Dense, GlobalAveragePooling2D
from tensorflow.keras.models import Model
from sklearn.pipeline import Pipeline
import numpy as np #Needed for this part
import os

# 1. Data Loading and Preprocessing
# Define image size and batch size
IMG_SIZE = (224, 224)
BATCH_SIZE = 32

# Define data directories
#The dataset can be downloaded from tensorflow: https://www.tensorflow.org/datasets/catalog/tf_flowers
#Remember to extract it to flowers_photos
DATA_DIR = "flowers_photos" # Change this to your local directory
try:
    #List all directories that will be the labels
    labels = [item for item in os.listdir(DATA_DIR) if os.path.isdir(os.path.join(DATA_DIR, item))]
except:
    print('Remember to create the folders')

# Create an ImageDataGenerator for data augmentation and preprocessing
datagen = ImageDataGenerator(
    rescale=1./255,
    rotation_range=20,
    width_shift_range=0.2,
    height_shift_range=0.2,
    horizontal_flip=True,
    validation_split=0.2  # 80% training, 20% validation
)

# Create data generators for training and validation
train_generator = datagen.flow_from_directory(
    DATA_DIR,
    target_size=IMG_SIZE,
    batch_size=BATCH_SIZE,
    class_mode='categorical',
    subset='training'
)

validation_generator = datagen.flow_from_directory(
    DATA_DIR,
    target_size=IMG_SIZE,
    batch_size=BATCH_SIZE,
    class_mode='categorical',
    subset='validation'
)

# 2. Transfer Learning with a Pre-trained Model
# Load the MobileNetV2 model (or another pre-trained model)
base_model = MobileNetV2(
    include_top=False,  # Exclude the classification layer
    weights='imagenet',
    input_shape=(IMG_SIZE[0], IMG_SIZE[1], 3)
)

# Freeze the weights of the pre-trained layers
base_model.trainable = False #Important for fine tunning

# 3. Adding a Custom Classification Head

# Add a global average pooling layer to reduce spatial dimensions
global_average_layer = GlobalAveragePooling2D()

# Add a dense layer with 128 units
prediction_layer = Dense(len(labels), activation='softmax') # len(labels) == the number of categories

# Create the model
inputs = tf.keras.Input(shape=(IMG_SIZE[0], IMG_SIZE[1], 3))
x = base_model(inputs, training=False)
x = global_average_layer(x)
outputs = prediction_layer(x)
model = tf.keras.Model(inputs, outputs) #final Keras model
# 4. Compiling and Training the Model

#Compile the model
model.compile(optimizer='adam',
              loss=tf.keras.losses.CategoricalCrossentropy(from_logits=False),
              metrics=['accuracy'])

# Train the model

epochs = 10 #Can be increased for fine tunning
history = model.fit(
    train_generator,
    steps_per_epoch=train_generator.samples // BATCH_SIZE,
    epochs=epochs,
    validation_data=validation_generator,
    validation_steps=validation_generator.samples // BATCH_SIZE
)

# Evaluate the model
loss, accuracy = model.evaluate(validation_generator, steps=validation_generator.samples // BATCH_SIZE)
print('Test accuracy :', accuracy)
#Plot images if needed.

# 5. Using Pipelines to combine the steps
from sklearn.base import BaseEstimator, TransformerMixin
from tensorflow.keras.applications.mobilenet_v2 import preprocess_input

class ImagePreprocessor(BaseEstimator, TransformerMixin):
    """Custom transformer to preprocess images for the model."""
    def __init__(self, img_size):
        self.img_size = img_size

    def fit(self, X, y=None):
        return self

    def transform(self, X):
        """Resize and preprocess images"""
        resized_images = [tf.image.resize(img, self.img_size) for img in X]
        preprocessed_images = [preprocess_input(img) for img in resized_images]
        return np.array(preprocessed_images)

class ModelWrapper(BaseEstimator, TransformerMixin):
    """Wrapper to use a Keras model in a Scikit-learn Pipeline."""
    def __init__(self, keras_model):
        self.keras_model = keras_model

    def fit(self, X, y=None):
        return self # Nothing to fit in this case
    def predict(self, X):
       return np.argmax(self.keras_model.predict(X), axis=1) #Predict and map to one value

    def transform(self, X):
        #Used to prepare data for prediction
        return self.predict(X)
    #def predict(self, X): # This is needed and does not exist transform, because some pipelines can not be predicted, also we need to change transform

#Example load images from files or any different source and fit into the skicit model
if __name__ == "__main__":

    # load images here

    # Create the pipeline (this will not run in the fit model directly
    # Because fit is done by the model, but in a normal use case
    image_pipeline = Pipeline([
        ('preprocessor', ImagePreprocessor(IMG_SIZE)),
        ('model_wrapper', ModelWrapper(model)) # Wrap keras model
        #Then in a predict call, the image will be prepared for the model
    ])
```

*   **Key Considerations:**

    *   **Dataset:** The code is setup to work with a *flowers_photos* dataset, but if it does not exist, it requests to create the folders and run the process again, if you have images on another data set, just change the load data and preprocess the data again to the model.
        Download the dataset here https://www.tensorflow.org/datasets/catalog/tf_flowers

    *   **Set up a python >= 3.8 environment**
    *   **Import tensorflow 2**
    *   Install with *pip install -U tensorflow tensorflow-datasets*
    *   *Make sure to be running in a GPU mode for fast processing*

        Then, the following folders must be created for the process to execute

        ```bash
        mkdir flowers_photos
        mkdir flowers_photos/daisy
        mkdir flowers_photos/dandelion
        mkdir flowers_photos/roses
        mkdir flowers_photos/sunflowers
        mkdir flowers_photos/tulips
        ```

        Where you copy the respective images.



*   **11.8 Conclusion:**

    This chapter provided a comprehensive guide to building machine learning pipelines with Scikit-learn. We explored how to create simple and complex pipelines, handle heterogeneous data with ColumnTransformer, create custom transformers, and optimize pipelines with grid search. By mastering these techniques, you'll be well-equipped to build robust and reproducible machine learning workflows.

