---
layout: post
title: Doctrine Migrations for a Doctrine-less App
published: true
---

## Preface

Several of our projects use [Doctrine 2](http://www.doctrine-project.org/projects/orm/2.0/docs/en)'s
ORM, which has an added bonus of being able to do database migrations based on changes to our schema
and entities.

However, we have some legacy code written in CodeIgniter that uses CI's proprietary ORM, but lacks
migration support.

Luckily, Doctrine de-couples it's ORM, DBAL, Common, and Migrations
([official documentation](http://www.doctrine-project.org/projects/migrations/2.0/docs/reference/introduction/en))
components for individual use.

## Stand-Alone Doctrine Migrations

### Install

I re-packaged this version myself to resolve some path issues with Symfony and allow for some
customization of the Command Line Client (CLI) (more on that in a bit).
*[View the code](http://github.com/ericclemmons/migrations/)*

#### 1. Download [doctrine-migrations.phar](http://github.com/downloads/ericclemmons/migrations/doctrine-migrations.phar).
#### 2. Save *doctrine-migrations.phar* to a folder such as:
    
    /myapp/bin/doctrine-migrations.phar
    
#### 3. Create `/myapp/bin/doctrine-migrations`:

    #!/bin/sh
    php doctrine-migrations.phar "$@"

#### 4. Test it out:
    
    /myapp/bin $ ./doctrine-migrations
    
    Doctrine Migrations version 2.0.0-DEV
    
    Usage:
      [options] command [arguments]
    
    ...

#### 5. That's it!

### Configuration

[Configuration Documentation](http://www.doctrine-project.org/projects/migrations/2.0/docs/reference/introduction/en)

We need to configuration our database connection parameters, where to store migrations and
(optionally) how the CLI's Input/Output behaves.

#### migrations-db.php

Doctrine will expect database parameters to be returned from this file:

    return array(
        'driver'    => 'pdo_mysql',
        'host'      => 'myDbHostIP',
        'user'      => 'migrations',
        'password'  => 'm1gr@t10n5',
        'dbname'    => 'myAppDb'        // Migrations will be performed on this database
    );

#### migrations.yml

Doctrine reads this file to determine which table to track migrations with and where to store
migration files on the filesystem.

    ---
    name: My App's Migrations
    migrations_namespace: MyAppsMigrations 
    table_name: my_apps_migration_version
    migrations_directory: ../db/migrations
    
#### migrations-input.php

Behind the scenes Doctrine uses [`\Symfony\Component\Console\Input\ArgvInput`](http://api.symfony-reloaded.org/PR3/index.html?q=ArgvInput)
to capture and parse values from `$_SERVER['argv']`.  You can customize this a bit if you'd like
(probably not).

    // You may need to use `ClassLoader` as shown in `migrations-output.php`
    $input = new \Symfony\Component\Console\Input\ArgvInput;
    $input->setArgument('verbose', true);
    return $input;

#### migrations-output.php

Doctrine Migrations, by default, **throws errors with SQL queries that contain HTML tags**, which is
why I adjusted the PHAR to support custom Input/Output classes.
    
    // Load the PHAR into memory so we can access Symfony dependencies
    Phar::loadPhar('doctrine-migrations.phar', 'migrations.phar');
    
    // Register the `ClassLoader` to save us the trouble of requiring several files
    require_once 'phar://migrations.phar/Doctrine/Common/ClassLoader.php';
    $classLoader = new \Doctrine\Common\ClassLoader('Symfony', 'phar://migrations.phar/');
    $classLoader->register();
    
    // This is what Doctrine Migrations uses by default
    $output = new \Symfony\Component\Console\Output\ConsoleOutput;

    // Enable styling for HTML tags, which would otherwise throw errors
    $htmlTags = array('p', 'ul', 'li', 'ol', 'dl', 'dt', 'dd', 'b', 'i', 'strong', 'em', 'hr', 'br');
    foreach ($htmlTags as $tag) {
        $output->setStyle($tag);    // Each tag gets default styling
    }

    return $output;

Now migrations with HTML tags won't throw exceptions.

### Using Doctrine Migrations

From here on out, just checkout the [Migration Classes Documentation](http://www.doctrine-project.org/projects/migrations/2.0/docs/reference/migration-classes/en).
