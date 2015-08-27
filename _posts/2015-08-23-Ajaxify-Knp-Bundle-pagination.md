---
layout: post
title: Ajaxify the Pagination of knp Paginator Bundle?
tags: symfony
---


<link rel="stylesheet" href="//cdnjs.cloudflare.com/ajax/libs/highlight.js/8.7/styles/default.min.css">
<script src="//cdnjs.cloudflare.com/ajax/libs/highlight.js/8.7/highlight.min.js"></script>



Ajaxify the Pagination of [knp Paginator](https://github.com/KnpLabs/KnpPaginatorBundle)  
first of all you will need to have knp Paginator installed which is easy :p so first of all
<h4>1-change the pagination view to bootstrap</h4>


{% highlight html js %}
//you-Project/vendor/knplabs/knp-paginator-bundle/Resources/views/Pagination/pagination.html.twig

{{ "{% set first = 1"}}%}

{{ "{% set previous = max(range(1, current-1)) "}}%}
{{ "{% set last = pagesNumber "}}%}
{{ "{% set next = min( last, current+1 ) "}}%}
{{ "{% set pages_range = range(max(first, current-3), min(last, current+3)) "}}%}

{{ "{% if pagesNumber > 1 "}}%}
    {{ "{%set first = 1 "}}%}
    {{ "{%set last = pagesNumber "}}%}
    {{ "{%set query= "}} {} {{" "}}%}
    {{ "{%set pageParameterName = 'page' "}}%}

<ul class="pagination">


    {{ "{% if first is  defined and current != first "}}%}
        <li class="previous">
            <a onclick="routeBringer('{{"{{ path(route, query|merge"}}({(pageParameterName): previous"})) {{" "}}}}', 'table_div')" >{{"{{ 'Previous'|trans "}}}}"</a>
        </li>
    {{ "{% else "}}%}
        <li class="disabled">
            <span>{{"{{ 'Previous'|trans "}}}}</span>
        </li>
    {{ "{% endif "}}%}





    {{ "{% for page in pages_range "}}%}
        {{ "{% if page != current "}}%}
            <li >
                <a onclick="routeBringer('{{"{{ path(route, query|merge"}}({(pageParameterName): page"})) {{" "}}}}', 'table_div')" >{{"{{ page"}}}}</a>
            </li>
        {{ "{% else "}}%}
            <li class="active">
                <span>{{"{{ page "}}}}</span>
            </li>
        {{ "{% endif "}}%}

    {{ "{% endfor "}}%}




    {{ "{% if last is defined and current != last "}}%}
        <li >
            <a  onclick="routeBringer('{{"{{ path(route, query|merge"}}({(pageParameterName): next"})) {{" "}}}}', 'table_div')" >{{"{{ 'Next'|trans "}}}}</a>
        </li>
    {{ "{% else "}}%}
        <li class="disabled">
            <span>{{"{{ 'Next'|trans "}}}}</span>
        </li>
    {{ "{% endif "}}%}
</ul>
{{ "{% endif "}}%}

{% endhighlight %}




>as you can notice in the twig code a call to routeBringer() function which is  exlpained in this [Post]() so this function will use the $.ajax() to bring the response in a specific div
i added this function in the three button type Previous,the number page button and in the next page button that will prevent the button from refreshing all the page with each pagination change  

the previous twig will be used in our main page

<h4>2-How to reload the table with each pagination change</h4>


the goal of this post is help you create a main page that load the table with ajax like this screen shot

<img src="{{ project.url }}/images/mainpage.png" alt="Main Page" >

the action of the previous page
{% highlight php %}

public function indexpaginatorAction(Request $request) {

//this action bring only the container twig view that will call later the dynamic table with our ajax function

        return $this->render('ContactBundle:Contact:Paginatorindex.html.twig', array();
    }

{% endhighlight %}

the twig template:
{% highlight php %}

{{ "{% extends '::home.html.twig' "}}%}


{{ "{% block body "}}%}
    <div id="table_div">

    </div>

{{ "{% endblock  "}}%}

{{ "{% block javascripts "}}%}

        <script src="{{ "{{ asset('assets/js/ajaxstand.js') "}} }}">

        <script>

            $(document).ready(function () {

            routeBringer( " {{" {{  path('slidepaginator')"}} }}"', 'table_div');//slidepaginator name of the route that bring the slide of your table,so when your page is loaded automatically the table will be loaded
           });
       </script>
{{ "{% endblock  "}}%}

{% endhighlight %}


>after reading the twig template w notice that we gonna use the slidepaginator route this route will bring only the table


the view of slidepaginator route


{% highlight php %}


{% endhighlight %}

the action of slidepaginator route

{% highlight php %}

public function slidespaginatorAction(Request $request) {

    $page = $request->query->get('page', 1);

    //count the number of row in Conatct Table ,that will help us in the request for the offset variable
    $myCount_old = $em->createQuery('SELECT COUNT(c) FROM Test1\ContactBundle\Entity\Contact c ')
            ->getSingleScalarResult();

    //we check if the number of row is bigger then our limit per page which is 4 you can change that by changing 4 by your prefered choice also the variable $limit

    if ($myCount_old < 4) {
        $myCount = 1;
    } else {
        $mod = $myCount_old % 4;
        $myCount = (int) ($myCount_old / 4);

        if ($mod != 0) {
            $myCount = $myCount + 1;
        }
    }
    //the variable $mycount is the number of pages in your pagination


    $limit = 4;
    if ($page == 1) {
        $offset = 0;
    } else {
        $offset = ($page - 1) * $limit;
    }

    //the variable $offset is from where our query will begin the selection of row



    //with ResultSetMapping you can map arbitrary SQL code to objects
    $rsm = new ResultSetMapping();
    $rsm->addEntityResult('Test1\ContactBundle\Entity\Contact', 'c');
    $rsm->addFieldResult('c', 'id', 'id');
    $rsm->addFieldResult('c', 'nom', 'nom');
    $rsm->addFieldResult('c', 'prenom', 'prenom');
    $rsm->addFieldResult('c', 'adresse', 'adresse');
    $rsm->addFieldResult('c', 'email', 'email');
    $rsm->addFieldResult('c', 'tel', 'tel');


    //this is our main query that select our result data
    $query = $this->getDoctrine()->getManager()->createNativeQuery("select id ,prenom,nom ,email,adresse,tel from contact inner join "
            . "(select id from contact  order by id asc limit ? offset ?) as contact"
            . " using (id)", $rsm);
                $query->setParameter(1, $limit);
                $query->setParameter(2, $offset);
    $queryres = $query->getResult();
    //I created this query to retrieve results with deferred join which will decrease the time of response



    $paginator = $this->get('knp_paginator');//call for knp_paginator service

    $pagination = $paginator->paginate(
            $queryres    //return only the $queryres variable
    );


    // send parameters to template
    return $this->render('ContactBundle:Contact:slidepaginator.html.twig', array('pagination' => $pagination, 'number_row' => $myCount_old, 'pages_number' => $myCount, 'page' => $page));
}

{% endhighlight %}


>So to recapitulate about this Post we have two action the first one is the container of our table and the other one is a dynamic action on our table which will be called with each switch of page number like Magic!!!

i hope that this will help you!!
