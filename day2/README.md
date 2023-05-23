# k8s-cloud4c-b1

### Revision 

<img src="rev.png">

### any code you want to run in a container -- 
### there is two step process

<img src="two.png">

## Introduction docker image building tools 

<img src="tools.png">

## Converting python code into docker image using Dockerfile 

### ashu.py -- python code 

```
import time

while True:
    print("Hello all , welcome to python..!!")
    time.sleep(3)
    print("Welcome to LnB..")
    time.sleep(2)
    print("Welcome to Containers ..!!")
    print("______________________")
    time.sleep(3)
```

### Dockerfile 

```
FROM python
# we are fetching python image lib from docker official 
LABEL name=ashutoshh
LABEL email=ashutoshh@linux.com 
#  image creator info -- optional field 
RUN mkdir  /code 
# creating folder inside image to store my code
COPY ashu.py /code/ashu.py 
# copy code inside image location 
CMD ["python","/code/ashu.py"]
# role of cmd is to tell docker 
# how to run your code -- whenever we create container from this image
```

### lets run docker file to build image 

```

```
