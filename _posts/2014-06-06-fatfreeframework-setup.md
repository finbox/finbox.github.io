---
title: How to setup the Fat-Free Framework
permalink: /fatfreeframework-setup/ 
layout: post
image: /images/posts/seagull.jpg
---
<img src="/images/posts/pig.png" class="rounded"> 

# Fat-Free Framework Setup 101
<h2 class="subtitle">A PHP micro-framework</h2>

Never heard of Fat-Free Framework? Well 2 months ago, neither had I. I was a long time CakePHP user and never really loved it. I always felt like choosing to use it meant that I was inviting a magical giant to reside on my webpage. Most of the time he would play nice, but sometimes he'd play the troll under the bridge. In search of something lightweight and less magical, I came across the Fat-Free Framework and within 15 minutes of using it, I could tell this was a much better match.

A few details about F3:

    * ~60kb in size
    * Only works with PHP 5.3 and higher

> "The philosophy behind the framework and its approach to software architecture is towards minimalism in structural components, avoiding application complexity and striking a balance between code elegance, application performance and programmer productivity."

## Setting up your project

First, you'll want to [download](https://github.com/bcosca/fatfree/archive/master.zip){:target="_blank"} the framework files. Create a new directory for your site and unzip the framework there. Next, you will want to create an *index.php* file. This will act as your routing file, but can also include additional functionality.

### Routing

Open up *index.php* and include the following lines:

    <?php
    $f3 = require ("fatfree/lib/base.php"); 
    $f3->set('DEBUG', 1); // Change to 0 when pushing to production.
    
    class Controller {
        function index($f3) {
            echo "No magic here!";
        }
    }
    
    // Routes
    $f3->route('GET /', 'Controller->index');
{:.language-php .line-numbers}

Now fire up a local php server in the root of your project directory, and direct a browser to it. If you've done everything correctly you should see a page with "No magic here!".

What I love about F3 is how easy it is to understand what is happening. On line 12 we define the route "GET /" and point it to the index method inside of our Controller class. That is why the page renders "No magic here!".

Adding additional routes is as simple.

    $f3->route('GET /job/@id', 'Controller->job');
    $f3->route('POST /job', 'Controller->createJob');
    $f3->route('DELETE /job/@id', 'Controller->deleteJob');
{:.language-php }

You'll notice that two of the routes have *@id*. F3 makes it really simple to grab parameters from the url. The following code shows how this is done. 

    class Controller {
        function job($f3, $params) {
            echo "This is the job with id:" . $params['id'];
        }
    }
    
    // Routes
    $f3->route('GET /job/@id', 'Controller->job');
{:.language-php }

### Views & Templates

What we're currently doing inside of our controller isn't very interesting, nor practical. To improve upon that, examine the following code.

*index.php*

    class Controller {
        function job($f3, $params) {
            $f3->set('id', $params['id']);
            echo View::instance()->render('/views/job.php');
        }
    }
    
    // Routes
    $f3->route('GET /job/@id', 'Controller->job');
{:.language-php .line-numbers}

*/views/job.php*

    <html>
    <head><title>Jobs page</title></head>
    <body>
    Access your variables by <?php echo $id?>
    </body>
    </html>
{:.language-php .line-numbers }

You'll notice that on line 3 of *index.php* we make the variable $id accessible to *jobs.php* on line 4. Structuring your code in this manner allows you to separate your views and controllers. F3 comes with its own templating engine which you can use by replacing line 4 of *index.php* with

    Template::instance()->render('/views/job.php');
{:.language-php }

This tutorial doesn't cover the basics of how to use their templating framework, but you can read about it [here](http://fatfreeframework.com/views-and-templates){:target="_blank"}.

### Database

F3 also has built in support for interacting with SQL and NoSQL databases. The following examples are all for a SQL database.

The following code sets up a connection to the database.

    $db=new DB\SQL(
        'mysql:host=localhost;port=3306;dbname=mysqldb',
        'admin',
        'p455w0rD'
    );
{:.language-php }

Queries can be made against the database as follows:

    $f3->set('result',$db->exec('SELECT brandName FROM wherever'));
    echo View::instance()->render('abc.htm');
{:.language-php }

And parameterized queries automatically protect against any SQL injection.

    $db->exec('SELECT * FROM users WHERE userID=?', $id);
    //or
    $db->exec('SELECT * FROM users WHERE f_name=:first AND l_name=:last',
        array(':f_name':'Freelance', ':l_name':'Inbox')
    );
{:.language-php }

F3 supports easy-to-use object-relation mappers as well and you can read about how to use them (as well as more advanced database features) [here](http://fatfreeframework.com/databases){:target="_blank"}.

## Wrapping up

Hopefully that gives you a brief introduction to the very powerful, but lightweight Fat-Free Framework. I have really enjoyed using it in my latest projects. It provides the basics I need to build a robust application quickly, but also gives me all of the control I want. 

>As a heads up in case you're like me, when it comes time to deploy your website, **don't forget to modify your .htaccess file** So far I'm 2 for 2 on forgetting this, and I spend a few minutes wondering why things aren't working.This is the code you'll need to add.
>
    RewriteEngine On
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_FILENAME} !-l
    RewriteRule .* index.php [L,QSA]
{:.language-php }

{% include kesler_bio.html %}


**Icons courtesy of <a href="http://icons8.com/" target="_blank">Visual Pharm</a>*