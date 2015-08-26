---
layout: post
title: Download & upload files in data base with symfony!
tags: symfony-files
---
<link rel="stylesheet" href="//cdnjs.cloudflare.com/ajax/libs/highlight.js/8.7/styles/default.min.css">
<script src="//cdnjs.cloudflare.com/ajax/libs/highlight.js/8.7/highlight.min.js"></script>



>Hey every body for this Post I am gonna share with you how you upload files in your database and next how to download them  

<h5>1-The Schema of our Entity "Files"</h5>
we gonna start with the Entity File which will be used in this Post

{% highlight html js %}

namespace Acme\Bundle\FileBundle\Entity;

use Doctrine\ORM\Mapping as ORM;

/**
 * File
 */
class File
{
    /**
     * @var integer
     */
    private $id;

    /**
     * @var string
     */
    private $name;

    /**
     * @var string
     */
    private $type;

    /**
     * @var \DateTime
     */
    private $creationDate;

    /**
     * @var string
     */
    private $data;

    /**
     * @var string
     */
    private $size;


    /**
     * @var string
     */

    /**
     * Get id
     *
     * @return integer
     */
    public function getId()
    {
        return $this->id;
    }

    /**
     * Set name
     *
     * @param string $name
     * @return File
     */
    public function setName($name)
    {
        $this->name = $name;

        return $this;
    }

    /**
     * Get name
     *
     * @return string
     */
    public function getName()
    {
        return $this->name;
    }

    /**
     * Set type
     *
     * @param string $type
     * @return File
     */
    public function setType($type)
    {
        $this->type = $type;

        return $this;
    }

    /**
     * Get type
     *
     * @return string
     */
    public function getType()
    {
        return $this->type;
    }

    /**
     * Set creationDate
     *
     * @param \DateTime $creationDate
     * @return File
     */
    public function setCreationDate($creationDate)
    {
        $this->creationDate = $creationDate;

        return $this;
    }

    /**
     * Get creationDate
     *
     * @return \DateTime
     */
    public function getCreationDate()
    {
        return $this->creationDate;
    }

    /**
     * Set data
     *
     * @param string $data
     * @return File
     */
    public function setData($data)
    {
        $this->data = $data;

        return $this;
    }

    /**
     * Get data
     *
     * @return string
     */
    public function getData()
    {
        return $this->data;
    }

    /**
     * Set size
     *
     * @param string $size
     * @return File
     */
    public function setSize($size)
    {
        $this->size = $size;

        return $this;
    }

    /**
     * Get size
     *
     * @return string
     */
    public function getSize()
    {
        return $this->size;
    }

  }

{% endhighlight %}
Next we add the mapping of the entity with yml
{% highlight html js %}
Acme\Bundle\FileBundle\Entity\File:
    type: entity
    table: file
    indexes:
        site_idx:
            columns:
                - id
    id:
        id:
            type: integer
            nullable: false
            unsigned: false
            id: true
            generator:
                strategy: IDENTITY
    fields:
        name:
            type: string
            nullable: false
            length: 255
            fixed: false
        type:
            type: string
            nullable: true
            length: 255
            fixed: false
        creationDate:
            type: datetime
            nullable: false
            default: '0000-00-00 00:00:00'
            column: creation_date
        data:
            type: blob
            nullable: true
            length: null
            fixed: false

        size:
            type: string
            nullable: true
            length: 100
            fixed: false
            column: size

    lifecycleCallbacks: {  }

{% endhighlight %}
<h5>2-Generate the CRUD of the Entity File</h5>
for generating the CRUD we run in our project file the command "app/console doctrine: generate: crud" like the picture below:

<img src="{{ project.url }}/images/CRUD.png" alt="Main Page" >


<h5>3-change the Form builder of the File Entity</h5>
{% highlight html js %}

public function buildForm(FormBuilderInterface $builder, array $options)
   {
       $builder// for all this attributs w shall delete them
           ->add('name')
           ->add('type')
           ->add('creationDate')
           ->add('data')
           ->add('size')
       ;
   }
   // for all these attributes we shall delete them
   =>Result

   public function buildForm(FormBuilderInterface $builder, array $options)
      {
          $builder// for all this attributs w shall delete them

          ;
      }
      //in this case we will have an empty form with a submit button so we will add a file input form
   {% endhighlight %}

<h5>4-Add the file input in new.html.twig</h5>

we will add a file input with name "myFile" this will be used in the submit action "create action"

{% highlight html js %}
{{"{% extends '::base.html.twig' "}}%}

{{"{% block body "}}-%}
    <h1>File creation</h1>
    {{"{{ form_start(form,"}}{{"{"}} 'attr': {{"{"}}'enctype': 'multipart/form-data','id': 'file_upload_form'  }  } ) {{""}}}}
//with enctype: 'multipart/form-data' No characters are encoded. This value is required when you are using forms that have //a file upload control
    <input   type="file" name="myFile"  />
    //you will need to put it inside the form like this or add it in the form builder
    {{"{{ form_end(form) "}}}}

        <ul class="record_actions">
    <li>
        <a href="{{"{{ path('file') "}}}}">
            Back to the list
        </a>
    </li>
</ul>
{{"{% endblock "}}%}

{% endhighlight %}


<h5>5-change the create Action</h5>
changing the create action so that the file selected will be saved correctly
{% highlight html js %}
if ($form->isValid()) {//if the form is validated

           $em = $this->getDoctrine()->getManager();

           $entity->setCreationDate(new \DateTime());//setting the different variables of our file
           $entity->setSize($_FILES['myFile']['size']);
           $entity->setName($_FILES['myFile']['name']);
           $entity->setType($_FILES['myFile']['type']);
           $entity->setData(file_get_contents($_FILES['myFile']['tmp_name']));
           //the attribute Data is the most important one because it's where we put our file Data

           $em->persist($entity);//persist the file
           $em->flush();
         }

{% endhighlight %}


>now we can upload any file in our database next we will see how to download them

<h5>5-create Download Action</h5>
we gonna create new action which will download our files by giving the id

{% highlight html js %}
//the route name is "file_download"
public function downloadAction($id) {
        $em = $this->getDoctrine()->getManager();

        $entity = $em->getRepository('FileBundle:File')->find($id);

        if (!$entity) {
            throw $this->createNotFoundException('Unable to find File entity.');
        }



        $response = new \Symfony\Component\HttpFoundation\Response(stream_get_contents($entity->getDataFile()), 200, array(
            'Content-Type' => $entity->getTypeFile(),
            'Content-Length' => $entity->getSizeFile(),
            'Content-Disposition' => 'attachment; filename="' . $entity->getNameFile() . '"',
        ));

        return $response;
    }

{% endhighlight %}


this action selects the entity and use the different attributes the give a response of type file by setting the type, the length and Disposition for the content of the response

so we use the download action like this
{% highlight html js %}

    <a href=" {{"{{ path('file_download',  "}}{{"{"}} 'id': entity.id } ) {{""}}}}" target="_blank">Download</a>
   //this download link will be opened in a new angle
    {% endhighlight %}

>when I worked with upload file I got a problem with uploading the file with Ajax without refreshing ,I found a solution about uploading the file in a hidden iframe and when the iframe is ready I make a call to display the list of the uploaded files
