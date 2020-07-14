# jog [![Build Status](https://travis-ci.org/qiangyt/jog.svg?branch=master)](https://travis-ci.org/qiangyt/jog)
Tool for viewing local log, replacing `tail -f <log file> && grep <keyword>`

## Features
The purpose is to help engineers to easily view local log files, not alternative of stuff like ELK:

   - Filtering by time range and log level
   - Full-text searching log by arbitrary fields
   - Convert and view JSON log as flat lines
   - Views ELK log as stream, jsut like `tail -f`
   - Colorizes the log files, for example, bold and red highlighted error
   - The configuration could be shareable by team to consistent reusable log view during development, via GIT

It has both terminal mode and Web GUI mode as well. The Web GUI is supported by an embedded web server running locally. Both modes supports reading input stream from files and stdin, like 'tail -f ...'.

Besides ELK, another ongoing work is to view the log as stream, like `tail -f`, but upon outputted log from Splunk and others log service from IaaS vendors.

The filter expression is SQL style, almost no learning cost for developers

One of target is to easy use in pratical, so it's written using Go, compiled as single file binaries without extra dependencies; And it is cross-platform (Windows, Mac OS X, Linux). The binaryies is downloadable them from releases.

The roadmap includes a Visual Studio Code extension as well so that we could one-click open log by JOG in Visual Studio Code.

See detailed feature tables [#Detailed Feature Table].

## Background

Such a tool like `tail -f <log file> && grep <keyword>` is still needed besides ELK, because:

1. ELK is not available everywhere all the time, for example, before log is connected to ELK, during initial development. It's overkilled for local development to output log to ELK, of course.

2. Streamed view of logs, by `tail -f`, is better than ELK in some cases.

Another important reason is that JSON log, AKA. structured log, becomes more and more popular, but JSON log is not convienent for developers to read. I know 2 approaches to do that:

1. Some logging library, such as Bunyan (https://github.com/trentm/node-bunyan), provides built-in tool to convert and view JSON log. However, it is only for Bunyan log format, that also means available only for Node.js applications only. In pratical, there're other issues for approach #1. For ex., I heavily use docker-compose for local development, however, `docker-compose log -f` inserts service name before each of log lines from containers, another example is PM2, a process management tool, which inserts extra process information before log lines too, all of such tools will break built-in tools like bunyan.

2. Sets up 2 different log appenders, one outputing JSON log to ELK, another outputting regular flat lines to console. That works fine but with extra effort and complexity. And another drawback is, a JSON log appender usually automatically outputs all fields, but a reuglar flag line log appender doesnot by default. I have to manually keep them synced up for new fields.

With a So,This tool can on-the-fly convert JSON log to traditional space-separated flat line log, friendly for developers. It then removes the effort to maintenain different output format for different environments (for ex. JSON log for test / production, but flat line log for local development).

## Design thinking

1. Feature should be flexible and customizable, but the default one should work well enough.

2. Detect to get default


## Detailed Feature list

   Anyway, although the feature list below is a little bit tedious and long but it is reserved to go through them quckly so that we could fully use Jog. It is a roadmap as well, with implementation status indicated.

   | Major Feature                                 | Minor Feature                                        | Detail |
   | :-------------------------------------------- | :--------------------------------------------------- | :----- |
   | [] 1. Filtering &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |        |
   |                                               | [] 1.1 Filtering by arbitrary time range             |        |
   |                                               | [] 1.2 Built-in frequently-used time range filtering | For ex., before 10 minutes |
   |                                               | [] 1.3 Filtering by log level                        |        |
   |                                               | [] 1.4 Filtering by log name                         |        |
   |                                               | [] 1.5 Filtering by arbitrary fields                 |        |
   |                                               | [] 1.6 SQL style filter expression                   |        |
   | [] 2. Full-text searching                     |                                                      |        |
   |                                               | [] 2.1 Available for arbitrary fields                |        |
   |                                               | [] 2.2 Follows ELK query languge                     |        |
   | [] 3. Input from both flat line and JSON line |                                                      |        |
   |                                               | [] 3.1 Input from local file                         |        |
   |                                               | [] 3.2 Input from stdin stream                       |        |
   |                                               | [] 3.3 Input from docker native log                  |        |
   |                                               | [] 3.4 Follow mode which is like `tail -f`           | turn on by default |
   |                                               | [] 3.5 Able to stop on designated lines              | Designates by either filter expression or full-text search. Helpful to capture particular log, like a debug breakpoint. Resumeable. |
   |                                               | [] 3.6 Detect known fields automatically             | For ex., timestamp, leve, log name, PID, thread, message, stack trace, and so on. Log level value are quite similar too: INFO, WARN, ERROR, .... For exceptional cases, for example, Bunyan takes a number value 50 as ERROR level, this is doable by customizing the configuration profile. For Bunyan, that is built-in already. For others, we prefer to get them built-in too for the purpose of easy usage, could you help to submit a PR or an issue? |
   |                                               | [] 3.7 Support various of field value types          | Number, String, Timestamp, Enum. Particularily, Enum is useful for presenting log level values. |
   |                                               | [] 3.8 Detect datetime format                        | Datetime format usually follows standards. Jog supports ELK GROK to parse the datetime format. Datetime format usually follows ISO standards, so Jog provides built-in ISO datetime GROK and tries to extract them automatically. And actually, if don't need time-range filtering, we don't need datetime extraction mostlikely, just present datetime fields as plain text. |
   |                                               | [] 3.9 Support customized fields                     | All fields that doesn't match standard field keywords are taken as customized fields. |
   |                                               | [] 3.10 Detect startup line                          | Detects a line that indicates the startup is finished, then it is possible to highlight that line output to help developers to quickly locate it. Doable by regex matching.  |
   |                                               | [] 3.11 Convert and normalize regular flat log       | Useful for sequential processing (highlight, filtering, and so on) |
   |                                               | [] 3.12 For regular flat log, extract fields following Logstash compatible way (GROK) |
   |                                               | [] 3.13 For GROK field extraction, provides built-in ELK compatible GROK patterns as much as possible |
   |                                               | [] 3.14 Convert and normalize JSON log line          |        |
   |                                               | [] 3.15 Detect various of JSON format                | There're various of JSON log format, major differences are just different keywords and level value and so on. Their keywords are quite similar and nearly able to be listed exhaustively via a case-insensitive dictionary. The dictionary is customizable. By detect those keywords, the default built-in conversion will work for most cases, without need to customization. And by comparing of matching keywords, we could tell if or not the input matches which known format. The target is to built-in support as many as possible known formats. |
   |                                               | [] 3.16 Support JSON lines mixed with regular lines  | Try best to convert the lines as JSON. If failed, take the line as non-JSON line, straightforward. For ex., for springboot banner lines. |
   |                                               | [] 3.17 Support JSON having non-JSON prefix          | For example, `docker-compose log -f` inserts service name before each of log lines from containers, then follows by JSON lines. Such prefix is extracted and remaining JSON parts could be still recognized. |
   |                                               | [] 3.18 Support nested JSON fields                   |        |
   | [] 4. Customizable output                     |                                                      |        |
   |                                               | [] 4.1 Output as regular flat log lines, with customizable output pattern | Even for regular flat log line input, it is still useful when need to get a slice of logs filtering by time range, for example. |
   |                                               | [] 4.2 Output as JSON log line                       | Also useful when need to get a slice of logs filtering by time range, for example. |
   |                                               | [] 4.3 Colorization                                  | For example, highlight log level with different color. Have built-in 16 RGB color, with extra style (bold, italic, background, ...) |
   |                                               | [] 4.4 Compress logger name                          | JAVA application usually takes a long and tedious fully-qualified class name as logger name. Many JAVA logging framework themselves supports compress already by removing non-first letter of package names, but it's recommended to keep them uncompressed and use Jog to do the compression here, so that when we found compressed logger names are not easy to read, we could dynamically turn off the compress to get back original full-qualified name. Jog also supports to turn on compress only for specific packages / prefix, so that we could have not-known 3rd-party package name uncompressed, and compress for our own code. That is more flexible than doing that in logger framework. In addition, Jog could allow us to totally hide package names to shorten the output. |
   |                                               | [] 4.5 Output log line number                        | Useful to reference
   |                                               | [] 4.6 Output specified fields in separated line     | Useful for stacktrace and customized fields |
   | [] 5. Output to either console or web browser |                                                      |         |
   |                                               | [] 5.1 Non-interactive console output mode           | all features, such as filtering and full-text search, should be configured via configuration file or command line arguments |
   |                                               | [] 5.2 Interactive console output mode               | configuration could be changed via a the interactive console UI |
   |                                               | [] 5.3 Output to web browser                         | like interactive console mode but in browser. Actually, Jog has an embeded tiny web server fo this purpose. |
   |  [] 6. External configuration                 |                                                      |         |
   |                                               | [] 6.1 YAML configuration file                       | Jog only supports YAML as configuration file format. Jog looks up them in locations: `$PWD/.jog`, `$HOME/.jog`,  `etc/.jog`. Jog configuration files in `$PWD/.jog` could be commited to project git repository to trace and share in the team. |
   |                                               | [] 6.2 Built-in default configuration file           | The built-in default configuration file is intellegent enough so most-likely works enough good. Run `jog -t` to see configuration example, and `jog -t full` to see full configuration items. |
   |                                               | [] 6.3 Support multiple profile                      | Each YAML configuration file specifies one profile which is the combination of output pattern & field extraction & field definition ..., and the file name is the profile name. Profile could be referred by name. |
   |  [] 7. Remove PII fields automatically        |                                                      | For example, password. Configurable via a #000000 list.
   |  [] 8. Telemetry                              |                                                      | Collect usage statistics to improve the detecting and get a better default configuration. Anonymous and turned on by default but of course allow to turn off |

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
