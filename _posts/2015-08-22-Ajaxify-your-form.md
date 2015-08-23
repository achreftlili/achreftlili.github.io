---
layout: post
title: Get Views with Ajax and Ajaxify their Forms?
---
<link rel="stylesheet" href="//cdnjs.cloudflare.com/ajax/libs/highlight.js/8.7/styles/default.min.css">
<script src="//cdnjs.cloudflare.com/ajax/libs/highlight.js/8.7/highlight.min.js"></script>



<h3>Get Views with Ajax</h3>
In this post we gonna see how to add ajax to our forms in symfony which will help us create ajaxified web pages in seconds so focus :p

  >first thing you will need [jQuery](https://jquery.com/) plugin,
   then we create a javascript function which we will use to get the response of a specific route, this function get two parameters, the first is the route path the other one is the id of the div where you wanna put the response of your route.


  {% highlight js %}

<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.3/jquery.min.js"></script>

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

  >After that we gonna  call it in a button which will create a new Contact for example.

{% highlight js %}

<div id='div_id'>

</div>
<button  onclick="routeBringer('{{ path('contact_new') }}','div_id')">Add Contact   </button>
{% endhighlight %}

>next we gonna ajaxify the form new Contact.

<h3>ajaxify your form</h3>

>the result that we get is our new form displayed then we add our script that prevent the default submit then you call the PostForm function which will handle the vars of form and make the submit with ajax

{% highlight php startinline %}


{{ "{{ form(form) "}}}} //display our form new Contact

<script>

$(document).ready(function(){
        var forms = ['[ name="{{ "{{ form.vars.full_name "}}}}"]'];

        $( forms.join(',') ).submit( function( e ){

        e.preventDefault();                         //prevent the default submit of our form
        postForm( $(this), function( response ){    //call our Function PostForm and pass as first variable the form attribute name
        $("#div_id").html(response)});          //put the response in the div result


             return false;
        });
    });

</script>


{% endhighlight %}

>this is the postForm Function

{% highlight js %}

function postForm( $form, callback ){


 /*
  * Get all form values
  */

 var values = {};
 $.each( $form.serializeArray(), function(i, field) {
     values[field.name] = field.value;
 });
 /*
  * Throw the form values to the server!
  */

 $.ajax({
     type        : $form.attr( 'method' ),
     url         : $form.attr( 'action' ),
     data        : values,
     success     : function(data) {
         callback( data );


     }
 });
}
{% endhighlight %}


> after reading the function postForm we notice that there is two section the first one we put every values of our form (first attribute) in values vary after turning with.serializeArray () our form passed in the first parameter to a Javascript Array of object so that it will be ready to be encoded as a JSON string the second part is $. Ajax, jQuery function that will send our form submit as Ajax request in the success of our Ajax request the function callback will be called with data as a parameter this function will put the response html to our div result


<h6>I hope that I helped you for understanding some Ajax work with Symfony sorry for my English !</h6>

<section class="post-comments">
  <h2>Comments</h2>
  <div id="disqus_thread"></div>

</section>

<script type="text/javascript">
    /* * * CONFIGURATION VARIABLES * * */
    var disqus_shortname = 'httpachreftliligithubio';

    /* * * DON'T EDIT BELOW THIS LINE * * */
    (function() {
        var dsq = document.createElement('script'); dsq.type = 'text/javascript'; dsq.async = true;
        dsq.src = '//' + disqus_shortname + '.disqus.com/embed.js';
        (document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(dsq);
    })();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript" rel="nofollow">comments powered by Disqus.</a></noscript>
