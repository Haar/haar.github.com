---
layout: post
title: "Under the magic: a debugging challenge"
category: experience
tags: [Rails, Debugging, Associations, Scope, ActiveRecord]
---
{% include JB/setup %}

###Tricky Business 1: ActiveRecord Association Order

I found myself debugging a frustrating piece of code recently. The main problem in debugging it was that the cause of the problem lay not in a single misbehaving section of code, but in a series of processes by which the code interacted.

First problem that occurred was due to the order of declarations within a Model; did you know that:

{%  highlight ruby %}
  Class Model < ActiveRecord::Base
    has_many :associations
    has_many :scoped_associations

    before_destroy :validate_property

    def validate_property
      return false if scoped_associations.property?
      associations.each do |association|
        return false if association.property?
      end
    end
  end
{% endhighlight %}

Does not function in the same way as:

{%  highlight ruby %}
  Class Model < ActiveRecord::Base
    before_destroy :validate_property

    has_many :associations
    has_many :scoped_associations

    def validate_property
      return false if scoped_associations.property?
      associations.each do |association|
        return false if association.property?
      end
    end
  end
{% endhighlight %}

In the prior code example, the before_destroy function on the models associations is called before the before_destroy function of the object itself, therefore by the time the before_destroy function on the model is called, it has no associations and therefore proceeds regardless.

###Tricky Business 2: Default Scope

The main reason for why this caused a problem with debugging was down to a separate bit of code in another associations model:

{% highlight ruby %}
  Class ScopedAssociation < ActiveRecord::Base
    default_scope :order => "alternate_objects.name ASC, description ASC",
                  :joins => :alternate_object
  end
{% endhighlight %}

This meant that when attempting to unit test the functionality of the before_destroy validation, if it had not been assigned an alternate_object (which was not validated for its presence), then the ScopedAssociation.all/find/method would return no objects where the alternate_object was nil.

This meant that in order to "unit test" the functionality of validate_property, I firstly need to create an alternate_object, an scoped_assocation with its alternate_object_attribute set to the alternate_object, then finally the original object you wished to test with said scoped_assocation set as its association. None of these association requirements were defined directly by the functionality of the code, instead being a series of chained events which caused its occurence.

This experience further taught me the benefits of Test First/Test Driven development, whereby any such complex chaining is discovered, and perhaps a cleaner design solution produced as a result.
