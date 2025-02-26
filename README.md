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

<b> 실행하기
```
orin@orin-desktop:~/deepstream_test5$ deepstream-app -c test5_dec_infer-resnet_tracker_sgie_tiled_display_int8.txt

```
<b>  실행 결과
desktop:~/deepstream_test5$ deepstream-app -c test5_dec_infer-resnet_tracker_sgie_tiled_display_int8.txt

(gst-plugin-scanner:9332): GStreamer-WARNING **: 13:05:07.965: Failed to load plugin '/opt/nvidia/deepstream/deepstream/lib/gst-plugins/libnvdsgst_udp.so': librivermax.so.0: cannot open shared object file: No such file or directory

(gst-plugin-scanner:9332): GStreamer-WARNING **: 13:05:07.967: Failed to load plugin '/opt/nvidia/deepstream/deepstream/lib/gst-plugins/libnvdsgst_inferserver.so': libtritonserver.so: cannot open shared object file: No such file or directory

(gst-plugin-scanner:9332): GStreamer-WARNING **: 13:05:07.986: Failed to load plugin '/usr/lib/aarch64-linux-gnu/gstreamer-1.0/deepstream/libnvdsgst_udp.so': librivermax.so.0: cannot open shared object file: No such file or directory

(gst-plugin-scanner:9332): GStreamer-WARNING **: 13:05:07.987: Failed to load plugin '/usr/lib/aarch64-linux-gnu/gstreamer-1.0/deepstream/libnvdsgst_inferserver.so': libtritonserver.so: cannot open shared object file: No such file or directory
Setting min object dimensions as 16x16 instead of 1x1 to support VIC compute mode.
0:00:00.676987432  9330 0xaaaacbca20a0 INFO                 nvinfer gstnvinfer.cpp:684:gst_nvinfer_logger:<secondary_gie_1> NvDsInferContext[UID 3]: Info from NvDsInferContextImpl::deserializeEngineAndBackend() <nvdsinfer_context_impl.cpp:2092> [UID = 3]: deserialized trt engine from :/home/orin/deepstream_test5/models/Secondary_VehicleTypes/resnet18_vehicletypenet_pruned.onnx_b16_gpu0_int8.engine
INFO: [FullDims Engine Info]: layers num: 2
0   INPUT  kFLOAT input_1:0       3x224x224       min: 1x3x224x224     opt: 16x3x224x224    Max: 16x3x224x224    
1   OUTPUT kFLOAT predictions/Softmax:0 6               min: 0               opt: 0               Max: 0               

0:00:00.677168174  9330 0xaaaacbca20a0 INFO                 nvinfer gstnvinfer.cpp:684:gst_nvinfer_logger:<secondary_gie_1> NvDsInferContext[UID 3]: Info from NvDsInferContextImpl::generateBackendContext() <nvdsinfer_context_impl.cpp:2195> [UID = 3]: Use deserialized engine model: /home/orin/deepstream_test5/models/Secondary_VehicleTypes/resnet18_vehicletypenet_pruned.onnx_b16_gpu0_int8.engine
0:00:00.696157982  9330 0xaaaacbca20a0 INFO                 nvinfer gstnvinfer_impl.cpp:343:notifyLoadModelStatus:<secondary_gie_1> [UID 3]: Load new model:/home/orin/deepstream_test5/config_infer_secondary_vehicletypes.txt sucessfully
Setting min object dimensions as 16x16 instead of 1x1 to support VIC compute mode.
0:00:00.748211956  9330 0xaaaacbca20a0 INFO                 nvinfer gstnvinfer.cpp:684:gst_nvinfer_logger:<secondary_gie_0> NvDsInferContext[UID 2]: Info from NvDsInferContextImpl::deserializeEngineAndBackend() <nvdsinfer_context_impl.cpp:2092> [UID = 2]: deserialized trt engine from :/home/orin/deepstream_test5/models/Secondary_VehicleMake/resnet18_vehiclemakenet_pruned.onnx_b16_gpu0_int8.engine
INFO: [FullDims Engine Info]: layers num: 2
0   INPUT  kFLOAT input_1:0       3x224x224       min: 1x3x224x224     opt: 16x3x224x224    Max: 16x3x224x224    
1   OUTPUT kFLOAT predictions/Softmax:0 20              min: 0               opt: 0               Max: 0               

0:00:00.748338680  9330 0xaaaacbca20a0 INFO                 nvinfer gstnvinfer.cpp:684:gst_nvinfer_logger:<secondary_gie_0> NvDsInferContext[UID 2]: Info from NvDsInferContextImpl::generateBackendContext() <nvdsinfer_context_impl.cpp:2195> [UID = 2]: Use deserialized engine model: /home/orin/deepstream_test5/models/Secondary_VehicleMake/resnet18_vehiclemakenet_pruned.onnx_b16_gpu0_int8.engine
0:00:00.756112138  9330 0xaaaacbca20a0 INFO                 nvinfer gstnvinfer_impl.cpp:343:notifyLoadModelStatus:<secondary_gie_0> [UID 2]: Load new model:/home/orin/deepstream_test5/config_infer_secondary_vehiclemake.txt sucessfully
gstnvtracker: Loading low-level lib at /opt/nvidia/deepstream/deepstream/lib/libnvds_nvmultiobjecttracker.so
[NvTrackerParams::getConfigRoot()] !!![WARNING] File doesn't exist. Will go ahead with default values
[NvMultiObjectTracker] Initialized
Setting min object dimensions as 16x16 instead of 1x1 to support VIC compute mode.
WARNING: Deserialize engine failed because file path: /home/orin/deepstream_test5/models/Primary_Detector/resnet18_trafficcamnet_pruned.onnx_b16_gpu0_int8.engine  # b16로 변경 open error
0:00:01.745215952  9330 0xaaaacbca20a0 WARN                 nvinfer gstnvinfer.cpp:681:gst_nvinfer_logger:<primary_gie> NvDsInferContext[UID 1]: Warning from NvDsInferContextImpl::deserializeEngineAndBackend() <nvdsinfer_context_impl.cpp:2080> [UID = 1]: deserialize engine from file :/home/orin/deepstream_test5/models/Primary_Detector/resnet18_trafficcamnet_pruned.onnx_b16_gpu0_int8.engine  # b16로 변경 failed
0:00:01.745251761  9330 0xaaaacbca20a0 WARN                 nvinfer gstnvinfer.cpp:681:gst_nvinfer_logger:<primary_gie> NvDsInferContext[UID 1]: Warning from NvDsInferContextImpl::generateBackendContext() <nvdsinfer_context_impl.cpp:2185> [UID = 1]: deserialize backend context from engine from file :/home/orin/deepstream_test5/models/Primary_Detector/resnet18_trafficcamnet_pruned.onnx_b16_gpu0_int8.engine  # b16로 변경 failed, try rebuild
0:00:01.745268018  9330 0xaaaacbca20a0 INFO                 nvinfer gstnvinfer.cpp:684:gst_nvinfer_logger:<primary_gie> NvDsInferContext[UID 1]: Info from NvDsInferContextImpl::buildModel() <nvdsinfer_context_impl.cpp:2106> [UID = 1]: Trying to create engine from model files
0:02:14.160063720  9330 0xaaaacbca20a0 INFO                 nvinfer gstnvinfer.cpp:684:gst_nvinfer_logger:<primary_gie> NvDsInferContext[UID 1]: Info from NvDsInferContextImpl::buildModel() <nvdsinfer_context_impl.cpp:2138> [UID = 1]: serialize cuda engine to file: /opt/nvidia/deepstream/deepstream-7.1/samples/models/Primary_Detector/resnet18_trafficcamnet_pruned.onnx_b1_gpu0_int8.engine successfully
Implicit layer support has been deprecated
INFO: [Implicit Engine Info]: layers num: 0

0:02:14.713688510  9330 0xaaaacbca20a0 INFO                 nvinfer gstnvinfer_impl.cpp:343:notifyLoadModelStatus:<primary_gie> [UID 1]: Load new model:/home/orin/deepstream_test5/config_infer_primary.txt sucessfully

Runtime commands:
	h: Print this help
	q: Quit

	p: Pause
	r: Resume

NOTE: To expand a source in the 2D tiled display and view object details, left-click on the source.
      To go back to the tiled display, right-click anywhere on the window.


**PERF:  FPS 0 (Avg)	
**PERF:  0.00 (0.00)	
** INFO: <bus_callback:291>: Pipeline ready

Opening in BLOCKING MODE 
NvMMLiteOpen : Block : BlockType = 261 
NvMMLiteBlockCreate : Block : BlockType = 261 
** INFO: <bus_callback:277>: Pipeline running

**PERF:  51.56 (51.50)	
**PERF:  59.97 (56.31)	
**PERF:  59.90 (57.57)	
**PERF:  60.10 (58.27)	
^C** ERROR: <_intr_handler:131>: User Interrupted.. 

**PERF:  59.98 (58.63)	
Quitting
nvstreammux: Successfully handled EOS for source_id=0
App run successful
orin@orin-desktop:~/deepstream_test5$ 


<b>
[secondary-gie0] enable=1
→ 이 옵션은 secondary-gie0 섹션에 정의된 Secondary GIE(추론 엔진)를 활성화하는 역할을 합니다.
즉, 이 플러그인을 파이프라인에 포함시켜 실행하겠다는 의미입니다.
enable-batch-process=1
→ 이 옵션은 배치 처리를 활성화하는 것으로, 여러 입력(예: 여러 프레임이나 객체)을 한 번에 묶어서 처리하도록 하여 효율성을 높이는 설정입니다.
이는 특정 플러그인(예: 트래커나 일부 GIE 설정)에서 사용되며, 활성화되면 배치 단위로 데이터를 처리합니다.

enable-perf-measurement=1
→ 파이프라인에서 성능 측정 기능을 활성화합니다.
이를 통해 FPS, 처리 지연 시간, 처리량 등과 같은 성능 지표들이 수집되고 로그에 출력됩니다.
perf-measurement-interval-sec=5
→ 성능 측정 정보를 5초 간격으로 업데이트
DeepStream 파이프라인에서 타일 디스플레이 기능을 설정하는 부분입니다.
enable=1
→ 타일 디스플레이 기능을 활성화합니다.
여러 스트림을 하나의 창에 그리드 형태로 표시할 수 있게 됩니다.
rows=1
→ 그리드의 행(row) 수를 1개로 설정합니다.
columns=1
→ 그리드의 열(column) 수를 1개로 설정합니다.
즉, 이 설정은 1행 1열, 즉 1개의 타일만 사용하여 단일 스트림(또는 단일 출력)을 표시한다는 의미입니다.
 DeepStream 파이프라인에서 출력(Sink)을 구성하는 부분으로, 각 항목은 아래와 같은 의미를 가집니다:
enable=1
→ 이 Sink를 활성화합니다. (활성화하지 않으려면 0으로 설정)
type=2
→ Sink의 종류를 지정합니다.
→ 주석에 따르면,
1 = FakeSink
2 = EglSink/nv3dsink (Jetson 전용)
3 = File
4 = RTSPStreaming
5 = nvdrmvideosink
→ 여기서는 2번으로 설정되어 있어, Jetson 플랫폼에서 EGL 또는 nv3dsink를 사용해 화면에 출력하게 됩니다.
sync=0
→ Sink의 동기화 옵션입니다.
→ 0이면 동기화(synchronization)를 하지 않아, 프레임 처리 속도에 따라 화면에 바로 출력됩니다.
→ 1로 설정하면 프레임 타이밍에 맞춰 출력하게 되어, 디스플레이와 동기화됩니다.
conn-id=0
→ 연결 식별자(Connection ID)로, 특정 디스플레이 장치(또는 출력 포트)를 지정할 때 사용됩니다.
→ 일반적으로 단일 디스플레이 환경에서는 0으로 설정합니다.
width=0 및 height=0
→ 출력 해상도를 지정합니다.
→ 0으로 설정하면 입력 스트림의 원본 해상도를 그대로 사용하게 됩니다.
plane-id=1
→ 출력에 사용할 디스플레이 plane을 지정합니다.
→ 특히 Jetson 플랫폼에서 여러 디스플레이 레이어가 있을 경우, 어느 plane에 출력할지를 결정합니다.
source-id=0
→ 이 Sink에 연결될 소스(source)의 ID를 지정합니다.
→ 여러 소스가 있을 때, 어떤 소스를 해당 Sink로 출력할지 선택할 수 있습니다.
이 설정은 Jetson 기반 시스템에서 EGL 또는 nv3dsink를 통해 기본 해상도와 기본 디스플레이 plane으로 첫 번째 소스를 출력하도록 구성한 것입니다.
DeepStream 파이프라인에서 파일로 출력(File Sink)을 구성하는 부분입니다. 각 옵션의 의미는 다음과 같습니다:
enable=1
→ 이 Sink를 활성화합니다. (1이면 활성화, 0이면 비활성화)
type=3
→ Sink의 종류를 지정합니다.
→ 주석에 따르면,
    - 1: FakeSink
    - 2: EglSink/nv3dsink (Jetson 전용)
    - 3: File Sink
    - 4: RTSPStreaming
    - 5: nvdrmvideosink
→ 여기서는 파일로 저장하기 위해 3번으로 설정되어 있습니다.
container=1
→ 출력 파일의 컨테이너 형식을 지정합니다.
→ 주석에 따르면,
    - 1: mp4
    - 2: mkv
→ 1은 mp4 형식을 의미합니다.
codec=1
→ 비디오 인코딩 코덱을 지정합니다.
→ 주석에 따르면,
    - 1: h264
    - 2: h265
    - 3: mpeg4
→ 여기서는 h264 코덱을 사용합니다.
enc-type=1
→ 인코더 타입을 설정합니다.
→ 주석에 따르면,
    - 0: Hardware 인코더
    - 1: Software 인코더
→ 1은 소프트웨어 인코더를 사용한다는 의미입니다.
sync=0
→ Sink 동기화 옵션입니다.
→ 0으로 설정하면, 소스의 타임스탬프에 동기화하지 않고 가능한 빠르게 데이터를 출력합니다.
bitrate=2000000
→ 비디오 인코딩 시 사용할 비트레이트를 지정합니다.
→ 여기서는 **2,000,000 bps (약 2 Mbps)**로 설정되어 있습니다.
요약하면, 이 설정은 파일로 출력하는 Sink를 활성화하여 mp4 컨테이너에 h264 코덱으로, 소프트웨어 인코더를 사용해 2Mbps 비트레이트로 비디오를 저장하도록 구성한 것입니다.

====================================================================================
# DeepStream 차량 제조사 및 유형 인식 설정 요약

## 1. 주요 설정 파일 구성

### 메인 설정 파일
- `test5_dec_infer-resnet_tracker_sgie_tiled_display_int8.txt`: 전체 DeepStream 파이프라인 구성

### 추론 설정 파일
- `config_infer_primary.txt`: 차량 감지 모델(PGIE) 설정
- `config_infer_secondary_vehiclemake.txt`: 차량 제조사 인식 모델(SGIE) 설정
- `config_infer_secondary_vehicletypes.txt`: 차량 유형 인식 모델(SGIE) 설정

## 2. 중요한 설정 매개변수

### 배치 크기 설정
- `[streammux]` 섹션의 `batch-size`: 비디오 소스 개수와 일치해야 함 (단일 비디오는 1)
- `[primary-gie]` 섹션의 `batch-size`: streammux와 일치해야 함 (1)
- `[secondary-gie0]`와 `[secondary-gie1]`의 `batch-size`: 프레임당 처리할 최대 객체 수 (16)

### 엔진 파일 이름
- 엔진 파일 이름에 포함된 배치 크기(`b1`, `b16` 등)는 실제 설정과 일치해야 함
- 예: `resnet18_trafficcamnet_pruned.onnx_b1_gpu0_int8.engine`

### 출력 설정
- `[sink0]`는 화면 출력용 (type=2: EglSink)
- `[sink1]`은 파일 출력용 (type=3: File), 활성화 시 `output-file` 경로가 올바르게 설정되어야 함

## 3. 신뢰도 임계값
- `classifier-threshold`: 인식 결과 표시 임계값 (기본값 0.51)
- 제조사 인식률을 높이려면 `config_infer_secondary_vehiclemake.txt`에서 이 값을 낮출 수 있음 (예: 0.4)

## 4. 해결한 주요 문제
1. **배치 크기 일관성**: 다양한 설정 파일에서 배치 크기를 일관되게 유지
2. **엔진 파일 생성**: 누락된 엔진 파일은 첫 실행 시 자동 생성됨
3. **sink 설정 오류**: 파일 출력 관련 설정 수정

## 5. 실행 명령어
```bash
deepstream-app -c test5_dec_infer-resnet_tracker_sgie_tiled_display_int8.txt
```

## 6. 성능 향상 팁
- 필요한 모든 엔진 파일을 미리 생성하여 시작 시간 단축
- 불필요한 sink 섹션은 비활성화하여 리소스 절약
- 신뢰도 임계값 조정으로 인식 결과의 균형 맞추기

## 7. 디버깅 방법
- 로그 메시지 확인으로 문제 진단
- 디버그 출력 활성화: `--gst-debug=3` 옵션 사용
- 구성 파일의 매개변수 변경으로 세부 조정
## 8. ntron


orin@orin-desktop:~/deepstream_test5$ netron /home/orin/deepstream_test2/models/Secondary_VehicleMake/resnet18_vehiclemakenet_pruned.onnx

![Screenshot from 2025-02-26 14-58-19](https://github.com/user-attachments/assets/cc859dfa-a4cd-467d-aee9-e444918efbae)

이 설정을 통해 단일 비디오 소스에서 차량 감지, 차량 제조사 인식, 차량 유형 인식을 성공적으로 수행할 수 있습니다.























