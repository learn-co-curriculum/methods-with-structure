#  Methods With Structure

### The problem

We want our methods to tell a story.  It is not just in a codebase that we try to group like functionality together - for example, by applying model view controller - but also within a method.

If we do not do this, we have methods that go back and forth with what they are doing, and confuse the reader of the code.

Here is an example of some problematic code.  Not only is it long, but it also performs similar tasks in very different parts of the method.  Try to identify some pieces of where this occurs.

```ruby
   def say(message, options={})
      return "" if message.nil?
      command = "cowsay"
      if options[:strings] && options[:strings][:eyes]
        command << " -e '#{options[:strings][:eyes]}'"
      end

      messages = case message
                 when Array then message
                 else [message]
                 end
      results = []
      messages.each do |message|
        @io_class.popen(command, "w+") do |process|
          results << begin
                       process.write(message)
                       process.close_write
                       result = process.read
                     rescue Errno::EPIPE
                       message
                     end
        end
      end
      output = results.join("\n")    
      if options[:out]
        options[:out] << output
      end
      destination = case options[:out]
                    when nil  then "return value"
                    when File then options[:out].path
                    else options[:out].inspect
                    end
      @logger.info "Wrote to #{destination}"
      if $? && ![0,172].include?($?.exitstatus)
        raise ArgumentError, "Command exited with status #{$?.exitstatus.to_s}"
      end
      output
    end
```
Did you see cases where similar functionality is not grouped together?  One example is how the code handles unwanted inputs.  For example, you can see that the code handles unwanted inputs both in the first line of the code with `return "" if message.nil?` and towards the very end of the code with the `raise ArgumentError` line.

### The Solution

Avdi Grimm has identified four common and recommended components of methods.  Take a look at the resources below.  Identify the four components in a method, and then apply his recommendations to your own codebase.

* [Confident Ruby](https://www.youtube.com/watch?v=T8J0j2xJFgQ)
* [Confident Ruby Blog Post](http://nithinbekal.com/posts/confident-ruby/)

### Additional Resources

* [Confident Ruby Book](http://www.confidentruby.com/)
<p class='util--hide'>View <a href='https://learn.co/lessons/methods-with-structure'>Methods With Structure</a> on Learn.co and start learning to code for free.</p>
