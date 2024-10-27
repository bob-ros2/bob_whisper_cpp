# ROS BOB_WHISPER_CPP Dockerfile

this Dockerfile integrates whisper.cpp into a ROS (Robot Operating System) environment.

Related links:
- https://github.com/ggerganov/whisper.cpp
- https://github.com/bob-ros2/bob_whisper_cpp
- https://ros.org

```bash
# build the image in the docker folder
sudo docker build --no-cache -t bob-whisper-ros:latest-humble .

# run whisper ROS node with access to pulseaudio
# starts the command node with activation prompt
# remaps the output topic to a GPT
sudo docker run -it --net=host \
    -v ./models:/home/ros/models \
    -v /run/user/1000/pulse/native:/run/user/1000/pulse/native \
    -e PULSE_SERVER=unix:/run/user/1000/pulse/native \
    -e ROS_DOMAIN_ID=100 \
    bob-whisper-ros:latest-humble ros2 run bob_whisper_cpp command \
        --model /home/ros/models/ggml-base.en.bin \
        --prompt "Hey Bob" \
        --prompt-ms 1000 \
        --vad-thold 0.7 \
        --ros-args -r command:=/gpt4all/gpt_in
```
### Running under Windows using Pulseserver

To run it with Docker Desktop under Windows a Pulseaudio server must be runnning. This was for me the most simple way to get it to work under Windows. To get Pulseaudio see one of the links below.
- https://www.freedesktop.org/wiki/Software/PulseAudio/Download
- https://github.com/pgaskin/pulseaudio-win32/releases
```powershell
# run pulseserver at the power shell
PS .\pulseaudio-1.1\bin\pulseaudio.exe --use-pid-file=false --exit-idle-time=-1 -D

# later when running the docker container use: -e PULSE_SERVER=tcp:<ip-host>
```

## Pre Build Docker Image
There is also a pre build image available. Replace the image name with this one in order to use it.

> ghcr.io/bob-ros2/bob-whisper-ros:latest-humble

Package path
- https://ghcr.io/bob-ros2/bob-whisper-ros

## Caution
> <font color="#F07070">Be warned that giving full access to the audio device opens the doors for unauthorized individuals to listen in on your conversations, potentially compromising your privacy and confidentiality. It is important to only grant access to trusted applications and to revoke permissions when they are no longer needed.</font>
