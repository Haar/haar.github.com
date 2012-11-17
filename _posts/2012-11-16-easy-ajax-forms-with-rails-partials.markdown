---
layout: post
title: "Easy AJAX Forms with Rails Partials"
category: experience
tags: [Rails, Partials, Views, AJAX, remote]
---
{% include JB/setup %}

So I finally got around to making another blog post; here we go.

A requirement of a client project (somewhat) recently, was asynchronous form submissions. Here's how I went about implementing them in a (relatively) painless way:

Firstly extracting the form object to a partial like so, for example the edit form:

{% highlight haml %}
/* apps/views/models/_#{action}_form.html.haml */
= simple_form_for(@model, html: { class: 'form-horizontal', multipart: true }, remote: true) do |f|
  .modal-body
    = f.error_notification
    .inputs
      = f.input :foo
      = f.input :bar
{% endhighlight %}

With the corresponding controller actions being a straight forward:

{% highlight ruby %}
# app/controllers/models_controller.rb
def edit
  @model = Model.find(params[:id])
end

def update
   @model = Model.find(params[:id])
   if @model.update_attributes(params[:model])
     respond_to do |format|
       format.html { redirect_to model_path(@model) }
       format.js { render "success" }
     end
   else
     respond_to do |format|
       format.html { render :edit }
       format.js { render "shared/reload" }
     end
   end
end
{% endhighlight %}

And the javascript views to tie it all together. This consists of three parts, first of all a shared success JS view that toggles and removes the modal that contains the form, as well as updating the flash message on the screen:

{% highlight erb %}
<%-# app/views/shared/_success.js.erb -%>
$("#<%= success_remote_object_name %>").modal('toggle');
$("#<%= success_remote_object_name %>").remove();
$("#notification").html("<%= escape_javascript(render partial: 'shared/flash_messages', locals: { flash: flash }) %>");
{% endhighlight %}

This functionality alone isn't usually enough to represent the actions intended, so you extend this with a javascript for the current model:

{% highlight erb %}
<%-# app/views/model/success.js.erb -%>
<%= render partial: "shared/success", locals: { flash: flash } %>
// Custom JS goes here
$("#model_details").html("<%= escape_javascript(render partial: "models/model_details", locals: { model: @model }) %>");
$("#model_title").html("<%= escape_javascript(render partial: "models/model_title", locals: { model: @model }) %>");
{% endhighlight %}

Finally, a shared reload action JS form that dynamically reloads the appropriate form.

{% highlight erb%}
<%-# app/views/shared/reload.js.erb -%>
$("#<%= escape_javascript(reload_remote_object_name) %>").html("<%= escape_javascript(render partial: "#{reload_action}_form") %>");
{% endhighlight %}

The appropriate form is loaded dynamically using the following helper methods. They depend on the controller and models matching up in the standard rails way, any further customisation would use a custom reload JS file that targets the specific form:

{% highlight ruby %}
# apps/helpers/application_helper.rb
ACTIONS = {"create" => "new", "update" => "edit"}

def success_remote_object_name
  controller.controller_name.singularize+"-"+ACTIONS[controller.action_name]
end

def reload_remote_object_name
  target = ACTIONS[controller.action_name]+"_"+controller.controller_name.singularize
  target += "_"+request.fullpath[-1] if params["action"] != "create"
  return target
end

def reload_action
  ACTIONS[controller.action_name]
end
{% endhighlight%}

For the majority of cases which follow the standard way, this works great.
