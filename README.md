zabbix-nginx-stats
==================

Simple script to import basic nginx statistics into zabbix. It parses the log file (currently only supports one basic format) and pushes the data into zabbix. It is written in perl with a small bash wrapper script which uses logtail.

Synced values include (all since last execution):

* Request Count
* Request count by status code
* Request Time: Average, Mean, Median, 90%, 80% and 25% lines
* Upstream Response Time (Same as Request Time)
* Body Bytes Sent: Average, Sum

The default zabbix template has preconfigured triggers to warn on 5 errors (status code 500, 503 and 403) and graphs for request time and request count by status.

Installation/Configuration:
----------------

### checkout from github
### install dependencies

* logtail2 (debian: apt-get install logtail)
    * for redhat: you need custom instal from source
 
      install dev tool
      ```shell
      sudo dnf groupinstall "Development Tools"
      sudo dnf install make gcc
      ```
      install logtail2 from source:
      ```shell
      git clone https://github.com/icyfork/logcheck.git
      make install
      ```
* Perl
* Statistics::Descriptive, Date::Parse and File::Temp
    * for debian: install packages libstatistics-descriptive-perl libtimedate-perl
    * For redhat: install packages perl-DateTime perl perl-CPAN perl-devel, so the first you need run sudo dnf config-manager --set-enabled crb

      Here's how you can install a Perl module from CPAN:

      1. First, ensure that you have Perl and its development tools installed:
      ```shell
      sudo dnf install perl perl-CPAN perl-devel
      ```
      2. Next, run the CPAN shell:
      ```shell
      sudo cpan
      ```
      3. Inside the CPAN shell, you can install the Statistics::Descriptive module by running:
      ```shell
      install Statistics::Descriptive
      ```

      Once the installation is complete, you can exit the CPAN shell by typing exit.

### configure logfile output:

         log_format timed_combined '$remote_addr $host $remote_user [$time_local]  '
                    '"$request" $status $body_bytes_sent '
                    '"$http_referer" "$http_user_agent" $request_time $upstream_response_time $pipe';
                    
         access_log /var/log/nginx/access.log timed_combined;

### Import template zbx_template_nginx.xml
### configure crontab to run every 10 minutes:

    8-59/10 * * * *   root      /home/scripts/zabbix-nginx-stats/zabbix-nginx-stats-runner.sh

### watch results coming in.

![Screenshot Requests by Status](docs/screenshot-requests-by-status.png)


Changelog
--------------

* 2013-07-15: Initial release

Roadmap
--------------

Allow pushing to multiple zabbix hosts - If you are using nginx as proxy in front of various applications it should be possible to filter these applications to get separated statistics.




[![Bitdeli Badge](https://d2weczhvl823v0.cloudfront.net/hpoul/zabbix-nginx-stats/trend.png)](https://bitdeli.com/free "Bitdeli Badge")

