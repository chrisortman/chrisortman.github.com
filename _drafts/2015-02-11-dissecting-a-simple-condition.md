---
layout: post
title: "Dissecting a simple condition"
tags: decisions
comments: true
 
---

As programmers we make countless decisions each day, some so small we probably don't even realize we made a choice. But the choices we make when writing our code will affect not only ourselves but everyone who comes to maintain the codebase after we're gone. Even decisions as simple as what to test in an if statement.

Suppose we have some ruby code like this that wants to print error messages for an ActiveRecord model accessor

```ruby

class Post < ActiveRecord::Base
  #title: string
  #published: bool
  has_many :comments
end

def Comment < ActiveRecord::Base
  #text: string
  belongs_to :post

  validates_presence_of :text, :post

end

def print_errors(method,model)
  assoc,fk_method = association_to_foreign_key(method,model)

  errors = []

  errors += model.errors.get(method)
  errors += (model.errors.get(fk_method) || [])

  errors
end

def association_to_foreign_key(method,model)
  assoc = model.reflect_on_association(method)
  if assoc != nil && assoc.macro == :belongs_to
    [assoc, assoc.foreign_key]
  else
    [assoc, method]
  end
end
```


I know, I know, there's some problems. But this is straight from the trenches, so bear with me. 

The problem is that if I pass :text to my function, I will get any validation messages twice because `association_to_foreign_key` just returns method if there is no association.

Also, association_to_foreign_key is a _common_ method and called in many places, so you don't want to change that if you don't have to.

So how do you fix this.

One possible solution would be

```ruby
  errors << model.errors.get(fk_method) if method != fk_method
```

Another would be

```ruby
  errors << model.errors.get(fk_method) if assoc.present?
```

But which one do you choose and why?
