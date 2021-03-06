<template>
  <div class="message is-info is-bold my-5">
    <div class="message-header mb-2">
      <h1 class="title has-text-white">{{ title }}</h1>
    </div>
    <div class="tile is-ancestor mx-1 py-2">
      <div class="tile is-parent is-3 px-0 py-0 my-2 mx-2">
        <div class="tile is-child box">
          <p class="mb-4">{{ subtitle }}</p>
            <button @click="setMode('html')" class="button mb-1">HTML</button>&nbsp;
            <button class="button mb-1" @click="setMode('css')">CSS</button>&nbsp;
            <button class="button mb-1" @click="setMode('preview');togglePrev();">Toggle preview</button>
        </div>
      </div>
      <div class="tile is-parent px-0 py-0 my-2 mx-2">
        <div class="tile is-child box is-vertical">
          <iframe class="my-2" style="height:100%;width:100%;" :srcdoc="preview['content']" v-if="preview['isAct']"></iframe>
          <div id="editors" style="height:100%;min-height:200px;" v-show="!preview['isAct']"></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import * as monaco from "monaco-editor";
export default {
  props: {
    title: String,
    subtitle: String,
    htmlProp: String,
    cssProp: String
  },
  data() {
    return {
      editors: null,
      editorHtml: null,
      editorCss: null,
      preview: { content: null, isAct: false },
    };
  },
  mounted() {
    this.editors = monaco.editor.create(document.getElementById("editors"), {
      theme: "vs-dark",
      minimap: { enabled: false },
      lineNumbers: "off",
      padding: { top: 15, bottom: 15 }
    });
    const html = this.htmlProp;
    const css = this.cssProp; 
    this.editorHtml = monaco.editor.createModel(html, "html");
    this.editorCss = monaco.editor.createModel(css, "css");
    this.editors.setModel(this.editorHtml);
    this.editors.onDidChangeModelContent(() => {
      this.setMode("preview");
    });
  },
  methods: {
    setMode(model) {
      if (model == "html") {
        this.editors.setModel(this.editorHtml);
        this.preview['isAct'] = false;
      } else if (model == "css") {
        this.editors.setModel(this.editorCss);
        this.preview['isAct'] = false;
      } else if (model == "preview") {
        const html = this.editorHtml.getValue();
        const css = this.editorCss.getValue();
        this.preview["content"] = html + `<style>${css}</style>`;
      }
    },
    togglePrev() {
        this.preview['isAct'] = !this.preview['isAct'];
    }
  },
};
</script>
