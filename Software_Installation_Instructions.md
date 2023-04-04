## 1. Install  Google’s  LangDetect  using  PIP  and  the  instructions  here 
https://pypi.org/project/langdetect/  

Run Command: `pip install langdetect`

## 2. Install  RTG  (Reader  Translator  Generator)  using  the  instructions  here 
https://gowda.ai/posts/2021/04/mtdata-nlcodec-rtg-many-english/  The  result  will  be  a 
REST service running on port 6000 

> MTData
A tool to download machine translation datasets https://github.com/thammegowda/mtdata/
- `pip install mtdata`

> NLCodec
https://isi-nlp.github.io/nlcodec/
- `pip install nlcodec`

> RTG
https://isi-nlp.github.io/rtg/
- `pip install rtg`

## 3. Download  and  install  Tika  Python  using  PIP  and  the  instructions  at 
http://github.com/chrismattmann/tika-python 
- With the RTG running, running tika’s translate module will automatically work fine (since it will pick up the RTG server) 
- The Tika language module (it’s language detector) should also work fine 

Run Command: `pip install tika`
- To check what tika is installed, run command: `pip show tika`

## 4. Install GeoTopicParser using the instructions here 
- Refer to DSCI550 Slack thread: https://uscdatascience.slack.com/archives/C04JM790KHS/p1679846853060489

#### Installing the Lucene Gazetteer
https://cwiki.apache.org/confluence/display/tika/GeoTopicParser  
> First you will need to download the Lucene Geo Gazetteer project and to install it. You can do so by:
- $ `cd $HOME/src` - change directory to the Repo directory
- $ `git clone https://github.com/chrismattmann/lucene-geo-gazetteer.git`
- $ `cd lucene-geo-gazetteer`
- $ `mvn install assembly:assembly` 
    - refer to "How to Install Apache Maven MVN"
- Change directory to `/src/main/bin/` inside of dir lucene-geo-gazetteer.
- $ `export PATH=$PWD:$PATH`

#### How to Install Apache Maven MVN 
- Install Maven: `brew install maven`

#### How to Use Lucene GeoGazetter
- The  result  of  this  should  be  the  Lucene  GeoGazetter  REST  server  running  as 
specified here: https://github.com/chrismattmann/lucene-geo-gazetteer   

Note: Run these commands inside the lucene-geo-gazetteer directory
1. `curl -O http://download.geonames.org/export/dump/allCountries.zip`
2. `unzip allCountries.zip`
3. `java -cp target/lucene-geo-gazetteer-0.3-SNAPSHOT-jar-with-dependencies.jar edu.usc.ir.geo.gazetteer.GeoNameResolver -i geoIndex -b allCountries.txt`
4. `java -cp target/lucene-geo-gazetteer-0.3-SNAPSHOT-jar-with-dependencies.jar edu.usc.ir.geo.gazetteer.GeoNameResolver -i geoIndex -s Pasadena Texas` (testing with e.g. Pasedena, Texas)
5. Test with: `lucene-geo-gazetteer -s Pasadena Texas -json | python -mjson.tool`
6. Test Service Mode (with e.g. Pasedena, Texas) in base terminal:
    - Launch Server: $ `lucene-geo-gazetteer -server`
    - Query: `$ curl "localhost:8765/api/search?s=Pasadena&s=Texas&c=2"`
        - `curl "http://localhost:8765/api/search?s=Pasadena&s=Texas" | python -mjson.tool`

#### How to Connect to Lucene GeoGazetter in Python
- You can connect the GeoGazetteer to Tika-Python using the instructions here: 
https://github.com/chrismattmann/tika-python#changing-the-tika-classpath  

#### Once Lucene GeoGazetter Server is Installed and Working, Now download and set up the NER model, and then link it to Tika [SKIP_IF_location-ner-model_dir_CREATED]
1. Create new directory in repo: `mkdir location-ner-model`
2. `cd location-ner-model`
3. Run curl command inside location-ner-model directory: `curl -O https://opennlp.sourceforge.net/models-1.5/en-ner-location.bin`
4. `mkdir -p org/apache/tika/parser/geo/`
5. `mv en-ner-location.bin org/apache/tika/parser/geo/`
6. `ls org`
7. `pwd`
8. `ls -alR`
    - Should see something like this: 
```js
total 0
drwxr-xr-x@  3 toddgavin  staff   96 Mar 26 19:06 .
drwxr-xr-x@ 19 toddgavin  staff  608 Mar 26 19:04 ..
drwxr-xr-x@  3 toddgavin  staff   96 Mar 26 19:06 or
```
#### Now we have to create the new application/geotopic MIME type, and map it to Tika. [SKIP_IF_geotopic-mime_dir_CREATED]
1. `mkdir geotopic-mime`
2. `cd geotopic-mime`
3. `mkdir -p org/apache/tika/mime`
4. `curl -O https://raw.githubusercontent.com/chrismattmann/geotopicparser-utils/master/mime/org/apache/tika/mime/custom-mimetypes.xml`
5. `mv custom-mimetypes.xml org/apache/tika/mime`
6. `ls org/apache/tika/mime/`

#### Now you need to grab an example of a file that you want to run the GeoTopicParser on... [SKIP_IF_polar.geot_CREATED]
1. `curl -LO https://raw.githubusercontent.com/chrismattmann/geotopicparser-utils/master/geotopics/polar.geot`
2. `cat polar.geot`

#### Final step is that you need a copy of Tika App, Tika Server, and also the Tika NLP-ML module (which has the GeoTopicParser in it). You can build all of these by building Tika, but there's an easier way. Just grab the JAR files. [SKIP_IF_tika-build_dir_CREATED]
1. `mkdir tika-build`
2. `cd tika-build`
3. Tika Parser NLP Package: `curl -LO https://repo1.maven.org/maven2/org/apache/tika/tika-parser-nlp-package/2.7.0/tika-parser-nlp-package-2.7.0.jar`
4. Tika App: `curl -LO https://repo1.maven.org/maven2/org/apache/tika/tika-app/2.7.0/tika-app-2.7.0.jar`
5. Tika Server: `curl -LO https://repo1.maven.org/maven2/org/apache/tika/tika-server-standard/2.7.0/tika-server-standard-2.7.0.jar`

#### > Now, we can run the command to test out the GeoTopicParser, first from the TikaApp / Command line interface (CLI). Then we'll run a Tika REST server, and try it there too.

#### Created a simple script, which I will paste below called geotopic-parser that wraps the Java command and classpaths and allows you to run it on a single file. [SKIP_IF_geotopic-parser_CREATED]
1. Create file geotopic-parser file:
```sh
TIKA_VERSION=2.7.0
export f=$1

java -classpath tika-build/tika-app-${TIKA_VERSION}.jar:tika-build/tika-parser-nlp-package-${TIKA_VERSION}.jar:${PWD}/location-ner-model:${PWD}/geotopic-mime \
		org.apache.tika.cli.TikaCLI -m $f
```
2. Give executable permission to the file geotopic-parser by running command: `chmod +x geotopic-parser`
3. Now, I will run it on the polar.geot file: `./geotopic-parser polar.geot`

#### Start up the geotopic-REST server [SKIP_IF_geotopic-server_CREATED]
- And then similarly create a simple script to do that too, called geotopic-server which I will paste below (don't forget to chmod +x geotopic-server before running it.) 
- Note once you run it, it will take control of the terminal unless you put it in the background, so you'll need a new terminal to test it out.

1. Create file geotopic-server file:
```sh
export TIKA_VERSION=2.7.0

java -classpath ${PWD}/location-ner-model:${PWD}/geotopic-mime:tika-build/tika-server-standard-${TIKA_VERSION}.jar:tika-build/tika-parser-nlp-package-${TIKA_VERSION}.jar \
     org.apache.tika.server.core.TikaServerCli     
```
2. Give executable permission to the file geotopic-server by running command: `chmod +x geotopic-server`
3. Run command: `./geotopic-server`
4. If its working, test out the GeoTopic REST server by opening new base terminal and running: `curl -T polar.geot -H "Content-Disposition: attachment; filename=polar.geot" http://localhost:9998/rmeta | python -mjson.tool`
    - `curl -T polar.geot -H "Content-Type: application/geotopic; filename=polar.geot" http://localhost:9998/rmeta | python -mjson.tool`
OUTPUT:
```js
[
    {
        "Geographic_LONGITUDE": "105.0",
        "Geographic_NAME": "People\u2019s Republic of China",
        "X-TIKA:Parsed-By-Full-Set": [
            "org.apache.tika.parser.DefaultParser",
            "org.apache.tika.parser.geo.GeoParser"
        ],
        "resourceName": "polar.geot",
        "Optional_NAME1": "United States",
        "Optional_LATITUDE1": "39.76",
        "Optional_LONGITUDE1": "-98.5",
        "X-TIKA:Parsed-By": [
            "org.apache.tika.parser.DefaultParser",
            "org.apache.tika.parser.geo.GeoParser"
        ],
        "X-TIKA:parse_time_millis": "1300",
        "X-TIKA:embedded_depth": "0",
        "Geographic_LATITUDE": "35.0",
        "Content-Length": "881",
        "Content-Type": "application/geotopic"
    }
]
```

#### Run GeoTopic Parser End-to-End
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

#### Running GeoTopic Server from Python
If you want to call your new GeoTopic server from Python, using Tika-Python it's simple! You just drop into Python, and run Tika on a *.geot file. 
```python
from tika import parser
parsed = parser.from_file('polar.geot', headers={ 'Content-Type' : 'application/geotopic'})
print(parsed)
```
OUTPUT:
```js
{'metadata': {'Geographic_LONGITUDE': '-98.5', 'Geographic_NAME': 'United States', 'X-TIKA:Parsed-By-Full-Set': ['org.apache.tika.parser.DefaultParser', 'org.apache.tika.parser.geo.GeoParser'], 'resourceName': "b'polar.geot'", 'Optional_NAME1': 'People's Republic of China', 'Optional_LATITUDE1': '35.0', 'Optional_LONGITUDE1': '105.0', 'X-TIKA:Parsed-By': ['org.apache.tika.parser.DefaultParser', 'org.apache.tika.parser.geo.GeoParser'], 'X-TIKA:parse_time_millis': '61', 'X-TIKA:embedded_depth': '0', 'Geographic_LATITUDE': '39.76', 'Content-Length': '881', 'Content-Type-Override': 'application/geotopic', 'Content-Type': 'application/geotopic'}, 'content': None, 'status': 200}
```

## 5. Install Detoxify using PIP and the instructions here: 
https://pypi.org/project/detoxify/  

Run Command: `pip install detoxify`

- Note that if you are using Mac and Python, using pyenv, and you run into issues 
installing  Detoxify  and  torch  with  PIP,  see  this  for  an  easy  workaround 
https://github.com/pytorch/pytorch/issues/53601#issuecomment-967307449   

## 6. Install Tika Image Dockers and generate captions for your Pixstory images posts 
- To  access  the  images,  use  the  URL  from  the  post  and give  it  the  URL  prefix 
“/optimized”,  such  as:  https://image.pixstory.com/optimized/Pixstory-image-
164416629024955.jpeg  

1. Download all 95k images associated with the posts 
    - Write a simple python script to do this 
2. Install Tika Dockers package for Image Captioning and Object Recognition 
    - Run Commands:
        - `git clone https://github.com/USCDataScience/tika-dockers.git`
        - Need to open "Docker Desktop" on Mac and ensure it is running before exectuign this command.
            - `docker pull uscdatascience/im2txt-rest-tika`
    - Read  and  test  out: https://cwiki.apache.org/confluence/display/TIKA/TikaAndVisionDL4J   
    - Read and test out: https://github.com/apache/tika/pull/189  
3. Iterate through all the Pixstory posts and add the generated image caption and the 
detect object(s) column to your dataset

## Errors
1. Kill all java processes: `killall java`
2. Kill tika server:
    1. To view running processes: `jps`
    2. They type the process number: `kill <process>`