# The Angel Network Monitor
© Mar/1998 by Marco Paganini (paganini@paganini.net)

## Introduction

Angel is a simple yet useful tool to monitor the services on your network.
Technically speaking, it's a Perl program that runs every 'n' minutes (usually
fired from your cron) and calls different perl subprograms (referred as
"plugins" from now on) to do the actual testing. It will then generate an HTML
table containing the status of your network.

## Main features

* Administration is centralized. Adding hosts and/or services involves editing
  only one file, usually.

* The program uses the 'plug-in' concept, meaning it's easy to customize and enhance.

* HTML output. You can monitor your hosts/network from anywhere

* Support for the excellent LEDSign Java applet.

* Javascript support. The browser will popup a child window with alerts, if you
  choose this option

* SSH Support.

* It's Free :)

The inspiration for this program (and for the name) came from the Excellent
"Big Brother" package. Big Brother is "watching you", just like your "Guardian
Angel". :)

## Program Basics

The core of the program is the angel perl script. It's meant to be run from
your cron every "N" minutes. Once run, the program will read the
conf/hosts.conf and conf/angel.conf files, test the specified conditions and
generate the HTML output containing the status of your hosts, detailed error
messages (returned from your plug-ins, etc).

## Installation Procedures

### Finding a place for the files

The first step (as always) is to find a suitable place for Angel. It's mostly a
matter of personal taste, and Angel will make its best to find its "home"
directory and proceed accordingly. I like putting custom things under the
`/usr/local directory`. If you do not object to that, you could do:

```
mkdir /usr/local/angel
cd /usr/local/angel
tar zxvf angel.tar.gz
```

This assumes your file is called `angel.tar.gz`.

### Configuring your web server

Angel generates HTML code but you need a web-server to see the results. If you
don't have a Web Server on the computer Angel is running, you won't be able to
see the output from the outside world (from other computers, I mean). Of
course, if your workstation runs Linux, you won't need an HTTP Server (Just
fire your browser and point to the place where Angel writes its files).

However, it would be healthy to have a running server, since it allows you to
watch your servers from anywhere. For now, I suppose you're running Apache.

What you have to do is configure your Apache server so you can "see" the files
generated by Angel more easily:

* Edit your srm.conf file.
* Add the line: `Alias /angel /usr/local/angel/html` to this file.
* Restart your webserver.
* From now on, you should be able to see the Angel output pointing to:
  `http://yourwebserver/angel/`

### Configuring The Program

Before you run the program, it would be a good idea to take a quick look at the
sample `conf/angel.conf` file. Some default variables can be changed to modify
the behaviour of the program. The most important ones are:

```
$main::Index_html_header
$main::Index_html_footer
```

These variables control the main HTML file generation. `Index_html_header` is sent to the html file before anything else. `Index_html_footer` is the last thing sent to the file.

```
$main::Index_column_width
```

Angel uses the HTML TABLE construct to format its output. This variable controls the default table width (in pixels) for each element. If you change this value, please remember that your red, yellow, green and black gifs should fit inside the table cell! If you have a large number of services, it could be a good idea to reduce this value. Please remember that the total width of your table is `(Number of services + 1) * Index_column_width`. Currently, this value is also used for the host part of the table (yes, it will be changed soon). Setting this variable to "-1" will cause Angel not to generate width info at all in the html output.

```
$main::Index_html_border
```

How thick (in pixels) should be the table borders. Some people love table
borders, others do not. The default is 0, or, no border.

```
$main::Error_host_width
$main::Error_label_width
$main::Error_column_width
```

Angel generates an html file containing all the errors found on the last cycle. The table columns contain:
1. The host name
1. The label (service) where the error was found and
1. The error description. These variables control the width of each of these columns.

```
$main::Use_ledsign
$main::Ledsign_html_header
```

If you set `$main::Use_ledsign`, Angel will include support for the excellent
Ledsign Java Applet from Darrick Brown. `$main::Ledsign_html_header` contains
the applet call. Do not change this unless you know exactly what you're doing.
Please, consult the included Ledsign package for more details.

```
$main::Remote_cmd
```

This tells the plugins which program should be used to do remote execution.
Linux normally uses rsh. HPUX uses remsh and SCO rcmd. It's a good idea to
enable SSH on your site and use SSH, since the "r" commands are insecure by
nature.

### Editing Your Configuration File

Everything Angel does is based on the contents of the conf/hosts.conf file.
Following the good Unix tradition, this is a pure ASCII file and should be
edited using your preferred editor. Every host and service you want to monitor
must be listed in this file.

For each key pair host/service, there must be one line in the configuration
file. If you want to check, say SMTP and HTTP from the same host, you must have
two different lines in the configuration file.

The basic format of this file is:

```
hostname:plugin:plugin_options:label:angel_options
```

Where:

#### hostname

Name of the host to be checked. This host must be locatable from the computer where angel is running.

#### plugin
The plugin name. Each plugin does a different thing and require different
`plugin_options` (see next definition). Currently (v.0.5) the only defined
plugins are `Check_tcp`, `Check_disk`, and `Check_load`.

#### plugin_options

These options are passed directly to the plugin. You must check the plugin
documentation, since each plugin requires specific options.

#### label

Angel always creates a table containing hostnames in the rows and labels in the
columns. But what is a "label"? Usually a label is a service. That's it. I
called it "label" just because it can be any string you wish. Internally, the
program will group using the pair hostname/label. For instance, you may choose
to label your http column as "Web" to make it easier for non-computer people to
understand the program output. Which service is effectively checked depends on
what you pass to the plugin using the `plugin_options` field.

#### angel_options

These options control some aspects of the core Angel script. They are not
related to the plugins and apply equally to all plugins. Currently, the valid
options are "alertyellow","alertred","alertblack". These options will cause
Angel to generate javascript code which in turn will open an auxiliary window
in your browser, warning you when a yellow, red or black condition happen,
respectively. Options are separated with a "!" (bang) sign.

It's a good idea to check the hosts.conf.example file that comes with this
package to make sure you understand the syntax. Also, check the Plugin specific
documentation below to customize Angel to your needs.

## Plugin Specific Documentation

This section discusses the basic options for each plugin. This is where you
should expect to find the `plugin_options` for each plugin.

* [Check_tcp](docs/check_tcp.md) - Checks for a TCP connection on a given port.

* [Check_disk](docs/check_disk.md) - Checks for low diskspace conditions.

* [Check_load](docs/check_load.md) - Checks for high system load conditions.

* [Check_ping](docs/check_ping.md) - Checks roundtrip times.

## Writing your own plugins

Please take a look at the existing plugins to understand how things work. Mail
me if you have any questions about how the plugins interact with the main
program.

After your plugin is complete, please send me a copy so I can include it on the
next Angel release (with the due credits, of course).

If you feel like it (it would be veeeery cool), also change the documentation
and add the particulars of the newly created plugin.

If you complete any of the previous steps, my sincere thanks. :)

## Things to do

As you know, a software project, no matter how small, generates a lot of work.
In fact, only this documentation took almost the same time as the code. Count
the debugging sessions (and the fact that I'm doing this in my spare time) and
you'll understand why bugs are very likely to exist.

If you feel like helping, know that I would love to receive emails with
comments, corrections and ideas for Angel. You can also send me PRs on github
or open issues.

Here's a (partial) list of things I know should be changed or improved:

My special thanks and credits to:

* Norbert Gruener (nog@MPA-Garching.MPG.DE) - for his ideas, improvements and
  bug corrections. Thanks to Norbert, you're going to see a whole different
  version of Angel soon.

* Philippe Charnier (charnier@xp11.frmug.org) for many patches to the
  `Check_disk` plugin.

* Adam Soltan (soltan@speech.kth.se) for patches to the `Check_disk` plugin.

* Michael Schilli (schilli@tep.e-technik.tu-muenchen.de) - for the great
  `Proc::Simple` perl module.

* MacLawran Group for "Big Brother". Big Brother gave me the idea for Angel
  (and for the "Angel" name too. :))

* Graham Barr for `IO::Socket`. It's much better than the regular socket module.
