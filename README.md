# The following branch is meant for anyone wanting to run the AWS JPL OSR Challenge locally with docker d(-_-)b

## Warranty: None

I've probably missed a few things in these instructions, feel free to ping me on slack with questions.

Assumptions
*  You have a somewhat recent/decent Nvidia graphics card
*  You are running Pop!_OS 19.10
*  You have the latest Nvidia drivers
*  You have installed the following
```
sudo apt install tensorman nvidia-container-toolkit nvidia-container-runtime system76-cuda-latest
```
* You've minimally tested things to verify  
    *  You can run a python script with TF GPU support
    ```
    tensorman run --gpu python -- ./script.py
    ```   
    *  You can run nvida-smi in a container, and have GPU support
    ```
    docker run --gpus all --rm nvidia/cuda nvidia-smi
    docker run --runtime=nvidia --rm nvidia/cuda nvidia-smi
    ```

User Guide

*  Clone the repo and checkout the aws-jpl-osr-challenge branch
```
git clone https://github.com/joebarbere/aws-robomaker-docker.git
git checkout aws-jpl-osr-challenge
```
* Stand up the docker containers
```
docker-compose up -d --build
```
* Navigate to the following url in a browser http://localhost:6080/
* In the browser, open a terminal :-) Yay VNC
* Log into minio, username=mino, password=miniokey, url=http://localhost:9000
* Add a bucket named beep
* Add a prefix named boop
* Modify the code to use minio
```
nano /home/ubuntu/catkin_ws/src/simulation_ws/src/mars/scripts/run_local_rl_agent.sh

python3 -m markov.rover_agent --aws-region=us-east-1 --model-s3-bucket=beep --model-s3-prefix=boop
```

```
nano /home/ubuntu/catkin_ws/src/simulation_ws/src/rl-agent/markov/s3_boto_data_store.py

session = boto3.session.Session(aws_access_key_id='minio', aws_secret_access_key='miniokey', region_name='us-east-1')
return session.client('s3', region_name=self.para,s.aws_region, use_ssl=False, endpoint_url='http://minio:9000')
```
* Run the following commands
```
cd /home/ubuntu/catkin_ws/src/simulation_ws/
./setup.sh
cd src
colcon build
cd rl-agent
pip3 install .
cd ..
source install/local_setup.sh
cd mars/launch/
roslaunch mars_full_sim.launch
```


Links to some helpful resources / sources of inspiration:

https://spacechallenge.tech/  
https://github.com/Christopheraburns/AWS-JPL-OSR-Challenge  
https://devopstar.com/2019/09/12/aws-robomaker-beginners-guide-to-robot-simulation/  
https://github.com/t04glovern/aws-robomaker-docker  
https://medium.com/@autonomousracecarclub/how-to-run-deepracer-locally-to-save-your-wallet-13ccc878687  
https://github.com/ARCC-RACE/deepracer-for-dummies



