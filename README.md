# Faster R-CNN under CPU only mode using docker

About Faster R-CNN, see [_README.md](py-faster-rcnn-cpu/_README.md)


## 0. Start installed Docker
docker start $(docker ps -a -q --filter "status=exited")
This will start all container which are in exited state.

docker exec -it <container-id> /bin/bash
This will connect to the particular container

## 1. System Info
 - Ubuntu 14.04 LTS x64 (I'm using VMWare Fusion)
 - Note: Please keep memory >= 4GB, or you may encounter following errors:
     - `g++: internal compiler error: Killed (program cc1plus)` when compiling
     - `*ptr host allocation of size 1382400000 failed` when running demo.py

## 2. Install Docker
 - see the [official document](https://docs.docker.com/engine/installation/linux/ubuntulinux/)
 - set up usergroup
 
 ```
 sudo groupadd docker
 sudo gpasswd -a ${USER} docker
 sudo service docker restart
 ```

## 3. Pull docker image & Install requirements

1. pull docker image
 
 `docker pull tleyden5iwx/caffe-cpu-master`
 
2. run image
 
 `docker run -i -t tleyden5iwx/caffe-cpu-master /bin/bash`
 
3. install requirements (cython already satisfied)
 
 ```
 sudo apt-get install python-opencv
 sudo pip install easydict
 ```
4. set up git

 ```
 git config --global user.name "John Doe"
 git config --global user.email johndoe@example.com
 ```
 

## 4. Clone & Run demo

1. clone
 
 ```
 git clone --recursive https://github.com/shawnau/py-faster-rcnn-cpu.git
 ```
 
2. compile(assuming the root of the repo is $FRCN_ROOT)
 
 - rename `$FRCN_ROOT/caffe-fast-rcnn/Makefile.config.example` into `Makefile.config`

 ```
 cd $FRCN_ROOT/lib
 make
 
 cd $FRCN_ROOT/caffe-fast-rcnn
 git checkout faster-rcnn-upstream-33f2445
 make -j8 && make pycaffe
 ```

3. download pretrained caffemodel

 ```
 cd $FRCN_ROOT
./data/scripts/fetch_faster_rcnn_models.sh
 ```

4. run demo

 ```
 #if error Failed to initialize libdc1394
 sudo ln /dev/null /dev/raw1394
 
 cd $FRCN_ROOT
 ./tools/demo.py --cpu --net zf
 ```
 - there is a problem with VGG16 net, may be it's memory's issue, see refer

5. results are saved into `tag_<filename>.jpg`under `$FRCN_ROOT/data/demo/`


## 5. Main modify

1. Problems with the cpu mode
 - `lib/fast_rcnn/config.py`
 - `lib/fast_rcnn/nms_wrapper.py`
 - `lib/setup.py`
 - `tools/test_net.py, train_net.py`
 - `caffe-fast-rcnn/Makefile.config`
2. change output, saving results into files instead of showing
 - `tools/demo.py`

---

## Refer

> [How to setup with CPU ONLY mode](https://github.com/rbgirshick/py-faster-rcnn/issues/123)  
> [I got the error when running demo.py](https://github.com/rbgirshick/py-faster-rcnn/issues/8)  
> [Caffe failed with py-faster-rcnn demo.py on TX1](https://devtalk.nvidia.com/default/topic/974063/caffe-failed-with-py-faster-rcnn-demo-py-on-tx1/)
