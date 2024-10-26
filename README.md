# ROS Package bob_whisper_cpp

## ROS Node Command

This ROS node is a basic STT Voice Assistant that accepts voice commands from the microphone and publishes it to a ROS std_msg/msg/String topic for further processing. It's basically the same as the `command` example of the `whisper.cpp` project extended with a ROS integration.\
More infos about the origin example:
* [ggerganov/whisper.cpp issue #171](https://github.com/ggerganov/whisper.cpp/issues/171)
* [ggerganov/whisper.cpp/tree/master/examples/command](https://github.com/ggerganov/whisper.cpp/tree/master/examples/command)
 

```bash
# Run the command ROS Node with default arguments and small model
ros2 run bob_whisper_cpp command -m ./models/ggml-small.en.bin -t 8
```

#### Docker Support
This ROS node is also as Docker container available
* https://github.com/bob-ros2/bob_whisper_cpp/docker

### Models
To get a model see here
* [ggerganov/whisper.cpp/blob/master/models](https://github.com/ggerganov/whisper.cpp/blob/master/models/README.md)

## Dependencies

* This ROS Node depends on the [whisper.cpp](https://github.com/ggerganov/whisper.cpp) project. `Whisper.cpp` is integrated as a GIT submodule and is downloaded along with cloning this package with the option `--recurse-submodules`.
* The SDL2 library is used to capture audio from the microphone. The library can be installed like this:

```bash
# Install SDL2
sudo apt-get install libsdl2-dev
```

## Setup Package

```bash
cd <colcon_ws>/src
git clone --recurse-submodules https://github.com/bob-ros2/bob_whisper_cpp.git
cd ..
# this builds the whisper.cpp and the bob_whisper_cpp ROS node
colcon build
. install/setup.bash
```

## Published ROS Topics

> Name: command\
  Type: std_msgs/msg/String\
  Outputs the detected text.

## Guided Mode

"Guided Mode" allows you to specify a list of commands (i.e. strings) and the transcription will be guided to classify your command into one from the list. This can be useful in situations where a device is listening only for a small subset of commands.

This approach might be extremely efficient in terms of performance.

```bash
# Run in guided mode, the list of allowed commands is in commands.txt
ros2 run bob_whisper_cpp command \
-m ./models/ggml-base.en.bin \
-cmd ./examples/command/commands.txt
```

## Activation Prompt

The "Activation Prompt" allows you to specify a prefix to identfy the beginning of a command. The prefix text will be stripped from the spoken text.

```bash
# Run with activation prompt and remaps the ROS topic to another topic name
ros2 run bob_whisper_cpp command \
--model ./models/ggml-base.en.bin \
--threads 8 \
--prompt "Hey Bob" \
--prompt-ms 1000 \
--vad-thold 0.7 \
--ros-args -r command:=/bob/llm/llm_in
```

## Command Line Arguments
This ROS node has no ROS Node related parameters and has to be configured using the commandline arguments.\
Using `--ros-args`, e.g. for topic remapping, is still possible. These args must be added at the end of the command arguments.

```bash
# show help
$ ros2 run bob_whisper_cpp command --help

usage: command [options]

options:
  -h,         --help           [default] show this help message and exit
  -t N,       --threads N      [4      ] number of threads to use during computation
  -pms N,     --prompt-ms N    [5000   ] prompt duration in milliseconds
  -cms N,     --command-ms N   [8000   ] command duration in milliseconds
  -c ID,      --capture ID     [-1     ] capture device ID
  -mt N,      --max-tokens N   [32     ] maximum number of tokens per audio chunk
  -ac N,      --audio-ctx N    [0      ] audio context size (0 - all)
  -vth N,     --vad-thold N    [0.60   ] voice activity detection threshold
  -fth N,     --freq-thold N   [100.00 ] high-pass frequency cutoff
  -tr,        --translate      [false  ] translate from source language to english
  -ps,        --print-special  [false  ] print special tokens
  -pe,        --print-energy   [false  ] print sound energy (for debugging)
  -ng,        --no-gpu         [false  ] disable GPU
  -fa,        --flash-attn     [false  ] flash attention
  -l LANG,    --language LANG  [en     ] spoken language
  -m FNAME,   --model FNAME    [models/ggml-base.en.bin] model path
  -f FNAME,   --file FNAME     [       ] text output file name
  -cmd FNAME, --commands FNAME [       ] text file with allowed commands
  -p,         --prompt         [       ] the required activation prompt
  -ctx,       --context        [       ] sample text to help the transcription
  --grammar GRAMMAR            [       ] GBNF grammar to guide decoding
  --grammar-penalty N          [100.0  ] scales down logits of nongrammar tokens
  --suppress-regex REGEX       [       ] regular expression matching tokens to suppress
```


