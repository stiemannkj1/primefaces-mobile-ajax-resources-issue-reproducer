# [PrimeFaces #1759](https://github.com/primefaces/primefaces/issues/1759) Reproducer

This project is a reproducer for
[PrimeFaces #1759](https://github.com/primefaces/primefaces/issues/1759): *Unecessary non-mobile resources loaded when navigating to PrimeFaces mobile view via Ajax*

## Steps to reproduce:

1. Clone the reproducer project:

        git clone https://github.com/stiemannkj1/primefaces-mobile-ajax-resources-issue-reproducer

2. Build the project with maven:

        cd primefaces-mobile-ajax-resources-issue-reproducer && mvn clean install

3. Deploy the project to tomcat:

        cp target/primefaces-mobile-ajax-resources-issue-reproducer*.war $TOMCAT_HOME/webapps/

4. Navigate to **`index.xhtml`** ([http://localhost:8080/primefaces-mobile-ajax-resources-issue-reproducer-1.0-SNAPSHOT/faces/index.xhtml](http://localhost:8080/primefaces-mobile-ajax-resources-issue-reproducer-1.0-SNAPSHOT/faces/index.xhtml)).

5. Right click on the page and select *Inspect Element*.

6. Click on the *Network* tab to see what resources are loaded.

7. Click the *page2* `h:commandButton`.

If the bug still exists, then **`components.js`** will be loaded via `GET`. If the bug is fixed, then **`components.js`** and all other non-mobile resources will not be loaded.

## Notes:

- During a non-ajax request, non-mobile resources are suppressed by the [PrimeFaces mobile `HeadRenderer`](https://github.com/primefaces/primefaces/blob/6_0/src/main/java/org/primefaces/mobile/renderkit/HeadRenderer.java#L96-L104):

	```
	if(library != null && library.equals("primefaces")) {
		String resourceName = (String) attrs.get("name");
		if(resourceName.startsWith("jquery")
				|| resourceName.startsWith("primefaces")
				|| resourceName.startsWith("components")
				|| resourceName.startsWith("core")) {
			shouldRender = false;
		}
	}
	```

- This bug only occurs when navigating from pages which do not contain **`components-mobile.js`** or **`components.js`** already.
- This bug causes JS errors in a portlet environment.
- This issue only occurs on PrimeFaces 6.0. This issue does not occur with PrimeFaces `5.3` and below.
