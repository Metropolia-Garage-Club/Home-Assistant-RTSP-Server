# Create a Camera Streaming Pipeline using Go2RTC 
### Repository for Docker-based RTSP camera streaming solution that integrates multiple IP cameras with Home Assistant using Go2RTC and WebRTC technology. 


## Table of Contents

- [Overview](#overview)
- [Project Goals](#project-goals)
- [Features](#features)
- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
- [Project Structure](#project-structure)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)
- [Team Members](#team-members)
- [Acknowledgments](#acknowledgments)


## Overview

This project implements a reliable RTSP-based camera streaming pipeline using Go2RTC and integrates it with Home Assistant. It enables real-time, low-latency streaming from multiple IP cameras through a Docker-based deployment. 

## Project Goals

- Stream from multiple RTSP-compatible cameras  
- Deploy Go2RTC as a containerized streaming relay  
- Integrate streams with Home Assistant using WebRTC  
- Create a scalable, reproducible deployment using Docker Compose


## Features

- Multi-Camera Support  
- WebRTC Low-Latency Playback  
- Docker-Based Deployment  
- Real-Time Streaming  
- Dedicated Docker Network Isolation

  
## Architecture

![Streaming Pipeline Flowchart](Flowchart.png)


## Prerequisites 

- Docker (version 20.10 or higher) 
- Docker Compose (version 1.29 or higher) 
- RTSP-compatible IP cameras or RTSP camera apps 
- Network access to camera streams 


## Installation 
### Step 1: Clone the Repository 

[Home Assistant RTSP Server](https://github.com/Metropolia-Garage-Club/Home-Assistant-RTSP-Server)

cd Home-Assistant-RTSP-Server 


### Step 2: Directory Structure 

```text
C:\Home-Assistant-RTSP-Server\
└── software\
    └── deployment\
        └── camera-streaming\
            ├── docker-compose.yml
            ├── go2rtc\
            │   ├── Dockerfile
            │   └── config\
            │       └── go2rtc.yaml
            └── home-assistant\
                ├── Dockerfile
                └── config\
                    ├── configuration.yaml
                    ├── automations.yaml
                    ├── scripts.yaml
                    └── scenes.yaml
```


### Step 3: Deploy with Docker Compose 

docker-compose up 

This will: 

- Build the Go2RTC and Home Assistant Docker images 
- Start both containers 
- Create a dedicated Docker network for communication


### Step 4: Verify Deployment 

Check that both containers are running: 

docker ps 

You should see: 

- go2rtc container running on ports 1984, 8554, 8555 
- homeassistant container running on port 8123 


## Configuration 
## Go2RTC Configuration
*Configuration file:* [software/deployment/camera-streaming/go2rtc/config/go2rtc.yaml](software/deployment/camera-streaming/go2rtc/config/go2rtc.yaml)

Edit this file to configure your camera streams:

```text
yaml
streams: 
  iphone_cam: 
    - rtsp://camera_ip:8554/stream 
  mobile_cam_2: 
    - rtsp://camera_ip:8554/ 
```


#### Configuration Notes: 

- Replace camera IP addresses with your actual camera IPs 
- For cameras requiring authentication, use the format: rtsp://username:password@ip:port/path 
- Stream names must match the names used in Home Assistant 


### Go2RTC Dockerfile 

*Dockerfile:* [software/deployment/camera-streaming/go2rtc/Dockerfile](software/deployment/camera-streaming/go2rtc/Dockerfile)

This Dockerfile sets up Go2RTC in an Alpine Linux container with FFmpeg support.


### Home Assistant Configuration

*Configuration file:* [software/deployment/camera-streaming/home-assistant/config/configuration.yaml](software/deployment/camera-streaming/home-assistant/config/configuration.yaml)

Key configurations included:
- Default integrations
- Stream integration for camera support
- Debug logging for camera and stream components

*Additional configuration files:*
- [automations.yaml](software/deployment/camera-streaming/home-assistant/config/automations.yaml)
- [scripts.yaml](software/deployment/camera-streaming/home-assistant/config/scripts.yaml)
- [scenes.yaml](software/deployment/camera-streaming/home-assistant/config/scenes.yaml)


### Home Assistant Dockerfile 

*Dockerfile:* [software/deployment/camera-streaming/home-assistant/Dockerfile](software/deployment/camera-streaming/home-assistant/Dockerfile)

Uses the official Home Assistant stable image with Helsinki timezone.


### Docker Compose Configuration

*Docker Compose file:* [software/deployment/camera-streaming/docker-compose.yml](software/deployment/camera-streaming/docker-compose.yml)

Defines two services:
- *go2rtc*: RTSP to WebRTC relay (ports 1984, 8554, 8555)
- *homeassistant*: Home automation platform (port 8123)

Both services run on an isolated streaming-network bridge network.



## usage
### Accessing Go2RTC Web Interface 

1. Open your browser and navigate to:  
   [http://localhost:1984](http://localhost:1984)
2. You should see all configured camera streams listed 
3. Click on any stream to verify it's working


### Adding Cameras to Home Assistant
#### Step 1: Add WebRTC Camera Integration

1. Go to Settings → Devices & Services 
2. Click + Add Integration 
3. Search for WebRTC Camera 
4. Click on it to add the integration 
5. Configure with your host IP address (e.g., 192.168.50.141) 
6. Complete the setup process

#### Step 2: Add Camera Cards to Dashboard 

1. Go to your Home Assistant Dashboard 
2. Click Edit Dashboard → Add Card 
3. Select webrtc-camera Card 
3. Add the following configuration:
   
   type: custom:webrtc-camera
   
 	 url: iphone_cam  
5. Repeat for additional cameras:
   
   type: custom:webrtc-camera
   
  	url: Mobile_cam_2 

 
	  type: custom:webrtc-camera
   
	  url: Camera_3

*Note:* The url field should match the stream name defined in your [go2rtc.yaml](software/deployment/camera-streaming/go2rtc/config/go2rtc.yaml) file.

### Viewing Camera Streams 
Once configured, you can: 

- View all camera streams on your Home Assistant dashboard 
- Access streams from mobile devices using the Home Assistant app 
- Create automations based on camera events


## Project Structure 

```text
Home-Assistant-RTSP-Server/ 
├── README.md 
├── docker-compose.yml 
├── go2rtc/ 
│   ├── Dockerfile 
│   └── config/ 
│       └── go2rtc.yaml 
└── home-assistant/ 
    ├── Dockerfile 
    └── config/ 
        ├── configuration.yaml 
        ├── automations.yaml 
        ├── scripts.yaml 
        └── scenes.yaml 
```


## Troubleshooting 
### Camera Stream Not Appearing in Go2RTC 

1. Verify camera IP address and RTSP URL 
2. Check network connectivity: 
   ping <camera-ip> 
3. Test RTSP stream with VLC or ffplay: 
   ffplay rtsp://<camera-ip>:<port>/<path> 
4. Check Go2RTC logs: 
   docker logs go2rtc 


### WebRTC Not Working in Home Assistant 

1. Verify WebRTC candidate IP matches your host IP 
2. Ensure port 8555 is accessible from your network 
3. Check Home Assistant logs:
bash
   docker logs homeassistant  
4. Verify the stream name in dashboard card matches [go2rtc.yaml](software/deployment/camera-streaming/go2rtc/config/go2rtc.yaml) 


### Container Won't Start 

1. Check for port conflicts: 
   netstat -tulpn | grep -E '1984|8123|8554|8555' 
2. Verify Docker daemon is running:
bash
   docker ps 
3. Check container logs:
bash 
   docker-compose logs 

#### Connection Refused Errors 

1. Ensure both containers are on the same Docker network:
 bash
   docker network inspect streaming-network
   
2. Verify firewall rules allow connections on required ports 
3. Check if cameras are reachable from the Docker container:
bash
   docker exec -it go2rtc ping <camera-ip>



## Contributing 

Contributions are welcome! Please follow these steps: 

1. Fork the repository 
2. Create a feature branch (git checkout -b feature/amazing-feature) 
3. Commit changes (git commit -m 'Add some amazing feature') 
4. Push to the branch (git push origin feature/amazing-feature) 
5. Open a Pull Request 


## License 

This project is part of a Metropolia University of Applied Sciences internship project. 

## Team Members

- Upeksha
- Lihini
- Shamila

## Acknowledgments 

- [Go2RTC](https://github.com/AlexxIT/go2rtc) - RTSP to WebRTC relay 
- [Home Assistant](https://www.home-assistant.io/) - Home automation platform 
- Metropolia University of Applied Sciences - Project supervision
