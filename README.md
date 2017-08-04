http://weworkweplay.com/play/automatically-connect-a-raspberry-pi-to-a-wifi-network/

deb http://ppa.launchpad.net/ddalex/gstreamer/ubuntu trusty main 
deb-src http://ppa.launchpad.net/ddalex/gstreamer/ubuntu trusty main 

sudo apt-get install gir1.2-gstreamer-1.0 gstreamer1.0-plugins-good libgstreamer-plugins-bad1.0-0 libgstreamer-plugins-base1.0-0 libgstreamer-plugins-good1.0-0 libgstreamer-plugins-good1.0-dev libgstreamer1.0-0 libgstreamer1.0-dev

sudo apt-get install gstreamer0.10-tools gstreamer-tools gstreamer0.10-plugins-base gstreamer0.10-plugins-good gstreamer0.10-plugins-bad

sudo apt-get install gstreamer1.0-libav

// Virtual
http://forum.kodi.tv/showthread.php?tid=86581

gst-inspect-1.0 --version
==. View version of gstreamer package

gst-inspect-1.0
==> List all current supported plugins

gst-inspect-1.0 <pluginname>
==> List all plugins properties
Example:
	+ gst-inspect-1.0 filesrc
	+ gst-inspect-1.0 filesink
	+ gst-inspect-1.0 matroskamux/matroskademux
	
Pipeline construction:
	src + transform/filter + encoder(H.264) + payloader + sink (rtpsink/filesink)
	Example:
		+++ stream videotestsrc via udp
			server: gst-launch-1.0 -v videotestsrc blocksize=3110400  ! video/x-raw, format=I420, width=1920, height=1080, framerate=30/1 ! imxvpuenc_h264 ! rtph264pay pt=96 ! udpsink host=192.168.2.10 port=1234
						
			server: gst-launch-1.0 -v videotestsrc blocksize=3110400 horizontal-speed=5 ! video/x-raw, format=I420, width=1920, height=1080, framerate=30/1 ! imxvpuenc_h264 ! rtph264pay pt=96 ! udpsink host=192.168.2.22 port=1234
			client: gst-launch-1.0 -v udpsrc port=1234 caps='application/x-rtp, payload=96, clock-rate=90000' ! rtph264depay ! avdec_h264 ! autovideosink
			
		+++ record to file
		gst-launch-1.0 -v videotestsrc blocksize=3110400 horizontal-speed=5 ! video/x-raw, format=I420, width=1920, height=1080, framerate=30/1 ! imxvpuenc_h264 ! h264parse ! matroskamux ! filesink location=/home/root/test.mkv
	
	
	blocksize = 1,5 * 1920 * 1080
//Ubuntu
gst-launch-1.0 videotestsrc horizontal-speed=5 ! video/x-raw-rgb, framerate=24/1, width=1920, height=1080 ! ffmpegcolorspace  ! avimux  ! filesink location=./test.avi

gst-launch videotestsrc horizontal-speed=5 ! video/x-raw-rgb, framerate=24/1, width=1920, height=1080 ! ffmpegcolorspace  ! avimux  ! filesink location=./test.avi

gst-launch videotestsrc ! video/x-raw-rgb, framerate=24/1, width=1920, height=1080 ! ffmpegcolorspace  ! avimux  ! filesink location=./test4.avi
	
decodebin
	
Kernel3.14.28:
- Encoder plugin:
	+ vpuenc: Plugin of NXP
	+ imxvpuenc_h264: Plugin of Gstreamer community
- Resize plugin:
	+ imxvideoconvert_ipu: plugin of NXP
	
Kernel 4.
- Encoder plugin:
	+ vpuenc_h264: Plugin of NXP
	+ imxvpuenc_h264: Plugin of Gstreamer community ==> Not exist
- Resize plugin:
	+ imxvideoconvert_ipu: plugin of NXP
===> Modification:
	+ vpuenc_h264: livestreaming = false
	+ imxvideoconvert_ipu: livestreaming	
	
//Debug streamer:	
export GST_DEBUG=*:4

export GST_DEBUG=2,*imx*:7
export GST_DEBUG=2,*imx*:9

$ export GST_DEBUG=[Level]
Level = 1 : GST_ERROR() 
Level = 2 : Level 1 + GST_WARNING()
Level = 3 : Level 2 + GST_INFO()
Level = 4 : Level 3 + GST_DEBUG()
Level = 5 : outputs everything

//=====================================================================================================================
// aes_256_note.txt
//=====================================================================================================================
filesrc name=filesrc location=./Video1/20161207113730A ! matroskademux name=matroskademux !
rtph264pay name=rtph264pay timestamp-offset=0 ! 
srtpenc name=srtpenc !
udpsink name=udpsink host=192.168.2.21 port=1234,

          StreamVar->VideoPath, STRMN_TIMESTAMP_OFFSET, EncryptionStr,
          StreamVar->MediaServerIp, StreamVar->MediaServerPort);
          
gst-launch-1.0 -v filesrc name=filesrc location=./Video1/20161207113730A ! rtph264pay name=rtph264pay timestamp-offset=0 ! srtpenc name=srtpenc key=DC5D1F5289BFEBECAAF33D3C0265E762F47D53D9D875EF180C57C463AA152986DC5D1F5289BFEBECAAF33D3C0265 ! udpsink name=udpsink host=192.168.2.21 port=1234

./gst-variable-rtsp-server -p 8554 -u "udpsrc caps=\"application/x-rtp\" port=1234 ! rtph264depay ! vpudec ! vpuenc_h264 livestreaming=false ! rtph264pay name=pay0 pt=96"

./gst-variable-rtsp-server -p 8554 -u "udpsrc port=1234 caps=\"application/x-srtp,payload=\(int\)96,ssrc=\(uint\)1356955624,srtp-key=\(buffer\)AD75E104F426D88919822A56C4A2CCD5817C2083FAD4FB6C2A0026AE745B6A21,srtp-cipher=\(string\)aes-128-icm,srtp-auth=\(string\)hmac-sha1-80,srtcp-cipher=\(string\)aes-128-icm,srtcp-auth=\(string\)hmac-sha1-80\" ! srtpdec ! rtph264depay ! vpudec ! vpuenc_h264 livestreaming=false ! rtph264pay name=pay0 pt=96"

./gst-variable-rtsp-server -p 8554 -u "udpsrc port=1234 caps=\"application/x-srtp,payload=\(int\)96,ssrc=\(uint\)1356955624,srtp-key=\(buffer\)2D4FAB21F0694828341E72DA5F9C2D8ADB113114E0B1462988423D4DED2CAA1A,srtp-cipher=\(string\)aes-128-icm,srtp-auth=\(string\)hmac-sha1-80,srtcp-cipher=\(string\)aes-128-icm,srtcp-auth=\(string\)hmac-sha1-80\" ! srtpdec ! rtph264depay ! vpudec ! vpuenc_h264 livestreaming=false ! rtph264pay name=pay0 pt=96"

./gst-variable-rtsp-server -p 8554 -u "udpsrc port=1234 caps=\"application/x-srtp,payload=\(int\)96,ssrc=\(uint\)3102577484,srtp-key=\(buffer\)166A6035883A42AC4B45978C728DFAEB91E6AAB2F320079A8239DE7AF3FB5A0A,srtp-cipher=\(string\)aes-128-icm,srtp-auth=\(string\)hmac-sha1-80,srtcp-cipher=\(string\)aes-128-icm,srtcp-auth=\(string\)hmac-sha1-80\" ! srtpdec ! rtph264depay ! vpudec ! vpuenc_h264 livestreaming=false ! rtph264pay name=pay0 pt=96"

./gst-variable-rtsp-server -p 8554 -u "udpsrc port=1234 caps=\"application/x-srtp, payload=(int)96, ssrc=(uint)3102577484, srtp-key=(buffer)043FDEE36B034693246AE6024CBB8233B056DA8FDB6C0B1BC7FE3DA819D2581D, srtp-cipher=(string)\"aes-128-icm\", srtp-auth=(string)\"hmac-sha1-80\", srtcp-cipher=(string)\"aes-128-icm\", srtcp-auth=(string)\"hmac-sha1-80\"\" srtpdec ! rtph264depay ! vpudec ! vpuenc_h264 livestreaming=false ! rtph264pay name=pay0 pt=96"

./gst-variable-rtsp-server -p 8554 -u "udpsrc port=1234 blocksize=460800 caps=\"application/x-srtp, payload=(int)96, ssrc=(uint)3102577484, srtp-key=(buffer)D0C234B01996ADAFA1019FFC96B0D7FCA82378A697FDE9CEDAE93FD2CBEB\" ! srtpdec ! rtph264depay ! vpudec ! vpuenc_h264 livestreaming=false ! rtph264pay name=pay0 pt=96"

./gst-variable-rtsp-server -p 8554 -u "udpsrc port=1234 blocksize=460800 caps=\"application/x-srtp, payload=(int)96, ssrc=(uint)3102577484, srtp-key=(buffer)B35CC98A38172736620D7184A8323B5F2F4B53C0B2B2EAB8D1442E67C9F1737C, srtp-cipher=(string)aes-128-icm, srtp-auth=(string)hmac-sha1-80, srtcp-cipher=(string)aes-128-icm, srtcp-auth=(string)hmac-sha1-80a\" srtpdec ! rtph264depay ! avdec_h264 ! x264enc ! rtph264pay name=pay0 pt=96"

//==============================================
AES-256

gst-launch-1.0 -v imxv4l2src device=/dev/video1 ! imxvpuenc_h264 ! rtph264pay name=pay0 pt=96 ssrc=1356955624 ! srtpenc key="DC5D1F5289BFEBECAAF33D3C0265E762F47D53D9D875EF180C57C463AA152986DC5D1F5289BFEBECAAF33D3C0267" ! udpsink host=192.168.2.25 port=1234

gst-launch-1.0 -v udpsrc port=1234 caps='application/x-srtp, payload=(int)96, ssrc=(uint)1356955624, srtp-key=(buffer)DC5D1F5289BFEBECAAF33D3C0265E762F47D53D9D875EF180C57C463AA152986DC5D1F5289BFEBECAAF33D3C0267, srtp-cipher=(string)aes-256-icm, srtp-auth=(string)hmac-sha1-80, srtcp-cipher=(string)aes-256-icm, srtcp-auth=(string)hmac-sha1-80' ! srtpdec ! rtph264depay ! avdec_h264 ! autovideosink

//==============================================
AES-128

gst-launch-1.0 -v imxv4l2src device=/dev/video1 ! imxvpuenc_h264 ! rtph264pay name=pay0 pt=96 ssrc=1356955624 ! srtpenc key="DC5D1F5289BFEBECAAF33D3C0265E762F47D53D9D875EF180C57C463AA15" ! udpsink host=192.168.2.25 port=1234

gst-launch-1.0 -v udpsrc port=1234 caps='application/x-srtp, payload=(int)96, ssrc=(uint)3102577484, srtp-key=(buffer)FBD6C488554846BD985CDA1D7E3F7923FC1A39D590A72C75212772444305163E, srtp-cipher=(string)aes-128-icm, srtp-auth=(string)hmac-sha1-80, srtcp-cipher=(string)aes-128-icm, srtcp-auth=(string)hmac-sha1-80' ! srtpdec ! rtph264depay ! avdec_h264 ! autovideosink

gst-launch-1.0 -v udpsrc port=1234 caps="application/x-srtp, payload=(int)96, ssrc=(uint)3102577484, srtp-key=(buffer)4D618990C067D25CFBA9C8B01360266D77393EE21B041E56D7A913927E035CCB, srtp-cipher=(string)aes-128-icm, srtp-auth=(string)hmac-sha1-80, srtcp-cipher=(string)aes-128-icm, srtcp-auth=(string)hmac-sha1-80" ! srtpdec ! rtph264depay ! h264parse ! avdec_h264 ! autovideosink
//=====================================================================================================================
//=====================================================================================================================

//Test1:
gst-launch-1.0 -v videotestsrc blocksize=3110400  ! video/x-raw, format=I420, width=800, height=480, framerate=30/1 ! vpuenc_h264 livestreaming=false ! rtph264pay pt=96 ! udpsink host=192.168.2.3 port=1234

Client:
gst-launch-1.0 -v udpsrc port=1234 caps='application/x-rtp, payload=96, clock-rate=90000' ! rtph264depay ! avdec_h264 ! autovideosink

//Test2:
gst-launch-1.0 -v imxv4l2src device=/dev/video1 blocksize=3110400  ! video/x-raw, format=I420, width=1920, height=1080, framerate=30/1 ! vpuenc_h264 livestreaming=false ! rtph264pay pt=96 ! udpsink host=192.168.2.10 port=1234

Client:
gst-launch-1.0 -v udpsrc port=1234 caps='application/x-rtp, payload=96, clock-rate=90000' ! rtph264depay ! avdec_h264 ! autovideosink


//==== Ubuntu Test3
sudo apt-get install libx264-dev
sudo add-apt-repository ppa:mc3man/trusty-media
sudo apt-get update
sudo apt-get install ffmpeg gstreamer0.10-ffmpeg libavcodec-extra-53 libavcodec53

//Server:
gst-launch-1.0 -v videotestsrc ! x264enc ! rtph264pay pt=96 name=pay0 ! udpsink host=127.0.0.1 port=1234

//Client:
gst-launch-1.0 -v udpsrc port=1234 caps='application/x-rtp, payload=96, clock-rate=90000' ! rtph264depay ! avdec_h264 ! autovideosink
	
ifconfig wlan0 down	

gst-launch-1.0 -v videotestsrc blocksize=3110400 horizontal-speed=5 ! video/x-raw, format=I420, width=1920, height=1080, framerate=30/1 ! vpuenc_h264 livestreaming=false ! rtph264pay pt=96 ! udpsink host=192.168.2.13 port=1234
	
gst-launch-1.0 -v udpsrc port=1234 caps='application/x-rtp, payload=96, clock-rate=90000' ! rtph264depay ! decodebin ! autovideosink
	
	
sudo apt-get install ubuntu-restricted-extras
