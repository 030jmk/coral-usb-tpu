# coral-usb-tpu
Getting the coral TPU to work with a raspberry pi 5.

I have had so many issues to get this to work without a docker file. pyenv also didnt work out for me for weird reasons. 
- Raspberry Pi 5 incl. PSU
- Coral USB TPU incl fast USB-A to USB-C cable

Installation
Flash latest Raspberry Pi OS image on an SD card

## Update System Packages
Run the following commands to update your system and install essential build tools:

    sudo apt update && sudo apt upgrade -y sudo apt install -y build-essential libssl-dev zlib1g-dev libncurses5-dev libncursesw5-dev libreadline-dev libsqlite3-dev libgdbm-dev libdb5.3-dev libbz2-dev libexpat1-dev liblzma-dev tk-dev libffi-dev tar wget cmake libgirepository1.0-dev libcairo2-dev pkg-config python3-dev gir1.2-gtk-3.0


## Download and Extract Python 3.9 Source Code
Python 3.9 is the latest version that will work with the dependencies. As of writing this, the latest security update is Python [3.9.21](https://docs.python.org/release/3.9.21/whatsnew/changelog.html)

    cd /usr/src
    sudo wget https://www.python.org/ftp/python/3.9.21/Python-3.9.21.tgz
    sudo tar xzf Python-3.9.21.tgz
    cd Python-3.9.21

## Compile and Install Python 3.9
Use make `altinstall` instead of `make install` to avoid overwriting the default python3 binary used by the system.

    sudo ./configure --enable-optimizations
    sudo make -j$(nproc)
    sudo make altinstall

## Verify the Installation

    python3.9 --version

## Install the Edge TPU runtime
The Edge TPU runtime provides the core programming interface for the Edge TPU

Add the debian package

    echo "deb https://packages.cloud.google.com/apt coral-edgetpu-stable main" | sudo tee /etc/apt/sources.list.d/coral-edgetpu.list
    curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
    sudo apt-get update

Install the Edge TPU

    sudo apt-get install libedgetpu1-std
Now connect the USB Accelerator to your computer using the provided USB 3.0 cable. If you already plugged it in, remove it and replug it so the newly-installed udev rule can take effect.

## Use Python 3.9 Without Affecting System Python
    python3.9 -m venv ~/python39_env
    source ~/python39_env/bin/activate

## Install [PyCoral](https://coral.ai/docs/accelerator/get-started/#pycoral-on-linux)
    pip install --extra-index-url https://google-coral.github.io/py-repo/ pycoral~=2.0
    pip install PyGObject "numpy<2" scipy PyOpenGL


## Clone the [pycoral](https://github.com/google-coral/pycoral) Repository

    cd
    mkdir coral && cd coral
    git clone https://github.com/google-coral/pycoral.git
    cd pycoral

Download models, labels and bird photo

    bash examples/install_requirements.sh classify_image.py
Run the image classifier with the bird photo 

    python3 examples/classify_image.py \
    --model test_data/mobilenet_v2_1.0_224_inat_bird_quant_edgetpu.tflite \
    --labels test_data/inat_bird_labels.txt \
    --input test_data/parrot.jpg



for later:
https://github.com/feranick/pycoral
