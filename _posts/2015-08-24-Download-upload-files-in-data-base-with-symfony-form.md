---
layout: post
title: Download & upload files in data base with symfony!
tags: symfony-files
---
<link rel="stylesheet" href="//cdnjs.cloudflare.com/ajax/libs/highlight.js/8.7/styles/default.min.css">
<script src="//cdnjs.cloudflare.com/ajax/libs/highlight.js/8.7/highlight.min.js"></script>



>Hey every body  for this Post i am gonna share with you how you upload files in your database and next how to download them  

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
    private $origName;

    /**
     * @var string
     */
    private $size;


    /**
     * @var string
     */
    private $extend;
    /**
     * Set extend
     *
     * @param string $extend
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
     * Set origName
     *
     * @param string $origName
     * @return File
     */
    public function setOrigName($origName)
    {
        $this->origName = $origName;

        return $this;
    }

    /**
     * Get origName
     *
     * @return string
     */
    public function getOrigName()
    {
        return $this->origName;
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

    /**
     * Set extend
     *
     * @param string $extend
     * @return File
     */
    public function setExtend($extend)
    {
        $this->extend = $extend;

        return $this;
    }

    /**
     * Get extend
     *
     * @return string
     */
    public function getExtend()
    {
        return $this->extend;
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
        origName:
            type: string
            nullable: true
            length: 100
            fixed: false
            column: orig_name
        size:
            type: string
            nullable: true
            length: 100
            fixed: false
            column: size
        extend:
            type: string
            nullable: true
            length: 20
            fixed: false
    lifecycleCallbacks: {  }

{% endhighlight %}

<h5>2-upload Action</h5>

>we will begin with the upload action which will create file in our database
