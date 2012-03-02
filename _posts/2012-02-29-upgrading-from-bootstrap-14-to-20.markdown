---
layout: post
title: "Upgrading from Bootstrap 1.4.3 to 2.0.3"
category: experience
tags: [Twitter Bootstrap, Rails, Twitter, Bootstrap]
---
{% include JB/setup %}

So I finally decided to take the plunge and upgrade to [Twitter Bootstrap 2.0.3](http://twitter.github.com/bootstrap/) from the version I'd settled with since the start of the project, 1.4.3.

The process of updating within my Rails 3.1.3 app was rather simple thanks to [Twitter-Bootstrap-Rails](https://github.com/seyhunak/twitter-bootstrap-rails) gem having simply ran a bundle update.

Having updated the gem to 2.0.3, I was required to reinstall the gem setup:

    bundle update twitter-bootstrap-rails
    rails g bootstrap:install

Then replace the following lines:

    # app/assets/stylesheets/application.css
    - *= require twitter/bootstrap
    + *= require bootstrap_and_overrides

And replaced my changes from bootstrap.css.less to bootstrap_and_overrides.css.less

Next I updated several helpers I had that generate appropriate buttons and stylising which needed to be updated to account for the updated css styles, some of which are listed [here.](http://twitter.github.com/bootstrap/upgrading.html)

I had also used a helper for displaying bootstrap-styled flash messages which needed to be updated:

{% highlight ruby %}
    # app/helpers/application_helper.rb
    def twitterized_type(type)
      case type
        when :alert
          "alert-warning"
        when :error
          "alert-error"
        when :notice
          "alert-notice"
        else
          "alert-info"
      end
    end
{% endhighlight %}

{% highlight haml %}
    # app/views/shared/_flash_messages.html.haml
    - flash.each do |type, message|
      %div.alert.fade.in{"data-alert" => "alert", :class => twitterized_type(type)}
        %a.close{:href => "#", :"data-dismiss" => "alert"} ×
        = message
{% endhighlight %}

Also with 2.x, Dropdowns do not contain carets by default, therefore I wrote another tiny helper to append carrets to dropdown links:

{% highlight ruby %}
    def append_caret(string)
      raw(string+" <span class='caret'></span>")
    end
{% endhighlight %}

Finally, each table required the new "table" class, and the navigation bar received a major overhaul (specifically dropdown and dropdown-menu).

{% highlight haml %}
    %li.dropdown{:"data-dropdown" => "dropdown"}
      = link_to 'Clients', '#', :class => :"dropdown-toggle"
{% endhighlight %}

then becomes:

{% highlight haml %}
    %li.dropdown
      = link_to append_caret("Clients"), '#', :class => "dropdown-toggle", :data-toggle" => :dropdown
{% endhighlight %}
