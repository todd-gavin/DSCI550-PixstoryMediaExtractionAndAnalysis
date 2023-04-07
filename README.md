# DSCI550-PixstoryMediaExtractionAndAnalysis

Collaborators: Todd Gavin, Daniil Abruzzesse, Jai Agrawal, Tania Dawood

## Apache Tika GeoTopic Parser
1. Extract all fo the Narrative Text from the Pixstory Posts and convert them into their own respetive .txt files.
2. Start Lucene and GeoTopic Servers.
3. Looping through each of the Narrative Text files, parse the narrative for th Geotopic Name, Latitude, and Longitude.
4. Populate the Datasets with these 3 columsn fro each respective Pixstory Post.



# Image Analysis 

## Instructions for installing dependencies:

1. Install the docker desktop app
2. pip install tensorflow
3. git clone https://github.com/tensorflow/models.git
4. $ export PYTHONPATH="$PYTHONPATH:`pwd`:`pwd`/slim"
5. $ python -c 'import tensorflow, numpy, dataset; print("OK")'
6. git clone https://github.com/USCDataScience/tika-dockers.git && cd tika-dockers
7. docker build -f InceptionRestDockerfile -t uscdatascience/inception-rest-tika 


## Instructions for how to execute the code:

1. Download the 80,000 images from the data set. 
- Open file "image_downloadv1.ipynb" and run the code from top to bottom. 80,000+ images will begin to download in the 95k_Images folder. 
- Use "ls -1 | wc -l" in 95k_Images to view download progress.

2. Let up a local host for your downloaded photos: 
- Set up a local host on your computer to access a list of image urls by navigating to the 95k_Images directory and running "python -mhttp.server 8888"
- For Macs: Locate and record IP address for en0 by running "iconfig" in terminal. You can now access your local port of images here: 'http://10.25.179.208:8888' where 10.25.179.208 is the IP address for en0 (replace with your actual IP address).

3. Image caption generation:
- Run the following on the Tika Image Dockers folder: "docker run -it -p 8764:8764 uscdatascience/im2txt-rest-tika"
- Open "captions_image_analysis.ipynb" and run code from top to bottom. This code will output a JSON file of images and their respective captions. Note that this will take roughly 20-30 hours to run, however the the code is written such each that every time 100 JSON objects are generated they are added to the JSON file, so you can pick up where you left off if the program terminates.
  
 4. Image objects detected generation:
 - Go to Docker Desktop and kill the container
 - Run the following on the Tika Image Dockers folder: "docker run -it -p 8764:8764 uscdatascience/inception-rest-tika"
 - Open "object_recognition_image_analysis.ipynb" and run code from top to bottom. This code will output a JSON file of images and their respective list of detected objects. Note that this will also take roughly 20-30 hours to run. Each time 100 JSON objects are generated they are added to the JSON file, so you can pick up where you left off if the program terminates.
  
5. Turn JSON files into DFs and merge into one DF that contains image URLs, image captions, and image detected objects
 
# Lang Detect
1. Pip install langdetect
2. Pip install Tika
# Translations

1. You need to make sure you have tika and get the docker image running with this command:
IMAGE=tgowda/rtg-model:500toEng-v1
docker run --rm -i -p 6060:6060 $IMAGE
2. You’ll also need to pip install tika and emoji.
3. Please make sure you have Java1.8 or higher intsalled on the environment you are using to run the notebook

# Detoxify
1. Make sure Detoxify and it's dependencies are installed
2. Run the notebook "Detoxification.ipynb" in 5_PyTorch Detoxify, and the scores will generate.

# Report Questions

 
1. Are there any age or gender or topic based correlations by location in the posts? 

2. What is the most prevalent language in the posts, and least prevalent? 

3. Is there a correlation between post language and identified mentioned locations? 

4. Are  there  correlations  between  the  sporting  events,  or  the  entertainment  events  with locations?  

5. Do the Detoxify scores and associated GLAAD and ADL or sarcasm flags line up? Is there any relationship between the flags and the identified Detoxify scores?

6. Do the image captions accurately represent the image? 

7. Are the identified objects present in the image described in the original post and/or the generated caption?

8. Are  there  any  age,  or  gender  specific  trends  you  see  in  the  text  captions  or  identified objects in the image media?

9. What are your thoughts about the ML and Deep Learning software like RTG, GeoTopicParser, Detoxify, LangDetect, Tika Image Captioning, etc. – what was easy about using it? What wasn’t?`


