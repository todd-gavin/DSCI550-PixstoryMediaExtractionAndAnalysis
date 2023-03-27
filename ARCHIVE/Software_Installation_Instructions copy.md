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

#### Once Lucene GeoGazetter Server is Installed and Working, Now download and set up the NER model, and then link it to Tika
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
#### Now we have to create the new application/geotopic MIME type, and map it to Tika.
1. `mkdir geotopic-mime`
2. `cd geotopic-mime`
3. `mkdir -p org/apache/tika/mime`
4. `curl -O https://raw.githubusercontent.com/chrismattmann/geotopicparser-utils/master/mime/org/apache/tika/mime/custom-mimetypes.xml`
5. `mv custom-mimetypes.xml org/apache/tika/mime`
6. `ls org/apache/tika/mime/`

#### Now you need to grab an example of a file that you want to run the GeoTopicParser on...
1. `curl -LO https://raw.githubusercontent.com/chrismattmann/geotopicparser-utils/master/geotopics/polar.geot`
2. `cat polar.geot`

#### Final step is that you need a copy of Tika App, Tika Server, and also the Tika NLP-ML module (which has the GeoTopicParser in it). You can build all of these by building Tika, but there's an easier way. Just grab the JAR files.
1. `mkdir tika-build`
2. `cd tika-build`
3. Tika Parser NLP Package: `curl -LO https://repo1.maven.org/maven2/org/apache/tika/tika-parser-nlp-package/2.7.0/tika-parser-nlp-package-2.7.0.jar`
4. Tika App: `curl -LO https://repo1.maven.org/maven2/org/apache/tika/tika-app/2.7.0/tika-app-2.7.0.jar`
5. Tika Server: `curl -LO https://repo1.maven.org/maven2/org/apache/tika/tika-server-standard/2.7.0/tika-server-standard-2.7.0.jar`

#### > Now, we can run the command to test out the GeoTopicParser, first from the TikaApp / Command line interface (CLI). Then we'll run a Tika REST server, and try it there too.

#### Created a simple script, which I will paste below called geotopic-parser that wraps the Java command and classpaths and allows you to run it on a single file.
```sh
TIKA_VERSION=2.7.0
export f=$1

java -classpath tika-build/tika-app-${TIKA_VERSION}.jar:tika-build/tika-parser-nlp-package-${TIKA_VERSION}.jar:${PWD}/location-ner-model:${PWD}/geotopic-mime \
		org.apache.tika.cli.TikaCLI -m $f
```
1. Give executable permission to the file geotopic-parser by running command: `chmod +x geotopic-parser`
2. Now, I will run it on the polar.geot file: `./geotopic-parser polar.geot`

#### Start up the geotopic-REST serve
- and then similarly create a simple script to do that too, called geotopic-server which I will paste below (don't forget to chmod +x geotopic-server before running it.) 
\- Note once you run it, it will take control of the terminal unless you put it in the background, so you'll need a new terminal to test it out):

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