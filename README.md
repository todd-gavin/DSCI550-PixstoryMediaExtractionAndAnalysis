# DSCI550-PixstoryMediaExtractionAndAnalysis

Collaborators: Todd Gavin, Daniil Abruzzesse, Jai Agrawal, Tania Dawood

## Apache Tika GeoTopic Parser
1. Extract all fo the Narrative Text from the Pixstory Posts and convert them into their own respetive .txt files.
2. Start Lucene and GeoTopic Servers.
3. Looping through each of the Narrative Text files, parse the narrative for th Geotopic Name, Latitude, and Longitude.
4. Populate the Datasets with these 3 columsn fro each respective Pixstory Post.



# Image Analysis 

## Instructions for how to execute the code:

1. Download the 80,000 images from the data set. 
- Open file "image_downloadv1.ipynb" and run the code from top to bottom. 80,000+ images will begin to download in the 95k_Images folder. 
- Use "ls -1 | wc -l" in 95k_Images to view download progress.

2. Let up a local host for your downloaded photos: 
- Set up a local host on your computer to access a list of image urls by navigating to the 95k_Images directory and running "python -mhttp.server 8888"
- For Macs: Locate and record IP address for en0 by running "iconfig" in terminal. You can now access your local port of images here: 'http://10.25.179.208:8888' where 10.25.179.208 is the IP address for en0 (replace with your actual IP address).

3. Image caption generation:
- Download docker desktop app
- Run the following on the Tika Image Dockers folder: "docker run -it -p 8764:8764 uscdatascience/im2txt-rest-tika"
- Open "captions_image_analysis.ipynb" and run code from top to bottom. This code will output a JSON file of images and their respective captions. Note that this will take roughly 20-30 hours to run, however the the code is written such each that every time 100 JSON objects are generated they are added to the JSON file, so you can pick up where you left off if the program terminates.
  
 4. Image objects detected generation:
 - Go to Docker Desktop and kill the container
 - Run the following on the Tika Image Dockers folder: "docker run -it -p 8764:8764 uscdatascience/inception-rest-tika"
 - Open "object_recognition_image_analysis.ipynb" and run code from top to bottom. This code will output a JSON file of images and their respective list of detected objects. Note that this will also take roughly 20-30 hours to run. Each time 100 JSON objects are generated they are added to the JSON file, so you can pick up where you left off if the program terminates.
  
5. Turn JSON files into DFs and merge into one DF that contains image URLs, image captions, and image detected objects
  
# Translations

1. You need to make sure you have tika and get the docker image running with this command:
IMAGE=tgowda/rtg-model:500toEng-v1
docker run --rm -i -p 6060:6060 $IMAGE
2. You’ll also need to pip install tika and emoji. I accidentally installed / imported requests too but didn’t end up using it
3. Please make sure you have Java1.8 or higher intsalled on the environment you are using to run the notebook


