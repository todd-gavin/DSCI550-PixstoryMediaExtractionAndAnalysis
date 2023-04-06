# DSCI550-PixstoryMediaExtractionAndAnalysis

Collaborators: Todd Gavin, Daniil Abruzzesse, Jai Agrawal, Tania Dawood

## Apache Tika GeoTopic Parser
1. Extract all fo the Narrative Text from the Pixstory Posts and convert them into their own respetive .txt files.
2. Start Lucene and GeoTopic Servers.
3. Looping through each of the Narrative Text files, parse the narrative for th Geotopic Name, Latitude, and Longitude.
4. Populate the Datasets with these 3 columsn fro each respective Pixstory Post.



## Image Analysis 

# Instructions for how to execute the code:

1. Download the 80,000 images from the data set. 
- Open file "image_downloadv1.ipynb" and run the code from top to bottom. 80,000+ images will begin to download in the 95k_Images folder. 
- Use "ls -1 | wc -l" in 95k_Images to view download progress.

2. Let up a local host for your downloaded photos: 
- set up a local host on your computer to access a list of image urls by navigating to the 95k_Images directory in terminal and entering "python -mhttp.server 8888"
- For Macs: Locate and record IP address for en0 by running "iconfig" in terminal 
- You can now access your local port of images here: 'http://<IP Address>:8888'

3. Prepare computer for image caption generation:
- Download docker desktop app
- Run the following on the Tika Image Dockers folder: "docker run -it -p 8764:8764 uscdatascience/im2txt-rest-tika"




