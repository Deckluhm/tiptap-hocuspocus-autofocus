<template>
  <main>
    <EditorContent v-if="editor" :editor="editor" />
  </main>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import { Editor, EditorContent } from "@tiptap/vue-3";
import Document from "@tiptap/extension-document";
import Paragraph from "@tiptap/extension-paragraph";
import Text from "@tiptap/extension-text";
import Collaboration from "@tiptap/extension-collaboration";
import { HocuspocusProvider } from "@hocuspocus/provider";
import * as Y from "yjs";

export default defineComponent({
  components: {
    EditorContent,
  },

  data: (): {
    editor: Editor | null;
    provider: HocuspocusProvider | null;
  } => ({
    editor: null,
    provider: null,
  }),

  mounted() {
    const ydoc = new Y.Doc();

    this.provider = new HocuspocusProvider({
      url: "ws://localhost:1234",
      name: "example-document",
      document: ydoc,
    });

    this.editor = new Editor({
      autofocus: "start",
      extensions: [
        Document,
        Paragraph,
        Text,
        Collaboration.configure({
          document: ydoc,
        }),
      ],
    });
  },

  beforeUnmount() {
    this.editor?.destroy();
    this.provider?.destroy();
  },
});
</script>

<style lang="scss">
/* Basic editor styles */
.ProseMirror {
  > * + * {
    margin-top: 0.75em;
  }
}

/* Placeholder (at the top) */
.ProseMirror p.is-editor-empty:first-child::before {
  content: attr(data-placeholder);
  float: left;
  color: #adb5bd;
  pointer-events: none;
  height: 0;
}
</style>
