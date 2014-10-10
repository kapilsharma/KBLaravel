# Laravel Authentication

This is the first page of our project. To make authentication, we have to complete following parts.
* Authentication routes
* Authentication Controller
* Template and views
* Database
* Configure Database
* Create user table migration script
* Migrate (To create table)
* Seeder (To add initial user)
* User Model
* Authentication Form
* Authentication and success/failure page

We hope you have some understanding of Laravel. If not, you cna continue with this tutorial but we are not going to complete whole laravel tutorial. This tutorial will cover only laravel learning needed to complete this project but will provide links for further reading on the topic.

We expect you had already set up Laravel on Homestead as per previous tutorial (ToDo: complete and provide link). If not, please go through laravel setup tutorial and setup development environment. This tutorial will be using Homestead for development but you are free to use any setup.

**Further recommendations**
Although it is not mandatory, we recommend you not to take code from here directly. Create your separate project on GitHub, make your code and commit it. Do it exactly like you are creating any professional project. If you stuck somewhere, you can always check the code in the repository.

We also expect, you have created project by name KAPsBudget and on homestead, your url is http://kapsbudget.app:8000. You are not forced to do it but if they are different, make sure you take care of changing related urls/project name.

## Authentication routes
As of now, we just installed Laravel. So opening http://kapsbudget.app:8000 will open Laravel default page.

Now as first task, we must update our routes to open login page. Our routes.php file already contains following code.

```PHP
Route::get('/', function()
{
    return View::make('hello');
});
```

Since our page is login page, lets change hello to login. So our return statement will be

```PHP
    return View::make('login');
```

Now refreshing our browser will through InvalidArgumentException with message 'View [login] not found.' This is obvious, we had not created login view. So lets rename 'app/views/hello.php' to 'app/views/hello.blade.php'. Don't get confused with '.blade.php' extension. We are going to use blade template engine which have extension '.blade.php'. Just a minute, if you have created your own git project, as I suggested earlier, you must use following command to rename file on git

```git
git mv app/views/hello.php app/views/login.blade.php
```

Doing so will ensure file is renames on git as well, just commit and push the changes to git.

OK now we have our login view file. Lets change the code a bit to confirm it is working properly. Open login.blade.php file, delete all the code there and add following code.

```HTML
<!doctype html>
<html lang="en">
  <head>
    <title>KAPsBudget: Login</title>
  </head>
  <body>
    <h1>Dummy template for login page.</h1>
  </body>
</html>
```

Now reload the browser again and you must get expected HTML page. But wait a minute, we are working with Laravel, which follow MVC pattern so where is controller?

We will soon learn more about routes in Laravel and find that it may contain some logic in closures and may call views directly, but this is not a recommended approach for big projects like KAPsBudget. Going forward, our login page will need to display login errors in case of failed login so we might need some logic to conditionally show error messages. So lets create a controller to handle the request.

## Authentication Controller

So first we need to create authenticate controller. To create controllers, we can use artisan tool. We run artisan on the root folder of project so if you are using Homestead, go to Code/KAPsBudget folder to run artisan commands.

*Dont run the command below immediately*

```
php artisan controller:make NameOfController
```

Above command will create controller with name `NameOfController` with following methods.

|Verb     |Path                      |Action   |Route Name       |
|:--------|:-------------------------|:--------|:----------------|
|GET      |/resource                 |index    |resource.index   |
|GET      |/resource/create          |create   |resource.create  |
|POST     |/resource                 |store    |resource.store   |
|GET      |/resource/{resource}      |show     |resource.show    |
|GET      |/resource/{resource}/edit |edit     |resource.edit    |
|PUT/PATCH|/resource/{resource}      |update   |resource.update  |
|DELETE   |/resource/{resource}      |destroy  |resource.destroy |

However we might not want to create all of these method all the time. So we can declear to create only specific methods (--only=index,create) or to omit specific methods (--except=destroy,update).

Right now, we just want to make index method so we will use following command

```
php atrisan controller.make AuthController --only=index
```

This command will create `app/controller/AuthController.php` with following code.

```PHP
< ?PHP
class AuthController extends \BaseController {

    /**
     * Display a listing of the resource.
     *
     * @return Response
     */
    public function index()
    {
        //
    }
}
```

Obviously this is just a basic structure of controller. First lets go through what it is doing. Generally, all of our controllers goes in directory `app/controller` and file name is same as controller class name. Also note, general naming convention for controller file is `XxxController.php`.

> Although it is recommended to keep controllers in `app/controller` folder, it is not not mandatory. Laravel use composer to auto load the classes so as long as controller know 

In the code, controller always extends BaseController, which is located at default namespace. Methods with in controller class are main controllers. So controller class is also used to group similar controllers.

Now we have basic understanding of controller, lets update our controller to load our view. Update the code as following.

```PHP
< ?php
class AuthController extends \BaseController {

    /**
     * Display the login page
     *
     * @return Response
     */
    public function index()
    {
        return View::make('login');
    }
}
```

Like route, here too, we are returning an object of Response. At the end, we must update our route to load controller instead of view. So lets update routes.php file as follow:

```PHP
Route::get('/', 'AuthController@index');
```

In get method of our route, we still have URI `/` as first parameter but we changed second parameter from closure to String. Format of the string is `NameOFController`@`nameOfMethod`.

So the above route commands controller name is `AuthController` and we want to call `index` method when ever this route is matched.

Let reload the page and we must get same result as we get earlier, as we had not changed our view. Next step is to write HTML for login page.

## Template and view

I'd created separate project KAPsBudgetTemplate so that while learning, we have complete and decent looking HTML design ready to use. You are free to design your own template but if you want to focus on learning laravel, you can pick up existing template I created for the purpose of KAPsBudget. Simply clone KAPsBudgetTemplate with following command

```
git clone https://github.com/kapilsharma/KAPsBudgetTemplate.git
```

If you wish to make any changes in the template, feel free to fork the project and send push request in case you feel your changes should be included in KAPsBudgetTemplate.

Now you have KAPsBudgetTemplate, go through it code a bit and load it in browser to get an idea of application we are about to make while learning laravel. It have two different templates:

* Original HTML template (Template on which KAPsBudgetTemplate is based)
* PHP template. You can set it up locally or on homestead to see it working.

For login page, login.html is original file and login.php is php file. For that, first create a folder `css` under `public` folder. Then from KAPsBudget folder, copy following files to css folder

* css/AdminLTE.css
* css/bootstrap.min.css
* css/font-awesome.min.css

We also need to copy `js/bootstrap.min.js`. As earlier, create folder `js` in public folder and copy above file. Along with that, we need to copy fontawesome. Create folder `public/fonts` and copy following three files from fonts folder of KAPsBudgetTemplate

* fonts/fontawesome-webfont.woff
* fonts/fontawesome-webfont.ttf
* fonts/fontawesome-webfont.svg

Once all 7 supporting files are copied, we are ready to code. Copy whole code from KAPsBudgetTemplate/pages/examples/login.html and paste to our view `app/views/login.blade.php`. Reload the page and we have better looking login page.

Now before we go ahead, we must configure our database. However to keep this page short, We will be presenting model introduction on Sprarate page [laravel-Auth-Model](https://github.com/phpreboot/CodeJugalbandi/tree/master/php/laravel/Auth/model.md)