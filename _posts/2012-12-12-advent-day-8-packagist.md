---
title: 'Advent Day 8: Packagist'
author: admin
layout: post
permalink: /adv8
custom_permalink:
  - adv8
categories: articles
tags:
  - Advent2012
  - BEAR
  - PHP
---
<div style="float: right; margin-left: 10px;">
  <a href="https://twitter.com/share" class="twitter-share-button" data-count="vertical" data-url="/blog/adv8">Tweet</a>
</div>

## Packagist登録

BEAR.PackageをPackagistに登録しました。  
composerのcreate-projectでインストールできるようになりました。

composerインストール  
{% highlight bash %}$ wget http://getcomposer.org/composer.phar{% endhighlight %}
または  
{% highlight bash %}$ curl -s https://getcomposer.org/installer | php{% endhighlight %}

composerでBEAR.Sunday sandboxアプリケーションインストールします。

{% highlight bash %}$ php composer.phar create-project -s dev bear/package /tmp/mysunday{% endhighlight %}

BEAR.Packageとその依存のBEAR.Sundayが指定したバージョンでインストールされます。<sup><a href="#footnote_0_1385" id="identifier_0_1385" class="footnote-link footnote-identifier-link" title=" インストールスクリプトはまだ用意できていないのでパーミッションの設定が現在必要です">1</a></sup> その後PHP5.4のbuilt-in web serverを立ち上げます。

{% highlight bash %}
$ composer.phar create-project -s dev bear/package /tmp/mysunday
Installing bear/package (dev-master bbc42caf8ed71e56c4f72f7270db012dc4b40d39)
  - Installing bear/package (dev-master master)
    Cloning master
Created project in /tmp/mysunday
Loading composer repositories with package information
Installing dependencies from lock file
  - Installing aura/installer-default (1.0.0)
  - Installing aura/web (1.0.0)
  - Installing aura/signal (1.0.0)
  - Installing aura/router (1.0.0)
  - Installing aura/di (1.0.1)
  - Installing doctrine/common (2.3.x-dev bb0aebb)
    Cloning bb0aebbf234db52df476a2b473d434745b34221c
  - Installing ray/aop (dev-master 3edfe6b)
    Cloning 3edfe6ba6b52e8d3190da62c14479ff7cce2377f
  - Installing ray/di (dev-master 1.0.0-beta3)
    Cloning 1.0.0-beta3
  - Installing zendframework/zend-stdlib (2.0.5)
  - Installing zendframework/zend-log (2.0.5)
  - Installing nocarrier/hal (dev-master cc46654)
    Cloning cc466546c6ca5df3e806cd91258cdf194518a12f
  - Installing twitter/bootstrap (master master)
    Cloning master
  - Installing firephp/firephp-core (dev-master c26d972)
    Cloning c26d972dcb28fd483fa193512091df7b3c85e450
  - Installing symfony/http-foundation (2.0.x-dev 4de1a1f)
    Cloning 4de1a1f9a81a58bd6f24607894f76fd7017d45e7
  - Installing symfony/console (2.0.x-dev v2.0.19)
    Cloning v2.0.19
  - Installing smarty/smarty (v3.1.12)
    Checking out /tags/v3.1.12/@4664
  - Installing pagerfanta/pagerfanta (dev-master 12f71d9)
    Cloning 12f71d99457b018fb80746f84514dd5b495c5789
  - Installing symfony/event-dispatcher (dev-master eb290a4)
    Cloning eb290a447c0af5bea0d3de5d95d498afd8c82f89
  - Installing guzzle/guzzle (v2.7.2)
  - Installing facebook/xhprof (0.9.2)
  - Installing doctrine/dbal (2.3.x-dev f63af19)
    Cloning f63af1948a609a96b8ea1c1302c7cdf2f9f51468
  - Installing printo/printo (dev-master abd0d6b)
    Cloning abd0d6b68d00dc98a71124215780b57ec3ede268
  - Installing vdump/vdump (0.1.0)
  - Installing bear/resource (dev-master 3ca644b)
    Cloning 3ca644bc29de4257ec9165ce1c9745150dc231dd
  - Installing bear/sunday (dev-master a9b6fdd)
    Cloning a9b6fdd7cd34b9e7d2075a7c95e066547bcf59ac
zendframework/zend-stdlib suggests installing pecl-weakref (Implementation of weak references for Stdlib\CallbackHandler)
zendframework/zend-log suggests installing zendframework/zend-db (Zend\Db component)
zendframework/zend-log suggests installing zendframework/zend-escaper (Zend\Escaper component, for use in the XML formatter)
zendframework/zend-log suggests installing zendframework/zend-mail (Zend\Mail component)
zendframework/zend-log suggests installing zendframework/zend-validator (Zend\Validator component)
pagerfanta/pagerfanta suggests installing doctrine/orm (2.*)
pagerfanta/pagerfanta suggests installing doctrine/mongodb-odm (2.*)
pagerfanta/pagerfanta suggests installing solarium/solarium (2.*)
symfony/event-dispatcher suggests installing symfony/dependency-injection (2.2.*)
symfony/event-dispatcher suggests installing symfony/http-kernel (2.2.*)
Generating autoload files
Do you want to remove the existing VCS (.git, .svn..) history? [Y,n]? Y
$ chmod -R 777 /tmp/mysunday/apps/Sandbox/data/
$ chmod -R 777 /tmp/mysunday/apps/Helloworld/data/
$ cd /tmp/mysunday/apps/Sandbox/public/
$ php -S localhost:8088 web.php
PHP 5.4.9 Development Server started at Wed Dec 12 13:11:37 2012
Listening on http://localhost:8088
Document root is /private/tmp/mysunday/apps/Sandbox/public
Press Ctrl-C to quit.
{% endhighlight %}

<ol class="footnotes">
  <li id="footnote_0_1385" class="footnote">
    インストールスクリプトはまだ用意できていないのでパーミッションの設定が現在必要です [<a href="#identifier_0_1385" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
</ol>
