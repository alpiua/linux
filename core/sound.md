sound
=====
Залипает звук

В файле /etc/pulse/daemon.conf найдите строку ; realtime-scheduling = yes. Присвойте ей значение no и удалите точку с запятой. Перезапустите PulseAudio командой pulseaudio -k