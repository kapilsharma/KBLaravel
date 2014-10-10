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

## Database

Working with database is one of the most common task of modern web applications. Any developer friendly framework thus must provide very easy way of communicating with database. Fortunately laravel too provide tools to communicate easily with database. However before that, laravel must know about our database (and before that, we must create database :D)

**Creating Database**

IF you are following homestead setup and configured PHPMySQL, simply create a database kapsbudget.

## Configure database in Laravel

Database configuration in laravel goes in file `app/config/database.php`. edit the file as following, we actually just need to change setting in `mysql` array:

```PHP
return array(
    'fetch' => PDO::FETCH_CLASS,
    'default' => 'mysql',
    'connections' => array(
        // ...
        'mysql' => array(
            'driver'    => 'mysql',
            'host'      => 'localhost',
            'database'  => 'kapsbudget', //change database name here
            'username'  => 'root',       //change db user name here
            'password'  => 'secret',     //change db user password here
            'charset'   => 'utf8',
            'collation' => 'utf8_unicode_ci',
            'prefix'    => '',
        ),
        // ...
    ),
    // ....
)
```

As you can see, we just need to change `database`, `username` and `password` under mysql array and we are all done.

However it is worth understanding the meaning of different configuration options. `fetch` told laravel that we want to fetch database as class. `default` says we are using `mysql` and that is why we want to update `mysql` array under connections. If you wish to use another DBMS, say postgres, change `default` form `mysql` to `pgsql`. However for this tutorial, I recommend to stick with mysql.

Now laravel knows about our database. Lets create a dummy table (that we will delete later) with some seed data for our further example while learning more about Laravel models. Feel free to run following SQL to create table and seed data.

> ToDo: Do it through migration and seeding

```sql
CREATE TABLE IF NOT EXISTS `users` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(50) NOT NULL,
  `email` varchar(100) NOT NULL,
  `password` varchar(100) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB;

INSERT INTO `kapsbudget`.`users` (`id`, `name`, `email`, `password`) VALUES 
(NULL, 'Kapil Sharma', 'kapil@example.com', '123456'), 
(NULL, 'Pari Sharma', 'pari@example.com', '654321');
```

*This is just a quick example. I do not recommend storing password in plain text, its bad idea.*

## Connecting database and playing with it

**Getting all users.**

To play with database and learn basics, lets create new route. Update you routes file to add following route.

```php
Route::get('/dbtest1', function(){
    $users = DB::table('users')->get();

    return $users;
});
```

Now load newly created page `http://kapsbudget.app:8000/dbtest1` and you will see the list of all the users.

Here we are using laravel's DB class. Static method `table` connects to the table and `get` fetch all the rows. SQL statement that will be generated by this will be:

```sql
select * form users
```

> Please note, you might get exception
> 
> SQLSTATE[42S02]: Base table or view not found: 1146 Table 'homestead.users' doesn't exist (SQL: select * from `users`)
> 
> You might see laravel is trying to connect 'homestead.users' table (database name homestead) but out database name is 'kapsbudget', not 'homestead'.
> 
> In that case, please check if your config folder contains directory `local`. If yes, you must make config changes in `app/config/local/database.app`. Reason is, laravel first check if environment specific configuration files are present, load configuration setting from them.

In above example, we returned users which will display as JSON object in single line, probably hard to read. Another option, instead of `return $users;`, we may use `dd($users);`. Try that.

> `dd` is a helper function provided by laravel which is equivalent to `die(var_dump($users));`

**where clause**

Most of the time, we might not was all the rows but selected rows. What if we want to execute query.

```sql
SELECT * FROM users
WHERE id = 1
```

To do that. we can use `find` methos instead of `get`. Lets create dbtest2 route.

```php
Route::get('/dbtest2', function(){
    $users = DB::table('users')->find(1);

    dd($users); //equivalent to die(var_dump($users));
});
```

> **Please note:** Although I really put all the routes we are going to discuss in this section, they will be removed in future versions and thus when you checkout the project, you will not get the routed we discuss here. However, I marked the code till this chapter as tag `v0.1.0`. So once you make a clone of the project, you must checkout with tag `v0.1.0` to see the changes. So the commands you will need are:
> 
> **Clone project**
> 
> *git clone https://github.com/phpreboot/KAPsBudget.git *
> 
> **Checkout tag `v0.1.0` **
> 
> *git checkout tags/v0.1.0*

Data returned by find or get or any other db function is an object of standard class. So if we wish to display data separately, we can get data as object property as follow.

```php
Route::get('/dbtest3', function(){
    $users = DB::table('users')->find(1);

    echo "Name: " . $users->name;
    echo "<br/>Email: " . $users->email;
    echo "<br/>id: " . $users->id;
    exit;
});
```

We will be using Eloquent for our project and thus, we will not go to laravel query builder in details. If you wish to go through laravel query builder in details, more details can be found at http://laravel.com/docs/database

**Eloquent**

Eloquent is a nice ORM. ORM stands for Object Relational Mapper and as the name suggest, it map database rows to PHP object. Lets leave further theory for books and continue to use eloquent.

For using ORM, we must define a model class. Laravel provide a default user model in every project. Open the file `app/models/User.php` and see the default code. At the top, it have some common interface and trait included. However it mainly define name of table in variable `$table` and that is users.

Eloquent mainly follow 'convention over configuration' principle that is, most of the obvious things are conventions. So these conventions should not go in configuration, unless we go against conventions. One other convention is the primary key. Eloquent assums every table have a primary key with name `id` and it is of integer type. As far as we stick to this very common configuration, we need not to configure id field. So in our example, we need not to mention primary key. However if we decide to have primary key with different name or data type, we must specify it. Another assumption is 'singular model and pural db table'. So out class name is User but table name is users.

Also note, User class extends Elequent class and this is where whole magic, we are about to see, happens.

Actually we do not need any changes in the User class in User.php file. So lets try our dbtest1 example again but this time with eloquent.

```php
Route::get('/dbtest5', function(){
    //$users = DB::table('users')->get();
    $users = User::all();

    return $users;
});
```

Similar to other previous examples, we can have

```PHP
User::find(1); //Find user with id 1
```

**Insert records**

Insert a new row through eloquent is as easy as creating new object and call a method. Lets chank an example of inserting new record to our user table.

```php
Route::get('/dbtest6', function(){
    $user = new User();

    $user->name     = "Alankar More";
    $user->email    = "alankar@example.com";
    $user->password = "123456";

    $user->save();

    $users = User::all();
    return $users;
});
```

However wait a minute, should we save password in plain text? Obviously no. Laravel provide a method of hashing passwords in secure way through Hash class. Replace `$user->password = "123456";` to `$user->password = Hash::make("123456");` and our password will be hashed in the database.

Now if you run this example, you must get an SQL error `Unknown column 'updated_at'`. What is happening? Well eloquent expect our table to have created_at and updated_at columns, which are missing in our table. To fix the error, we have two options:

* Add missing columns OR
* Tell laravel we do not have those columns to dont try to add record in those columns.

Although most of the time we will have those columns but for this example, let try second approach. To do that, go to `app/models/User.php` file and add new property `public $timestamps = false;`. This will tell laravel that out table do not have created_at and updated_at columns so do not try to read/write them. Try to run above example once again, it should insert a record in database are return all users.

Another way to insert records in database is to use create method of eloquent. Consider following example:

```php
Route::get('/dbtest7', function() {
    User::create([
            'name'     => 'Amol Ogale',
            'email'    => 'amol@example.com',
            'password' => Hash::make('123456')
        ]);

    $users = User::all();

    return $users;
});
```

However running this example will cause `Illuminate\Database\Eloquent\MassAssignmentException`. Actually many developers have habit of sending $_POST array in database while inserting new records. This is called 'Mass assignment' and is insecure. So eloquent, by default, do not allow to insert new records using this method. If you like this mathod of inserting records, you must tell eloquent. To do this, we can define two array in model class named `fillable` or `guarded`. Using `fillable` array, we tell which fields can be inserted using mass assignment and `guarded` tell the field which can not be inserted using mass assignment. So lets go to User.php file and add following variable

```php
protected $fillable = ['name', 'email', 'password'];
```

Now try above example again and it must work.

**Updating records**

Like save, updating records using eloquent is also very easy. Consider following example where we will update password of frist record from plain text to hashed password.

```php
Route::get('/dbtest8', function() {
    $user = User::find(1);
    $user->password = Hash::make($user->password);
    $user->save();

    $users = User::all();
    return $users;
});
```

In the first line, we get the user to be updated. In line 2, we are actually hashing the password and then in line 3, we are saving user back to database. Line 4 and 5, as earlier is displaying all the records.

**Deleting records**

By looking example till now, you must have noticed, eloquent uses most obvious method names and thus you probably could guess the code to delete user without even looking at documentation or reading code.

So as an exercise, try to write code to delete the only record that still have plain password that is the record with id '2'.

If you could not do that, don't worry, code is given in routed file of version 0.1.0 as route '/dbtest9'. You can quickly view it at https://github.com/phpreboot/KAPsBudget/blob/v0.1.0/app/routes.php line 90-96.