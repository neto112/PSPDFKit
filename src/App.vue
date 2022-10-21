<template>
  <div id="app">
    <label for="file-upload" class="custom-file-upload">
      Open PDF
    </label>
    <input id="file-upload" type="file" @change="openDocument" class="btn" />
      <PSPDFKitContainer :pdfFile="pdfFile" @loaded="handleLoaded" />
  </div>
</template>

<script>
import PSPDFKitContainer from "@/components/PSPDFKitContainer";
import PSPDFKit from "pspdfkit";

export default {
  data() {
    return {
      pdfFile: this.pdfFile || "/pspdfkit-web-demo.pdf",
    };
  },
  components: {
    PSPDFKitContainer,
  },
  methods: {
    handleLoaded(instance) {
      // console.log("PSPDFKit has loaded: ", instance);
      // this.mergePDFs(instance)
      // this.rotatePages(instance, [0, 1])
      // this.addPage(instance);
      this.removePages(instance, [0])
    },
    openDocument(event) {
      if (this.pdfFile && this.pdfFile.startsWith('blob:')) {
        window.URL.revokeObjectURL(this.pdfFile);
      }
      this.pdfFile = window.URL.createObjectURL(event.target.files[0]);
    },
    mergePDFs(instance) {
      fetch(this.pdfFile)
        .then((res) => {
          if (!res.ok) {
            throw res;
          }
          return res;
        })
        .then((res) => res.blob())
        .then((blob) => {
          instance.applyOperations([
            {
              type: "importDocument",
              beforePageIndex: 0,
              document: blob,
            }
          ])
        })
    },
    rotatePages(instance, pageIndexes) {
      instance.applyOperations([
        {
          type: "rotatePages",
          pageIndexes,
          rotateBy: 180,
        }
      ])
    },
    addPage(instance) {
      instance.applyOperations([
        {
          type: "addPage",
          afterPageIndex: instance.totalPageCount -1,
          backgroundColor: new PSPDFKit.Color({ r: 100, g: 200, b: 255}),
          pageWidth: 750,
          pageHeight: 1000,
        }
      ])
    },
    removePage(instance, pageIndexes) {
      instance.applyOperations([
        {
          type: "removePages",
          pageIndexes,
        },
      ]);
    },
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
body {
  margin: 0;
}
input[type="file"] {
    display: none;
}
.custom-file-upload {
    border: 1px solid #ccc;
    border-radius: 4px;
    display: inline-block;
    padding: 6px 12px;
    cursor: pointer;
    background:#4A8FED;
    padding:10px;
    color:#fff;
    font:inherit;
    font-size: 16px;
    font-weight: bold;
}
</style>
