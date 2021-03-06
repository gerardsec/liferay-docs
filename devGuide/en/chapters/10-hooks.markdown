# Customizing and Extending Functionality with Hooks [](id=customize-and-extend-functionality-hooks-liferay-portal-6-2-dev-guide-en)

---

![Note](../../images/tip-pen-paper.png) This chapter has not yet been updated to
reflect the new features in Liferay 6.2. 

---

Liferay Hooks are the best plugin for customizing Liferay's core features. If
possible, use hooks whenever you need to override Liferay's core functionality.
It's possible to use Ext plugins for many of the same tasks, but hooks are
hot-deployable and more forward compatible, so we urge you to use them
preferentially. 

In this chapter, we'll learn how to create hooks and we'll explore their most
common uses.

We'll cover the following topics:

- Creating a Hook
- Overriding Web Resources
- Customizing JSPs Without Modifying the Original
- Customizing Sites and Site Templates with Application Adapters 
- Performing a Custom Action 
- Overriding and Adding Struts Actions 
- Extending and Overriding *portal.properties* 
- Overriding a Portal Service
- Overriding a *Language.properties* file
- Other Hooks

As with portlets, layout templates, and themes, the easiest way to create and
manage hooks is via Liferay IDE or Developer Studio. However, if you don't want
to use an IDE, you can use the terminal. We'll demonstrate how to create and
deploy a hook using both Liferay IDE and the terminal.

## Creating a Hook [](id=creating-a-hook-liferay-portal-6-2-dev-guide-10-en)

Regardless of whether you use Liferay IDE or your terminal to create hooks,
hooks projects are stored in the Plugins SDK's `hooks` directory. 

***Using Liferay IDE:***

1.  Go to *File* &rarr; *New* &rarr; *Liferay Project*. 

2.  Enter *example* for your Project name and *Example* for your Display name. 

3.  Select the Plugins SDK and Portal Runtime you've configured. 

4.  Select the *Hook* Plugin Type. 

5.  Click *Finish*. 

![Figure 10.1: Creating a hook plugin is easy with Liferay IDE. Just click *File* &rarr; *New* &rarr; *Liferay Project*, enter a project name and display name, select a Plugins SDK and Liferay Runtime, select *Hook*, then click *Finish*.](../../images/06-hooks-1.png)

The Plugins SDK automatically names the hook by appending "-hook" to the project
name. With Liferay IDE, you can create a hook in a completely new plugin project
or create a hook in an existing plugin project. Use *File* &rarr; *New* &rarr;
*Liferay Project* to create a new plugin project and *File* &rarr; *New* &rarr;
*Liferay Hook* to create a hook in an existing plugin project.

***Using the terminal:*** Navigate to your Plugins SDK directory in a terminal
and enter the appropriate command for your operating system:

1.  In Linux and Mac OS X, enter

        ./create.sh example "Example"

2.  In Windows, enter

        create.bat example "Example"

A BUILD SUCCESSFUL message from Ant tells you there's a new folder named
`example-hook` inside the Plugins SDK's `hooks` folder. The Plugins SDK
automatically named the hook by appending "-hook" to the project name. 

Now that you've created a hook, let's go ahead and deploy it.

### Deploying the Hook [](id=deploying-the-hook-liferay-portal-6-2-dev-guide-10-en)

***Using Liferay IDE:*** Click and drag your hook project onto your server.
Upon deployment, your server displays messages indicating that your hook was
read, registered and is now available for use. 

    Reading plugin package for example-hook
    Registering hook for example-hook
    Hook for example-hook is available for use

If you ever need to redeploy your plugin while in Liferay IDE, right-click
your plugin's icon located underneath your server and select *Redeploy*. 

***Using the terminal:*** Open a terminal window in your `hooks/example-hook`
directory and enter

    ant deploy

A BUILD SUCCESSFUL message indicates your hook is now being deployed. If you
switch to the terminal window running Liferay, in a few seconds you
should see the message "Hook for example-hook is available for use". 

 ![Note](../../images/tip-pen-paper.png) **Note:** When we created portlets and
 themes, they were fully functional upon deployment. Hooks aren't like that
 because they're Liferay customizations. The default customization is the
 original implementation! 

### Anatomy of the Hook [](id=anatomy-of-the-hook-liferay-portal-6-2-dev-guide-10-en)

To make your hook useful, you need to customize something in Liferay. You begin
by mirroring the structure of Liferay's code that you plan to customize. A hook
plugin is built to contain this: 

- `example-hook/`
    - `docroot/WEB-INF/src/`
    - `docroot/`
        - `META-INF/`
            - `MANIFEST.MF`
        - `WEB-INF/`
            - `lib/`
            - `liferay-hook.xml`
            - `liferay-plugin-package.properties`
            - `web.xml`
    - `build.xml`

In Liferay IDE's *Package Explorer*, here's what the hook structure looks
like:

![Figure 10.2: In Liferay IDE, the folder structure of a newly created hook plugin looks like this.](../../images/06-hooks-3.png)

The particular files you'll work on depend on the Liferay features you're
overriding with your hook. We'll start by making one of the most common hook
plugin customizations: a customization of Liferay's web resources. 

## Overriding Web Resources [](id=overriding-web-resources-with-hook-liferay-portal-6-2-dev-guide-en)

Hooks are commonly used to override web resources, found in `portal-web` in
Liferay's source. You can use a hook to override JSP files, JSPF files,
JavaScript files, CSS files, or images. 

---

 ![Important](../../images/tip-pen-paper.png) **Important:** Some resources have
 additional requisites:

 - ***JSPF:*** Changes won't take effect unless you modify the JSP that
   includes it. 
 - ***CSS:*** When modifying a CSS file imported by another CSS file, the
 changes won't take effect unless you modify the parent CSS file (usually
 `main.css`). 

---

Replacing a portal JSP is a simple task with hooks. Let's create and deploy a
hook to modify your portal's *Terms of Use* page. 

1.  Create the directory `hooks/example-hook/docroot/META-INF/custom_jsps`.

2.  Edit `hooks/example-hook/docroot/WEB-INF/liferay-hook.xml` by adding the
    following line between `<hook></hook>`:

        <custom-jsp-dir>/META-INF/custom_jsps</custom-jsp-dir>

    When your hook is deployed, any JSP you put in the `custom_jsps` directory
    replaces a corresponding JSP with the same name and relative path found
    inside your Liferay instance. The directory structure inside this folder
    must mirror the one within
    `liferay-portal-[version]/tomcat-[tomcat-version]/webapps/ROOT`. 

3.  Create the directory
    `hooks/example-hook/docroot/META-INF/custom_jsps/html/portal/`, then place
    in it a copy of the `terms_of_use.jsp` file from
    `liferay-portal-[version]/tomcat-[tomcat-version]/webapps/ROOT/html/portal/`.

4.  Open your copy of the `terms_of_use.jsp` and modify as necessary. 

5.  Deploy your hook and wait until it is deployed successfully. 

6.  Create a new user and log in. The *Terms of Use* page should include the
    changes you made above. 

<!--
The steps above work but we should warn the reader that lots of errors appear
when editing the terms_of_use.jsp in Eclipse since the required imports don't
exist in the hook project; they're in Liferay. -Jesse, 9/27/2013
-->

Now there are two *Terms of Use* JSP files in the
`liferay-portal-[version]/tomcat-[tomcat-version]/webapps/ROOT/html/portal`
directory. One is called `terms_of_use.jsp` and another
`terms_of_use.portal.jsp`. `terms_of_use.jsp` is your hook's version, while
`terms_of_use.portal.jsp` is the original. To revert back to the original,
undeploy your hook. Your replacement JSP is removed, and the `.portal.jsp` file
is automatically renamed, taking its place. You can override any JSP in the
Liferay core, while retaining thew ability to easily revert your changes.
However, it's not possible to override the same JSP from multiple hooks; Liferay
won't know which version to use. 

---

 ![Note](../../images/tip-pen-paper.png) **Note:** We don't recommend changing
 Liferay's *Terms of Use* with a hook. You can replace the *Terms of Use* with a
 piece of web content simply by specifying values for these two properties in
 `portal-ext.properties`:

    terms.of.use.journal.article.group.id=
    terms.of.use.journal.article.id=

 Although our example hook doesn't provide any new functionality, it
 demonstrates how to override Liferay's JSP files. 

---

Next, we'll look at a different way to customize a JSP.

## Customizing JSPs Without Overriding the Original [](id=customize-jsp-without-overriding-original--liferay-portal-6-2-dev-guide-en)

If we can override a JSP with a hook plugin, why learn another way to accomplish
the same thing? Good question. Each time the original (overridden) file is
updated by Liferay (for example, to fix a bug) after you override it, you'll
have to manually make the same changes to your customized file if you want to
receive the update. 

You can avoid this drawback and make your JSP modifications less invasive by
rendering the original JSP into a string and modifying it dynamically
afterwards. This way, you can change minor elements of a JSP, like adding a new
heading or button, without modifying your hook every time you upgrade Liferay.
Here's an example that customizes the Search portlet. Specifically, it adds
some helpful text to aid the user in searching for content. Since this technique
involves string manipulation, it's mainly useful for making a small number of
changes to a JSP.

1.  Copy the
    `[LIFERAY_HOME]/tomcat-[version]/webapps/ROOT/html/portlet/search/search.jsp`
    file to your hook project: First, create the
    `docroot/META-INF/custom_jsps/html/portlet/search` folder in your hook
    project and then copy the `search.jsp` to this folder.

2.  Append the following code to the end of the `search.jsp` file:

        <liferay-util:buffer var="html">
            <liferay-util:include page="/html/portlet/search/view.portal.jsp" />
        </liferay-util:buffer>

        <%
        html = StringUtil.add(
            html, 
            "Didn't find what you were looking for? Refine your search and " +
                "try again!",
            "\n");
        %>

        <%= html %>

3.  Make sure your Liferay server is running and redeploy your hook plugin.

4.  Add the *Search* portlet to a page by selecting *Add* &rarr; *Applications*
    &rarr; *Tools* &rarr; *Search*.

5.  Input some text into the search field and click *Search*.

Your custom string is now displayed at the bottom of the Search portlet.

![Figure 10.3: After customizing the JSP file in your hook and redeploying, your custom string is displayed at the bottom of the Search portlet.](../../images/jsp-search-string.png)

Next, we'll explore application adapters and what they can do for your sites and
site templates.

## Customizing Sites and Site Templates with Application Adapters [](id=application-adapter-hook-liferay-portal-6-2-dev-guide-en)

The JSP hooks that we've demonstrated so far are scoped to the portal. What if
you need to customize specific sites without propagating the customizations
throughout the entire portal? You can! *Application Adapters* are special hooks
that let you make changes at the site level. They are used for overriding JSPs. 

There's a Sample Application Adapter in the [Liferay Plugins Repository](https://github.com/liferay/liferay-plugins/tree/master/hooks/sample-application-adapter-hook).
How do we build an Application Adapter of our own? 

To create an Application Adapter, you need a hook with custom JSPs, and you need
to turn the hook's global custom JSP setting off. You can do this by configuring
your `liferay-hook.xml` with the following directives: 

    <custom-jsp-dir>/META-INF/custom_jsps</custom-jsp-dir>
    <custom-jsp-global>false</custom-jsp-global>

When you deploy your hook, Liferay installs the Application Adapter under the
name of the hook. An Application Adapter hook named *Foo* becomes available to
sites and site templates under the name *Foo Hook*. 

Now, let's discuss the perks of including the original JSP when overriding it.

### Including an Original JSP [](id=including-an-original-jsp-with-hook-liferay-portal-6-2-dev-guide-en)

If you override a JSP from the portal, we recommend you include the original
JSP (when possible). 

As we already demonstrated, including the original JSP file for global hooks is
accomplished by referencing the original JSP file from a
`<liferay-util:include>` tag and appending the suffix `.portal.jsp` to the
original file's name. Here's what including the original Navigation portlet's
view JSP in a global hook looks like:

    <%@ taglib uri="http://liferay.com/tld/util" prefix="liferay-util" %>

    <liferay-util:include page="/html/portlet/navigation/view.portal.jsp" />

For Application Adapter hooks, we include the original JSP by setting the
`<liferay-util:include>` tag's `useCustomPage` attribute to `false`, as below:

    <%@ taglib uri="http://liferay.com/tld/util" prefix="liferay-util" %>

    <liferay-util:include
        page="/html/portlet/navigation/view.jsp"
        useCustomPage="false"
    />

The view JSP is specified as `view.jsp`, *not* `view.portal.jsp` as for global
hooks. In the next section, we'll create and test an application adapter. 

### Creating an Application Adapter [](id=create-an-application-adapter-hook-liferay-portal-6-2-dev-guide-10-en)

Let's create an Application Adapter hook named `example-hook`. It will override
the Navigation portlet's `view.jsp`, while including the original Navigation
portlet's JSP with some custom text after its contents.

Here's how we do it:

1.  Modify your hook's `liferay-hook.xml` to specify the location of your custom
    JSP and set the global custom JSP setting to `false`:

        <hook>
            <custom-jsp-dir>/META-INF/custom_jsps</custom-jsp-dir>
            <custom-jsp-global>false</custom-jsp-global>
        </hook>

2.  Create a `docroot/META-INF/custom_jsps/html/portlet/navigation` directory in
    your hook project, create a new `view.jsp` file in this directory, and
    insert code to include the original JSP:

        <%@ taglib uri="http://liferay.com/tld/util" 
        prefix="liferay-util" %>

        <liferay-util:include
            page="/html/portlet/navigation/view.jsp" 
            useCustomPage="false"
        />

        <p>
        This was modified by the Example Application Adapter.
        </p>

3.  Deploy your Application Adapter hook plugin. 

4.  In your web browser, navigate to the Liferay site where you'll use the
    Application Adapter. 

5.  Select *Admin* &rarr; *Configuration* to access the Site Settings section of
    the Site Administration interface. From the *Application Adapter* field's
    drop-down selector menu, select *Example*. Then click *Save*. 

    ![Figure 10.4: Your *Application Adapters* are easily accessible from the Site Settings section of the Site Administration interface.](../../images/06-hooks-select-site-app-adapter.png)

6.  Navigate to your site's pages, add the Navigation portlet to a page, and
    make sure that the modification message from your Application Adapter hook
    plugin's `view.jsp` file is displayed there.

7.  Navigate to a different site's Navigation portlet to verify that only the
    content of the portlet's *original* `view.jsp` file displays. 

Using Application Adapter hook plugins to override Liferay's core functionality
at the site scope is easy!

You can also apply Application Adapters to Site Templates. 

Suppose you want to make an Enterprise Resource Planning (ERP) solution for a
company's departments. Your ERP solution requires an extension of Liferay's Wiki
portlet so you implement that extension as an Application Adapter. Then you
incorporate the Application Adapter in a Site Template (named *ERP site*) for
the company's ERP sites. The company's administrative user creates the sites by
going to *Control Panel* &rarr; *Sites* and adding sites based on the "ERP site"
template. The added sites include your Application Adapter automatically. 

That's it for Application Adapters. Let's learn about performing custom actions
through hooks. 

## Performing a Custom Action [](id=perform-a-custom-action-with-hook-liferay-portal-6-2-dev-guide-en)

Hooks are useful for triggering custom actions on common portal events, like
user login or system startup. The actions for each of these events are defined
in `portal.properties` so we need to extend this file to create a custom action.
Hooks make this a simple task.

1.  In your hook project, create the directory
    `docroot/WEB-INF/src/com/liferay/sample/hook` and create a file called
    `LoginAction.java` inside it with this content:

        package com.liferay.sample.hook;

        import com.liferay.portal.kernel.events.Action;
        import javax.servlet.http.HttpServletRequest;
        import javax.servlet.http.HttpServletResponse;

        public class LoginAction extends Action {
            public void run(HttpServletRequest req, HttpServletResponse res) {
                System.out.println("## My custom login action");
            }
        }

2.  Create a `portal.properties` file inside your hook project's
    `docroot/WEB-INF/src` folder with this content:

        login.events.pre=com.liferay.sample.hook.LoginAction

3.  Edit your `docroot/WEB-INF/liferay-hook.xml` file, adding the following line
    above `<custom-jsp-dir>`:

        <portal-properties>portal.properties</portal-properties>

4.  Redeploy your hook. Once deployment is complete, log out and back in, and
    you should see your custom message, *## My custom login action*, displayed
    in the terminal window running Liferay. 

Custom action hook plugins aren't limited to the login event. You can define
custom actions for other events, too. For actions that require access to the
`HttpServletRequest` object, extend `com.liferay.portal.kernel.events.Action`;
for others, extend `com.liferay.portal.struts.SimpleAction`.

---

 ![Important](../../images/tip-pen-paper.png) **Important:** For better forward
 compatibility, use hooks to customize Struts actions rather than Ext plugins.

---

Extending and overriding portal properties is just as easy, so let's do that
next.

## Extending and Overriding *portal.properties* [](id=extend-override-portal-properties-liferay-portal-6-2-dev-guide-en)

In our hook that created a custom login action, we modified the
`login.events.pre` portal property. This property accepts *multiple* values, so
our value was appended to the existing `login.events.pre` values. We can
repeatedly modify the property from additional hooks because it accepts
multiple values. Some portal properties only accept a *single* value, such as
the `terms.of.use.required` property, which is either `true` or `false`. Only
modify single value properties from a single hook plugin; otherwise Liferay
won't know which value to use.

---

 ![Note](../../images/tip-pen-paper.png) **Note:** Hooks support customizing a
 specific list of predefined properties.  For a list of portal properties that
 can be overridden via hook, see the
 [liferay-hook_6_2_0.dtd](http://docs.liferay.com/portal/6.2/definitions). In
 addition to defining custom actions, hooks can override portal properties to
 define model listeners, validators, generators, and content sanitizers. If you
 want to customize a property that's not found in this list, you must use an Ext
 plugin (see the chapter of this guide on Ext plugins). For more information
 about the properties themselves, you can view an online version of Liferay's
 [portal properties](http://docs.liferay.com/portal/6.2/propertiesdoc/portal.properties.html)
 file.

---

Next, let's learn how to override and add Struts actions from a hook plugin. 

## Overriding and Adding Struts Actions [](id=override-and-add-struts-actions-with-hook-liferay-portal-6-2-dev-guide-en)

Do you want to add a new Struts action to Liferay or override existing Struts
actions? *Struts action hooks* let you do just that. 

Let's consider the interfaces used for Struts actions. There are two:

- `com.liferay.portal.kernel.struts.StrutsAction`
- `com.liferay.portal.kernel.struts.StrutsPortletAction`

The `StrutsAction` interface is for regular Struts actions from the portal, like
`/c/portal/update_email_address`. The `StrutsPortletAction` interface is used
for similar Struts actions, but from portlets. 

Struts actions are defined as classes, and they're all connected in a
`struts-config.xml` file. The `struts-config.xml` for a Liferay Portal instance
running on Apache Tomcat can be found in the
`liferay-portal-[version]/tomcat-[version]/webapps/ROOT/WEB-INF` directory. The
`struts-config.xml` file links actions to specific JSP pages. Each action
performs a specific task and then returns a *forward*, an object containing a
name and path. The forward defines the page the portal sends the user to after
the action completes. When a user submits a form that maps to one of these
actions, the action class is loaded, executed, and returns a forward. 

A Struts action hook can wrap or override existing Struts actions or create a
new Struts path; we'll demonstrate both here. We'll override the Struts actions
in the `struts-config.xml` file by using a Struts action hook to point to a
custom class. Then we'll create a new Struts path: `/c/portal/sample` and
navigate to it. Let's get started! 

First, let's override the Sign In portlet's Struts action using the example-hook
project that we've been using in this chapter. 

Here's the current action in your portal's `struts-config.xml` file:

    <action path="/login/login"
        type="com.liferay.portlet.login.action.LoginAction">

        <forward
            name="portlet.login.login"
            path="portlet.login.login"
        />
        <forward
            name="portlet.login.login_redirect"
            path="portlet.login.login_redirect"
        />
    </action>

1.  Navigate to your `example-hook/docroot/WEB-INF` folder and open
    `liferay-hook.xml`. 

2.  Insert the following code before the closing `</hook>` tag:

        <struts-action>
            <struts-action-path>/portal/sample</struts-action-path>
            <struts-action-impl>
            com.liferay.sample.hook.action.ExampleStrutsAction
            </struts-action-impl>
        </struts-action>
        <struts-action>
            <struts-action-path>/login/login</struts-action-path>
            <struts-action-impl>
            com.liferay.sample.hook.action.ExampleStrutsPortletAction
            </struts-action-impl>
        </struts-action>

3.  Create a new package `com.liferay.sample.hook.action` in your
    `example-hook/docroot/WEB-INF/src` folder. 

4.  In your new package, create a class named *ExampleStrutsPortletAction*, which
    will wrap the login portlet Struts action. Insert the following code:

        package com.liferay.sample.hook.action;

        import com.liferay.portal.kernel.struts.BaseStrutsPortletAction;
        import com.liferay.portal.kernel.struts.StrutsPortletAction;
        import com.liferay.portal.theme.ThemeDisplay;
        import com.liferay.portal.kernel.util.WebKeys;
        import javax.portlet.ActionRequest;
        import javax.portlet.ActionResponse;
        import javax.portlet.PortletConfig;
        import javax.portlet.RenderRequest;
        import javax.portlet.RenderResponse;
        import javax.portlet.ResourceRequest;
        import javax.portlet.ResourceResponse;

        public class ExampleStrutsPortletAction extends BaseStrutsPortletAction {

            public void processAction(
                    StrutsPortletAction originalStrutsPortletAction,
                    PortletConfig portletConfig, ActionRequest actionRequest,
                    ActionResponse actionResponse)
                throws Exception {
                ThemeDisplay themeDisplay =
                    (ThemeDisplay)actionRequest.getAttribute(WebKeys.THEME_DISPLAY);

                Long currentuser = themeDisplay.getUserId();

                if (currentuser != null) {
                    System.out.println("Wrapped /login/ action2");

                }
                originalStrutsPortletAction.processAction(
                    originalStrutsPortletAction, portletConfig, actionRequest,
                    actionResponse);
            }

            public String render(
                    StrutsPortletAction originalStrutsPortletAction,
                    PortletConfig portletConfig, RenderRequest renderRequest,
                    RenderResponse renderResponse)
                throws Exception {

                System.out.println("Wrapped /login/ action");

                return originalStrutsPortletAction.render(
                    null, portletConfig, renderRequest, renderResponse);

            }

            public void serveResource(
                    StrutsPortletAction originalStrutsPortletAction,
                    PortletConfig portletConfig, ResourceRequest resourceRequest,
                    ResourceResponse resourceResponse)
                throws Exception {

                originalStrutsPortletAction.serveResource(
                    originalStrutsPortletAction, portletConfig, resourceRequest,
                    resourceResponse);

            }

        }

5.  Create a new class named `ExampleStrutsAction` in the
    `com.liferay.sample.hook.action` package. It will implement your new portal
    Struts action. Insert the following code:

        package com.liferay.sample.hook.action;

        import com.liferay.portal.kernel.struts.BaseStrutsAction;
        import com.liferay.portal.kernel.util.ParamUtil;

        import javax.servlet.http.HttpServletRequest;
        import javax.servlet.http.HttpServletResponse;

        public class ExampleStrutsAction extends BaseStrutsAction {

            public String execute(
                HttpServletRequest request, HttpServletResponse response)
                throws Exception {
    
                String name = ParamUtil.get(request, "name", "World");
    
                request.setAttribute("name", name);
    
                return "/portal/sample.jsp";
            }

        }

We've overridden the `execute(HttpServletRequest, HttpServletResponse)` method
of `BaseStrutsAction`, but not the `execute(StrutsAction, HttpServletRequest,
HttpServletResponse)` method. The original Struts action's `execute()` method
is ignored. That's fine for our example.

---

 [Note](../../images/tip.png) **Best practice**: When overriding an existing
 Struts action, it's usually best to override the method that takes the original
 Struts action handle as a parameter and execute that original Struts action.
 Think of the original action as a servlet filter or aspect. If you override the
 method that *takes* the original action handle as a parameter and don't
 explicitly execute it, the original action won't be executed. If you override
 the `execute` method that *does not take* the original action as a parameter,
 you are ignoring the original action and it won't be executed.

---

That's it for overriding the Struts actions! Now let's get our new Struts
path working. 

1.  Create a `sample.jsp` file in the
    `example-hook/docroot/META-INF/custom_jsps/html/portal` directory. Insert
    the following code:

        <%
        String name = (String)request.getAttribute("name");
        %>
        Hello <%= name %>!

2.  Add `/portal/sample` to your portal's list of paths that don't require
    authentication by copying your existing `auth.public.paths` property
    assignment from your portal's `portal.properties` into your
    `portal-ext.properties` file and adding `/portal/sample` to the end of the
    value list. It looks similar to the assignment below:

            auth.public.paths=\
                /asset/get_categories,\
                ...
                /wiki/rss,\
                /portal/sample

3.  Restart your portal server. 

Congratulations! Your Struts action hook plugin is complete! Now when you access
the *Sign In* portlet, this message prints to your console:

    Wrapped /login/ action

When you actually log in, this message prints to your console:

    Wrapped /login/ action2
    Wrapped /login/ action

Both custom Struts actions are executed via your Struts action hook! 

Try your new Struts path by accessing it from your browser (e.g.
`http://localhost:8080/c/portal/sample`). 

![Figure 10.5: Your new Struts action displays *Hello World!* in your browser.](../../images/06-hooks-5.png)

Let's continue our hooks expedition by overriding a portal service.

## Overriding a Portal Service [](id=override-a-portal-service-with-hook-liferay-portal-6-2-dev-guide-en)

All the functionality provided by Liferay is enclosed in a layer of services
that are accessed by the controller layer in portlets. This is a standard
architecture, and it lets you change how a core portlet of Liferay behaves
without changing the portlet itself; you're customizing the backend services
that the portlet uses. You can leverage this architecture to customize portal
service behavior, and hook plugins are your tool for doing so. 

Liferay generates dummy wrapper classes for all its services. For example,
`UserLocalServiceWrapper` is created as a wrapper for `UserLocalService`, a
service for adding, removing, and retrieving user accounts. To modify the
functionality of `UserLocalService` from our hook, create a class that extends
`UserLocalServiceWrapper`, override the methods you want to modify, and instruct
Liferay to use your service class instead of the original. 

1.  Inside your example-hook project's
    `/docroot/WEB-INF/src/com/liferay/sample/hook` folder, create a new file
    called `MyUserLocalServiceImpl.java` with the following content:

        package com.liferay.sample.hook;
    
        import com.liferay.portal.kernel.exception.PortalException;
        import com.liferay.portal.kernel.exception.SystemException;
        import com.liferay.portal.model.User;
        import com.liferay.portal.service.UserLocalService;
        import com.liferay.portal.service.UserLocalServiceWrapper;
    
        public class MyUserLocalServiceImpl extends UserLocalServiceWrapper {
    
            public MyUserLocalServiceImpl(UserLocalService userLocalService) {
                super(userLocalService);
            }
    
            public User getUserById(long userId)
                throws PortalException, SystemException {
    
                System.out.println(
                    "## MyUserLocalServiceImpl.getUserById(" + userId + ")");
    
                return super.getUserById(userId);
            }
        }

    ---

    ![tip](../../images/tip-pen-paper.png) **Note:** The wrapper class
    (`MyUserLocalServiceImpl` in this example) will be loaded in the hook's
    class loader. That means it will have access to any other class included in
    the same WAR file, but *not* the *internal* classes of Liferay.

    ---

2.  Edit `liferay-hook.xml`, located in the `example-hook/docroot/WEB-INF`
    directory, by adding the following after `</custom-jsp-dir>`:

        <service>
            <service-type>
            com.liferay.portal.service.UserLocalService
            </service-type>
            <service-impl>
            com.liferay.sample.hook.MyUserLocalServiceImpl
            </service-impl>
        </service>

Redeploy your hook and refresh your browser. In the terminal window running
Liferay, you should see *## MyUserLocalServiceImpl.getUserById(...)* messages
displayed by your hook. 

There are other Liferay services that you may need to extend to meet advanced
requirements:

- **OrganizationLocalService:** Adds, deletes and retrieves organizations. Also
  assigns users to organizations and retrieves the list of organizations of a
  given user. 
- **GroupLocalService:** Adds, deletes and retrieves sites. 
- **LayoutLocalService:** Adds, deletes, retrieves and manages pages of sites,
  organizations and users. 

For a complete list of available services and their methods, check the [Liferay Portal 6.2 Javadocs](http://docs.liferay.com/portal/6.2/javadocs/)
or access the Javadocs for your version of Liferay at
[http://docs.liferay.com/portal](http://docs.liferay.com/portal) and click on
the *Javadocs* link. 

Now that you know how to override a portal service, let's learn how to override
a `Language.properties` file.

## Overriding a *Language.properties* File [](id=override-a-language-properties-hook-liferay-portal-6-2-dev-guide-en)

Hooks let you change any of the messages displayed by Liferay to suit your
needs. To do so, create a *Language* file for the locale of the messages you
want to customize, then refer to your file from your `liferay-hook.xml` file.
For example, to override the Spanish and French message translations of portal's
`Language.properties` file, create *Language* files for them in the same
directory and refer to these language files in your `liferay-hook.xml` file,
like this:

    <hook>
        ...
        <language-properties>content/Language_es.properties</language-properties>
        <language-properties>content/Language_fr.properties</language-properties>
        ...
    </hook>

---

 ![Tip](../../images/tip-pen-paper.png) **Tip:** Check the DTD of each Liferay
 XML file, you modify for the elements and attributes that can be included in the
 XML and the specified order for those elements. You can find the Liferay DTDs
 online here: [http://docs.liferay.com/portal/6.2/definitions](http://docs.liferay.com/portal/6.2/definitions).

---

Great! You now know how to customize language keys. Next, let's consider some
other types of hooks that may interest you.

## Other Hooks [](id=other-hooks-liferay-portal-6-2-dev-guide-10-en)

Since hooks are the preferred plugin type for customizing Liferay's core
features, the Liferay team is happy to keep providing you new hooks. This
section is a placeholder for hooks that are available in Liferay Portal 6.2,
but aren't yet fully documented. Here's a summary of these hooks:

- **Servlet filter hook:** Servlet filters allow you to pre-process requests
  going *to* a servlet and post-process responses coming *from* a servlet. As
  server requests are received that match URL patterns or match servlet names
  specified in your servlet filter mappings, your specified servlet filters are
  applied. Hook elements `servlet-filter` and `servlet-filter-mapping` have been
  added to `liferay-hook.xml` so you can configure your servlet filters. For a
  working example, see the
  [sample-servlet-filter-hook](https://github.com/liferay/liferay-plugins/tree/master/hooks/sample-servlet-filter-hook)
  in the Plugin SDK. 

- **CMIS extension hook:** The Documents and Media Library now supports multiple
  CMIS repositories mounted for each Documents and Media Portlet. If a
  repository doesn't fully implement CMIS or has native features that you'd like
  to leverage, you can use a *CMIS extension hook* to apply your desired
  extension implementation. 

## Summary [](id=summary-liferay-portal-6-2-dev-guide-10-en)

In this chapter, we discussed some of the many uses of the versatile hook
plugin, the preferred tool for customizing Liferay. You learned how to perform
custom portal actions, override and extend custom portal JSPs, modify portal
properties, and replace portal services and language properties. 

Next, we'll show you what it takes to develop apps for publishing to *Liferay
Marketplace*. 
