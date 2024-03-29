# VisualEditorLineBreaks
Improvements to address single line break issues with VisualEditor

## Features

 1. Allows single line-breaks via shift-enter within paragraphs.
 2. Allows single line-breaks via shift-enter within lists.
 3. Avoids newline characters being displayed as special characters by VisualEditor. Instead, VisualEditor will display a single space, matching what is shown in the content view.

The resulting markup matches what would be expected when having line breaks in paragraphs. After inserting single line spaces in VisualEdior, the source looks like:

```
Some text and<br />this goes to a new line 

* Some item
* Another item<br />this goes to a new line
* Extra item

Normal paragraph 1

Normal paragraph 2
```

## Technical implementation notes
 1. Allows VisualEditor `paragraph` nodes to be treated as content nodes with significant namespaces. This prevents special newline characters from appearing on the surface.
 2. Handles the VisualEditor shift modifier for `paragraph` nodes to insert a `break` type of node (`<br>`) within the `paragraph` node.
 3. Relaxes the parsoid DOMHandler BRHandler, preventing it from stripping `<br>` nodes from paragraphs (which it does by default) when doing its **html2wt** processing.
 
 ## Installation
 ### Patching the parser
Switch to the [WikiTeq fork](https://github.com/WikiTeq/mediawiki-services-parsoid) of `wikimedia/mediawiki-services-parsoid`. WikiTeq cloned and patched the `0.12.3` version of Parser that is used by MediaWiki version 1.35. The method for doing this depends on your MediaWiki setup.
 
Klarna can accomplish this by modifying the `_bluespice/build/klarna/composer.json` file of its **bluespice** repository and rebuilding the artifact. The `repositories` section currently exists, and it can be modified to include:
 ```
	"repositories": [
		{
			"type": "vcs",
			"url": "https://github.com/WikiTeq/mediawiki-services-parsoid.git"
		}
	]
```

### Patching VisualEditor

* Put both `.diff` patches into `_bluespice/patches/extensions/VisualEditor/lib/ve` directory

WikiTeq has tested and confirmed the patch applies using the latest commit of the Klarna **bluespice** repo.
