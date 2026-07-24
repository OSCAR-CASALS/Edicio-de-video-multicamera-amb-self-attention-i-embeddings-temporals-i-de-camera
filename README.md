# Edició de vídeo multicàmera (Català)
Notebooks amb els quals s'han elaborat dos models capaços de suggerir quin és el punt de vista més indicat a mostrar en un entorn multicàmera, un utilitza com a backbone **SwinV2** i l'altre **MobileNetV3**.

## Dependències

El codi presentat s'ha elaborat amb les següents llibreries:

|Llibreria  |Versió  |Descripció                                     |
|-----------|--------|-----------------------------------------------|
|Pytorch    |2.11    |Llibreria de Deep Learning amb suport per a GPU.|
|Timm       |1.0.27  |Col·lecció de codi obert que inclou models pre-entrenats de deep-learning.|
|torchvision|0.26.0  |Conjunts de datasets populars, models i conjunt d'operacions d'imatge en deep learning.|
|matplotlib |3.10.9  |Llibreria per a crear visualitzacions en Python.|
|numpy      |2.4.4   |Llibreria de python per a treballar amb arrays.|
|seaborn    |0.13.2  |Llibreria construïda sobre matplotlib que simplifica la creació de gràfics avançats.|

També es requereix el _dataset_ TVMCE (https://virtualfilmstudio.github.io/projects/multicam/) o un altre conjunt de dades estructurat de la mateixa forma, en concret han de posseir el següent:

- Un Json amb les mostres d'entrenament i altre amb les de test, cada mostra deu tenir la següent estructura:
    + _boundary_: Si s'ha considerat realitzar un canvi de càmera (1) o no (0).
    + _videoID_: Id del video al que pertany la mostra.
    + _CAMFrame_: Id del fotograma candidat.
    + _outputList_: Fotogrames anteriors al candidat.
    + _currentCam_: Id de la càmera que s'ha utilitzat en els fotogrames anteriors.
    + _CAMList_: Id de les càmeres a les que pertany cada candidat.
    + _selectCAM_: El Id de la càmera a la que pertany el fotograma candidat que finalment es va considerar.

- Un directori per als vídeos del conjunt d'entrenament, i un altre amb el de test, que segueixint la següent estructura:


entrenament\
&emsp;&emsp;|-- video_0002 (nom del vídeo)\
&emsp;&emsp;|&emsp;&emsp;|-- output (fotograma utilitzat al video final)\
&emsp;&emsp;|&emsp;&emsp;|&emsp;&emsp;|-- 18362.jpg (Fotograma)\
&emsp;&emsp;|&emsp;&emsp;|&emsp;&emsp;|-- ...\
&emsp;&emsp;|&emsp;&emsp;|-- CAM1 (Id de la càmera)\
&emsp;&emsp;|&emsp;&emsp;|&emsp;&emsp;|-- 18460.jpg\
&emsp;&emsp;|&emsp;&emsp;|&emsp;&emsp;|-- ...



test\
&emsp;&emsp;|-- video_0002 (nom del vídeo)\
&emsp;&emsp;|&emsp;&emsp;|-- output (fotograma utilitzat al video final)\
&emsp;&emsp;|&emsp;&emsp;|&emsp;&emsp;|-- 18362.jpg (Fotograma)\
&emsp;&emsp;|&emsp;&emsp;|&emsp;&emsp;|-- ...\
&emsp;&emsp;|&emsp;&emsp;|-- CAM1 (Id de la càmera)\
&emsp;&emsp;|&emsp;&emsp;|&emsp;&emsp;|-- 18460.jpg\
&emsp;&emsp;|&emsp;&emsp;|&emsp;&emsp;|-- ...



Finalment, les id de cada fotograma hauran de reflectir la seva posició temporal en el vídeo.

En el repositori s'inclou un entorn _pi_ amb les dependències necessàries per a executar els notebook.

``
pip install -r requirements.txt
``

## Arquitectura del model

Els models creats estan formats per 3 components:
- **Extractor de característiques**:  Crea un _feature vector_ amb les característiques d'un fotograma, el _frame offset_, i el id de la càmera. S'aplica tant a fotogrames passats com candidats.
- **Barrejador de fotogrames passats amb fotogrames candidats**:  Aplica self-attention sobre els feature vectors per a dotar-los de context.
- **MLP**: S'aplica només sobre els vectors amb context corresponents als candidats i s'encarrega d'assignar-los una puntuació en funció de que tan adequats són.

## Resultats

En les _releases_ del repositori, s'inclou en versió onnx del model que millors resultats ha donat, el que empra **SwinV2** com a backbone, per a que pugui ser utilitzat en entorns diferents a Pytorch.

En cas que es vulgui entrenar un model amb aquesta arquitectura sense utilitzar cap dels _backbones_ esmentats, es pot modificat la cel·la "_Definint timm backbone, dimensions, i nom del model_" present en els dos notebooks amb algun dels models pre-entrenats presents a _timm_.

Concretament, s'haurà d'especificar:

- Nom del backbone (variable _Timm\_Backbone_)
- Les dimensions de les capes del model, que han de correspondre amb el nombre de característiques que doni el _backbone_ per a cada imatge (variable _dimensions\_model_).
- L'amplada i alçada de les imatges requerides pel _backbone_(variables _image\_width_ i _image\_height_).
- Nom del model sota el qual es guardarà:
      + El _state\_dict_ del model.
      + Una versió en onnx del model en cas de que el notebook executat sigui SwinV2_Multicamera_Model.ipynb.

## Estructura

El repositori inclou:

|Artxiu|Descripció|
|------|----------|
|MobilNet_Multicamera_Model.ipynb| Codi amb el que s'ha creat i evaluat el model amb backbone MobileNetV3|
|SwinV2_Multicamera_Model.ipynb| Codi amb el que s'ha creat i evaluat el model amb backbone SwinV2|
|MobilNet_Multicamera_Model.html| Versió en html del notebook MobilNet_Multicamera_Model.ipynb|
|SwinV2_Multicamera_Model.html| Versió en html del notebook SwinV2_Multicamera_Model.ipynb|
|Edició de vídeo multicàmera amb self-attention i embeddings temporals i de càmera.pdf| Treball de fi de Master on es donen més detalls sobre la creació del model.|
|Multicamera video editing with self-attention and temporal and camera embeddings (English AI translation).pdf| Traducció al anglès feta amb IA del Treball de fi de Master.|
|requirements.txt| Entorn _pip_ amb el qual s'han executat els dos notebooks.|


# Multicamera Video Editing (English)

This repository contains the notebooks used to develop two models capable of suggesting the most appropriate camera viewpoint to display in a multicamera environment. One model uses **SwinV2** as its backbone, while the other uses **MobileNetV3**.

## Dependencies

The code was developed using the following libraries:

| Library | Version | Description |
|---------|---------|-------------|
| PyTorch | 2.11 | Deep learning library with GPU support. |
| timm | 1.0.27 | Open-source collection of pre-trained deep learning models. |
| torchvision | 0.26.0 | Collection of popular datasets, models, and image processing operations for deep learning. |
| matplotlib | 3.10.9 | Python library for data visualization. |
| numpy | 2.4.4 | Python library for numerical computing and array manipulation. |
| seaborn | 0.13.2 | Visualization library built on top of matplotlib that simplifies the creation of advanced plots. |

The **TVMCE** dataset (https://virtualfilmstudio.github.io/projects/multicam/) or another dataset with the same structure is also required. Specifically, the dataset must include:

- A JSON file containing the training samples and another containing the test samples. Each sample must follow the structure below:
    + _boundary_: Whether a camera switch was made (`1`) or not (`0`).
    + _videoID_: Identifier of the video the sample belongs to.
    + _CAMFrame_: Identifier of the candidate frame.
    + _outputList_: Previous frames before the candidate frame.
    + _currentCam_: Identifier of the camera used for the previous frames.
    + _CAMList_: Camera identifiers corresponding to each candidate frame.
    + _selectCAM_: Identifier of the camera to which the selected candidate frame belongs.

- One directory containing the training videos and another containing the test videos, both following the structure below:

```text
training
    |-- video_0002 (video name)
    |   |-- output (frame used in the final edited video)
    |   |   |-- 18362.jpg (frame)
    |   |   |-- ...
    |   |-- CAM1 (camera ID)
    |   |   |-- 18460.jpg
    |   |   |-- ...
```

```text
test
    |-- video_0002 (video name)
    |   |-- output (frame used in the final edited video)
    |   |   |-- 18362.jpg (frame)
    |   |   |-- ...
    |   |-- CAM1 (camera ID)
    |   |   |-- 18460.jpg
    |   |   |-- ...
```

Finally, the identifier of each frame must reflect its temporal position within the video.

The repository also includes a **pip** environment with all the required dependencies to run the notebooks.

```
pip install -r requirements.txt
```

## Model Architecture

The proposed models consist of three main components:

- **Feature Extractor**: Generates a feature vector from each frame, its frame offset, and the camera ID. This module is applied to both previous frames and candidate frames.
- **Past Candidate Mixer**: Applies self-attention over the feature vectors to provide them context.
- **MLP**: Operates only on the contextualized candidate feature vectors and assigns a score according to how suitable each candidate frame is.

## Results

This repository's _releases_ include an ONNX version of the best-performing model, which uses **SwinV2** as its backbone, allowing it to be deployed in environments other than PyTorch.

If you wish to train this architecture using a different backbone, you can modify the notebook cell named "_Definint timm backbone, dimensions, i nom del model_" in either notebook and replace the backbone with any of the pre-trained models available in _timm_.

Specifically, you must define:

- The backbone name (_Timm\_Backbone_).
- The dimensions of the model's layers (_dimensions\_model_), which must match the number of features produced by the selected backbone for each image.
- The image width and height required by the backbone (_image\_width_ and _image\_height_).
- The model name, which will be used to save:
    - The model's _state\_dict_.
    - An ONNX version of the model (when running `SwinV2_Multicamera_Model.ipynb`).

## Repository Structure

The repository contains:

| File | Description |
|------|-------------|
| `MobilNet_Multicamera_Model.ipynb` | Notebook used to create and evaluate the model with MobileNetV3 backbone. |
| `SwinV2_Multicamera_Model.ipynb` | Notebook used to create and evaluate the model with SwinV2 backbone. |
| `MobilNet_Multicamera_Model.html` | HTML version of `MobilNet_Multicamera_Model.ipynb`. |
| `SwinV2_Multicamera_Model.html` | HTML version of `SwinV2_Multicamera_Model.ipynb`. |
| `Edició de vídeo multicàmera amb self-attention i embeddings temporals i de càmera.pdf` | Master's thesis providing additional details about the proposed model. |
| `Multicamera video editing with self-attention and temporal and camera embeddings (English AI translation).pdf` | AI-generated English translation of the Master's thesis. |
| `requirements.txt` | pip environment used to run both notebooks. |










