---
layout: post
title: Ajaxify your Forms?
---

In this post we gonna see how to add ajax to our forms in symfony which will help us create ajaxified web pages in seconds so focus :p

  >first think we gonna create a javascript function which we will use to get the response of a specific route this function get two parameters, the first is the route path the other one is the id of div where you one put the response of the route .

  {% highlight js %}


  function routeBringer(route,id_of_div) {
    $('#'+id_of_div).html("Chargement ...");
    $.ajax({
      type: "POST",
      url: route,
      data: "",
      cache: false,
      success: function(html){ $('#'+idul).html(html); }});
}


  {% endhighlight %}

after that you added this methode we gonna use it in button for example  button that create a new entry
{% highlight js %}
<button class="btn btn-sm btn-success" onclick="newContact()">
Ajouter Contact
   </button>
{% endhighlight %}
