# dumbo

A planned C library for executing CSS selectors on HTML trees from a variety of
libraries, including gumbo.

## Goals

* Portable: Uses C99 and autotools
* Small: Focussed feature-set *only* for retrieving elements from HTML trees
* Customisable: Allows compile-time toggling of HTML library support
* Support CSS 3 (including the deprecated `:contains()`)
* Support selections on streaming HTML parsers (e.g., SAX parsers)

## Implementation notes

As much as possible, `dumbo` shouldn't do any allocations.  So, the API for
executing selectors should be an iterator interface, and take `void *` since
client code should know what kind of tree they have (and thus, node type).
Upon match, the library should just modify the out parameter to point to the
element.

Example code:

```c
#include <dumbo.h>
// ...
char *html = ...;
char *selector = ...;

MyTree *tree = mytree_parse(html);
MyTree *root = mytree_getroot(tree);
// Wraps into a struct that stores the library's tag (some internal enum).
dumbo_tree dtree = DUMBO_TREE_WRAP_MYTREE(root);
dumbo_css *css = dumbo_css_parse(selector);

MyTree *node;
while (dumbo_css_exec(css, &dtree, &node) == 0) {
	// ...
}
// ...
dumbo_css_free(css);
mytree_free(tree);
```
