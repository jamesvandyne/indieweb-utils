Usage
=====

.. _installation:

.. toctree::

   :ref:"Installation"
   :ref:"Custom Properties"

Installation
------------------------

To install this package, run the following command:

.. code-block:: python

    pip install indieweb-utils

You can import the package using the following line of code:

.. code-block:: python

    import indieweb_utils

Find a Post Type
------------------------

To find the post type associated with a web page, you can use the `get_post_type` function.

The `get_post_type` function function uses the following syntax:

.. autofunction:: indieweb_utils.get_post_type

Here are the arguments you can use:

- `url`: The URL of the web page to discover the post type of.
- `custom_properties`: A list of custom properties to use when discovering the post type. This list must contain tuples.

The function returns a single string with the post type of the specified web page.

See the Post Type Discovery specification for a full list of post types.

Custom Properties
------------------------

The structure of the custom properties tuple is:

.. code-block:: python

   (attribute_to_look_for, value_to_return)

An example custom property value is:

.. code-block:: python

    custom_properties = [
        ("poke-of", "poke")
    ]

This function would look for a poke-of attribute on a web page and return the "poke" value.

By default, this function contains all of the attributes in the Post Type Discovery mechanism.

Custom properties are added to the end of the post type discovery list, just before the "article" property. All specification property types will be checked before your custom attribute.

Here is an example of the `get_post_type` function in use:

.. code-block:: python

    import indieauth_helpers

    url = "https://jamesg.blog/2021/12/06/advent-of-bloggers-6/"

    post_type = indieweb_utils.post_type_discovery.discover_endpoint(url)

    print(post_type)

This code returns the following string:

.. code-block:: python

    article

Discover a Webmention Endpoint
------------------------------------

Webmention endpoint discovery is useful if you want to know if you can send webmentions to a site or if you want to send a webmention to a site.

You can discover if a URL has an associated webmention endpoint using the `discover_webmention_endpoint` function:

.. autofunction:: indieweb_utils.discover_webmention_endpoint

Here is an example of the function in action:

.. code-block:: python

    import indieauth_helpers

    url = "https://jamesg.blog/2021/12/06/advent-of-bloggers-6/"

    url, message = indieauth_helpers.webmention.discover_webmention_endpoint(url)

    print(url)

If successful, this function will return the URL of the webmention endpoint associated with a resource. The message value will be a blank string in this acse.

If a webmention endpoint could not be found, URL will be equal to None. A string message value will be provided that you can use for debugging or present to a user.

Canonicalize a URL
------------------------

Canonicalization turns a relative URL into a complete URL.

To canonicalize a URL, use this function:

.. autofunction:: indieweb_utils.canonicalize

This function requires two arguments.

- `url`: The URL to canonicalize.
- `domain`: The domain to use during canonicalization.

This function will return a URL with a protocol, host, and path.

The domain of the resource is needed so that it can be added to the URL during canonicalization if the URL is relative.

A complete URL returned by this function will look like this:

.. code-block:: python

    https://indieweb.org/POSSE

Discover an Article Author
------------------------------------

You can discover the original author of an article as per the Authorship Specification.

To do so, use this function:

.. autofunction:: indieweb_utils.discover_author

Here are the arguments you can use:

- `url`: The URL of the web page whose author you want to discover.
- `page_contents`: The unmodified HTML of a web page whose author you want to discover.

The page_contents argument is optional.

If no page_contents argument is specified, the URL you stated will be retrieved and authorship inference will begin.

If you specify a page_contents value, the HTML you parsed will be used for authorship discovery. This will save on a HTML request if you have already retrieved the HTML for another reason (for example, if you need to retreive other values in the page HTML). You still need to specify a URL even if you specify a page_contents value.

The discover_author function can return one of two values:

- An author name (i.e. "James").
- The h-card of an author.

These are the two outputs defined in the authorship inference algorithm. Your program should be able to handle both of these outputs.

Here is an example of the `discover_author` function in action:

.. code-block:: python

    import indieauth_helpers

    url = "https://aaronparecki.com/2021/12/07/8/drone"

    post_type = indieauth_helpers.authorship_discovery.discover_author(url)

    print(post_type)

This code returns the following h-card:

.. code-block:: python

    {
        'type': ['h-card'],
        'properties': {
            'url': ['https://aaronparecki.com/'],
            'name': ['Aaron Parecki'],
            'photo': ['https://aaronparecki.com/images/profile.jpg']
        },
        'value': 'https://aaronparecki.com/'
    }

Handle an IndieAuth Callback Request
------------------------------------

The last stage of the IndieAuth authentication flow for a client is to verify a callback response and exchange the provided code with a token.

This function implements a callback handler to verify the response frmo an authorization server and redeem a token.

To use this function, you need to pass in the following arguments:

.. autofunction:: indieweb_utils.indieauth_callback_handler

This function verifies that an authorization server has returned a valid response and redeems a token.

You can leave the "me" value equal to None if any URL should be able to access your service. Otherwise, set "me" to the URL of the profile that should be able to access your service. Setting a me value other than None may be useful if you are building personal services that nobody else should be able to access.

If successful, this function will return a None value for "message" and the JSON object returned by an IndieAuth authorization server. This JSON object should look like this:

.. code-block:: python

    {
        "me": "https://jamesg.blog/",
        "access_token": "ACCESS_TOKEN",
        "scope": "SCOPE_LIST"
    }

The function will return a message and a None value for response if there was an error. The message value tells you what went wrong during the token verification process.

Generate Reply Context
------------------------

To generate reply context for a given page, use the following function:

.. autofunction:: indieweb_utils.get_reply_context

This function will return a dictionary with the following keys:

.. code-block:: python

    {
        "p-name": "The title of the page",
        "post_body": "The body of the page",
        "author_name": "The name of the author",
        "author_url": "The URL of the author",
        "author_image": "The URL of the author's photo",
    }

A None value will be specified if any of the above attributes cannot be found.

Find the Original Version of a Post
------------------------------------

To find the original version of a post per the Original Post Discovery algorithm, use this code:

.. autofunction:: indieweb_utils.discover_original_post

This function will return the URL of the original version of a post, if one is found. Otherwise, None will be returned.