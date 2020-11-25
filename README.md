# Ruby-On-Rails-basic-concepts
### 1. What are all naming conventions I need to be aware of?

db table is plural, model is singular, controller is plural. so you have the ```User``` model that is backed by the ```users``` table, and visible through the ```UsersController```.

files should be named as the wide_cased version of the class name. so the ```FooBar``` class needs to be in a file called ```foo_bar.rb```. If you are namespacing with modules, the namespaces need to be represented by folders. so if we are talking about ```Foo::Bar``` class, it should be in ```foo/bar.rb```.

### 2. How should controller actions be structured and named?

Controller actions should be RESTful. That means that you should think of your controllers as exposing a resource, not as just enabling RPCs. Rails has a concept of member actions vs collection actions for resources. A member action is something that operates on a specific instance, for example ```/users/1/edit``` would be an edit member action for users. A collection action is something that operates on all the resources. So ```/users/search?name=foo would be a collection action```.

The tutorials above describe how to actually implement these ideas in your routes file.

### 3. What are the best ways to render information in a view (via ```:content_for``` or render a partial) and what are ways I shouldn't use?

```content_for``` should be used when you want to be able to append html from an inner template to an outer template -- for example, being able to append something from your view template into your layout template. A good example would be to add a page specific javascript.

```
# app/views/layout/application.rb
<html>
  <head>
    <%= yield :head %>
...

# app/views/foobars/index.html.erb

<% content_for :head do %>
  <script type='text/javascript'>
    alert('zomg content_for!');
  </script>
<% end %>
```
partials are either for breaking up large files, or for rendering the same bit of information multiple times. For example

```
<table>
  <%= render :partial => 'foo_row', :collection => @foobars %>
</table>

# _foo_row.html.erb

<tr>
 <td>
  <%= foobar.name %>
 </td>
</tr>
```

### 4.What should go into a helper and what shouldn't?

your templates should only have basic branching logic in them. If you need to do anything more intense, it should be in a helper. local variables in views are an abomination against all that is good and right in the world, so that is a great sign that you should make a helper.

Another reason is just pure code reuse. If you are doing the same thing with only slight variation over and over again, pull it into a helper (if it is the exact same thing, it should be in a partial).

### 5. What are common pitfalls or something I need to do correctly from the very beginning?

partials should never refer directly to instance (@) variables, since it will prevent re-use down the line. always pass data in via the ```:locals => { :var_name => value }``` param to the render function.

Keep logic out of your views that is not directly related to rendering your views. If you have the option to do something in the view, and do it somewhere else, 9 times out of 10 doing it somewhere else is the better option.

We have a mantra in rails that is "fat models, skinny controllers". One reason is that models are object oriented, controllers are inherantly procedural. Another is that models can cross controllers, but controllers cant cross models. A third is that models are more testable. Its just a good idea.

### 6. How can you modularize code? Is that what the lib folder is for?

the lib folder is for code that crosses the concerns of models (i.e. something that isn't a model, but will be used by multiple models). When you need to put something in there, you will know, because you wont be able to figure out what model to put it in. Until that happens, you can just ignore lib.

Something to keep in mind is that as of rails 3, lib is not on the autoload path, meaning that you need to ```require ``` anything you put in there (or add it back in)

A way to automatically require all modules in the ```lib``` directory:

```
#config/initializers/requires.rb
Dir[File.join(Rails.root, 'lib', '*.rb')].each do |f|
  require f
end
```
