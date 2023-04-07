# DSCI550-PixstoryMediaExtractionAndAnalysis

Collaborators: Todd Gavin, Daniil Abruzzesse, Jai Agrawal, Tania Dawood

#### For information on how to install the neccessary dependencies and softwares, please refer to the `.md` file titled `Software_Installation_Instructions.md`.

## Run Apache Tika Image Analysis - Caption Generation and Object Recognition

### Instructions for installing dependencies:
1. Install the docker desktop app
2. Run command: `pip install tensorflow`
3. Run command: `git clone https://github.com/tensorflow/models.git`
4. Run command: `export PYTHONPATH="$PYTHONPATH:`pwd`:`pwd`/slim"`
5. Run command: `python -c 'import tensorflow, numpy, dataset; print("OK")'`
6. Run command: `git clone https://github.com/USCDataScience/tika-dockers.git && cd tika-dockers`
7. Run command: `docker build -f InceptionRestDockerfile -t uscdatascience/inception-rest-tika`

### Instructions for how to execute the code:
1. Download the 80,000 images from the data set. 
    - Open file `image_downloadv1.ipynb` and run the code from top to bottom. 80,000+ images will begin to download in the 95k_Images folder. 
    - Use `ls -1 | wc -l` in 95k_Images to view download progress.
    - Note: We downloaded 80,581 images out of a total of 80,585 images. This is because 4 images returned errors when downloading due to their link being broken.  
2. Set up a local host for your downloaded photos: 
    - Set up a local host on your computer to access a list of image urls by navigating to the 95k_Images directory and running `python -mhttp.server 8888`
    - For Macs: Locate and record IP address for en0 by running `iconfig` in terminal. You can now access your local port of images here: `http://10.25.179.208:8888` where `10.25.179.208` is the IP address for en0 (replace with your actual IP address).
3. Image caption generation:
    - Run the following on the Tika Image Dockers folder: `docker run -it -p 8764:8764 uscdatascience/im2txt-rest-tika`
    - Open `captions_image_analysis.ipynb` and run code from top to bottom. This code will output a JSON file of images and their respective captions. Note that this will take roughly 20-30 hours to run, however the the code is written such each that every time 100 JSON objects are generated they are added to the JSON file, so you can pick up where you left off if the program terminates.
4. Image objects detected generation:
    - Go to Docker Desktop and kill the container
    - Run the following on the Tika Image Dockers folder: `docker run -it -p 8764:8764 uscdatascience/inception-rest-tika`
    - Open `object_recognition_image_analysis.ipynb` and run code from top to bottom. This code will output a JSON file of images and their respective list of detected objects. Note that this will also take roughly 20-30 hours to run. Each time 100 JSON objects are generated they are added to the JSON file, so you can pick up where you left off if the program terminates.
5. Turn JSON files into DFs and merge into one DF that contains image URLs, image captions, and image detected objects
 
## Run Tika and Google Language Detect
1. `pip install langdetect`
2. `pip install Tika`
Insights: 
Language detection found that English was the most prevalent language and Telugu the least prevalent. This analysis also found differences between Tika and Google Lang Detect in identifying the languages.

## Run Tika Language Translations
1. You need to make sure you have tika and get the docker image running with this command:
- `IMAGE=tgowda/rtg-model:500toEng-v1`
- `docker run --rm -i -p 6060:6060 $IMAGE`
2. You’ll also need to pip install tika and emoji.
3. Please make sure you have Java1.8 or higher intsalled on the environment you are using to run the notebook

## Run Apache Tika GeoTopic Parser End-to-End
1. Kill all java processes and kill tika server is already running (refer to Errors section fo ReadMe)
    - `killall java`
    - List current processes: `jps`
        - Kill all tika processes with: `kill <process number>`
2. Navigate to directory /3_Tika_GeoTopic_Parser and run command to start lucene server: `lucene-geo-gazetteer -server`
    - Should see this if working: `INFO: Starting ProtocolHandler ["http-nio-8765"]`
    - If its not working, re-run command to set up path inside of /lucene-geo-gazetteer/src/main/bin: `export PATH=$PWD:$PATH`
3. In new terminal window, navigate to directory /3_Tika_GeoTopic_Parser and run command to start geotopic server: `./geotopic-server`
    - Should see this if working: `INFO  [main] 16:25:04,222 org.apache.tika.server.core.TikaServerProcess Started Apache Tika server ff835cb6-9aa1-4817-ba8d-d035eb174c87 at http://localhost:9998/`
4. In new terminal window again, navigate to directory /3_Tika_GeoTopic_Parser and run command to test servers: `curl -T polar.geot -H "Content-Type: application/geotopic; filename=polar.geot" http://localhost:9998/rmeta | python -mjson.tool`
    - Should Get if running correctly: `United States, 39.76, -98.5`
5. Run `geotopicParser.ipynb` on the 95k text files generated from the dataset.

## Run Detoxify
1. Make sure Detoxify and it's dependencies are installed
2. Run the notebook `Detoxification.ipynb` in directory `5_PyTorch Detoxify`, and the scores will generate.

# Report Questions
1. Are there any age or gender or topic based correlations by location in the posts? 

Note: Visualizations can be found in `/6_Report Questions/q1_figures/`

### Age and Location
There are a few significant correlations between Age and GeoTopic Name (location). To determine these correlations, I created a heatmap that shows the GeoTopic Name along the X-axis and the Age along the Y-Axis. 
    - As we can see from figure "ageVsLocationHeatMap.png", the GeoTopic name of "British Indian Ocean Territory" has the greatest clustering of asssoiciating user narratives with ages between 18 and 24. This group also has a signficiant amount of users between the ages of 42 and 47. 
    - This is logical because Pixstory is a social media that is very popular in India.
    - The GeoTopic Name "Islamic Republic of Afghanistan" has a cluster of associated user narratives with ages between 21 and 22.
    - The GeoTopic Name "Manchester" has a cluster of associated user narratives with ages between 21 and 24.

Overall, for all GeoTopic Names, narratives with associating user ages between 18 and 26 is the greatest.

### Gender and Location
There are a few significant correlations between Gender and GeoTopic Name (location). To determine these correlations, I created a heatmap that shows the GeoTopic Name along the X-axis and the Gender along the Y-Axis. 
    - As we can see from figure "genderVsLocationHeatMap.png", the GeoTopic name of "British Indian Ocean Territory" has the greatest number of associaitng user narratives for males over females.
    - The "Islamic Republic of Afghanistan" is the only GeoTopic with more females than males.

### Interests and Location
There are a few significant correlations between Interest and GeoTopic Name (location). To determine these correlations, I created a heatmap that shows the GeoTopic Name along the X-axis and the Interests along the Y-Axis. 
    - As we can see from figure "interestsVsLocationHeatMap.png", the GeoTopic name of "British Indian Ocean Territory" has the greatest greatest clustering for multiple interests such as health, politics, and sports.
    - However, the Geotopic "Russian Federation", shows a high amount if Interest clustering for topics surrounfing the Russia-Ukraine War such as war, ukraine war, ukraine, us government, vladimir putin, etc.

2. What is the most prevalent language in the posts, and least prevalent? 

Note: Visualizations can be found in `/6_Report Questions/31_figures/`

In the figures "tikaLanguageVsLocationHeatMap.png" and "googleLanguageVsLocationHeatMap", we can see that a majority of the clustering is for language code en (english). The largest clusetrings though is for the GeoTopics of "British Indian Ocean Territory", "Delhi", "Manchester", and "Islamic Republic of Afghanistan". Behind the language code en (english), the second highest language code is it (italian), with ofcourse the GeoTopic "Town of Italy" (and other related GeoTopics) have the highest clustering.

3. Is there a correlation between post language and identified mentioned locations? 

4. Are  there  correlations  between  the  sporting  events,  or  the  entertainment  events  with locations?  

5. Do the Detoxify scores and associated GLAAD and ADL or sarcasm flags line up? Is there any relationship between the flags and the identified Detoxify scores?

6. Do the image captions accurately represent the image? 
- Findings: image caption generation is relatively inconsistent; there are many instances of a proper description but many that entirely missrepresent what is shown in the image. 

7. Are the identified objects present in the image described in the original post and/or the generated caption?
- Findings: on average, the identified objects present captured 26.8 percent of the narratives and 23.5 percent of the generated captions. This indicates that the identified objects presen in the image, on average, are not strongly related to what was described in the original post or the generated caption.

8. Are  there  any  age,  or  gender  specific  trends  you  see  in  the  text  captions  or  identified objects in the image media?

9. What are your thoughts about the ML and Deep Learning software like RTG, GeoTopicParser, Detoxify, LangDetect, Tika Image Captioning, etc. – what was easy about using it? What wasn’t?`
