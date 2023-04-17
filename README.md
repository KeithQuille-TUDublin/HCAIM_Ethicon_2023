# HCAIM Ethicon 2023

<img src=https://humancentered-ai.eu/wp-content/uploads/2022/04/boy-index.png width=400>


  <b>Table of Contents</b>
  <ol>
    <li><a href="#Overview">Overview</a></li>
    <li><a href="#Instructions">Instructions</a></li>
    <li><a href="#Dataset">Dataset</a></li>
    <li><a href="#Code">Code</a></li>
  </ol>


## Overview
<i>(Note: all images and logos below were generated by OpenAi.com DALL.E 2)</i>


<img src=https://github.com/KeithQuille-TUDublin/HCAIM_Ethicon_2023/blob/16e35b153209d97dd526bdb37ecebdbae9dcd46b/components/evilVacCompany.jpg width=200>


A new vaccine company- Vavintiilo, has created a new vaccine that claims to eliminate cancer. They plan to roll this out without the need for clinicians or doctors to administer the vaccine itself, thus aiming to stop delays and provide vaccines for everyone on the planet in a very short time. 


<img src=https://github.com/KeithQuille-TUDublin/HCAIM_Ethicon_2023/blob/568dba99df7392aadda358e745e3133567f621f0/components/vaccineBooth.jpg width=200>


To do this they are building automatic vaccine booths where a picture is taken as a person enters the booth to receive their vaccine, and depending on the AI that processes the image, one of two vaccines is administered. 

**Concerns for Ethicon**

There is a significant concern over the image system, as the vaccines have significant effects if given to the wrong sex. The company makes two vaccines, one for males and one for females. If given to the wrong sex, the vaccine can have significant effects on the reproduction system for that person, thus the image recognition system for the automated booths is a real concern.


The company has claimed that it is very accurate at identifying sex when a person enters the booth, thus assuring the public that the overall system is safe. It was trained using the UTKFace dataset which has an almost even distribution of male and female faces (more on this dataset set below). Their rationale is the distribution of sex in the dataset (almost equal representation, as presented in the figure below):

<img src=https://github.com/KeithQuille-TUDublin/HCAIM_Ethicon_2023/blob/d3baaaab926dd264c442d25c14142ce30dea500d/components/distribution.png width=400>


The company has released access to its production API so that we can test the soundness of its sex identification system for the automated booths. 


## Instructions
The Ethicon challenges students to investigate if the Vavintiilo facial identification system is indeed as accurate as they say and if it negatively affects target-groups (such as gender, race or age). To do this you must download images from the UTKFace dataset (see Dataset section for details). Then you must use the sample code that uses the Vavintiilo API to determine if the system is biased. There are three parts to the Ethicon:

* First you must carefully (due to time constraints and API call limitations - it takes time per API request) select sample images that are representative of gender, race and age and determine if the system is as good as they say at predicting the gender of the person entering the booth. **(time allotted 1 hour)**

* The conclusion of this Ethicon is a presentation detailing any biases that you have found and how they may affect the people being vaccinated. **(time allotted 30 minutes)**

* The presentations and judging of the winners **(time allotted 30 minutes)**


## Dataset
UTKFace dataset:

<img src=https://github.com/KeithQuille-TUDublin/HCAIM_Ethicon_2023/blob/22f1c2c5a620eb8d53f5167a26d5f39c5bbfb029/components/logoWall2.jpg width=400>

We will be using the same dataset as Vavintiilo (<b>note: we used the Aligned&Cropped Faces version</b>), full details on this dataset, its file naming scheme and the download zip file can be found at: https://github.com/aicip/UTKFace 

It can also be downloaded via:  https://1drv.ms/f/s!An-taX4pRW2np7QrH7VlnvqvTd_Iuw?e=Qxnc6M

The labels of each face image are embedded in the file name, formatted like `[age]_[gender]_[race]_[date&time].jpg`

* `[age]` is an integer from 0 to 116, indicating the age
* `[gender]` is either 0 (male) or 1 (female)
* `[race]` is an integer from 0 to 4, denoting White, Black, Asian, Indian, and Others (like Hispanic, Latino, Middle Eastern).
* `[date&time]` is in the format of yyyymmddHHMMSSFFF, showing the date and time an image was collected to UTKFace


This dataset/hosting of the dataset is credited to: [Yang Song](http://web.eecs.utk.edu/~ysong18/) and [Zhifei Zhang](http://web.eecs.utk.edu/~zzhang61/).

## Code

Vavintiilo API URL: `http://20.254.176.109:8501/v1/models/img_classifier:predict`

**Notes**: The code below assomes you have the following folder structure (you can cahange this depending on the IDE you are using):
- dataset
  - UKTFace
    - <i>Images</i>

  
The images are 200 x 200 px in RGB format. This is required for the API.

The Docker Hub Image (not needed for this Ethicon) is: `https://hub.docker.com/repository/docker/kquille/ethicon_api/general`

A Google CoLab copy can be found at: `https://colab.research.google.com/drive/1xwx9rzT7wIJCSAW4Hs28yQ-G3MvexiFh?usp=sharing`


```
import matplotlib.pyplot as plt
%matplotlib inline  
import time
import numpy as np

from PIL import Image
import glob

import requests
import json


# API URL and restful port
apiUrl = 'http://20.254.176.109:8501/v1/models/img_classifier:predict'

# function to query API
def make_prediction(instances):
    data = json.dumps({"signature_name": "serving_default", "instances": instances.tolist()})
    headers = {"content-type": "application/json"}
    json_response = requests.post(apiUrl, data=data, headers=headers)
    predictions = json.loads(json_response.text)["predictions"]
    return predictions

# This path for the images will be different depending on local IDE used
im=Image.open(r"dataset\UTKFace\50_0_0_20170120221455142.jpg.chip.jpg")

# Convert image to array and normalise (the RGB range is 256 values thus 0-255 is the normalisation range) 
X = np.array(im)
X = X /255

# Converting to array and reshaping for use in the CNN ([one image, 200 px, 200px, 3 channels RGB])    
instances = np.array(X)
X = X.reshape(1,200, 200, 3).astype('float32')

# Make prediction from API
predictions = make_prediction(X)

# Show prediction and plot image used
pred = predictions[0]
imgplot = plt.imshow(im)
plt.show()
if np.argmax(pred) == 1:
    print("Female")
else:
    print("Male")
```

**Sample output:**
<br><br>


<img src=https://github.com/KeithQuille-TUDublin/HCAIM_Ethicon_2023/blob/1bcf497ed3621c89f2935b0ac5924497892a245a/components/sampleOutput.png width=800>

