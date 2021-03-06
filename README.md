# mdast-util-noddity

[Micromark](https://github.com/micromark/micromark) and [mdast](https://github.com/syntax-tree/mdast) extension to parse [noddity](http://noddity.com/) flavored markdown.

**Note:** if you're looking for a more complete library, have a look at [noddity-micromark-renderer](https://github.com/saibotsivad/noddity-micromark-renderer). This package does not resolve Noddity links or anything like that, it just parses the markdown to a tree.

## Install

This package is ESM only.

Install the usual ways:

```bash
npm install mdast-util-noddity
```

## Use

Say we have the following markdown file `example.md`:

```md
This is an [[file.md|internal link]] to a file. Here is a ::template.md|cars|wheels=2:: with variables.
```

...and our module `example.js` looks as follows:

```js
import { readFile } from 'node:fs/promises'
import { fromMarkdown } from 'mdast-util-from-markdown'
import { micromarkFromNoddity, mdastFromNoddity } from 'mdast-util-noddity'

const string = await readFile('example.md', 'utf8')
const tree = fromMarkdown(string, {
	extensions: [ micromarkFromNoddity() ],
	mdastExtensions: [ mdastFromNoddity() ],
})

console.log(tree)
```

…now running node `example.js` yields (positional info removed for brevity):

```json
{
	"type": "root",
	"children": [
		{
			"type": "text",
			"value": "This is an "
		},
		{
			"type": "noddityLink",
			"file": "file.md",
			"text": "internal link"
		},
		{
			"type": "text",
			"value": " to a file. Here is a "
		},
		{
			"type": "noddityTemplate",
			"file": "template.md",
			"children": [
				{
					"type": "noddityTemplateVariable",
					"name": "cars"
				},
				{
					"type": "noddityTemplateVariable",
					"name": "wheels",
					"value": "2"
				}
			]
		},
		{
			"type": "text",
			"value": " with variables."
		}
	]
}
```

## Tree

These are the defined output node types.

#### `noddityLink`

This corresponds to the Noddity link syntax, and has these properties:

* `type = "noddityLink"` - The type.
* `file: String` - The file reference part of the link.
* `children?: Array<MdastTree>` - *(Optional)* If there is a text part of the link, it will be parsed as `mdast` itself, so child elements will be a deeper `mdast` tree.

#### `noddityTemplate`

This corresponds to the Noddity template syntax, and has these properties:

* `type = "noddityTemplate"` - The type.
* `file: String` - The file reference part of the link.
* `children?: Array<noddityTemplateVariable>` - *(Optional)* An ordered array of defined variables.

#### `noddityTemplateVariable`

These are the variables used in the template syntax, they have these properties:

* `type = "noddityTemplateVariable"` - The type.
* `name: String` - If this is a named variable, the name will be here.
* `value?: String` - The value of the variable.

## To-Do

The to-markdown portion of this package is not yet available. I don't have any use for it, so I probably won't get around to adding it, but pull requests will be accepted.

Test coverage for Noddity link and template syntax is very weak. Pull requests to add tests would be very welcome.

## License

Published and released under the [Very Open License](http://veryopenlicense.com).

If you need a commercial license, [contact me here](https://davistobias.com/license?software=mdast-util-noddity).
