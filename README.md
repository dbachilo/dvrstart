# dvrstart
Dmitry Bachilo's DVR software for FreeBSD<br>
Based on Ivan Avdeev's shell scripts logic

This is a free DVR software for FreeBSD written in Gambas.
This software uses ffmpeg to record video and audio (if available) from a survailance cameras.
As of initial release it supports Axis and Hikvision cameras (because I have them)
It records rtsp flow an devides it into 20-minute mp4 files sorted by subdirectories with camera names.

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

recorded files will have a filename based on a time of record, i.e.
/root/cams/Parking_Lot/record-[2020-09-27]-16-20.mp4
The files are 20 minutes long, so you will have three files per hour. This could be ajusted in future releases

<b>/usr/local/etc/dvr/storedays.conf</b> must contain the amount of days the records are kept on the server.
for example:
25

this means that on september 26 the records made on september 1st will be deleted from the server.

It does not matter, where your dvrstart.gambas file is located, you can run it from anywhere, but it is assumed that you run it from /usr/local/bin
during the system startup and it has root priviledges.
