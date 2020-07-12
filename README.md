# jog [![Build Status](https://travis-ci.org/qiangyt/jog.svg?branch=master)](https://travis-ci.org/qiangyt/jog)
Replace 'tail -f <log file> && grep <keyword>'

## Features
The purpose is to help engineers to view local log files more conveniently, it is not alternative of either ELK or Filebeat & Fluentd & Logstash:

   - Filtering by time range and log level.
   - Searching log by arbitrary fields
   - Convert and view JSON log files as flat lines, since JSON log is not friendly for human to read
   - Colorizing the log files, for example, bold and red highlighted error

It has both terminal mode and Web GUI mode as well. The Web GUI is from a embedded simple web server running locally. Both modes supports reading input stream from files and stdin, like 'tail -f ...'.

The filter expression is C-style, almost no learning cost for developers

It's written using Go, compiled as single file binaries without extra dependencies;And it is cross-platform (Windows, Mac OS X, Linux). Download them from releases.

The roadmap includes a Visual Studio Code extension as well so that we could one-click open log by JOG in Visual Studio Code.

## Background

Not all logs are available in ELK, I started this project when needs a simple tool during local development to view JSON log, AKA. structured log. I really like JSON log since it is great for log collectors, but it is not convienent for developer to read. Then I got 2 approaches to do that:

1. Some logging library, such as Bunyan, provides built-in tool to convert and view JSON log. However, only for Bunyan log format, and only for Node.js applications that uses Bunyan.

2. Sets up 2 different log appenders, one outputing JSON log to ELK, another outputting regular flat lines in console. That works fine with extra effort and complexity. And another drawback is, a JSON log appender usually automatically outputs all fields, but a reuglar flag line log appender by default not. I have to manually keep them synced up for new fields.

This tool helps to on-the-fly convert those structured JSON log to traditional space-separated flat line log, friendly for developers. It then removes the effort to maintenain different output format for different environments (for ex. JSON log for test / production, but flat line log for local development).

## Features

   Feature request is welcomed, for ex. new JSON log format. Submit issue for that please.

   - [x] Detect format, automatically

   - [x] Most-likely know unknow format via customizeable dictionary

   - [ ] Built-in supports as many as possible formats:

      - [x] Logstash
      - [x] Uber zap
      - [x] Bunyan (https://github.com/trentm/node-bunyan)
      - [x] Customizable format. Run `jog -t` to see configuration example.

   - [x] Support Mac OSX, Windows, Linux

   - [x] Supports customized fields

   - [x] Supports nested JSON fields

   - [x] Customizable output pattern

   - [x] Customizable output colorization

   - [x] Hightlight startup line

   - [x]  Support JSON log mixed with non-JSON log line (for ex., springboot banner) - just directly print them

   - [ ] Able to directly read many sources:
      - [x] stdin & stream
      - [x] local file
      - [ ] remote file (HTTP/HTTPs/FTP/SFTP)
      - [ ] k8s log
      - [x] docker-compose log
      - [x] docker log
      - [ ] aggregate multiple log

   - [x]  Friendly to multi-containers log outputted by docker-compose

   - [x]  Compressed logger name - only first letters of package names are outputed

   - [ ]  Filtering by level and field

## Usage:
  Download the executable binary to $PATH. For ex.

  ```shell
     curl -L https://github.com/qiangyt/jog/releases/download/v0.9.2/jog.darwin -o /usr/local/bin/jog
     chmod +x /usr/local/bin/jog
  ```

   * View a local JSON log file: `jog sample.log`

   * From stdin: `cat sample.log | ./jog`

   * From stdin steam: `tail -f sample.log | ./jog`

   * Check full usage: `jog -h`

     ```
      Convert and view structured (JSON) log
      v0.9.0

      Usage:
        jog  [option...]  <your JSON log file path>
           or
        cat  <your JSON file path>  |  jog  [option...]

      Examples:
        1) view a json log:                                               jog app-20200701-1.log
        2) view a json log with specified config file:                    jog -c another.jog.yml app-20200701-1.log
        3) view docker-compose log:                                       docker-compose logs | jog
        4) print the default template:                                    jog -t
        5) view the json log with WARN level foreground color set to RED: jog -cs fields.level.enums.WARN.color=FgRed app-20200701-1.log
        6) view the WARN level config item:                               jog -cg fields.level.enums.WARN

      Options:
        -c,  --config <config file path>                            Specify config YAML file path. The default is .jog.yaml or $HOME/.job.yaml
        -cs, --config-set <config item path>=<config item value>    Set value to specified config item
        -cg, --config-get <config item path>                        Get value to specified config item
        -t,  --template                                             Print a config YAML file template
        -h,  --help                                                 Display this information
        -V,  --version                                              Display app version information
        -d,  --debug                                                Print more error detail
     ```

## Build

   *  Install GOLANG

   *  In current directory, run `./build.sh`

## License

[MIT](/LICENSE)
