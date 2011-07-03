Thor
====

Description
-----------
Thor is a simple and efficient tool for building self-documenting command line
utilities.  It removes the pain of parsing command line options, writing
"USAGE:" banners, and can also be used as an alternative to the [Rake][rake]
build tool.  The syntax is Rake-like, so it should be familiar to most Rake
users.

[rake]: https://github.com/jimweirich/rake

Installation
------------
    gem install thor

Usage and documentation
-----------------------
Please see [the wiki](https://github.com/wycats/thor/wiki) for basic usage and other documentation on using Thor.

$ gem install wycats-thor -s http://gems.github.com

## Thor::Wrapper Usage

This version of Thor adds a Thor::Wrapper class, not (currently) included in the main branch
of the Thor gem:

Thor can also "wrap" the function of other commands, using the Thor::Wrapper class. Wapper 
classes extend the tasks available through other commands. That is, the wrapper class can 
define its own tasks, which are added to (or override) those provided by the wrapped ("parent")
command. So, for instance:

	class Wrapping < Thor::Wrapper
	  wraps "textmate"
		
	  desc "bar", "Do cool stuff"
	  def bar
		puts "plugh"
	  end
	
	  desc "update", "Hijack the update command"
	  def update
	    puts "Oh no, you didn't"
	  end
	end

This wraps the existing <tt>textmate</tt> command, defining <tt>bar</tt> and <tt>update</tt> 
tasks. These will override the definition of any corresponding <tt>textmate</tt> tasks, if they
exist. Assuming that <tt>textmate</tt> is Yehuda Katz's textmate script 
(https://github.com/wycats/textmate), then the following behavior occurs:

    > wrapping help
	Tasks:
	  wrapping bar              # Do cool stuff
	  wrapping help [TASK]      # Describe available tasks or one specific task
	  wrapping install NAME     # Install a bundle. Source must be one of trunk, review, github, or per...
	  wrapping list [SEARCH]    # lists all the bundles installed locally
	  wrapping reload           # Reloads TextMate Bundles
	  wrapping search [SEARCH]  # Lists all the matching remote bundles
	  wrapping uninstall NAME   # uninstall a bundle
	  wrapping update           # Hijack the update command

	> wrapping help list
	Usage:
	  wrapping list [SEARCH]

	lists all the bundles installed locally	
	> wrapping help update
	Usage:
	  wrapping update

	Hijack the update command
	> wrapping update
	Oh no, you didn't
	> wrapping list
	
	User Bundles
	------------
	...
	>
	
Thor::Wrapper defines some convenience methods, including <tt>parent</tt>, <tt>parent_path</tt>, 
<tt>wrap</tt> and <tt>forward</tt>. <tt>parent</tt> returns the name of the parent command ("textmate" in 
the example above). <tt>parent_path</tt> returns the path of the parent command, as determined by the 
active load path. <tt>wrap</tt> and <tt>forward</tt> pass a command along to the parent command. In the
example above,<tt>forward("update")</tt> would run the <tt>textmate update</tt> command 
(that is, the original version, without the override defined in class Foo). The difference between 
<tt>wrap</tt> and <tt>forward</tt> lies in how the command is invoked -- <tt>wrap</tt> uses backticks, 
and returns the output of the forwarded command, while <tt>forward</tt> uses <tt>system()<tt>, 
and returns the return code of the command. These methods allow for Thor::Wrapper classes to wrap 
behavior around other commands, for example:

	desc "update", "Update with before and after hooks"
	def update
	  call_before_update_hook
	  forward("update")
	  call_after_update_hook
	end
	
## Further Reading

Thor offers many scripting possibilities beyond these examples.  Be sure to read
through the [documentation](http://rdoc.info/rdoc/wycats/thor/blob/f939a3e8a854616784cac1dcff04ef4f3ee5f7ff/Thor.html) and [specs](http://github.com/wycats/thor/tree/master/spec/) to get a better understanding of the options available. 

License
-------
Released under the MIT License.  See the [LICENSE][license] file for further details.

[license]: https://github.com/wycats/thor/blob/master/LICENSE.md
