dmarc-report-converter
======================

Convert DMARC reports from xml to human-readable formats.

Example of html_static output:
![html](screenshots/html_static.png)

Support input formats:

* **.xml** file: dmarc report in xml format

* **.gz** file: gzipped dmarc report in xml format

* **.zip** file: zipped dmarc report in xml format

* **imap**: connect to imap server and download emails. If attachments contains **.xml**, **.gz** or
  **.zip**, try to convert them

Support output formats:

* **html_static** output file is the html, generated from template templates/html_static.gotmpl.
  This format uses bootstrap hosted on bootstrapcdn, so you don't need to configure self-hosted
  bootsrap assets.

* **html** output file is the html, generated from template templates/html.gotmpl.
  This format uses self-hosted bootsrap and javascript assets, so you need to configure your web
  server and *output -> assets_path* option.

* **txt** output file is the plain text, generated from template templates/txt.gotmpl

* **json** output file is the json

Installation
------------

1. Get installation archive. There are two ways: download pre-builded archive from
   [github releases](https://github.com/tierpod/dmarc-report-converter/releases) page or
   [build from sources](#building-from-sources)

2. Unpack to destination directory, for example to "/opt":

   ```bash
   sudo tar -xvf dmarc-report-converter*.tar.gz -C /opt
   ```

3. Copy example config file and [edit](#configuration):

   ```bash
   cd /opt/dmarc-report-converter/
   sudo cp config.dist.yaml config.yaml
   sudo nano config.yaml
   ```

4. If you want to execute it daily, add crontab daily job:

   ```bash
   sudo cp install/dmarc-report-converter.sh /etc/cron.daily/
   ```

   or systemd service unit + systemd timer unit (see examples in "install" directory)

5. If you want to use "html" output, you have to configure your web server to serve **assets**
   directory and change assets_path in configuration file. Example for nginx:

   ```bash
   sudo cp -r assets /usr/share/nginx/html
   ```

   config.yaml:

   ```yaml
   output:
     assets_path: "/dmarc/assets"
   ```

   location configuration:

   ```nginx
   location /dmarc/ {
       root /usr/share/nginx/html;
       autoindex           on;
       autoindex_localtime on;
   }
   ```

    and go to the http://your-web-server/dmarc

Configuration
-------------

Copy config/config.dist.yaml to config.yaml and change parameters:

* input: choose and configure **dir** OR **imap**. If **delete: yes**, delete source
  files after converting (with configured imap, delete source emails)

* output: choose format and file name template. If **file** is empty string "" or "stdout", print
  result to stdout.

* lookup_addr: perform reverse lookup? If enabled, may take some time.

* imap_debug: show all network activity?

Building from sources
---------------------

1. Install go compiler and building tools:

   ```bash
   # debian/ubuntu
   sudo apt-get install golang-go make git tar

   # centos/fedora, enable epel-release repo first
   sudo yum install epel-release
   sudo yum install golang make git tar
   ```

   or follow [official instruction](https://golang.org/dl/)

2. Download sources:

   ```bash
   go get -u github.com/tierpod/dmarc-report-converter
   ```

3. Build binary and create installation archive:

   ```bash
   cd $HOME/go/src/github.com/tierpod/dmarc-report-converter
   make release
   ```

4. Installation archive will be places inside _release_ directory. Also, if you want to test
   dmarc-report-converter without installation, you can execute:

   ```bash
   ./bin/dmarc-report-converter -config /path/to/config.yaml
   ```

Thanks
------

* [bootstrap](https://getbootstrap.com/)
* [jquery](http://jquery.com/)
* [ChartJS](http://chartjs.org/)
* [golang emersion packages](https://github.com/emersion) (go-imap, go-message, go-sasl, go-textwrapper)
