##### deepstream-7.1
###### 초보가 작성한 내용임.
https://docs.nvidia.com/metropolis/deepstream/dev-guide/text/DS_plugin_gst-nvinfer.html
 
1. deepstream-7.1 설치
   
   1-1. sdkmanager.
   
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
``` bash
orin@ubuntu:~$ sudo apt install ffmpeg

orin@ubuntu:~$ sudo apt install python3-pip
orin@ubuntu:~$ pip3 install yt-dlp
orin@ubuntu:~$ which yt-dlp
/usr/local/bin/yt-dlp
```
<b>  2. 환경 준비. 나는 nvidia자료에 있는 sample영상으로 사용했음.
deepstream_test5폴더를 만들어서 진행
``` bash
orin@orin-desktop:~$ mkdir deepstream_test5
orin@orin-desktop:~/deepstream_test5$ cp /opt/nvidia/deepstream/deepstream-7.1/samples/streams/sample_720p.mp4 .
```
cp명령으로 아래와 같이 deepstream_test5에 필요한 내용을 원본에서 복사함.

``` bash
orin@ubuntu:~/deepstream_test5$ /usr/local/bin/yt-dlp -U
```
Latest version: stable@2025.02.19 from yt-dlp/yt-dlp
yt-dlp is up to date (stable@2025.02.19 from yt-dlp/yt-dlp)
orin@ubuntu:~/deepstream_test5$ /usr/local/bin/yt-dlp --version
2025.02.19
```
orin@orin-desktop:~/deepstream_test5$ ls
```
 config_infer_primary.txt
 config_infer_secondary_vehiclemake.txt
 config_infer_secondary_vehicletypes.txt
 config_tracker_IOU.yml
 config_tracker_NvDCF_accuracy.yml
 config_tracker_NvDCF_perf.yml
 config_tracker_NvDeepSORT.yml
 config_tracker_NvSORT.yml
 models
 out.mp4
 sample_720p.mp4  # sample stream
 test5_dec_infer-resnet_tracker_sgie_tiled_display_int8.txt
 traffic.mp4    #
 ```
orin@ubuntu:~/deepstream_test5$ /usr/local/bin/yt-dlp -f 'bestvideo[height<=720]+bestaudio' https://www.youtube.com/watch?v=DF51C88xJ6E -o street_traffic.mp4
```
<b>
[youtube] Extracting URL: https://www.youtube.com/watch?v=DF51C88xJ6E
[youtube] DF51C88xJ6E: Downloading webpage
[youtube] DF51C88xJ6E: Downloading tv client config
[youtube] DF51C88xJ6E: Downloading player e7567ecf
[youtube] DF51C88xJ6E: Downloading tv player API JSON
[youtube] DF51C88xJ6E: Downloading ios player API JSON
[youtube] DF51C88xJ6E: Downloading m3u8 information
[info] DF51C88xJ6E: Downloading 1 format(s): 136+140
[download] Destination: street_traffic.f136.mp4
[download] 100% of   11.80MiB in 00:00:01 at 7.74MiB/s
[download] Destination: street_traffic.f140.m4a
[download] 100% of  734.65KiB in 00:00:02 at 363.00KiB/s
[Merger] Merging formats into "street_traffic.mp4"
Deleting original file street_traffic.f136.mp4 (pass -k to keep)
Deleting original file street_traffic.f140.m4a (pass -k to keep)
 
``` bash
orin@ubuntu:~/deepstream_test5$ ls
```
street_traffic.mp4
<b> 3. TensorRT 엔진 만들기
주의할 점 
batch-size를 통일해준다. 여기서는 b16으로 해주었다.
![Screenshot from 2025-02-26 14-30-28](https://github.com/user-attachments/assets/9d79729a-49b5-496b-844f-3c922ad034a9)


deepstream_test5/models에 있는 Primary_Detector, Secondary_VehicleMake, Secondary_VehicleType3개를 전부 만들어준다.
![Screenshot from 2025-02-26 14-34-37](https://github.com/user-attachments/assets/04a255b3-ba08-48cb-8e0e-200e3a4df723)
![Screenshot from 2025-02-26 14-34-21](https://github.com/user-attachments/assets/d7681eb5-2d09-47e6-a5ac-3af1517719ee)
![Screenshot from 2025-02-26 14-34-05](https://github.com/user-attachments/assets/5f72939a-4cf6-4d62-9ddf-574118574403)



```
orin@orin-desktop:~/deepstream_test5$ /usr/src/tensorrt/bin/trtexec --onnx=/home/orin/deepstream_test5/models/Secondary_VehicleMake/resnet18_vehiclemakenet_pruned.onnx --shapes=input_1:0:16x3x224x224 --saveEngine=/home/orin/deepstream_test5/models/Secondary_VehicleMake/resnet18_vehiclemakenet_pruned.onnx_b16_gpu0_int8.engine --int8 --calib=/home/orin/deepstream_test5/models/Secondary_VehicleMake/cal_trt.bin

```

























