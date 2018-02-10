---
layout: post
current: post
cover: https://cdnp-2f3a.kxcdn.com/blog/wp-content/uploads/2016/04/Why-Laravel.jpg
navigation: True
title: Different views from one Laravel controller method.
date: 2017-02-09 05:22:00
tags: [laravel, tech]
class: post-template
subclass: 'tags-tech, tags-laravel'
author: ovac4u
---

<!-- # Multiple views from one Laravel controller -->

Interestingly, this is very simple, but it could keep you hitting your head for a few hours.

When faced with this challenge, the recommended option is to implement the methods required on a base controller, and then for each view that needs the data on the base controller, a new controller should be created. After the controllers are created, they should each extends from the base controller -- but then, does it always have to be this complex?

Sometimes, the best solution is just to get things working. In my own opinion, If it's testable, readable and maintainable, it's worth it.

If you find yourself in such a position, this is a very simple solution.

# Return a preferred view using one controller based on the requesting URL.

In this example, we will be considering two views and two routes. The controller 'HelloController' will return the preferred view based on conditions and constraints.

### Views
  1. view1.blade.php
  2. view2.blade.php

### Routes
  1. http://exampe.com/hello
  2. http://exampe.com

The first view (view1.blade.php) should be returned if the route is 'http://exampe.com/hello', but if it is the root (e.g http://exampe.com), then it should return the second view (view2.blade.php).

First, in your route, be sure to point both urls to the same controller. The following is a simple method using the Laravel route optional parameters with regex constraints.

```php
  Route::resource('{path?}', 'HelloController')
        ->where("path", "hello");
```

* Then in your the controller, you may return the view based on the requesting route.
```php
  <?php

  namespace App\Http\Controllers;

  use Illuminate\Http\Request;

  class HelloController extends Controller
  {
      public function index(Request $request)
      {
          // data retrieval could be done here

          if ($request->is('hello')) {
            return view('view1');
          }

          return view('view2');
      }
```

As you see, it's a lot easier than one may perceive. It is also easily noticeable that the controller is testable, easily readable and therefore maintainable since the implementation has been done with less code and fewer files.

* You may also suggest other cases and solutions. I give full credits! Did you find any typo or do want to make corrections or add your contributions? if so, please send me a pull request on github as [this blog post is hosted on github using markdown](https://github.com/ovac/blogs/blob/master/_posts/2018-02-09-multiple-views-from-one-laravel-controller.md)
