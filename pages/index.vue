<template>
  <div>
    <div class="actions text-center p-3">
      <button
        class="button button--red"
        @click="clear"
      >
        clear
      </button>

      <button
        class="button"
        @click="copy"
      >
        copy
      </button>
    </div>

    <div class="flex">
      <div class="markdown-editor">
        <textarea :id="id" v-model="code" />
      </div>

      <div class="markdown-body__wrapper">
        <div class="markdown-body" v-html="codeOutput" />
      </div>
    </div>
  </div>
</template>

<script>
import CodeMirror from 'codemirror'
import '../node_modules/codemirror/mode/markdown/markdown'
import DOMPurify from 'dompurify'
import marked from 'marked'

export default {
  data () {
    return {
      code: `### Headings

# Heading 1
## Heading 2
### Heading 3

### Bold

**bold text**

### Italic

*italic text*

### Blockquote

> This is a blockquote.

### Ordered List

1. First item
2. Second item
3. Third item

### Unordered List

- First item
- Second item
- Third item

### Code Block

\`\`\`
{
  "firstName": "Marko",
  "lastName": "Denic",
  "twitterHandle": "denicmarko"
}
\`\`\`

### Inline Code

This is an \`inline code\`.

### Horizontal Rule

---

### Link

[Link Text](https://www.example.com)

### Image

![This is an ALT text](/icon.png "This is a title text.")

### Table

| firstName   | LastName |
| ----------- | -------- |
| John        | Doe      |
| Jane        | Doe      |
| Marko       | Denic    |

### Strikethrough

~~The world is flat.~~

### Task List

- [x] Create an app
- [ ] Deploy it
- [ ] Announce it`,
      id: 'md-editor',
      codemirror: null
    }
  },

  computed: {
    codeOutput () {
      return DOMPurify.sanitize(marked(this.code, {
        highlight: code => require('highlight.js').highlightAuto(code).value
      }))
    }
  },

  mounted () {
    this.init()
  },

  methods: {
    init () {
      this.codemirror = CodeMirror.fromTextArea(document.getElementById(this.id), {
        mode: 'markdown',
        lineNumbers: true,
        theme: 'default',
        extraKeys: { Enter: 'newlineAndIndentContinueMarkdownList' }
      })

      this.codemirror.on('change', (cm) => {
        this.code = cm.getValue()
      })
    },
    clear () {
      this.codemirror.doc.setValue('')
    },
    copy () {
      if (!navigator.clipboard) {
        // Clipboard API not available
        alert('It looks like you can\' use this button! Please, copy the code with CTRL + C / CMD + C!')
      }
      navigator.clipboard.writeText(this.code).then(() => {
        // Copied to clipboard
      }, () => {
        alert('It looks like you can\' use this button! Please, copy the code with CTRL + C / CMD + C!')
      })
    }
  }
}
</script>
