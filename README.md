  Piclodio2 Music & Webradio
==============================

I forked this program from Sispheor to add the following features : 

 - Upload/Delete/Play music files on the Raspberry. 
 - You can now choose 'Webradio' or 'Music' when you create your AlarmClock. 
 - When a music AlarmClock is launched, music will be selected randomly in the music database.
 - Set the system time manually. 

__Next Steps__ : 
In the next version of this program, by clicking on "Play Music Randomly" on the home page, I want to be able to listen an infinite number of music files randomly selected.  
Now, clicking on "Play Music randomly" will launch a Mplayer process for only one music and return the selected music on the web UI. 
The main problem is that the web UI isn't notified when the music is finished, so the page will not be reloaded. 
So, I think a Websocket mecanism is needed to have real-time notifications between Mplayer and GUI components. 

Feel free to fork, improve, or help me ;) 


Installation
=========== 

Piclodio2 is a web radio player and a also an alarm clock. You can add url stream to complete the collection. Scheduling alarm clock is easy and can be periodic.

![alt tag](https://raw.github.com/isador999/Piclodio2-Musique-et-Webradio/1.3-dev/img/piclodio_music_home.png)


Prerequisites
==========

**pip** for python dependencies, **Mplayer** to play stream, **at** to stop alarm clock automatically, **sqlite3** to store data, **git** to clone the project and **python-alsaaudio** to manage sound

```
sudo apt-get install python-pip mplayer at sqlite3 git python-alsaaudio
```

Install Django framework from pip

```
sudo pip install Django==1.7.7
```

Clone the project. Notice we didn't use sudo here. The folder has to belong to the Pi user.
```
cd /home/pi
git clone https://github.com/isador999/Piclodio2-Musique-et-Webradio.git
mv Piclodio2-Musique-et-Webradio Piclodio2
```

You need then to create the destination folder for the music files, and pi user must be owner.
```
mkdir /srv/fichiers/
chown -R pi:pi /srv/fichiers/
```


Option 1 : Use Django's server to run piclodio
==========

It's not the best practice but it's easy and fast.

As pi user :

Copy the init script
```
sudo cp Piclodio2/run_piclodio/init_script/piclodio.sh /etc/init.d/piclodio
sudo chmod +x /etc/init.d/piclodio
sudo update-rc.d piclodio defaults
```
You can edit it to customise settings
Start piclodio :
```
sudo /etc/init.d/piclodio start
```
That's it, you can now access your piclodio at http://yourip:8000


Option 2 : Run Piclodio with apache
==========

Prerequisites
```
sudo apt-get install apache2 libapache2-mod-wsgi
```
Move Piclodio in default apache directory and give access
```
sudo mv /home/pi/Piclodio2 /var/www
sudo chown -R www-data: /var/www/Piclodio2
```
Copy vHost from sources folder into apache vHost configuration folder
```
sudo cp /var/www/Piclodio2/run_piclodio/apache/piclodio.conf /etc/apache2/sites-available/piclodio
```
Enable the vHost
```
sudo a2ensite piclodio
```
And last, we have to allow the Apache user www-data to use mplayer. Edit the sudoers file with the command
```
sudo visudo
```
and add this line at the end of the file
```
www-data ALL=NOPASSWD:/usr/bin/mplayer* ,/usr/bin/pgrep mplayer ,/usr/bin/killall mplayer, /usr/bin/at
```
Add Apache user to audio group to allow him to control sound
```
sudo usermod -a -G audio www-data
```
Reload Apache
```
sudo service apache2 reload
```

That's it! Piclodio is now available on it IP address on **http://RPI_IP_ADDRESS/piclodio**
