===================
Admin Customization
===================

Mezzanine uses the standard `Django admin interface 
<http://docs.djangoproject.com/en/dev/ref/contrib/admin/>`_ allowing you to 
add admin classes as you normally would with a Django project, but also 
provides the following enhancements to the admin interface that are 
configurable by the developer. 

Navigation
==========

When first logging into the standard Django admin interface a user is 
presented with the list of models that they have permission to modify data 
for. Mezzanine takes this feature and uses it to provide a navigation menu 
that persists across every section of the admin interface making the list 
of models always accessible.

Using the standard Django admin the grouping and ordering of these models 
aren't configurable, so Mezzanine provides the setting 
``ADMIN_MENU_ORDER`` that can be used to control the grouping and 
ordering of models when listed in the admin area. 

This setting is a sequence of pairs where each pair represents a group of 
models. The first item in each pair is the name to give the group and the 
second item is the sequence of app/model names to use for the group. The 
ordering of both the groups and their models is maintained when they are 
displayed in the admin area.

For example, to specify two groups ``Content`` and ``Site`` in your admin 
with the first group containing models from Mezzanine's ``pages`` and 
``blog`` apps, and the second with the remaining models provided by Django, 
you would define the following in your projects's ``settings`` module::

    ADMIN_MENU_ORDER = (
        ("Content", ("pages.Page", "blog.BlogPost", "blog.Comment",)),
        ("Site", ("auth.User", "auth.Group", "sites.Site", "redirects.Redirect")),
    )

Any admin classes that aren't specifed are included using Django's normal 
approach of grouping models alphabetically by application name.

Custom Items
============

It is possible to inject custom navigation items into the 
``ADMIN_MENU_ORDER`` setting by specifying an 
item using a two item sequence, the first item containing the title and 
second containing the named urlpattern that resolves to the url to be used. 

Continuing on from the previous example, Mezzanine includes a fork of the 
popular `django-filebrowser <http://code.google.com/p/django-filebrowser/>`_ 
application which contains a named urlpattern ``fb_browse`` and is given 
the title ``Media Library`` to create a custom navigation item::

    ADMIN_MENU_ORDER = (
        ("Content", ("pages.Page", "blog.BlogPost", "blog.Comment",
            ("Media Library", "fb_browse"),)),
        ("Site", ("auth.User", "auth.Group", "sites.Site", "redirects.Redirect")),
    )


Dashboard
=========

When using the standard Django admin interface, the dashboard area shown 
when a user first logs in provides the list of available models and a list 
of the user's recent actions. Mezzanine makes this dashboard configurable 
by the developer by providing a system for specifying Django `Inclusion Tags 
<http://docs.djangoproject.com/en/dev/howto/custom-template-tags/#inclusion-tags>`_ 
that will be displayed in the dashboard area. 

The dashboard area is broken up into three columns, the first being wide and 
the second and third being narrow. Mezzanine then provides the setting 
``DASHBOARD_TAGS`` which is a sequence of three sequences - one for 
each the three columns. Each sequence contains the names of the inclusion 
tags in the format ``tag_lib.tag_name`` that will be rendered in each of the 
columns . 

The list of models and recent actions normally found in the Django admin are 
available as inclusion tags via ``mezzanine_tags.app_list`` and 
``mezzanine_tags.recent_actions`` respectively. For example, to configure the 
dashboard with a blog form above the model list in 
the first column, a list of recent comments in the second column and the 
recent actions list in the third column, you would define the following in 
your projects's ``settings`` module::

    DASHBOARD_TAGS = (
        ("blog_tags.quick_blog", "mezzanine_tags.app_list"),
        ("blog_tags.recent_comments",),
        ("mezzanine_tags.recent_actions",),
    )

Here we can see the ``quick_blog`` and ``recent_comments`` inclusion tags 
which are provided by the ``mezzanine.blog.templatetags.blog_tags`` module.
