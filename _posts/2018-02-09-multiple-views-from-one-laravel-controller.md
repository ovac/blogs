---
layout: post
current: post
cover: https://cdnp-2f3a.kxcdn.com/blog/wp-content/uploads/2016/04/Why-Laravel.jpg
navigation: True
title: Multiple views from one Laravel controller.
date: 2017-02-09 05:22:00
tags: [laravel, tech]
class: post-template
subclass: 'tags-tech, tags-laravel'
author: ovac4u
---

<!-- # Multiple views from one Laravel controller -->

Interestingly, this is very simple, but it could keep you hitting your head for a few hours.

Honestly, the best option is to extend from a controller that holds the implementation and give each view it's own controller - but then, does it always have to be so complex?

Sometimes, the best solution is just to get things working. If it's testable, it's worth it.

If you find yourself in such a position, here's are some simple ways to get it fixed and working.


## Different views based on URL sharing the same controller and data.

In this case, the first view (view1.blade.php) should be returned if the url is 'http://exampe.com/hello', but if it is the root (e.g http://exampe.com), then it should return the second view (view2.blade.php).

* First, in your route, be sure to point both urls to the same controller. The following is a simple method using the laravel route optional parameters with regex constraints.

```php
  Route::resource('{path?}', 'HelloController')->where("path", "hello");
```

* Then in your the controller, you may render the view based on the url request.
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

it is that simple.

# Come back for other cases. I'll update this post and make it even more comprehensive before the end of the day.


* You may also suggest other cases and solutions. I give full credits! Find typos or want to make corrections or add your contributions? Just send a pull request as this blog post is hosted [here on github using markdown](https://www.github.com/ovac/blogs/_posts/2018-02-09-multiple-views-from-one-laravel-controller.md)
