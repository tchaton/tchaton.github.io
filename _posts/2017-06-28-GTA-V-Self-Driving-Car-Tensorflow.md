---
layout: post
title:  "GTA V Self driving car using TensorFlow part I"
date:   2017-06-30 12:00:00 +0200
categories: blog update
comments: true
---

Hey there,

Today we'll start a series on how to implement your own self-driving car in GTA V using TensorFlow on Python. I'm working on this project with some other interns at Amadeus, and just got started, as soon as we make progress I'll keep you updated. Let's get to it !

What are the prerequisites ?
===

Creating the dataset
---

Well first of all you'll need GTA V installed on your computer in order to create a dataset that we'll use to train our model.
Once you have GTA V installed on your computer you can clone following repos: 
* [DeepDriving](https://github.com/ai-tor/DeepGTAV), simply follow install instructions, it is very easy to implement.
* [VPilot](https://github.com/ai-tor/VPilot) is a client used to comunicate with previously installed plugin.

Once you have those two installed you are almost ready to create your dataset. But what data do we need ? In order to have a model that works in most environments, we'll want to have different conditions: weather, daytime, type of road... In order to do that, we'll create a custom dataset.py, keeping some of the basics VPilot already provides, here is the code we'll use to create the dataset, you can customize yours in order to fit your resolution for example.

	from deepgtav.messages import Start, Stop, Dataset, frame2numpy,Scenario
	from deepgtav.client import Client

	import argparse
	import time
	#import cv2
	import json
	import numpy as np

	# Stores a dataset file with data coming from DeepGTAV
	if __name__ == '__main__':
		parser = argparse.ArgumentParser(description=None)
		parser.add_argument('-l', '--host', default='localhost', help='The IP where DeepGTAV is running')
		parser.add_argument('-p', '--port', default=8000, help='The port where DeepGTAV is running')
		parser.add_argument('-d', '--dataset_path', default='dataset.pz', help='Place to store the dataset')
		args = parser.parse_args()

		weathers = ["CLEAR", "EXTRASUNNY", "CLOUDS", "OVERCAST", "RAIN", "CLEARING", "THUNDER", "SMOG", "FOGGY", "XMAS", "SNOWLIGHT", "BLIZZARD", "NEUTRAL", "SNOW" ] #
		hours = [0,4,8,12,16,20]
		## CREATE A REAL DATASET WITH WEATHER CONDITIONS IN DIRECTORY.
		for weather in weathers:
			# Creates a new connection to DeepGTAV using the specified ip and port. 
			# If desired, a dataset path and compression level can be set to store in memory all the data received in a gziped pickle file.
			for hour in hours:
				client = Client(ip=args.host, port=args.port, datasetPath=args.dataset_path, compressionLevel=9)
				infos = {}
				infos['weather'] = weather
				infos['hour'] = hour
				infos['time'] = int(time.time())

				# Configures the information that we want DeepGTAV to generate and send to us. 
				# See deepgtav/messages.py to see what options are supported
				dataset = Dataset(rate=4, frame=[1280,640], throttle=True, brake=True, steering=True, vehicles=True, peds=True, trafficSigns=True, reward=[15.0, 0.0], direction=None, speed=True, yawRate=True, location=True, time=True)
				# Send the Start request to DeepGTAV.
				scenario = Scenario(time=[hour,0],weather=weather,drivingMode=[786603,15.0]) # Driving style is set to normal, with a speed of 15.0 mph. All other scenario options are random.
				client.sendMessage(Start(dataset=dataset,scenario=scenario))
				stoptime = time.time() + 2*60
				while time.time() < stoptime:
					try:
						message = client.recvMessage(infos)	
					except KeyboardInterrupt:
					# We tell DeepGTAV to stop
						break
			# We tell DeepGTAV to stop
		client.sendMessage(Stop())
	client.close()

Now launch GTA, once GTA is launched, open a console and start the datasetp.py script. This will allow to generate the dataset, a folder called dataset will be created, storing for each weather and each day time captured frames in a gzipped pickle files.

Installing useful tools
---

In this series we'll use Python 3.6, but probably any python supporting TensorFlow will do (TensorFlow is not available for python 2.7 on windows !). The best thing is to install the GPU version for TF. This is not always easy, you first need to have a CUDA supported NVidia GPU. You then need to install CUDA and all its dependencies, *pay close attention to what version TF tells you to install for CUDA*, it is described on their site, a too recent version might not work, you need to install exactly the version they describe ! Check if CUDA is present in your système variables. Once CUDA is installed, you need to download and install CUDnn, what is meant by that, is simply copying and pasting the CUDnn DLLs into the CUDA directory.
Once CUDA + CUDnn is installed, you can install the tensorflow gpu version with pip:

	pip3 install --upgrade tensorflow-gpu

That's it, you should be good to run TF with your GPU.

If you don't have a GPU, you can directly install tensorflow with pip:

	pip3 install --upgrade tensorflow

That's it for the first part, we have installed the most important dependencies !

See you soon for the following parts :)

Cédric 