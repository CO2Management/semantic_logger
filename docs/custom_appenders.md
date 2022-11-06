---
layout: default
---

## Custom Appender

To write your own log appender it should meet the following requirements:

* Inherit from `SemanticLogger::Subscriber`
* In the initializer connect to the resource being logged to
* Implement #log(log) which needs to write to the relevant resource
* Implement #flush if the resource can be flushed
* Write a test for the new appender

The #log method takes the `Log Struct` as a parameter.
For the format of the `Log Struct`, see [Log Struct](log_struct.html)

Basic outline for an Appender:

~~~ruby
require "semantic_logger"

class SimpleAppender < SemanticLogger::Subscriber
  attr_reader :host
  
  # Add additional arguments to the initializer while supporting all existing ones.
  def initialize(host: host, **args, &block)
    @host = host
    super(**args, &block)
  end

  # Display the log struct and the text formatted output
  def log(log)
    # Display the raw log structure
    p log

    # Display the formatted output
    puts formatter.call(log)
  end

  # Optional
  def flush
    puts "Flush :)"
  end

  # Optional
  def close
    puts "Closing :)"
  end
end
~~~

Sample program calling the above appender:

~~~ruby
SemanticLogger.default_level = :trace
# Log to file dev.log
SemanticLogger.add_appender(file_name: "dev.log")
# Also log the above sample appender
SemanticLogger.add_appender(appender: SimpleAppender.new)

logger = SemanticLogger["Hello"]
logger.info "Hello World"
~~~

Look at the [existing appenders](https://github.com/reidmorrison/semantic_logger/tree/master/lib/semantic_logger/appender) for good examples

### Tests

To have your custom appender included in the standard list of appenders, submit it along
with complete working tests.
See the [Graylog Appender Test](https://github.com/reidmorrison/semantic_logger/blob/master/test/appender/graylog_test.rb) for an example.

## Design

This section introduces the internal design of Semantic Logger, which will be helpful for anyone
that wants to contribute changes for others in the community to take advantage of.

### Log message flow diagram

Shows how log messages events are emitted from the various log instances, placed in the in-memory queue,
and then written to one or more appenders on a separate thread.

![Log message flow diagram](images/log_event_flow.png "Flow Diagram")

### Class Diagram

![Class diagram](images/class_diagram.png "Class Diagram")

### [Next: Log Event ==>](log_struct.html)
