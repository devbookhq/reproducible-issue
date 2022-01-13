# Reproducible issues with Devbook

This is an example of how Devbook can be use to reproduce issues.

**This Devbook has a full-fledged VM attached to it that both the author and a reader can use.**

Issues created with Devbook allow developers to quickly recreate the problem and share it with their collegues. They can start exploring the problem and fixing it right away.

## It's interactive!

Every piece of code here can edited and executed. You can deploy any container to the attached VM  and run them alongside. You can see the environment running in the left sidebar.

## It has multiplayer!

Devbook support collaborative editing. Open this URL in another window and you'll see each other's cursor.

Each session has it's own VM.

## Issue we are about to reproduce

We will create an issue for the <https://github.com/ueberdosis/tiptap> package using the Next.js template. TipTap is a package that allows you to easily create rich text editors. You are reading this in a TipTap editor too!

# Unmounting TipTap editor throws error - fails to execute `removeChild` 

When we unmount component with a TipTap editor from the `useEditor` hook we get an unhandled runtime error. This happens only when the component we are unmounting has a `BubbleMenu` and when the component is returning a React fragment.

Using `@tiptap/react` on version `2.0.0-beta.104`

## Examples

### Error example

The following code reproduces the issue - when you click on the "Change editor" in the iframe you get the runtime error:

**NotFoundError: Failed to execute 'removeChild' on 'Node': The node to be removed is not a child of this node.**

<meta cell-type="iframe" src="https://3000-ckya7y0x7143607609ijv1m9oawa_1ad1629e-f565ad896794.o.usedevbook.com/cc">

```tsx {"cell-id":"kq0jofgm","cell-name":"cc.tsx","document-env-id":"1ad1629e","template-id":"nextjs-v11-components"}
import { useEditor, BubbleMenu } from '@tiptap/react'
import StarterKit from '@tiptap/starter-kit'
import { useState } from 'react'

function Editor(props) {
  const editor = useEditor({
    extensions: [StarterKit],
    content: `<p>Editor ${props.editorID} text</p>`,
  })

  return (
    <>
      {props.editorID}
      <BubbleMenu editor={editor}></BubbleMenu>
    </>
  )
}

function ExampleWithError() {
  const [selectedEditorID, setSelectedEditorID] = useState<number>(1)

  function switchEditor() {
    setSelectedEditorID(e => e === 1 ? 2 : 1)
  }
  
  return (
    <>
      Example with error
      <button onClick={switchEditor}>
        Change editor
      </button>
      <div>
        {selectedEditorID === 1 && <Editor editorID={1}/>}
        {selectedEditorID === 2 && <Editor editorID={2}/>}
      </div>
    </>
  )
}

export default ExampleWithError
```

### Working example with a "hack"

In this example the unmount error does not happen. In the `Editor` component we changed the React fragment to a `div`. Maybe the error is caused by something like <https://github.com/facebook/react/issues/15985>?

<meta cell-type="iframe" src="https://3000-ckya7y0x7143607609ijv1m9oawa_1ad1629e-f565ad896794.o.usedevbook.com/cc-ctf">

```tsx {"cell-id":"zpe6khp3","cell-name":"cc-ctf.tsx","document-env-id":"1ad1629e","template-id":"nextjs-v11-components"}
import { useEditor, BubbleMenu } from '@tiptap/react'
import StarterKit from '@tiptap/starter-kit'
import { useState } from 'react'

function Editor(props) {
  const editor = useEditor({
    extensions: [StarterKit],
    content: `<p>Editor ${props.editorID} text</p>`,
  })

  return (
    // <----- When we have a div here instead of a React fragment
    // the unmount error does not happen
    <div>
      {props.editorID}
      <BubbleMenu editor={editor}></BubbleMenu>
    </div>
  )
}

function ExampleWithHack() {
  const [selectedEditorID, setSelectedEditorID] = useState<number>(1)

  function switchEditor() {
    setSelectedEditorID(e => e === 1 ? 2 : 1)
  }
  
  return (
    <>
      Example with hack
      <button onClick={switchEditor}>
        Change editor
      </button>
      <div>
        {selectedEditorID === 1 && <Editor editorID={1}/>}
        {selectedEditorID === 2 && <Editor editorID={2}/>}
      </div>
    </>
  )
}

export default ExampleWithHack
```