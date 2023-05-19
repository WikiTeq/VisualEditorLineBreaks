# VisualEditorLineBreaks
Improvements to address the single line break issue with VisualEditor

## Features

 1. Allows single line-breaks via shift-enter within paragraps.
 2. Allows single line-breaks via shift-enter within lists.
 3. Avoids newline characters being displayed as special characters by VisualEditor. Instead, VisualEditor will display a single space, matching what is shown in the content view.

The resulting markup is the closest to what is expected of having line breaks in paragraphs, e.g.:

    enter code here
```
Some text and<br />this goes to the new line 

* Some item
* Another item<br />this goes to the new line
* Extra item

Normal paragraph 1

Normal paragraph 2
```

## Technical implementation notes
 1. Allows VisualEditor `paragraph` nodes to be treated as content nodes with
    significant namespaces. This prevents special newline characters
    from appearing on the surface.
 2. Handles VE shift modifier for `paragraph` nodes to insert a `break` type of node (`<br>`) within the `paragraph` node.
 3. Relaxes the parsoid DOMHandler BRHandler, having it not strip `<br>` nodes from paragraphs (which it does by default) when doing its **html2wt** processing.
 
 ## Installation
 ### Patching the parser
 Switch to the [WikiTeq fork](https://github.com/WikiTeq/mediawiki-services-parsoid/commit/c8af61bec0d4420567177e60031b20b4a71f1f2b) of  `wikimedia/parser`. WikiTeq cloned and patched the `0.12.3` version of Parser that is used by MediaWiki version 1.35. The method for doing this depends on your MediaWiki setup.
 
 Klarna can accomplish this by modifying the `_bluespice/build/klarna/composer.json` file of its **bluespice** repository and rebuilding the artifact. The `repositories` section currently exists, and it can be modified to include:
 ```
	"repositories":[
		{
			"type": "vcs",
			"url": "https://github.com/WikiTeq/mediawiki-services-parsoid.git"
		}
	]
```

Once that file has been modified, run `composer update --no-dev`.

### Patching VisualEditor
The [Ve.lib.ve.9208da11345f71c4f56bae2ace6319bd482d8745.br.patch](https://github.com/WikiTeq/VisualEditorLineBreaks/blob/master/Ve.lib.ve.9208da11345f71c4f56bae2ace6319bd482d8745.br.patch) patch applies to the `lib/ve` submodule of `VisualEditor` extension. Therefore, it must be applied after the full VisualEditor installation, after `git submodule update --init` has been performed. 

WikiTeq has tested and confirmed the patch applies using the latest commit of the Klarna **bluespice** repo within `_bluespice/pre-autoload-dump.d/99-apply_patches.sh`.
