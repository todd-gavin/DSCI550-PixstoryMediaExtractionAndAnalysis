### 1. Install  Google’s  LangDetect  using  PIP  and  the  instructions  here 
https://pypi.org/project/langdetect/  

Run Command: `pip install langdetect`

### 2. Install  RTG  (Reader  Translator  Generator)  using  the  instructions  here 
https://gowda.ai/posts/2021/04/mtdata-nlcodec-rtg-many-english/  The  result  will  be  a 
REST service running on port 6000 

> MTData - A tool to download machine translation datasets https://github.com/thammegowda/mtdata/
`pip install mtdata`

> NLCodec - https://isi-nlp.github.io/nlcodec/
`pip install nlcodec`

> RTG - https://isi-nlp.github.io/rtg/
`pip install rtg`

### 3. Download  and  install  Tika  Python  using  PIP  and  the  instructions  at 
http://github.com/chrismattmann/tika-python 
    - With the RTG running, running tika’s translate module will automatically work fine (since it will pick up the RTG server) 
    - The Tika language module (it’s language detector) should also work fine 

Run Command: `pip install tika`
- To check what tika is installed, run command: `pip show tika`

### 4.  Install  GeoTopicParser  using  the  instructions  here 
https://cwiki.apache.org/confluence/display/tika/GeoTopicParser  
a. The  result  of  this  should  be  the  Lucene  GeoGazetter  REST  server  running  as 
specified here: https://github.com/chrismattmann/lucene-geo-gazetteer   
b. You can connect the GeoGazetteer to Tika-Python using the instructions here: 
https://github.com/chrismattmann/tika-python#changing-the-tika-classpath  


### 5. Install Detoxify using PIP and the instructions here: 
https://pypi.org/project/detoxify/  
a. Note that if you are using Mac and Python, using pyenv, and you run into issues 
installing  Detoxify  and  torch  with  PIP,  see  this  for  an  easy  workaround 
https://github.com/pytorch/pytorch/issues/53601#issuecomment-967307449   


### 6. Install Tika Image Dockers and generate captions for your Pixstory images posts 
a. To  access  the  images,  use  the  URL  from  the  post  and give  it  the  URL  prefix 
“/optimized”,  such  as:  https://image.pixstory.com/optimized/Pixstory-image-
164416629024955.jpeg  
b. Download all 95k images associated with the posts 
i. Write a simple python script to do this 
c. Install Tika Dockers package for Image Captioning and Object Recognition 
i. git  clone  https://github.com/USCDataScience/tika-dockers.git  and 
https://hub.docker.com/r/uscdatascience/im2txt-rest-tika  
ii. Read  and  test  out: 
https://cwiki.apache.org/confluence/display/TIKA/TikaAndVisionDL4J   
iii. Read and test out: https://github.com/apache/tika/pull/189  
d. Iterate through all the Pixstory posts and add the generated image caption and the 
detect object(s) column to your dataset