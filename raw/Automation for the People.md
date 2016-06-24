原文：[Automation for the People](https://gist.github.com/classam/9e07a36aa63624ca2dda75a1367a53c6)

---

Long ago, the first time I read "The Pragmatic Programmer", I read some advice that really 
stuck with me. 

"Don't Use Manual Procedures".

This in the chapter on Ubiquitous Automation. To summarize, they want you to automate all the things.

The trouble was that I hadn't much of an idea how to actually go
about automating _any_ of the things. At the time, I was still trapped in a world
where every program was a bulky Java program with a CLI, and I was under the 
impression that the command line interface was nothing but a dying remnant of times past. 

My journey to some semblance of automation is filled with pitfalls and embarassing tales
of my own incompetence, and I hope to share these tales with you. 

## Bash Scripting

One of the pitfalls of interacting with a command-line system is that every complicated
interaction takes an equally complicated command to invoke - and for commands that I might
have to run again and again, I would have to remember the precise invocation each time.

Most of the time this worked out, although with no small amount of checking Google or 
StackOverflow every single time. 

One persistent thing about fantasy fiction is you always see acolytes with a wand, 
but no spellbook. This is because they are stupid. Wizards carry a spellbook.

So I would start writing down my commands, so that I wouldn't forget them.
Instead of writing these commands down on post-it notes or in a notebook, 
I'd write them to my home directory.

    $ cat work
    ssh classam@workcomputer.blorf -P 8888 -p=hunter2

At first, I would just cat this file, and then copy the command back into the command line. 

    $ cat work
    ssh classam@workcomputer.blorf -P 8888 -p=hunter2
    $ ssh classam@workcomputer.blorf -P 8888 -p=hunter2
    Welcome to Work Computer. Blorf.
 
I told you that I would share embarassing tales of my incompetence.

I soon realized that, without really having intended to, I was writing bash scripts -
I just wasn't executing them sanely. If I just told the system that I wanted 
to execute this file like a program, it should work sanely enough. 

    $ chmod u+x work
    $ ./work
    Welcome to Work Computer. Blorf.

### Hashbang

This alone is good enough to get this script into working order, but we're not doing a lot
to help the system figure out exactly how to run this mystery script that we have passed it.

Let's imagine we create a file: 

    print("hello")

This is valid Python 3, but not valid Python 2. If we save this file as 'hello', 
then we know very little about how to run it. And if we try to run it, the computer
will guess, and it will guess wrong.

    $ ./hello
    idfk, man, that doesn't look right to me

If we name the file hello.py, we give ourselves a handy clue as to what sort of file
it is, and then we can execute it with the Python interpreter.

    # python hello.py
    that's python 3, man, I'm python 2. Whateva.
    # python3 hello.py
    hello

Okay, that worked - but it's a lot of extra stress for a program invocation. 
One Unix innovation from the seventies can clear up this problem: the hashbang.
At the beginning of our script, we can declare exactly which interpreter we want
to use.

    #!python3
    print("hello")
    
    #!bash
    echo "hello"

The only rule, here, is that the interpreter has to be visible from the system's $PATH.
Which is great if you're running the script locally - but if you're running the script
remotely without a pseudo-terminal session set, you might not have a $PATH, which would
cause your hashbang declaration to fail. 

The workaround? The hashbang can include the full path to the interpreter that you 
intend to use for the program. 

    #!/bin/python3
    print("hello")

    #!/bin/bash
    echo "hello"

You don't see this exciting technology in a lot of modern programming, because it's
not spectacularly portable - the location and even the name of the interpreter you're
using to execute a python program is often wildly different from system to system.

Bash is always at /bin/bash, though, so that base is covered, which is why every bash
program under the sun opens with #!/bin/bash. 

### Bashy Bash

So, before long, my home directories everywhere started to fill up with helpful little
bash files with names like `work.sh` and `test.sh`.

## Alias

If you've programmed in Django, you might remember that just about every command
that you can issue to your Django environment starts by directly invoking the program
`manage.py`. 

I might start the Django development server like this:

    $ cd ~/code/django_project
    $ ./manage.py runserver 0:8000

Which is easy enough, but I had to start that Django development server dozens of times a day!

All it took was a slight adjustment to my `.bashrc` file:

    alias dj="/home/classam/code/django_project/manage.py"

And suddenly, regardless of my current working directory, I could spin up the server
with a simple

    $ dj runserver 0:8000

This was a new and heady feeling. This alias tool would serve me well. 

## Make
As I was building more and more subtle automation steps into my programming environments, 
it started to become unwieldy to have a separate file for each instruction I wanted
to perform. 

Enter the Make tool. 

Now, this is not at all what the make tool is for - Make is a build tool! But it served my
purposes quite well to lay out all of a project's automations in a make file, like:

    run:
        manage.py runserver 0:8000
    shell:
        manage.py shell
    test:
        nosetests /home/vagrant/code/things 
    lint:
        pyflakes /home/vagrant/code/things --no-bitching-about-long-lines

Then, I could run my coterie of assorted tasks without having to interact with a group
of disconnected shell files, like this:

    $ make lint

Ultimately, this wasn't _much_ more stable or convenient than shell files, and it introduced
a strange Make dependency on a bunch of projects for which a Make dependency didn't make 
any real sense. 

But it did introduce me to the next step in my journey: Instead of using scripts, try
a task-runner. 

## Fabric

Instead of a Make dependency, why not a dependency that also exists within the Python
universe? Enter Fabric, an excellent Python command runner. If you're more familiar 
with Ruby than Python, you might remember Fabric's evil counterpart, Capistrano. 

Fabric allowed me to structure my test runs like this: 

    from fabric.api import run
    
    def go():
        """
        Documentation documentation documentation.
        """
        run('manage.py runserver 0:8000')
    
    def test():
        run('nosetests /home/vagrant/code/things')
    
    def lint():
        run('pyflakes /home/vagrant/code/things --no-bitching-about-long-lines')

Which I could then call with a:

    $ fab go

There we go- all of our utility scripts in one convenient spot. 

This was pretty nice,
and I started using it for everything. Even the thorny problem
of deploying things to remote servers - which Fabric supports extremely well. 

Just tell it to SSH into a remote server, or a whole bunch of remote servers, at once, 
and fabric was able to run my scripts across all of them.

I started using Fabric to set up servers. New servers would need PostgreSQL and nginx,
and it was nice to have scripts to set those things up for me rather than having
to handle all of their fiddly installation steps by hand. 

## Ansible

Fabric is great, but there are a few things it doesn't do particularly well.

While debugging the Fabric tasks, I'd run
the tasks on the same server again and again and again, and each time it would
start from the beginning and run the whole script. If there was a slow step, 
it could add hours to my debugging process. 

Which meant that I would just comment out large chunks of my scripts as I worked
on them, testing out the "head" of the script while I left the tail commented
out, so that I could push further and further into my automations. 

There were lots of operations that would pass the first time they ran, and then
fail all subsequent times, like "create a database" or "create a directory" - 
these operations would fail the second time around because the database or
directory already existed the second time I ran them. 

The problem? Most Fabric operations lacked a quality called "idempotence" - 
a five dollar word that means "running the operation more than once 
always produces the same result."

Another problem was that of large configuration files. I'd find myself 
often either writing ugly Bash operations to perform minor surgery on 
large configuration files, or using string tools to build the configuration
files in Python and then drop them into place. 

One last problem? Credentials. It was never quite clear exactly where
credentials needed to be located in order to have systems working properly.
I eventually settled on environment variables, but it meant that every
new system that I'd set up, I'd immediately need to commit three dozen
variables immediately to a `.bashrc` file. 

This still worked pretty well, but we could do better. Fortunately, 
there was a framework out there that could run operations with these
properties, and still keep us firmly within the realm of Python. Ansible.

Ansible provides a YAML format that can be used to describe a working 
system, and then it will build that system over an SSH connection. That's
pretty nice - once I got over some of the 
[inherent disgust of 'coding' in YAML](http://cube-drone.com/comics/c/a-rant-about-markup)!
Ansible also contains, amongst other things, a full-featured 
templating system for creating configuration files, and a password
store with two-way encryption to stash important configuration credentials. 

Ansible solves a lot of problems.

## Invoke

But one problem that Ansible doesn't solve? It's not a very good task runner.

In fact, Ansible commands can be downright arcane to run. 

Running a playbook on a set of servers might look like this:

    ansible-playbook -i inventory.yml mongo -u root --vault-password-file ~/.vaultpw mongo.yml

I don't know about you, but that's not the sort of thing
that I can easily commit to muscle memory. 

So once again we have need of a task runner. We could go back to Fabric, but
the maintainers of Fabric have essentially abandoned it in favor of an 
extremely ambitious version 2.0 that is showing some definite signs
of [second-system effect](http://c2.com/cgi/wiki?SecondSystemEffect) - the 
most obvious symptom being that it's been in development for 4 years and
has yet to see the light of day.

So Fabric is out of the question for now - but while Fabric 2.0 is struggling
towards life, a half-completed chunk of Fabric 2.0 has emerged. And while
limited in functionality, it's ... much better than Fabric. 

It's called [Invoke](http://www.pyinvoke.org/) and it just provides the
"task runner" half of Fabric without providing any of the "ssh" or "deployment"
bits. But that's all we want! It's perfect! 

So we can wrap our ansible deployment like this:

    from invoke import task, run

    @task
    def provision_mongo(ctx):
        ctx.run('ansible-playbook -i inventory.yml mongo -u root --vault-password-file ~/.vaultpw mongo.yml')

And then run it like this:

    $ inv provision_mongo

And we can include that with the utility scripts that we use to run the 
rest of the application. 

    @task
    def go(ctx):     
        ctx.run('manage.py runserver 0:8000')
    
    @task
    def test(ctx):
        ctx.run('nosetests /home/vagrant/code/things')

Invoke has many more features, but covering them all would be beyond the scope
of this already over-long blog post.

## Conclusion

Clearly, my journey towards repeatable, useful project automation still
has a long way to go, but I've definitely found some very useful tools
in the intersection between `invoke` and `ansible`. 

We get all of the composability of Python, all of the practicality 
of Ansible, and all of the convenience of a task runner. 

## P.S.

One final gripe: Invoke supports Python 3 beautifully, but Ansible is still
tied to the Python Dark Ages of Python 2, so in order to run both Invoke and
Python together, we must accept an inferior Python.

Darn it. 

## P.P.S.

As far as I can tell, Javascript doesn't have anything like this. The closest I can find is `gulp`. Am I going to have to settle for `gulp`? Yegh. 