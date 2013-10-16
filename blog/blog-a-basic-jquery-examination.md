An email I sent to iiNet earlier this evening. It bounced: `Sorry, no mailbox here by that name. (#5.1.1)` ; so posting it here instead!

----

Hi there,

Thought I would give you a heads up on a bug I found on your site.

My BoB2 modem stopped working earlier this evening - it appears to
have done a factory reset of itself without me asking it to.

That led me to get redirected to
https://toolbox.iinet.net.au/cgi-bin/playpen/boblogin.cgi when I
visited any page. I submitted the login form, as requested by the
page, and absolutely nothing happened - no network activity.

Thus I began to investigate, while waiting on the line for tech support.

You will find below part of the HTML markup, and part of the main
Javascript file that was loaded.

HTML:

    <div>
        <p id="username_prompt">
            Username: <input type="text" name="username" id="boblogin_username" maxlength="50" size="30">
        </p>
        <p id="password_prompt">
            Password: <input type="password" name="password" id="boblogin_password" maxlength="50" size="30">
        </p>
        <div id="boblogin_result"></div>
        <p>
            <input type="button" id="login_button" class="toolbox_button" value="configure">
            <br><br><span id="loading_image" style="display: none;"><img id="loading_image" src="https://toolbox.iinet.net.au/images/Throbber.gif"></span>
        </p>
        <p>
            <a href="https://toolbox.iinet.net.au/cgi-bin/playpen/9pointidcheck.cgi" target="__blank">Forgot login details?</a>
        </p>
    </div>

Javascript:

    init = function() {
        $('login_button').onclick = function() { checkLogin(); };
    };

Quickly spotted the bug - if I run:


    $('login_button')
    []


If I run instead:

    $('#login_button')
    [<input type="button" id="login_button" class="toolbox_button"
    value="configure">]

It thus appears that $ is jQuery, and you have simply omitted the # in
the selector. This is required whenever selecting based on id
attributes.

That was the main bug, which prevented this page from working.

Further to that, I would suggest a few more improvements.

Again, the same function:

    init = function() {
        $('login_button').onclick = function() { checkLogin(); };
    };

Firstly, wrapping checkLogin in a function is extraneous, you could do instead:

    $('#login_button').onclick = checkLogin;

... as checkLogin is itself a function.

In idiomatic jQuery:

    $('#login_button').click(checkLogin);

Secondly init is declared without using var, and thus pollutes the
global scope. This is bad practice, and the init function is not the
only offender here - all of the functions in this file do the same. On
a relatively simple page like this, it makes little difference, but
should it get more complex, this can cause weird side effects.
Consider not only using var, but also placing these functions within a
scope. A rather nice explanation:
[http://stackoverflow.com/a/13352212/194982](http://stackoverflow.com/a/13352212/194982)

Finally, this init function is invoked from the HTML markup using:

    <script type="text/javascript">
        init();
    </script>

One cannot guarantee that the contents of this script tag will be run
after the DOM is rendered. If it so happens that this runs before the
DOM is rendered, the selector will return an empty set, and the click
handler will not be set on the intended button.

Consider instead using:

    $(document).ready(init);

... within the Javascript file. The additional benefit of this is that
we do not need any in line scripts defined in the markup.

Cheers,

Brendan
