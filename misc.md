# Miscellaneous

## Commands

### AWS

#### ElasticBeanstalk: Use 'npm run ...' during SSH session

```bash
sudo su
export PATH=$PATH:`ls -vdr /opt/elasticbeanstalk/node-install/node-* | head -1`/bin
/opt/elasticbeanstalk/bin/get-config environment | python -c "import json,sys; obj=json.load(sys.stdin); f = open('/tmp/eb_env', 'w'); f.write('\n'.join(map(lambda x: 'export ' + x[0] + '=' + x[1], obj.iteritems())))"
source /tmp/eb_env
rm -f /tmp/eb_env
cd /var/app/current/

# npm run ...
```

### Docker

#### Clean

```bash
docker-compose down # Stop the container(s)
docker rm -f $(docker ps -a -q) # Delete all containers
docker volume rm $(docker volume ls -q) # Delete all volumes
docker-compose up # Restart the containers
```

### Heroku

#### Login / Swap user

```bash
heroku login
```

### Apache Bench

#### Simple request

```bash
ab -n 10000 -c 512 -k http://127.0.0.1:3000/
```

### Symbolic link

```bash
ln -s /path/to/original/ /path/to/link
```

### Diff

```bash
diff --unified=3 file1.ext file2.ext > file.diff
```

Where the number after `--unified=` is the number of lines showing the context. To fully output the file, put a big number (> number of lines).

### SSL

#### Convert .pem & .key to .pfx (pkcs12)

This example use Cloudflare Origin CA root certificates for CAfile but the idea is the same.

```bash
openssl pkcs12 -export -out myCertificate.pfx -in myCertificate.pem -inkey myCertificate.key -CAfile origin_ca_rsa_root.pem
```

It will then ask for a password before creating the certificate.

### SSH Key

#### Generate

```bash
ssh-keygen -t rsa -b 4096 -C "comment, usually mail address" -f "filename, usually username-Platform"
```

#### Add

```bash
ssh-add ~/.ssh/PRIVATE-KEY-FILE &> /dev/null
```

### PostgreSQL

#### List all collations

```sql
SELECT * FROM pg_collation;
```

#### Create Database with UTF8

```sql
CREATE DATABASE "myDatabase" ENCODING 'UTF8' LC_COLLATE = 'en_US.utf8' LC_CTYPE = 'en_US.utf8' TEMPLATE template0;
```

### Python

#### Uninstall all pip packages

```bash
pip freeze | xargs pip uninstall -y
```

### Git

#### Aliases

```bash
[alias]
 rebase-last = "!b=\"$(git branch --no-color | cut -c3-)\" ; h=\"$(git rev-parse $b)\" ; echo \"Current branch: $b $h\" ; c=\"$(git rev-parse $b)\" ; echo \"Recreating $b branch with initial commit $c ...\" ; git checkout --orphan new-start $c ; git commit -C $c ; git rebase --onto new-start $c $b ; git branch -d new-start ; git gc"
```

### GPSBabel

#### Convert GPX Track to GPX Waypoints

```bash
gpsbabel -i gpx -f INPUT.gpx -x transform,wpt=trk -o gpx -F OUTPUT.gpx
```

### Pandoc

#### Convert Markdown to PDF

```bash
pandoc file.md --pdf-engine=xelatex -V geometry:margin=1.5in -V urlcolor=blue -o file.pdf
```

## macOS

### SimC building

```bash
# Clean & Pull
git clean -xdf
git pull

# CLI only
make -C engine -j $(expr $(sysctl -n hw.ncpu) / 2) LTO=1 optimized

# CLI + GUI
qmake simulationcraft.pro
make -j $(expr $(sysctl -n hw.ncpu) / 2) LTO=1
```

### SimC debugging

```bash
make -C engine -j $(expr $(sysctl -n hw.ncpu) / 2) debug
lldb ./engine/simc XXX.simc
run
```

### Force enable TRIM

```bash
sudo trimforce enable
```

### Get ulimit current values

```bash
ulimit -a
```

### Get ulimit max values

```bash
ulimit -a -H
```

### Increase open files

```bash
ulimit -n 2048
```

### Keyboard reset

Sometimes macOS keyboard is confused and there is no other choice than deleting `/Library/Preferences/com.apple.keyboardtype.plist` file.
See: <http://eng.raneri.it/blog/2009/01/17/how-to-reset-the-mac-keyboard/>

### Concat multiple video files (using ffmpeg)

`videos.txt`
```bash
file '/path/to/video1.ext'
file '/path/to/video2.ext'
file '/path/to/video3.ext'
```

```bash
ffmpeg -f concat -safe 0 -i videos.txt -c copy video.ext
```

### Splitting a zip into multiple parts

```bash
zip -s 10g 100g-split.zip 100g.zip
```

### Unzip multiple archive parts

The easiest way is to cat all of them into a single zip, like this:

```bash
cat /path/to/archive-parts/my-archive.zip.001 /path/to/archive-parts/my-archive.zip.002 /path/to/archive-parts/my-archive.zip.003 > my-archive.zip
```

If the multi-part was correctly done (i.e. a master .zip), double-clicking on the master .zip should do the trick though.

### Delete a part of a zip (mostly used to remove the annoying __MACOSX folder)

```bash
zip -d Archive.zip __MACOSX/\*
```

### Razer Synapse clean reinstall

Step 1: Open the Application Finder. Click on Utilities and launch Terminal. In your terminal: stop and remove launch agents by typing these commands one at a time.

```bash
launchctl remove com.razer.rzupdater

launchctl remove com.razerzone.rzdeviceengine

sudo rm /Library/LaunchAgents/com.razer.rzupdater.plist

sudo rm /Library/LaunchAgents/com.razerzone.rzdeviceengine.plist
```

Step 2: Remove HID kernel extension

```
sudo rm -Rf /System/Library/Extensions/RazerHid.kext
```

Step 3: Manually delete Razer Synapse app from Applications in Finder

Step 4: Delete Razer files from "Application Support" folders:

```
sudo rm -rf /Library/Application\ Support/Razer/
```

Step 5: Restart your Mac.

Step 6: Reinstall Synapse. <http://drivers.razersupport.com//index.php?_m=downloads&_a=viewdownload&downloaditemid=2626&nav=0,343,239>

### Manually install XCode Command Line Tools missing headers in macOS 10.14+

See: <https://github.com/pyenv/pyenv/issues/1219>

```bash
sudo installer -pkg /Library/Developer/CommandLineTools/Packages/macOS_SDK_headers_for_macOS_10.14.pkg -target /
```
