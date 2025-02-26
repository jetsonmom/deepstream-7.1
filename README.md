##### deepstream-7.1
###### 초보가 작성한 내용임.
https://docs.nvidia.com/metropolis/deepstream/dev-guide/text/DS_plugin_gst-nvinfer.html
 
1. deepstream-7.1 설치
      1-1. sdkmanager
      1-2.  deepstream_sdk_v7.1.0 다운로드 링크.
   ``` bash
            https://catalog.ngc.nvidia.com/orgs/nvidia/resources/deepstream/files
   ```
      1-3.  orin@ubuntu:~/Downloads$ ls
            deepstream_sdk_v7.1.0_jetson.tbz2

 ``` bash
           orin@ubuntu:~/Downloads$ cd /opt/nvidia/
            orin@ubuntu:/opt/nvidia$ 
            orin@ubuntu:/opt/nvidia$ sudo tar -xvf ~/Downloads/deepstream_sdk_v7.1.0_jetson.tbz2

            orin@ubuntu:/opt/nvidia$ echo 'export PATH=/opt/nvidia/deepstream/deepstream-7.1/bin:$PATH' >> ~/.bashrc
            echo 'export LD_LIBRARY_PATH=/opt/nvidia/deepstream/deepstream-7.1/lib:$LD_LIBRARY_PATH' >> ~/.bashrc
            echo 'export GST_PLUGIN_PATH=/opt/nvidia/deepstream/deepstream-7.1/lib/gst-plugins:$GST_PLUGIN_PATH' >> ~/.bashrc
            source ~/.bashrc
            orin@ubuntu:/opt/nvidia$ cd /opt/nvidia/deepstream/deepstream-7.1
            orin@orin-desktop:/opt/nvidia/deepstream/deepstream-7.1$ ls
            bin                   LICENSE.txt                         sources
            doc                   README                              uninstall.sh
            install.sh            rtpjitterbuffer_eos_handling.patch  update_rtpmanager.sh
            lib                   samples                             version
            LicenseAgreement.pdf  service-maker

            orin@orin-desktop:/opt/nvidia/deepstream/deepstream-7.1$ sudo ./install.sh
            orin@orin-desktop:/opt/nvidia/deepstream/deepstream-7.1$ sudo ldconfig
```            
``` bash          
            sudo apt install \
            libssl3 \
            libssl-dev \
            libgstreamer1.0-0 \
            gstreamer1.0-tools \
            gstreamer1.0-plugins-good \
            gstreamer1.0-plugins-bad \
            gstreamer1.0-plugins-ugly \
            gstreamer1.0-libav \
            libgstreamer-plugins-base1.0-dev \
            libgstrtspserver-1.0-0 \
            libjansson4 \
            libyaml-cpp-dev
```
