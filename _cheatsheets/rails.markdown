---
layout: page
title: Rails
---
# Rails Cheatsheet

Start With: 
 	
```ruby
rails new <your_app> -m https://raw.github.com/RailsApps/rails-composer/master/composer.rb
```

## Stack
* rspec
* devise
* cancan
* [slim](http://slim-lang.com)
### Additional Gems
* [shoulda-matchers](https://github.com/thoughtbot/shoulda-matchers)


##Snippets

#### Form Helper

```ruby
    module FormHelper
    
      def bootstrap_form_for(name, *args, &block)
        options = args.extract_options!
        form_for(name,*(args << options.merge(:builder => BootstrapFormBuilder)),&block)
    
      end
    
      class BootstrapFormBuilder < ActionView::Helpers::FormBuilder
        def text_field(attribute, options={})
          options[:class] = 'form-control'
          super(attribute,options)
    
        end
    
        def form_group(attribute, options={})
        html = <<HTML
    <div class="form-group">
      #{label(attribute)}
      #{text_field(attribute)}
    </div>
    HTML
          html
        end
      end
    end
```



