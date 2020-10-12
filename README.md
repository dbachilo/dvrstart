# dvrstart
Dmitry Bachilo's DVR software for FreeBSD<br>
Based on Ivan Avdeev's shell scripts logic

This is a free DVR software for FreeBSD written in Gambas.<br>
This software uses ffmpeg to record video and audio (if available) from a survailance cameras.<br>
As of initial release it supports Axis and Hikvision cameras (because I have them)<br>
It records rtsp flow and devides it into 20-minute mp4 files sorted by subdirectories with camera names.<br>

Feel free to contribute.

To run it you obviously need Gambas and FFMpeg installed on your FreeBSD machine (or Linux, or any other system that supports Gambas and FFMpeg)
Now, I have a gambas installation script for FreeBSD 12.x AMD64, which is basically a binary distrib I compiled myself:
just downwload and run http://allunix.ru/back/gbinst.sh on your FreeBSD fresh installed machine and you are ready to run console Gambas software.

You will also need to create /usr/local/etc/dvr directory and place three files in there.

<b>/usr/local/etc/dvr/cams.conf</b> should contain your cameras, one in a row, in a following format:<br>
&lt;ip&gt;;&lt;login&gt;;&lt;password&gt;;&lt;manufacturer&gt;;&lt;name&gt;
  
for example:

192.168.10.22;root;admin;Axis;Parking_Lot<br>
192.168.10.23;Admin;Admin;Hikvision;Entrance

and so on.

<b>/usr/local/etc/dvr/storage.conf</b> must cantain full path to the place where you want to store your records

for example:
/root/cams/

the last slash is mandatory

In this case Parking_Lot camera will put it's records to /root/cams/Parking_Lot/
You will also have to create this directory yourself. At least at the moment of this initial software release, I am planning to automate that.

recorded files will have a filename based on a time of record, i.e.<br>
/root/cams/Parking_Lot/record-[2020-09-27]-16-20.mp4<br>
The files are 20 minutes long, so you will have three files per hour. This could be ajusted in future releases.<br>
While dvrstart is created to make 20-minute files, it tries to keep the files easily sorted, so that every record starts at eiter 00 or 20 or 40 minutes of the current hour. If dvrstart was lauched, say, at 13:22, it would wait until it's 13:40 and then stop the record and immidiately restart it, so that the next file will be called 13-40, then 14:00, then 13:20 and so on. It happens only at startup. If your server loses the connection with the camera, it will stop the record and would try to regain access. When it would reach the camera again, it would start the recording immidiatly, making all other records iterate every 20 minutes for now on whatever the time is. However every midnight it adjusts the filenames again. relaunching the recording, so the day always starts at 00:00 and then keeps on recording this way.

In other words, say, you started the recording at 21:38 and lost a connection for a few seconds at 23:03. What you would have in your camera_name directory is:<br>
record-[2020-09-27]-21-38.mp4 - this file would be 2 minutes long<br>
record-[2020-09-27]-21-40.mp4 - this and further files would be 20 minutes long<br>
record-[2020-09-27]-22-00.mp4<br>
record-[2020-09-27]-22-20.mp4<br>
record-[2020-09-27]-22-40.mp4<br>
record-[2020-09-27]-23-00.mp4 - this file would be 3 minutes long<br>
record-[2020-09-27]-23-03.mp4 - this and next file would be 20 minutes long<br>
record-[2020-09-27]-23-23.mp4<br>
record-[2020-09-27]-23-43.mp4 - this file would be 17 minutes long<br>
record-[2020-09-28]-00:00.mp4 - this and further files would be 20 minutes long<br>
record-[2020-09-28]-00:20.mp4<br>
record-[2020-09-28]-00:40.mp4<br>
record-[2020-09-28]-01:00.mp4<br>
and so on.<br>

<b>/usr/local/etc/dvr/storedays.conf</b> must contain the amount of days the records are kept on the server.
for example:
25

this means that on september 26 the records made on september 1st will be deleted from the server.

It does not matter, where your dvrstart.gambas file is located, you can run it from anywhere, but it is assumed that you run it from /usr/local/bin
during the system startup and it has root priviledges.

It does not have to be run as root as of now, because it does not write it's config files yet. If you have a user, which has write privs to the path of records, then it would work just as good.
