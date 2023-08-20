# Ribbion Camera setup

## enable legacy camera interface

1. `sudo raspi-config`
2. navigate to interface
3. legacy camera interface
4. disable it
5. reboot

## try taking a picture

Saves an image to `~/image.jpeg`. This is farily slow (takes 6 seconds for me).

```
sudo libcamera-jpeg -o ~/image.jpeg
```

## capture logs of frames:

Take a bunch of quick pictures. These will be lower quality than the jpeg from above but are _much_ faster and, with my setup, are perfectly fine quality still.

```
mkdir ~/test-images;
sudo libcamera-vid -n --codec mjpeg -t 1000 --segment 1 -o ~/test-images/test_%04d.jpg --width 4056 --height 3040 --shutter 8000 --saturation 1.0 --sharpness 1.5 --awbgains 1.1,1.55
```

Note that you could try tweaking the `--awbgains` value if the color is super messed up for you.

## Streaming

- Install deps
  ```bash
  sudo apt install -y libpcap-dev
  sudo apt install -y python3-pyqt5 python3-opengl
  sudo apt install -y python3-picamera2
  
  ```
- put this file somewhere on your pi: https://github.com/raspberrypi/picamera2/blob/main/examples/mjpeg_server.py
- run `sudo python ./mjpeg_server.py`
- go to `http://<pi-ip-address>:8000` in a browser on another computer on the local network


