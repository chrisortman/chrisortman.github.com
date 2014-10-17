---
published: true
layout: post
date: "2014-10-17 10:43"
comments: true
categories: null
---

How to track down which version of libxml your nokogiri is using
================================================================

We ran into a strange issue this week. We had some capybara tests that worked on some people's machines but not others. The tests were simple enough along the lines of

    expect(xhtml).to have_xpath("//span[@class='some-class'][1]",:text => "1.1")
    expect(xhtml).to have_xpath("//span[@class='some-class'][2]",:text => "1.2")
    
The first expect passed, the second failed (but should have passed). On machines where it didn't work the supplied xpath
expression actually returned no nodes.

We noticed when running the tests that *nokogiri* would issue a warning on the failing machines:

    WARNING: Nokogiri was built against LibXML version 2.7.8, but has dynamically loaded 2.8.0
    
So we rebuilt the gem

    gem uninstall --executables --ignore-dependencies nokogiri
    gem install nokogiri -v 1.5.6
    
This fixed the warning, but the problem remained.

Because some of nokogiri is native it has to compile some C code when you install the gem. If you want to see how this is done you can look at the Makefile it uses

    cat $GEM_HOME/gems/nokogiri*/ext/nokogiri/Makefile | view -
    
What you're looking for here is a libpath line. Mine looks like this

    libpath = . $(libdir) /usr/local/Cellar/libxml2/2.9.1/lib /opt/local/lib /usr/local/lib /Users/cortman/.rvm/rubies/ruby-1.8.7-p374-cheerful0/lib /usr/lib
    
This means the compiler is going to search these paths in order when it is looking for libraries. Which libraries is it looking for? Look at the **LIBS** line

    LIBS = $(LIRUBYARG_SHARED) -lexslt -lxslt -lxml2 ....
    
My guess is that somewhere within either make or gcc is knowledge to expand these things into file names such that *-lxml2* becomes *libxml2.dylib*

So we can write a short ruby script

    libpaths = %w(
        /Users/cortman/.rvm/rubies/ruby-1.9.3-p547/lib
        /usr/local/Cellar/libxml2/2.9.1/lib
        /opt/local/lib
        /usr/local/lib
        /usr/lib
    )
    libpaths.map{|p| p + "/libxml2.dylib"}.each do |path|
    	puts "Found #{path}" if File.exists?(path)
    end
    
And run it to see which libxml2 is found first.

The key to this puzzle is in these libpaths. You can see on my machine it is searching my local ruby build, homebrew, macports, and system paths (in that order) but on the machines where it wasn't working it was searching local ruby, macports, system *and* they had macports installed

So something in the macports version of libxml2 is broken which ripples all the way up to our capybara tests. 

I'm not sure if the macports library can just be upgraded and everything will be fine. Along the way we figured out that if we enclosed the xpath with parens it would do the right thing so we opted to do that in order to avoid having to patch build servers with a new library.