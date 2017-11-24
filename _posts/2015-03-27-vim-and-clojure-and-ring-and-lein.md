---
layout: code
title:  "vim and clojure and lein and ring and probably some other stuff"
date:   2015-03-27 20:20:00 -0500
tags: vim clojure 
---

So, the last post was mostly about vim, which is awesome, right? Right.

Now we're going to jump into clojure and vim and their compatriots. This isn't going to be a blog about clojure itself, so much as how to effectively use vim and vim-fireplace to interact with clojure and the lein repl. I'm going to assume you have a passing knowledge of clojure and the repl, but if not, you should checkout <a href="http://www.braveclojure.com/">Clojure for the Brave and True</a> by Daniel Higginbotham. It's a great intro to clojure, despite his use of Emacs. :)

If you're writing a clojure web app, you'll almost certainly be using ring, the clojure HTTP library. If you create a new project using something like
<code>lein new compojure-app myapp</code>
then leiningen will create a nice starter kit for you to develop in. The typical route from here might be to cd into the new directory and start lein with 
<code>lein ring server</code>
and have your web server start, but the problem is that your repl is now tied up. You could start a new prompt and tie into the repl that's already running, but that's a pain in the ass. If you just type
<code>lein repl</code> 
instead you should see that you get dropped into a myapp.repl namespace instead of the usual user namespace. This gives us a bit of extra power that we'll look at in a minute, but for now let's look at how we got here.

First, open the project.clj file using <code>vim project.clj</code> and then type <code>/profiles</code>

You should see a :profiles key with various profile definitions in it. Look for the :dev key. This tells lein how to behave while you're developing; what special dependencies to load for your dev environment, a source path to look on and which namespace to start your repl in, which is how we ended up in the myapp.repl namespace! lein added this stuff for us in the <code>:repl-options {:init-ns myapp.repl}</code> portion of the projects.clj file when it created the application template, but you could easily add this yourself to any project.clj file. So, if you are developing another app and want to automatically land in the namespace where you'll be starting your app, then you can add that information to the :init-ns key and lein will automatically put you in that namespace and then load that file, saving you from having to manually change namespaces and require things and all the other messy stuff you might have to type to get to where you want to be.

If you open the file in env/dev/clj/repl.clj you'll see that lein created a few helper functions for you. One of them is start-server, so if you 
<code>(start-server)</code> 
from the repl, your ring server will start up, your web app will automatically launch in your browser as it did when you started the repl with
<code>lein ring server</code>
but this time, your repl isn't tied up. You're free to type in it and play with your code all you like. Admittedly, there's not a lot in this namespace other than the code to stop and start the ring server, but things will certainly change as you develop. 

As you may have noticed, though, if you're going to develop code in a file, you'll have to keep a repl open along with your vim session and switch back and forth between the two. Wouldn't it be great if you could do one from the other? Enter Tim Pope's wonderful vim plugin <a href="https://github.com/tpope/vim-fireplace">vim-fireplace</a>. Tim Pope writes vim plugins at the same rate that Stephen King writes novels and I collect them the same way because they're always useful. If you've ever heard the Emacs kids talking about how great cider is (and I have to admit, it's pretty great), then you'll be pretty happy with vim-fireplace. While it doesn't have all the same benefits as cider, fireplace is incredibly useful. Once you have the plugin installed (just follow the directions on the site), you'll still need to start a repl through the command prompt and remember the port that it started on, then use vim to open a clojure file, say the repl.clj file. When that's loaded type <code>:Connect</code> (remember, the : tells vim you're about to issue a command) and you'll be prompted for the type of connection (choose nrepl), the host (localhost in this case) and the port (you did remember that number, right?). Congratulations! You're now connected to the repl through vim. 

So, now what? I suggest you read the README on the vim-fireplace github, but here's the TL;DR version. fireplace has a few ways to let you evaluate clojure forms from inside vim and it gives you a "quasi-repl" inside a vim minibuffer below the main buffers. If you type <code>:%Eval</code> in vim, fireplace will evaluate the entire file and push it onto the repl; doing this from the repl.clj file should return #'myapp.repl/stop-server in the minibuffer, letting you know the name of the last form that was evaluated. Let's test out the connection between vim and the repl.

We saw earlier that we could type
<code>(start-server)</code>
from the repl and our web server would start. We can do the same thing from inside vim. Go to the bottom of the file (shift-g takes you there quickly) and open a new line (type o while in Normal mode) the type <code>(start-server)</code> and hit ESC. Now, if you <code>:%Eval</code? again, fireplace will re-evaluate the entire file and run your command to start the server, just like from the repl! But, you may not want to re-evaluate the entire file every time so fireplace lets you evaluate one form at a time by just quickly typing cpp while in Normal mode. cpp evaluates the innermost form under the cursor, so if you had
<code>(def summy (reduce + [1 2 3]))</code>
and your cursor was somewhere inside the reduce form, then hitting cpp would evaluate to 6; if it was inside the vector, it would evaluate to the vector. To get it to evaluate the entire form, make sure your cursor is on the opening or closing parens or somewhere inside those parens but not inside any other form. Play around with it and you'll see what's up. 

We see that vim is tied to the repl, but does that connection go both ways? Lets find out. Again, open a new line in the repl.clj file and type
<code>(defn print-it [] "it!)</code> 
then hit ESC and type cpp. In the minibuffer you should see something like #'myapp.repl/print-it. This lets us know that fireplace has evaluated this new function to the repl. Now, switch over to the repl at your command prompt and type 
<code>(print-it)</code>
You should see "it!" in the repl. So, yep, the connection flows both ways. This is a really powerful tool for repl driven development because you can create functions in a file, evaluate them into the repl and then either play with them inside the clojure file or switch over to the repl and play with them there. 

There's one more neat trick that ring supplies to help us with development. By default in development mode, ring will listen for changes to your files and then automatically reload them, so you don't have to re-evaluate your code; just type and save and ring will pick up the changes and you'll see them when you refresh your browser page. You can even tell ring to automatically refresh your browser for you by add this to the :dev profile key in your project.clj
<code>:ring{
   :auto-refresh? true}
</code>

Whew, if you read through the vim intro and this post, you've taken in a lot. Take a break from reading and start writing some code. You should be on your way to creating a solid clojure development environment using vim and vim-fireplace. Feedback is great, btw, so let me know what you think in the comments or on twitter @_gignosko_

